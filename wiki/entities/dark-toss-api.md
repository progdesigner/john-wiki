---
tags: [entity, project, trading, toss-securities, dark-system]
created: 2026-07-16
updated: 2026-07-16
---
# dark-toss-api

[[dark-system]] 모노레포 안의 앱(`apps/dark-toss-api`) — 토스증권(Toss Securities) API를 연동한
**자동매매(트레이딩 봇) 서비스**. [[progdesigner]] 개인 GitHub 계정(`progdesigner/dark-system`) 소유.

2026-07-15 [[2026-07-15-dark-toss-api-장전매수-코드조사]] 세션에서 처음 내용이 조사되며 정체가
드러났다 — 그 전까지 `dark-system`은 [[works-project-portfolio]]에 이름만 있고 미문서화 상태였다.

## 구조 (2026-07-15 시점 조사된 범위)
- `src/trading/trading.service.ts` — 매매 사이클(`runCycle()`, 852줄)·지역별 장 시간 프로필
  (`REGION_PROFILES`, 56줄)·자동 매수 실행(`executeBuy()`, 1290~1330줄)·수동 매수(`manualBuy()`,
  757줄)·장 시간 판정(`isRegionMarketOpen()`, 1624~1638줄)·자동 장이동(`maybeAutoSwitchRegion()`,
  1647~1669줄).
- `src/config/trading.config.ts` — 매매 설정값. `marketHoursOnly`(210줄, 기본 `false`),
  `autoRegionSwitch`(84/129줄, 기본 `false`).
- `src/toss/toss.service.ts` — 토스증권 API 클라이언트. `createOrder()`(429~448줄, 바디:
  `symbol/side/orderType/quantity/price/clientOrderId`), `getHoldings`/`getBuyingPower`. **장 세션
  구분 필드(`PRE_MARKET`/`session`/`status` 등)를 전혀 파싱하지 않음.**
- controller(150줄)가 `manualBuy` HTTP 엔드포인트 노출.

## 확인된 동작 (2026-07-15 코드 조사)
- 국장(KR) 장 시간은 로컬 KST 시계로 `09:00~15:30`만 하드코딩 판정 — 토스 API의 실시간 세션 정보는
  사용하지 않음.
- **`marketHoursOnly` 기본값이 `false`** → AUTO 매매 루프가 장 시간과 무관하게 항상 돎(08시대 포함).
- **`autoRegionSwitch` 기본값도 `false`** → "양쪽 시장 모두 마감 시 자동 일시정지" 기능은 이 옵션을
  켰을 때만 작동하는 부가 기능. 켜져 있어도 08:00~09:00(동시호가 등) 구간을 별도로 차단하지는 않음.
- `executeBuy()`에 시간대 체크가 전혀 없고, `manualBuy()`는 `runCycle`을 거치지 않아 게이트 유무와
  무관하게 시간 체크 경로 자체가 없음.
- 결론: **코드는 장전 매수를 막지도, 인지하지도 않는다.** 실제 체결 여부는 토스 서버가 시간외 주문을
  받아주는지에 전적으로 달려 있음(코드로는 확인 불가).

## 미결정 사항
- 어시스턴트가 `marketHoursOnly` 기본값을 `true`로 바꿀지 제안했으나, 소스 로그가 거기서 끝나
  **사용자 응답·실제 변경 여부 미확인**. 다음에 다뤄질 때 반드시 실제 설정값을 다시 읽어 확인할 것.

## 관련
- [[dark-system]] · [[progdesigner]]
- [[2026-07-15-dark-toss-api-장전매수-코드조사]]
- [[config-flag-gate-audit]]
