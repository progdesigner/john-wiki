---
tags: [session, dark-system, dark-toss-api, trading, toss-securities, code-audit]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-15 — dark-toss-api 국장 9시 이전 매수 가능 여부 코드 조사

## 요약
[[progdesigner]]가 "국장 9시 전에도 매수할 수 있는지 소스 API를 확인해 달라"고 요청 (2026-07-15
23:04~23:06 UTC, 2왕복). 어시스턴트가 `dark-system` 모노레포의 `apps/dark-toss-api`(토스증권 API
연동 자동매매 서비스) 소스를 file:line 단위로 근거를 대며 조사.

이 세션으로 `dark-system`(그동안 [[works-project-portfolio]]에 "미문서화"로만 남아 있던 저장소)의
실제 정체가 처음 드러났다 → [[dark-toss-api]] 엔티티 생성.

## 조사 결과 (핵심)
1. **토스 API는 장전/장중 세션 정보를 아예 안 씀** — `toss.service.ts`에 `PRE_MARKET`/`session`/`status`
   같은 필드가 전혀 파싱되지 않음. 토스 API가 실제로 그런 정보를 내려주는지와 무관하게 이 시스템은
   확인하지 않음.
2. **대신 로컬 시계로 09:00~15:30만 하드코딩** — `trading.service.ts:56` `REGION_PROFILES.KR`에
   `openMin:540, closeMin:930`. `isRegionMarketOpen()`(1624~1638줄)이 KST 로컬 시계로만 판정.
3. **그 게이트 자체가 기본 꺼짐** — `marketHoursOnly` 기본값 `false`(`trading.config.ts:210`) → AUTO
   루프(`runCycle()`, 852줄)가 장 시간 체크 없이 08시대에도 계속 돔. `autoRegionSwitch` 기본값도
   `false`(84/129줄) → "양쪽 다 마감이면 일시정지" 로직(`maybeAutoSwitchRegion()`, 1647~1669줄)도
   옵션을 켰을 때만 작동하고, 켜져도 08:00~09:00 구간을 별도로 차단하진 않음(그 구간엔 이미 마감
   판정으로 일시정지 상태일 뿐).
4. **`executeBuy()`(1290~1330줄)에 시간 체크가 전혀 없음** — 보유종목 수·쿨다운·예산 계산만 하고
   `mode==='live'`면 바로 `toss.createOrder()`(1327줄) 호출.
5. **`manualBuy()`(757줄, controller 150줄)는 `runCycle`을 거치지 않고 `executeBuy`를 직접 호출** —
   marketHoursOnly 게이트 유무와 무관하게 애초에 시간 체크 경로 자체가 없음.

**결론**: 코드는 08:00~09:00(장전 동시호가 등) 매수를 막지도, 시간외 주문임을 인지하지도 않는다.
기본 설정 그대로면 08시대에도 신호가 뜨면 주문을 쏜다. 실제 체결 여부는 전적으로 토스 서버 측이
시간외 주문을 받아주는지에 달려 있음(코드로 확인 불가, 토스증권 API 문서 확인 필요). 어시스턴트가
`marketHoursOnly` 기본값을 `true`로 바꿀지 제안했으나 **로그 상 사용자 응답/실제 변경 여부는 이
소스에 없음** — 미결정 상태로 남음.

## 특이사항
- 이전에 있었던 [[2026-07-15-주식-개장전-매수시간-질문]]과 표면적으로 비슷해 보이지만 성격이 다르다:
  그 세션은 "국내 주식시장 일반 거래시간 구조"에 대한 개인 재무 상담(동시호가 제도 설명)이었고, 이
  세션은 [[progdesigner]] 소유의 실제 자동매매 코드베이스(`dark-toss-api`)가 **실제로 그 시간대에
  매수 주문을 막는지**를 코드로 검증한 기술 조사다.
- 사용자 메시지에 오타/구어체("베스 하시는지")가 섞여 있었으나 의도(매수 가능 여부 확인)는 명확히
  파악되어 응답에 영향 없었음.
- 조사 방법론이 [[bank-refund-invoice-reconciliation]] 스킬과 같은 "코드/자료 단정 금지, 근거 우선"
  원칙을 그대로 따름 → 재사용 가능한 절차로 일반화해 [[config-flag-gate-audit]] 스킬 신설.

## 관련
- 엔티티: [[dark-toss-api]] · [[dark-system]] · [[progdesigner]]
- 토픽: [[works-project-portfolio]]
- 스킬: [[config-flag-gate-audit]]
- 세션: [[2026-07-15-dark-system-저장소-클론]] (dark-system 최초 클론), [[2026-07-15-주식-개장전-매수시간-질문]] (표면적 유사, 성격 다름)
