## user — 2026-07-15T02:53:49.307Z

API 사용이 아닐 때 컨텍스트를 누르면 사용량 한도도 남은 량을 알 수 없나?

## assistant — 2026-07-15T02:53:55.119Z

구독(OAuth) 모드에서 컨텍스트 잔여율 표시와 별개로 "사용량 한도(리밋)" 잔여량을 보여줄 수 있는지 확인해보겠습니다. 관련 코드부터 찾아보겠습니다.

## assistant — 2026-07-15T02:54:12.489Z

조사 결과를 기다리는 동안 먼저 알려진 사실부터 답변드리면, 이건 결론이 거의 정해져 있는 질문입니다.

**결론부터: 불가능합니다.** Claude 구독(Pro/Max) 사용량 한도(5시간/주간 리밋)는 Anthropic이 공개 API로 노출하지 않기 때문에, 람파스가 "컨텍스트 잔여율"처럼 실시간으로 계산해서 보여줄 방법이 없습니다.

- **컨텍스트 잔여율**은 순수 로컬 계산입니다 — 현재 대화의 토큰 수를 세서 모델의 컨텍스트 윈도우(예: 200K) 대비 얼마나 남았는지 클라이언트에서 추정하는 것이라, 별도 API 호출 없이도 가능합니다.
- **사용량 한도(5시간 세션 한도, 주간 한도 등)**는 Anthropic 계정 서버 쪽에서 관리되는 값이고, Claude Code CLI/OAuth 세션에는 이걸 조회하는 공개 엔드포인트가 없습니다. Claude.ai 웹/앱 UI에서만 "사용량 한도 임박" 알림이 뜨는 것과 같은 원리로, 서드파티 하네스는 그 값을 미리 알 수 없고 실제로 리밋에 걸렸을 때 에러 응답(429류)으로만 알게 됩니다.

즉 지금 구조에서 할 수 있는 최선은 "리밋에 걸리면 에러 메시지로 알려주기"이지, "잔여량을 미리 게이지로 보여주기"는 API 미지원으로 불가능합니다. 코드 조사 결과가 오면 실제로 람파스가 리밋 관련 에러를 어떻게 처리하고 있는지 확인해서 보완드리겠습니다.

## assistant — 2026-07-15T02:56:17.170Z

quick.html에는 컨텍스트/사용량 표시 코드가 없다. SDK 패키지 내부를 확인한다.

## assistant — 2026-07-15T02:56:17.172Z

Confirmed the SDK exposes rate-limit data but src/server.ts doesn't use it anywhere. Let me double check server.ts doesn't call `usage_EXPERIMENTAL` or listen to `rate_limit_event`.

## assistant — 2026-07-15T02:56:17.172Z

## 조사 결과

**1. 컨텍스트 잔여율 계산/표시 (src/server.ts:1871-1891, apps/web/index.html:1242-1243)**
`src/server.ts:1873`에서 SDK 스트림 객체의 `stream.getContextUsage()` 컨트롤 채널 API를 턴 종료(`result` 메시지) 시점마다 호출해 `{totalTokens, maxTokens, percentage}`를 받아 `UsageTotals.contextWindow`(server.ts:926, 990)에 저장한다. `apps/web/index.html:1242-1243`에서 `isOauth`(구독/OAuth 모드)일 때만 `100 - percentage`로 "컨텍스트 잔여" %와 잔여 토큰 수를 렌더링한다. `quick.html`에는 관련 코드 없음.

**2. 사용량 한도(rate limit) 코드 — 없음**
`src`, `apps`, `package.json` 전체에서 `rate limit`, `usage limit`, `5-hour`, `weekly limit` 등을 grep해도 매치 없음. 즉 하네스 코드는 5시간/주간 사용량 한도를 조회하거나 표시하지 않는다.

**3. SDK는 해당 정보를 이미 제공함 (node_modules/@anthropic-ai/claude-agent-sdk/sdk.d.ts)**
- `sdk.d.ts:2383` `usage_EXPERIMENTAL_MAY_CHANGE_DO_NOT_RELY_ON_THIS_API_YET()` — `/usage` 커맨드 데이터: `rate_limits.five_hour/seven_day/seven_day_opus/seven_day_sonnet/model_scoped/extra_usage` (utilization %, resets_at) 포함 (sdk.d.ts:3081-3150). `rate_limits_available`가 false면 API key 세션이라 null.
- `sdk.d.ts:3971-3999` `SDKRateLimitEvent`(`type:'rate_limit_event'`)와 `SDKRateLimitInfo` — `status`, `rateLimitType`(five_hour/seven_day 등), `utilization`, `resetsAt` 등을 실시간 이벤트로도 제공.
- `server.ts`는 이 두 API 모두 호출하지 않음 — `usage_EXPERIMENTAL...` 및 `rate_limit_event` grep 결과 0건.

**4. package.json:23** `"@anthropic-ai/claude-agent-sdk": "^0.3.201"` — 위 rate-limit API를 포함한 버전.

**결론**: 컨텍스트 잔여율만 구현돼 있고, SDK가 제공하는 5시간/7일 사용량 한도 정보는 이 저장소에서 전혀 사용되지 않는다.

## assistant — 2026-07-15T02:56:17.173Z

정정하겠습니다 — 제 앞선 답변이 틀렸습니다. **가능합니다.** SDK가 이미 사용량 한도 정보를 제공하는데 람파스 코드가 그냥 안 쓰고 있는 것뿐입니다.

조사 결과:

- 컨텍스트 잔여율은 `src/server.ts:1873`에서 SDK의 `stream.getContextUsage()`를 호출해 얻고, `apps/web/index.html:1242-1243`에서 OAuth(구독) 모드일 때만 표시하고 있습니다.
- **SDK(`@anthropic-ai/claude-agent-sdk` v0.3.201)에는 사용량 한도 API가 이미 존재**합니다(`node_modules/@anthropic-ai/claude-agent-sdk/sdk.d.ts`):
  - `usage_EXPERIMENTAL_MAY_CHANGE_DO_NOT_RELY_ON_THIS_API_YET()` — `/usage` 커맨드가 쓰는 것과 같은 데이터로, `rate_limits.five_hour` / `seven_day` / `seven_day_opus` / `seven_day_sonnet` 등의 `utilization`(사용률 %)과 `resets_at`(리셋 시각)을 반환. API 키 세션이면 `rate_limits_available: false`로 null 처리됨(구독 모드에서만 유효).
  - `SDKRateLimitEvent`(`type: 'rate_limit_event'`) — 턴 진행 중 실시간으로도 같은 정보를 이벤트로 흘려줌.
- 다만 `src/server.ts`는 이 두 API를 **전혀 호출하지 않습니다** — 컨텍스트 잔여율만 붙였고 사용량 한도 쪽은 손대지 않은 상태였습니다.

정리하면: "API 사용이 아닐 때" 컨텍스트 옆에 5시간/주간 사용량 한도 잔여율도 같이 보여주는 건 기술적으로 충분히 가능하고, 필요한 SDK 훅도 이미 설치돼 있습니다. 원하시면 `getContextUsage()` 옆에 `usage_EXPERIMENTAL...()` 호출을 추가해서 UI에 "5시간 한도 O%, 주간 한도 O%" 식으로 표시하도록 구현해드릴까요?
