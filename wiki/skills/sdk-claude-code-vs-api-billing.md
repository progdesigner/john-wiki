---
name: sdk-claude-code-vs-api-billing
description: Claude Agent SDK로 실행할 때 Claude Code 구독(OAuth) 과금과 API 종량 과금 중 어느 쪽으로 도는지 판별하고, options.env로 전환하는 절차. 구독 모드 5시간/주간 사용량 한도(rate limit) 조회 API 상세 포함
created: 2026-07-15
updated: 2026-07-16
tags: [claude-agent-sdk, billing, auth, oauth, env, rate-limit, lampas-harness]
---
# Claude Agent SDK — 구독 과금 vs API 과금 판별·전환

## 언제 쓰는가
`@anthropic-ai/claude-agent-sdk`의 `query()`로 에이전트를 실행할 때 —
- "지금 Claude Code 정액(구독)으로 도나, API 종량으로 도나?"를 확정해야 할 때,
- 사용자가 두 과금 방식을 UI에서 골라 쓰게 하고 싶을 때,
- 구독 모드에서 남은 컨텍스트/한도를 보여주고 싶을 때.

## 핵심 사실
- **SDK `Options`에는 `apiKey` 필드가 없다.** 대신 `env?: Record<string,string|undefined>`만 있고,
  설정하면 서브프로세스 env를 **통째로 교체(병합 아님)**한다.
- `query()`에 `env`를 안 주면 서브프로세스는 부모 `process.env`를 상속하고, CLI 기본 로직이 자격증명을 고른다:
  **`ANTHROPIC_API_KEY`가 있으면 API 종량**, 없으면 **`CLAUDE_CODE_OAUTH_TOKEN`(=`claude login` 구독)**.
- 실제 사용된 인증은 init 메시지(`SDKSystemMessage`)의 `apiKeySource`로 확인:
  `'user'|'project'|'org'|'temporary'` = API 키, **`'oauth'` = 구독**.

## 절차 — 판별
1. `.env`/셸에서 `ANTHROPIC_API_KEY`(활성 여부, 주석 상태)와 `CLAUDE_CODE_OAUTH_TOKEN` 존재 확인.
2. `query()` 호출부에 `apiKey`/`env`를 넘기는지 확인 — 안 넘기면 위 상속 규칙대로.
3. 확정하려면 init 메시지의 `apiKeySource`를 읽어라(코드가 이걸 버리고 있으면 캡처하도록 추가).
   `'oauth'`면 구독, 그 외면 API.

## 절차 — 전환 토글
서브프로세스 env에서 **원치 않는 자격증명 변수를 빼서** 인증 방식을 강제한다:
```ts
function claudeAuthEnv(apiBilling: boolean): Record<string,string|undefined> {
  if (apiBilling) {
    if (!process.env.ANTHROPIC_API_KEY) throw new Error("API 모드인데 ANTHROPIC_API_KEY 없음");
    return { ...process.env };                              // API 키 그대로 → 종량 과금
  }
  const { ANTHROPIC_API_KEY, ...rest } = process.env;       // 키 제거 → OAuth(구독)으로 폴백
  return rest;
}
// query({ options: { env: claudeAuthEnv(opts.apiBilling), ... } })
```
- `env`는 **통째 교체**이므로 반드시 `...process.env`를 베이스로 깔고 원하는 변수만 가감한다.
- API 모드인데 키가 없으면 조용히 구독으로 폴백되지 않도록 **명시적으로 에러**를 던진다.

## 구독 모드 컨텍스트/한도 조회
`query()`가 반환한 `stream`(=`Query`) 핸들에서:
- `stream.getContextUsage()` → `{ totalTokens, maxTokens, percentage, categories, gridRows }` (컨텍스트 윈도우 잔여율).
- `stream.usage_EXPERIMENTAL_MAY_CHANGE_...()` → `session.total_cost_usd`, `subscription_type`
  (`'pro'|'max'|'team'|'enterprise'|null`), `rate_limits.five_hour/seven_day/seven_day_opus/
  seven_day_sonnet/model_scoped/extra_usage` — 각각 `utilization`(사용률 %)과 `resets_at`(리셋 시각)
  포함 (`/usage` 커맨드와 같은 데이터, sdk.d.ts:3081-3150). **API 키 세션은 `rate_limits_available: false`**
  로 null — 이 값으로도 모드 구분 가능.
- `SDKRateLimitEvent`(`type: 'rate_limit_event'`, sdk.d.ts:3971-3999) — 위와 같은 한도 정보를 턴
  진행 중 **실시간 이벤트**로도 받을 수 있다. `SDKRateLimitInfo`에 `status`/`rateLimitType`
  (`five_hour`/`seven_day` 등)/`utilization`/`resetsAt`.
- 턴 종료 후(또는 세션 핸들 보관 후 별도 엔드포인트에서) 호출해 SSE/`/api/usage`로 내려준다.
  `for await`로 스트림만 순회하면 이 메서드들은 호출되지 않으니 별도로 불러야 한다.

## 상태 (2026-07-15 재확인)
[[lampas-harness]]의 `src/server.ts`는 `usage_EXPERIMENTAL...()`도 `rate_limit_event`도 **둘 다 호출/
리스닝하지 않는다**(grep 0건 확인). 즉 컨텍스트 잔여율만 구현돼 있고, 5시간/주간 사용량 한도 게이지는
SDK 훅이 이미 있는데도 **미구현 상태**(기술적으로는 가능, 구현 승인 여부 미확인) →
[[2026-07-15-사용량한도-rate-limit-sdk-확인]].

## 크레딧 소진의 파급 범위 (2026-07-15 재확인)
API 크레딧이 부족하면 대화 자체(구독/OAuth 과금 경로)는 멀쩡해도, **API 과금 경로를 타는 다른 기능은
같이 실패**한다. 실측 사례:
- 대화별 "API 사용" 토글이 켜진 상태에서 `/compact`(컨텍스트 압축)가 API 키로 과금되다 크레딧 부족으로 실패.
- **백그라운드 `memory-ingest`(위키 야간 자동 적재) 잡도 같은 이유로 반복 실패**("Credit balance is too
  low", 2회 재시도 모두 실패) — 이 위키(`[[john-wiki]]`) 자신의 저장 파이프라인에 영향을 준다는 점에서
  특히 주의. → [[long-term-memory-architecture]]

해결은 (a) 크레딧 충전, (b) 해당 기능을 구독(OAuth) 과금 경로로 전환(위 `claudeAuthEnv` 패턴 적용) 둘 중
하나. 백그라운드 잡처럼 사용자가 토글을 직접 못 켜는 경로는 **기본값을 구독(OAuth)으로 고정**해두는 편이
크레딧 잔액에 덜 취약하다. → [[2026-07-15-gpt-realtime-음성입력-길게누르기]]

**근본 원인 확정(2026-07-15 22:42~22:52)**: `ANTHROPIC_API_KEY` 자체는 `.env`에 정상 설정돼 있으나
**크레딧 잔액이 0**이라 API 호출이 400으로 거절됨을 [[model-selection]] Auto 판정 기능 구현 중
확인 — "크레딧 부족"이 막연한 추정이 아니라 확정 사실. → [[2026-07-15-auto모델-기능-최초구현]]

## 주의사항 / 함정
- API 키가 있어도 목록 조회용으로만 쓰이고 실행엔 안 쓰일 수 있다 — 실행 인증은 반드시 `apiKeySource`로 확정.
- `.env`의 주석 상태는 실제와 어긋날 수 있다(이 소스 세션에서도 "주석됨↔해제됨" 진술이 엇갈림) — 코드/`apiKeySource`로 교차검증.
- 실험적 usage API는 이름에 `EXPERIMENTAL_MAY_CHANGE_DO_NOT_RELY_ON_THIS_API_YET`이 붙어 SDK 버전 간 깨질 수 있다.
- **한도 API의 존재 여부를 일반론으로 단정하지 말 것** — "Anthropic이 공개 API로 안 준다"는 추정만으로
  먼저 "불가능"이라 답했다가, 실제 SDK 조사 후 정정한 사례가 있다. 구독/한도 관련 질문은 반드시
  `sdk.d.ts` 실물 확인 후 답할 것. → [[2026-07-15-사용량한도-rate-limit-sdk-확인]]

## 출처: [[2026-07-15-과금모드-토글-컨텍스트표시]] · [[2026-07-15-사용량한도-rate-limit-sdk-확인]] · [[2026-07-15-gpt-realtime-음성입력-길게누르기]] ([[lampas-harness]])
</content>
