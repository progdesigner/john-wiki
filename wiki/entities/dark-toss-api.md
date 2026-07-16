---
tags: [entity, project, trading, toss-securities, dark-system]
created: 2026-07-16
updated: 2026-07-16
---
> 2026-07-16 갱신: [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] 세션(21:52~22:38 UTC,
> 아래 코드 조사 세션보다 **먼저** 있었던 세션)에서 국장/미장 자동 장이동(`autoRegionSwitch`)에
> "양쪽 마감 시 자동판단 일시정지·재개장 시 재개" 기능이 처음 구현·배포됨. 아래 "확인된 동작"
> 섹션의 `autoRegionSwitch` 서술은 이 세션 이후 상태를 반영한다(코드 조사 세션이 그 결과를 확인한 것).
> 자매 앱 [[dark-toss-web]]·[[dark-upbit-api]]·[[dark-upbit-web]] 엔티티도 이 세션에서 신설됨.
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
  (`autoRegionSwitch`는 별개 플래그로, 아래 기능과는 무관하게 여전히 기본 `false`.)

## 자동 장이동(`autoRegionSwitch`) — 마감 시 일시정지·개장 시 재개 (2026-07-15 구현)
[[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] 세션에서 구현·배포. `autoRegionSwitch`가
켜져 있을 때만 적용되는 부가 기능(꺼져 있으면 이 안전장치도 같이 비활성화됨 — [[config-flag-gate-audit]]
"부가 기능에 딸린 안전장치" 함정의 실제 사례).
- **개장 추적**(기존 동작): 국장 장중이면 KR, 미장 장중이면 US로 자동 전환, 겹치면 국장 우선. 60초
  감시 타이머 + 매 사이클 직전 체크.
- **양쪽 마감 → 자동 판단 일시정지**(신규): AUTO 루프가 돌고 있으면 정지시키고 `autoPaused` 상태로
  노출. `marketHoursOnly` 기본값이 `false`라 이 기능 전에는 장이 다 닫혀도 사이클(전략 스캔 LLM
  호출 포함)이 계속 돌고 있었음 — 이 기능은 그 낭비를 `autoRegionSwitch` ON 조건에서만 줄인다.
- **재개장 → 자동 판단 재개**(신규): 일시정지 상태에서 국장이든 미장이든 열리면 해당 장으로 전환 후
  AUTO 자동 재시작.
- **사용자 의도 우선**: 사용자가 AUTO를 직접 끄면 일시정지 상태도 해제(개장해도 재시작 안 함).
  `autoRegionSwitch` 자체를 끄면 재개 주체가 없어지므로 일시정지 해제 + AUTO는 정지 상태로 남음.
- 화면 표시: [[dark-toss-web]] 배지 "AUTO ⏸ 장마감 · 개장 시 재개".
- 배포 시점(2026-07-15 22:33 UTC) 기준 `autoRegionSwitch`·AUTO 둘 다 꺼진 상태 — 화면에서 둘 다 켜야
  이 기능이 실제로 동작한다.

## LLM 모델 카탈로그 — 날짜 스냅샷 모델 필터 (2026-07-15)
[[dark-upbit-api]]와 동일한 `llm-catalog.ts`(별도 파일, 공유 패키지 아님)에 `isDatedSnapshotModel`
필터 적용 — 상세 규칙은 [[dark-upbit-api]] 참고.

## 초기화(reset) 시 LLM 비용도 초기화 (2026-07-15)
`POST /trading/reset`이 포트폴리오뿐 아니라 `llmUsage` 비용 집계도 초기화하도록 변경. Toss는 비용이
KR/US 지역 슬롯 간 동기화되는 구조라 **활성 지역 슬롯만 지우면 장 전환 때 비용이 되살아나는 문제**가
있어, 초기화 시 전 지역 슬롯에서 비용을 제거하도록 수정(live 경로 동일). 아직 포트폴리오에 집계되지
않고 LLM 서비스에 대기 중이던 사용량도 `drainUsage` 후 폐기해, 초기화 직후 첫 사이클에서 이전 비용이
되살아나지 않게 함. [[dark-upbit-api]]도 동일하게 수정됨(단, upbit은 지역 슬롯 없이 단일 집계).

## 관련
- [[dark-system]] · [[progdesigner]] · [[dark-toss-web]] · [[dark-upbit-api]] · [[dark-upbit-web]]
- [[2026-07-15-dark-toss-api-장전매수-코드조사]]
- [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]]
- [[config-flag-gate-audit]] · [[deploy-sandbox-pnpm-shim]]
