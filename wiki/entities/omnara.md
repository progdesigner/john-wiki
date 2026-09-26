---
tags: [entity, external-project, agent-architecture, durable-execution, reference, go, postgres, uninstalled]
created: 2026-09-26
updated: 2026-09-26
---
# omnara

`[[progdesigner]]`의 프로젝트가 아니라 **비교·레퍼런스용으로 분석한 외부 durable-agent 런타임**.
[[2026-09-18-lampas-agent-omnara분석-durable-run구현]] 세션에서 서브에이전트가 코드베이스를 정독해
"프로세스가 아니라 DB 상태 머신으로서의 에이전트" 설계를 [[lampas-agent]] 개선안의 근거로 삼았다.
Go 백엔드 + PostgreSQL, 프론트엔드 SDK(`frontend/packages/sdk`) 보유.

> ⚠️ **같은 이름의 실사용 설치와는 별개 사건** — 위 분석은 코드베이스를 읽은 것이고, 이와 별도로
> 맥미니에 **실제 Omnara 소프트웨어**(`Omnara.app`, CLI, `claude_wrapper`, `omnara-voice` 포함,
> `com.omnara.daemon` LaunchAgent로 상주)가 설치돼 있었다. [[macos-launchctl-cleanup-candidates]]의
> 2026-07-16 launchctl 원시 로그에 `com.omnara.daemon`이 이미 찍혀 있어 최소 그 시점부터 설치돼
> 있었던 것으로 보이나, 그 세션에서는 분석 대상에서 누락됐다. **2026-09-26**: 사용자 요청으로
> 완전 제거 완료(`~/.omnara` 512MB 전체 삭제, LaunchAgent 등록 해제, `.zshrc` PATH 설정 제거,
> 관련 캐시 삭제 — 절차는 [[macos-app-complete-uninstall]] 스킬로 추출) →
> [[2026-09-26-omnara-완전제거]]. Claude Code 자체 세션 기록(`~/.claude.json`의 옛 워크트리 경로
> 프로젝트 항목 14개, `~/.claude/projects`의 관련 세션 2개)은 Omnara 소프트웨어가 아니라 대화
> 기록이라 판단해 삭제하지 않고 보존.

## 핵심 철학
**에이전트는 장기 실행 프로세스가 아니라 DB 상태 머신이다.** 상태 없는 워커 풀이 트랜잭션 하나로
"다음 한 단계"를 claim → 실행 → 커밋 → 반납한다. 메모리에 아무것도 남지 않으므로 워커가 죽어도
복구가 "공짜"다.

## 1. 에이전트 생명주기·durability (테이블 설계)
- `agents` — `state ∈ (active, archived)`, `next_event_sequence`(단조증가), `idempotency_key` 프로젝트
  내 유일. id/org/project/idempotency_key 변경을 트리거로 금지.
- `agent_events` — append-only 로그. `event_kind ∈ (agent_input, model_output, tool_result,
  context_checkpoint)`, `UNIQUE(agent_id, sequence)`, `UNIQUE(agent_id, idempotency_key)`, UPDATE/DELETE를
  트리거로 원천 차단해 **DB 레벨에서 진짜 불변**.
- `agent_inputs` — 인박스. `state ∈ (received, resolved, rejected, canceled)`, `delivery_mode ∈ (queued,
  steering, immediate)`. 거대한 CHECK 제약으로 불법 상태 조합을 아예 표현 불가능하게 만듦. 입력은
  `admitted_event_id`로 실제 이벤트에 연결돼야만 "히스토리"가 된다.
- `agent_turns` — 턴 시퀀스, 매 턴은 opening event 필수(지연 제약 트리거).
- `agent_wakeups(agent_id PK, ready_at)` — 스케줄링 프론티어(할 일이 있는 에이전트 한 줄).
- `agent_runtime_locks(agent_id UNIQUE, worker_process_id, lease_expires_at, cancel_requested_at)` —
  상호배제 + 크래시 복구의 핵심 primitive. `cancel_requested_at`으로 협조적 취소도 지원.
- `model_call_contexts` — 모델 호출 시도 1회의 durable 레코드: `operation_kind ∈ (normal, compaction)`,
  `attempt_number`, `state`, `recovery_kind ∈ (retry, compact, reduce_compaction_source)`, `retry_at`,
  토큰 종류별 카운트(합이 `input_tokens_total`과 일치해야 한다는 CHECK).
  `tool_calls` — `CHECK((state='running') = (runtime_lock_id IS NOT NULL))`: 실행 중인 툴은 반드시
  살아있는 리스가 있어야 함이 스키마 레벨에서 증명됨.

**루프**: `Worker.Run`이 `capacity`개 고루틴으로 `runLoop`→`RunOnce` 반복. `RunOnce`(`ClaimNextAgentWork`)는
**단일 트랜잭션**에서 (1) due-wakeup pop, (2) 다음 한 단계 우선순위 결정(툴 작업 → 미완료 툴 배치 대기
→ steering 입력 → 모델 작업 → 재시도 대기 → 큐 입력 → idle), (3) 런타임 리스 획득, (4) 필요시 입력
admit + 턴 개시를 원자적으로 처리. "툴 작업과 모델 작업이 동시에 있다"는 상태는 허용되는 레이스가
아니라 **에러**로 취급.

**복구**: 메모리엔 아무것도 없다. 죽은 워커의 리스는 그냥 만료되고, 별도 maintenance 프로세스가
배치로 만료 리스를 회수하면 다음 claim이 `ModelWorkResume`/`Continue`로 복구한다. 진행 중 리스는
갱신되고, 갱신 실패 시 해당 작업이 스스로 취소돼 중복 쓰기를 막는다.

**멱등성**은 여러 층에 걸쳐 있다: `agents.idempotency_key`, `agent_events.idempotency_key`
(`'agent_input:'+input.id`), `agent_inputs`의 idempotency scope/key, `tool_calls UNIQUE(agent_id,
model_output_id, provider_call_id)`, cron의 `"cron_trigger:"+id+":"+dueAt.RFC3339`.

## 2. 이벤트 모델·전송
이벤트 종류는 4개뿐, 본문은 kind별 사이드 테이블에. 전송은 WS가 아니라 **SSE** — Postgres
LISTEN/NOTIFY 저지연 경로 + 10초 폴링 백업 경로(reconciler) 이중화, 알림은 트랜잭션 커밋 후에만
발행. 클라이언트(`openAgentEventStream`)는 durable `cursor`(마지막 sequence) 유지, `Last-Event-ID`로
재접속, `sequence <= cursor`면 스킵, 풀지터 백오프(1s→30s), 서버 하트비트보다 긴 35초 stall
타임아웃, transport/503/408/429/5xx만 재시도.

## 3. 툴·스킬
`agent.yaml`은 JSON Schema로 검증되며 **불변** — 새 설정을 만들고 프로필을 optimistic concurrency로
재포인트하는 방식. **"deferred tools + `tool_search`"**가 특히 눈여겨볼 아이디어: 툴을 `deferred: true`로
표시해 모델의 툴 배열에서 빼두고, 모델이 `tool_search{pattern, max_results}`로 스키마를 필요할 때만
당겨온 뒤 `call_deferred_tool`로 호출 — 대형 MCP 카탈로그가 컨텍스트를 잡아먹지 않는다.

오버플로 처리 패턴: 큰 관측값을 컨텍스트에 그대로 넣지 않고 **blob 아티팩트**로 저장한 뒤, 잘린 텍스트
+ `media_ref`(artifact_id)만 반환, 모델이 필요하면 페이지 단위 `read_file`로 더 읽는다.

권한은 `always_allow | always_ask | always_deny` 3단, `always_ask`는 `agent_interactions` 행을 만들고
파킹.

## 4. 머신/데몬 프로토콜
전형적 "명령→실행"이 아니라 **offer → accept → ack** 3단 프로토콜. `AckStatus ∈ {committed,
cleanup_only, permanent_reject, transient_error}`로 "저장했다"와 "잊어도 된다"를 구분. 하트비트 간격은
서버가 매 ack마다 지시(`next_heartbeat_after_ms`)해 클라이언트 재배포 없이 조절 가능. 재접속 시
로컬 상태 정리를 먼저 한 뒤 재연결. 전달 실패한 리포트는 로컬 아웃박스에 쌓였다가 재접속 시 재생
(at-least-once + 서버측 `ReportID` 멱등).

## 5. 스케줄링(cron)
5필드 cron + 명시적 `timezone` 필드(`TZ=` 접두사는 문법 차원에서 거부 — 시간대는 데이터지 문법이
아니다). 배치 클레임에 `ClaimToken` 부여, 파싱 불가 스케줄은 자동 비활성화. 실패 시 재시도 여부를
`permanentFireError` 분류로 판단. 템플릿은 크기·시간 상한이 있는 샌드박스 `text/template`.

## 6. 컨텍스트 압축
`RecentTailTargetTokens = min(usable/4, 20_000)` — 최근 구간은 항상 verbatim으로 25%까지 유지. 리텐션
경계는 툴콜/툴결과 쌍을 절대 자르지 않는 "안전한 경계"만 선택. 요약은 **누적 append-only 체크포인트**
(덮어쓰기 아님), 고정 구조(`## Goal/Instructions/Progress/Artifacts/Next Steps`) 강제. **연속 체크포인트
최대 3회 상한** + "프론티어를 실제로 전진시키는가" 가드로 압축 무한루프를 원천 차단. 압축 자체도
`model_call_contexts` 행으로 기록돼 크래시 복구·재시도 대상이 된다.

## 7. Human-in-the-loop
`agent_interactions(tool_call_id, kind ∈ (permission, question), state)` — 승인 필요한 툴은
`awaiting_permission`에 파킹, 응답은 `interaction_response` kind의 `agent_inputs` 행으로 도착해 같은
durable 입력 파이프라인을 통과. Slack이 양방향 1급 시민(스레드 답장이 입력이 되고, 승인/질문이 Block
Kit 버튼으로 렌더링).

## 8. 예시 에이전트 (신호 수집형)
posthog-pulse-agent(호스팅 MCP만, write 툴 URL 필터로 배제)·x-signal-agent(전용 머신 + curl)·
reddit-signal-agent(Apify MCP 비동기 폴링)로 모두 provisioning 스크립트 하나가 config→profile→agent
생성→이벤트 스트림 구독까지 수행. 재사용 가치가 큰 것은 **프롬프트 규율**: 하드 호출 예산("정확히
검색 1회", "MCP 호출 8회 이하"), 재시도 루프 금지("실패하면 본문 보고 후 종료"), 명시적 빈 결과 허용
("오늘은 볼 것 없음"이 좋은 결과), 고정 출력 스키마, 실행 간 완전 무상태(매번 베이스라인 재계산).

## 9. 관측성
`internal/log`는 라인 로깅이 아니라 span형 — `NewEvent`가 필드·자식 트레이스를 누적하다 `Done()`
시점에 레코드 1개만 방출. 도메인별 typed attacher 어휘집으로 필드명 드리프트 방지. Prometheus
수집기 + build-tagged blackbox 스위트(배포된 인스턴스를 공개 HTTP API로만 검증, 내부 임포트 없음).

## lampas-agent에 이식된 아이디어 (2026-09-18)
[[lampas-agent]]가 이 분석을 근거로 실제 구현한 것 → [[2026-09-18-lampas-agent-omnara분석-durable-run구현]]
"4. 구현" 절 참고. 이식된 핵심 5가지: append-only 이벤트 로그 + 파생 상태(SQLite `data/agent.db`),
리스 기반 단일 실행 락, `claimNextWork()` 단일 트랜잭션 클레임, 시도 단위 durable 레코드, 멱등키 조합
패턴. 전체 10개 순위 목록은 → [[durable-agent-runtime-design-patterns]] 스킬 페이지.

## 관련
- 세션: [[2026-09-18-lampas-agent-omnara분석-durable-run구현]](코드 분석), [[2026-09-26-omnara-완전제거]](실사용 설치 제거)
- 엔티티: [[lampas-agent]](이식 대상), [[progdesigner]](맥미니 운용자)
- 스킬: [[durable-agent-runtime-design-patterns]], [[macos-app-complete-uninstall]](제거 절차)
- 토픽: [[lampas-system-ai-call-architecture-audit]](비교 대상이 된 lampas-system 쪽 현황), [[macos-launchctl-cleanup-candidates]](2026-07-16 시점 이미 노출됐던 흔적)
