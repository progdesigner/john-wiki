---
name: sdk-claude-code-vs-api-billing
description: Claude Agent SDK로 실행할 때 Claude Code 구독(OAuth) 과금과 API 종량 과금 중 어느 쪽으로 도는지 판별하고, options.env로 전환하는 절차
created: 2026-07-15
tags: [claude-agent-sdk, billing, auth, oauth, env, lampas-harness]
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
  (`'pro'|'max'|'team'|'enterprise'|null`), `rate_limits.five_hour/seven_day`(claude.ai 5시간/7일 한도).
  **API 키 세션은 `rate_limits_available: false`** — 이 값으로도 모드 구분 가능.
- 턴 종료 후(또는 세션 핸들 보관 후 별도 엔드포인트에서) 호출해 SSE/`/api/usage`로 내려준다.
  `for await`로 스트림만 순회하면 이 메서드들은 호출되지 않으니 별도로 불러야 한다.

## 주의사항 / 함정
- API 키가 있어도 목록 조회용으로만 쓰이고 실행엔 안 쓰일 수 있다 — 실행 인증은 반드시 `apiKeySource`로 확정.
- `.env`의 주석 상태는 실제와 어긋날 수 있다(이 소스 세션에서도 "주석됨↔해제됨" 진술이 엇갈림) — 코드/`apiKeySource`로 교차검증.
- 실험적 usage API는 이름에 `EXPERIMENTAL_MAY_CHANGE_DO_NOT_RELY_ON_THIS_API_YET`이 붙어 SDK 버전 간 깨질 수 있다.

## 출처: [[2026-07-15-과금모드-토글-컨텍스트표시]] ([[lampas-harness]])
</content>
