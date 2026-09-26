---
tags: [session, lampas-agent, lampas-studio, omnara, agent-architecture, durable-execution, git, security]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-18 lampas-agent 고도화 — omnara 비교분석 + durable run 구현

`lampas-system` 작업 디렉토리, Tool: claude. 2026-09-18T08:23 시작. **2026-09-26 뒤늦게 ingest**
(원본: `raw/conversations/2026-09-18-lampas-agent-omnara분석-durable-run구현.md`, 토큰 1개 레닥트 처리 —
[[secrets-plaintext-exposure-pattern]] 참고).

> ⚠️ **타임라인 정정**: [[lampas-agent]] 엔티티는 지금까지 "위키 최초 노출"을 2026-09-19로 기록해왔다
> (원래 09-20 기록 → 09-19로 한 차례 정정됨). 이 세션은 그보다 **하루 이른 2026-09-18**에 이미
> `apps/lampas-agent`가 존재(막 병합 완료)했음을 보여준다 — 아래 "코드 최신화" 절 참고.

## 1. 코드 최신화 (git)
원격 main에 12개 커밋, 로컬엔 미커밋 변경 119개 파일이 쌓여 있었다. `stash → fast-forward → pop`
방식으로 진행, 충돌 4건 발생·해결:

- 원격의 핵심 변경은 **`lampas-agent-clips` + `lampas-agent-pulse` (별도 두 앱) → `apps/lampas-agent`
  (단일 앱) 병합** — 즉 `apps/lampas-agent`는 이 시점 직전까지 두 개의 분리된 앱이었다가 막 하나로
  합쳐진 상태. launchd 라벨도 `io.lampas.agent.daemon`으로 이때 바뀜.
- 루트 `CLAUDE.md`: 로컬 내용(trends 모듈·scenario 보이스 제거) 유지 + 원격의 이름 변경만 반영.
- 에이전트 `LOG.md`: 09-17 병합 기록 + 09-15 Tailscale 기록 둘 다 보존.
- `setup-tailscale.sh`: 원격 `AGENT_*` 변수명 위에 로컬 443 기본값 + Funnel 주석 합침.
- 원격에서 삭제된 구 `lampas-agent-clips/CLAUDE.md`의 로컬 수정분(접속 URL·OAuth/S3 CORS 전제)을
  새 `apps/lampas-agent/CLAUDE.md`로 이식.
- `pnpm install` 후 `apps/lampas-agent` 테스트 30개 파일 260개 모두 통과. 구 `apps/lampas-agent-clips`·
  `lampas-agent-pulse`엔 비추적 잔재(`dist/logs/node_modules`)만 남아 삭제 가능 상태.

## 2. 병렬 아키텍처 분석 (subagent 2개)
"코드 최신화"와 별개로, 두 서브에이전트를 병렬로 띄워 (a) `lampas-agent` 자체 구조와 (b)
**omnara**(외부 durable-agent 런타임 레퍼런스, [[omnara]])의 설계를 각각 분석시켰다.

### (a) lampas-agent 구조 분석 결과 (요약)
- **서버**(`serve.ts`): `/healthz`·`/v1/*`(로컬 lampas-api 프록시)·`/bank/v1/*`(api.lampas.io 프록시)·
  정적/Vite. 프록시는 타임아웃·재시도·바디 크기 제한·업스트림 헬스체크 **전무**. `/pulse/ws` WS에
  **인증·origin 검사 없음** — Funnel로 공개된 URL이면 로그인 쿠키를 쥔 Chrome에 누구나 클릭·키 입력
  주입 가능.
- **Pulse 엔진**: OBSERVE/DECIDE/ACT/VERIFY/EXTRACT는 로그 라벨일 뿐, `src/server`에 **LLM 호출
  0건** — 스크롤→방문→정규식 추출→`select.ts` 고정 점수식의 하드코딩 스크립트. `PulseSession.observe()`
  (주석 "LLM의 눈")는 호출자 없는 죽은 코드.
- **재개 불가**: 후보 큐는 메모리에만, selections는 끝에서만 저장 → 크래시 시 `missions.status='RUNNING'`
  영구 잔류 + 선별 작업 소실. `watch`는 launchd 유닛도 락파일도 없는 foreground `setTimeout` 루프.
- **재시도 없음**: 후보별 try/catch로 삼키고 넘어감, `RemotePulseStore`도 재시도 없이 로그 한 줄당 POST
  하나 fire-and-forget.
- **Clips 파이프라인이 브라우저 탭 안**에서 전부 돎(`runIngestPipeline.ts`) — 탭 닫히면 진행 정지,
  `uploadClip()`은 체크포인트를 업로드 **뒤에** 써서 크래시 시 중복 업로드 가능.
- **보안**: `env/.env.local`·`.env.production`이 git 추적 상태로 DB 비밀번호·`PULSE_API_TOKEN`을 담고
  있음.
- 서버 쪽 테스트 공백(어댑터 4종·러너 루프·스토어 2종·`serve.ts`·`PulseSession` 전부 무테스트).

### (b) omnara 아키텍처 분석 결과 (요약) → 전체 → [[omnara]]
핵심 설계 철학: **"에이전트는 프로세스가 아니라 DB 상태 머신"** — append-only 이벤트 로그, 리스 기반
런타임 락, `claimNextWork()` 단일 트랜잭션 클레임, 시도 단위 durable 레코드(`model_call_contexts`),
2단계 툴 실행(Transactional→Async), 아티팩트 오버플로, 누적 체크포인트 압축, deferred tools +
`tool_search`, 커서 기반 재접속 SSE. 상세는 [[omnara]] 엔티티 페이지.

## 3. 개선안 정리 + 확인
두 분석 결과를 받은 뒤 핵심 주장(서버 LLM 0건, `/pulse/ws` 무인증, env 파일 git 추적)을 코드에서 직접
재확인 → 모두 일치. omnara 대비 lampas-agent 개선안 10가지를 우선순위순으로 제시(보안 → SQLite 잡
원장 → `claimNextWork` 워커 루프 → 재시도 정책 → Clips 서버 이관 → Pulse LLM 결정 루프 → 아티팩트
오버플로 → 구조화 로그 → Control Deck 세션 분리 → 테스트).

## 4. 구현 ("구현해고 구현이 끝나면 에이전트 재작해줘")
사용자가 개선안 구현 + 완료 후 데몬 재기동을 요청. 순서: 설계 확정 → 기반 모듈(구조화 로그·재시도
정책·접근 토큰/오리진 검사·프록시 헤더 필터, 테스트 28개) → SQLite 잡 원장(runs·events·schedules) +
claim/스케줄 순수 로직 → 워커 루프 + AI 모듈(게이트웨이·검색어 계획·교차 심사·비전 폴백·차단 감지) →
러너·미션 실행·원격 스토어에 연결. 최종 336→337개 테스트 + 타입체크 통과 → 빌드 → launchd 데몬
재기동.

설계 문서: `docs/superpowers/specs/2026-09-18-lampas-agent-durable-runs-design.md`.

**구현 내용**:
- **잡 원장 + 워커** — SQLite `data/agent.db`에 런·이벤트·스케줄 기록, 데몬 내 워커가 런을 하나씩
  claim해 실행. 리스 만료 복구·멱등키·지수 백오프 재시도·취소가 모두 원장 이벤트로 남음. 데몬 재시작
  후에도 대기/재시도 런이 이어짐.
- **재시도·차단 감지** — 실패를 rate_limit/blocked/network/timeout/server/deterministic으로 분류해
  정책대로 재시도, 캡차·레이트리밋·로그인 리다이렉트 감지 시 플랫폼 실행 즉시 중단.
- **AI 결정 단계** — lampas-api 게이트웨이로 검색어 계획·DOM 추출 실패 시 비전 폴백·선별 교차 심사
  (주제 적합도)를 미션당 예산 안에서 수행. 파트너 키 없으면 종전 수식 동작으로 폴백.
- **접근 제어** — `/v1`·`/bank`·`/pulse/ws`·`/agent/*` 토큰 필수, WS는 Origin 검사, 프록시는
  쿠키·토큰을 업스트림에 넘기지 않고 타임아웃·본문 한도 적용.
- **CLI·UI** — `pnpm pulse queue|schedule add --every 6h|runs|cancel`, Control Deck 우측에
  진행/런/스케줄 탭, 결과 카드에 선별 근거 + AI 사용량. 데몬 로그를 JSON Lines로 전환.

**검증**: 유닛 테스트 41개 파일 337개 + 타입체크 통과. 데몬 스모크(무토큰 401·잘못된 Origin WS 403·
쿠키 발급 302). E2E: CLI로 등록한 런을 워커가 45초 만에 실행해 미션 #9를 로컬 MySQL에 저장(탐색 4·
선별 2).

**배포**: `io.lampas.agent.daemon` v1.0.1로 재기동, 로컬 7452·공개 URL `/healthz` 모두 정상. 공개
URL은 이제 접근 토큰 필요 — 최초 1회 `/agent/auth?token=…` URL을 열어 쿠키를 받아야 함(토큰 값은
[[secrets-plaintext-exposure-pattern]] 정책에 따라 이 위키에서 레닥트, 원문은 원본 대화에도 마스킹).
AI 단계는 `AGENT_AI_API_KEY` 미설정으로 꺼진 채 배포됨(platform.lampas.io에서 발급 필요, 키 소유
계정에 과금).

## 5. 구현 중 발견한 기존 버그 2건
1. **`env/.env.local`의 `PULSE_DB_PASSWORD`가 맥미니 MySQL(root, 무비밀번호)과 불일치** — 로컬 저장이
   전부 거부되고 있었음. 빈 값으로 수정.
2. **접근 토큰이 든 env 파일이 저장소 관례상 git 추적됨** — `PULSE_API_TOKEN`과 같은 처지. 이번엔
   건드리지 않고 정책 결정 필요로 남김 → [[secrets-plaintext-exposure-pattern]] 4번째 사례로 등재.

## 남긴 것 (사용자가 후속 결정 필요)
- Clips 파이프라인은 여전히 브라우저 탭에서 돌고 업로드 멱등키 없음.
- Control Deck `PulseSession`은 여전히 전역 싱글턴.
- 어댑터 fixture 테스트 없음.
- **변경 미커밋** — 기존 미커밋 119개 파일과 섞여 있어 커밋 단위는 사용자가 직접 결정하기로 함.

## 후속 세션과의 정합성 확인 (2026-09-26 ingest 시점 교차 확인)
- [[lampas-system-ai-call-architecture-audit]](2026-09-20 세션)가 "저장소 전체에서 유일하게 잘 짜인
  AI 클라이언트"로 지목한 `lampas-agent/src/server/pulse/ai/gateway.ts`의 `AiBudget`이 바로 이 세션이
  구현한 AI 결정 단계 모듈이다 — 2일 뒤에도 살아있었다는 교차 확인.
- 이 세션이 구현한 **SQLite 잡 원장·워커 루프·`/pulse/ws` 접근 토큰 게이트**는 이후 09-19·09-20 세션
  (Clips 재생목록·Pulse 로그인 세션 개편·Flow 신설)의 요약에서 언급되지 않는다 — 존속 여부가 이
  ingest 시점 기준 **미확인**. 09-19 Pulse 로그인 개편이 이 세션의 "미션 소유자 정체성" 문제를 다른
  각도(Google 로그인 계정 vs env 폴백)에서 다루고 있어 완전히 겹치진 않지만, 접근 토큰 게이트와 로그인
  게이트가 공존하는지, 아니면 토큰 게이트가 이후 제거/우회됐는지는 다음 ingest에서 확인 필요.

## 관련
- 엔티티: [[lampas-agent]] · [[omnara]] · [[lampas-studio]]
- 토픽: [[lampas-system-ai-call-architecture-audit]] · [[secrets-plaintext-exposure-pattern]]
- 스킬: [[durable-agent-runtime-design-patterns]] · [[resumable-worker-checkpoint-resume]](비슷한
  문제의식, 별개 사례) · [[rebase-local-feature-onto-refactored-remote]](이번 세션은 stash→ff→pop이라
  다른 절차지만 "원격이 구조를 리팩터했다"는 전제는 동일)
