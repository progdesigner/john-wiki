---
name: durable-agent-runtime-design-patterns
description: 브라우저/스크래핑/미디어 파이프라인처럼 오래 걸리고 자주 죽는 비동기 작업을, 프로세스 상태가 아니라 DB 상태 기준으로 재시작에 강하게 설계할 때 적용하는 10가지 패턴 ([[omnara]] 분석에서 역추출)
created: 2026-09-26
tags: [architecture, durable-execution, worker, idempotency, agent-design]
---
# durable-agent-runtime-design-patterns

## 언제 쓰는가
다음 신호 중 하나라도 있으면 적용을 검토한다:
- "재시작해도 이어서 한다"고 안내해놓고 실제론 크래시·데몬 재시작 시 진행 정보가 사라지거나 중복
  실행된다.
- 후보/작업 큐가 메모리에만 있어 프로세스가 죽으면 통째로 사라진다.
- 재시도·백오프가 개별 `try/catch`로 산발적으로 흩어져 있어 정책을 한 곳에서 바꿀 수 없다.
- 브라우저 탭/사람이 열어둔 창이 열려 있어야만 파이프라인이 진행된다.
- 긴 관측값(전사문·페이지 덤프)을 그대로 모델 컨텍스트에 밀어넣어 잘림·타임아웃이 반복된다.
- 툴/MCP 카탈로그가 커져 모델 컨텍스트에 모든 스키마를 다 넣기 부담스럽다.

## 절차 (10가지 패턴, 우선순위순 — 상황에 맞게 일부만 골라 적용 가능)

1. **append-only 이벤트 로그 + 파생 상태를 유일한 진실로.** SQLite도 충분하다:
   `events(id, run_id, seq UNIQUE, kind, at, idempotency_key UNIQUE)`, kind는
   `input | model_output | tool_result | checkpoint` 정도로 시작. 재시작 시 이벤트를 replay해서
   상태를 재구성한다 — 현재 상태를 별도 컬럼에 손으로 유지하지 않는다.
2. **리스 기반 단일 실행 락으로 인메모리 상태를 대체.** `runtime_locks(run_id UNIQUE, worker_id,
   lease_expires_at)` + 주기적 갱신(renew) + 만료분 회수(reap). 크래시 복구가 "그냥 리스가 만료됨"으로
   공짜가 되고, 두 워커가 같은 자원(같은 클립, 같은 브라우저 프로필)을 동시에 건드리는 걸 원천 차단.
   `cancel_requested_at` 컬럼 하나로 협조적 취소도 얹을 수 있다.
3. **`claimNextWork()`를 단일 트랜잭션 + 단일 우선순위 함수로.** "툴 작업 → 모델 작업 → 대기 입력 →
   idle" 같은 고정 우선순위로 **정확히 한 단계**를 반환하게 만들고, 워커 루프는 이걸 반복 호출만 한다.
   "동시에 두 종류 작업이 있다" 같은 불변 조건 위반은 조용히 넘기지 말고 **에러로 던진다** — 침묵하는
   불변식 위반은 나중에 더 큰 버그로 돌아온다.
4. **멱등키를 안정적 정체성의 조합으로 만든다.** `"cron_trigger:"+id+":"+dueAt.toISOString()`,
   `"segment:"+videoId+":"+startMs`, `"upload:"+sourceId+":"+clipIndex` 처럼. 이 패턴 하나로 "재실행이
   안전한 no-op"과 "at-least-once 배달"을 동시에 확보한다.
5. **시도(attempt) 단위 durable 레코드를 따로 둔다.** `attempts(run_id, step, attempt_number,
   state ∈ started|succeeded|failed|canceled, recovery_kind, retry_at, error)`. ad-hoc try/catch를
   "조사 가능한 상태"로 승격시키고, 토큰/비용 집계도 여기 얹으면 공짜로 딸려온다. 재시도 판단은
   `decide(err, attempt) → {retry|stop, delayMs}` 같은 **순수 함수**로 분리해 provider의 `Retry-After`를
   존중하고, 결정적 실패(파싱 실패 등)는 즉시 stop 처리한다.
6. **긴 작업은 2단계로 쪼갠다: Transactional(의도 커밋) → Async(느린 작업) → 결과 커밋.**
   `tool_calls.state`에 `CHECK((state='running') = (lease_id IS NOT NULL))` 같은 제약을 걸면 "리스 없이
   running인 고아 행"이 스키마 레벨에서 불가능해진다. 다운로드·ffmpeg·ASR·브라우저 내비게이션처럼
   느린 미디어 작업에 정확히 맞는 모양.
7. **오버플로는 잘린 문자열이 아니라 아티팩트로.** 전사문·페이지 덤프를 프롬프트에 그대로 넣지 않는다
   — 블롭으로 저장하고 `{text: truncated, media_ref: {artifact_id}}`를 반환, 페이지 단위 `read_file`로
   모델이 필요한 만큼만 읽게 한다. 관측값 바이트 상한(예: 기본 8KB/최대 64KB)을 툴 레이어에서
   명시적으로 강제한다.
8. **컨텍스트 압축은 누적 체크포인트 + 원자 그룹 경계 보존으로.** 최근 구간은 `min(window/4, 20k)`
   토큰만큼 항상 verbatim 유지, 툴콜/결과 쌍은 절대 자르지 않는 경계에서만 자른다. 요약은 고정 구조
   (Goal/Instructions/Progress/Artifacts/Next Steps)로 강제하고, **연속 압축 횟수에 상한(예: 3회)**을
   두고 "프론티어를 전진시키는가"를 확인 못 하면 거부 — 압축 무한루프를 막는 안전장치.
9. **툴이 많아지면 deferred + `tool_search`.** 대부분의 툴을 `deferred: true`로 표시해 모델의 기본
   툴 배열에서 빼고, `tool_search(pattern, max_results=5)`로 필요한 스키마만 온디맨드로 당겨온다.
   구현 비용 대비 컨텍스트 절약이 크다.
10. **재접속 가능한 스트림 클라이언트는 durable cursor + 풀지터 백오프로.** 마지막 처리한 sequence를
    커서로 저장, 재접속 시 `Last-Event-ID`류로 이어받고, `sequence <= cursor`면 중복으로 스킵.
    백오프는 1s→30s 풀지터, stall 타임아웃은 **서버 하트비트 간격보다 길게** 잡는다. 재시도 대상 에러
    화이트리스트(transport/408/429/5xx)를 명시적으로 좁힌다.

## 주의사항 / 함정
- 워커 프로세스가 이미 존재한다고 해서 "durable"이라고 착각하지 않는다 — **상태를 신뢰하는지**가
  관건이지 프로세스 존재 여부가 아니다 (→ [[resumable-worker-checkpoint-resume]]는 이 패턴 3·5·6번이
  구체적으로 위반됐을 때의 진단·수정 절차).
- 1~4번은 대체로 기존 코드에 바로 붙일 수 있는 범위지만, 3·6번(워커 루프 재작성, 2단계 툴 실행)은
  실제 구조 변경이라 비용이 더 크다. 상황에 맞게 우선순위를 재조정할 것 — 전체 10개를 한 번에
  도입하려 하지 말 것.
- 오프너-어셉터-액 3단 프로토콜(offer → accept → ack, `AckStatus ∈ {committed, cleanup_only,
  permanent_reject, transient_error}`)은 컨트롤러/워커 프로세스가 물리적으로 분리될 때(예: 브라우저·
  미디어 워커를 별도 프로세스로 뺄 때) 적용할 계약이다 — 단일 프로세스 내 워커 루프라면 3번 패턴
  (`claimNextWork`)만으로 충분할 수 있다.

## 출처: [[omnara]] 아키텍처 분석 (외부 레퍼런스) — [[2026-09-18-lampas-agent-omnara분석-durable-run구현]]
[[lampas-agent]]에 1~5번을 이식한 실제 구현 결과는 해당 세션 페이지의 "4. 구현" 절 참고.
