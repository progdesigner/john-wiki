---
tags: [session, lampas-harness, claude-agent-sdk, rate-limit, billing, feature-request]
created: 2026-07-15
updated: 2026-07-16
---
# 2026-07-15 — 사용량 한도(5시간/주간) 게이지 표시 가능 여부 확인

`[[lampas-harness]]` 웹 채팅에서 구독(OAuth) 모드일 때 컨텍스트 잔여율 옆에 **5시간/주간 사용량
한도(rate limit) 잔여율**도 보여줄 수 있는지 조사한 세션. 2026-07-15 02:53~02:56, 같은 날 앞선
[[2026-07-15-과금모드-토글-컨텍스트표시]](01:17~01:40, 컨텍스트 잔여율 구현) 세션의 자연스러운 후속
질문. 소스: [[raw/conversations/2026-07-15-사용량한도-rate-limit-sdk-확인]].

## 발단
사용자: "API 사용이 아닐 때 컨텍스트를 누르면 사용량 한도도 남은 량을 알 수 없나?"

## 답변이 중간에 뒤집힘 (주목할 점)
배경 조사 에이전트가 코드를 확인하는 동안, 어시스턴트가 먼저 **"불가능합니다"**라고 단정적으로
답변(Anthropic이 사용량 한도를 공개 API로 노출하지 않는다는 일반론 근거) — 그런데 조사가 끝나자
**"정정하겠습니다 — 제 앞선 답변이 틀렸습니다. 가능합니다"**로 스스로 뒤집었다. 실제로는 SDK가 이미
해당 정보를 제공하고 있었고, 하네스 코드가 안 쓰고 있었을 뿐. → 일반론적 추정으로 먼저 단정하지 말고
조사 결과를 기다려야 했던 사례. 사용자 재질문·불만은 소스에 없음(1왕복으로 자체 정정 완결).

## 조사 결과 (핵심)
1. **컨텍스트 잔여율**(이미 구현됨, 이전 세션) — `src/server.ts:1873` `stream.getContextUsage()`,
   `apps/web/index.html:1242-1243`에서 OAuth 모드일 때만 렌더링. `quick.html`엔 관련 코드 없음.
2. **사용량 한도(rate limit) 코드 — 하네스엔 전혀 없음.** `rate limit`/`usage limit`/`5-hour`/
   `weekly limit` grep 전체 무매치.
3. **SDK(`@anthropic-ai/claude-agent-sdk@0.3.201`)는 이미 두 가지 방법으로 이 정보를 제공**:
   - `stream.usage_EXPERIMENTAL_MAY_CHANGE_DO_NOT_RELY_ON_THIS_API_YET()` (`sdk.d.ts:2383`) —
     `/usage` 커맨드와 같은 데이터. `rate_limits.five_hour` / `seven_day` / `seven_day_opus` /
     `seven_day_sonnet` / `model_scoped` / `extra_usage` 각각 `utilization`(%)과 `resets_at`(리셋
     시각) 포함(`sdk.d.ts:3081-3150`). API 키 세션이면 `rate_limits_available: false`로 null.
   - `SDKRateLimitEvent`(`type: 'rate_limit_event'`, `sdk.d.ts:3971-3999`) — 턴 진행 중 실시간
     이벤트로 `status`/`rateLimitType`/`utilization`/`resetsAt` 스트리밍.
   - `server.ts`는 이 두 API를 **호출도, 리스닝도 하지 않음**(grep 0건) — 이전 세션([[sdk-claude-code-vs-api-billing]])이
     `usage_EXPERIMENTAL...()`의 존재를 언급만 했지 실제 배선은 안 됐다는 뜻.

## 결론 / 상태
5시간·주간 한도 게이지 표시는 **기술적으로 가능**(SDK 훅 이미 존재), **미구현**. 어시스턴트가 구현
여부를 물었으나 이 소스 트랜스크립트는 여기서 끝남 — **사용자 응답(구현 승인 여부) 없음**, 후속 세션
확인 필요.

## 관련
- [[lampas-harness]] · [[sdk-claude-code-vs-api-billing]] (이 세션 내용으로 세부 갱신) ·
  [[2026-07-15-과금모드-토글-컨텍스트표시]] · [[claude-model-pricing]]
