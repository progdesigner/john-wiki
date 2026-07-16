## user — 2026-07-15T23:04:28.294Z

소스 API를 확인해서 그 아홉시 전에도 국장은 매수할 수 있는지 한번 확인해 줘. 베스 하시는지 아홉시 전에?

## assistant — 2026-07-15T23:04:34.695Z

토스 증권 API 쪽 소스를 확인해서 9시(정규장 개장) 이전에도 매수가 가능한지 코드 레벨에서 확인해 볼게요.

## assistant — 2026-07-15T23:04:50.872Z

조사를 시작했습니다. 코드에서 토스 증권 API 호출부와 장운영시간 판단 로직을 확인하는 대로 결과를 정리해서 알려드릴게요.

## assistant — 2026-07-15T23:06:28.869Z

조사 결과를 요청하신 형식으로 정리했습니다.

## 국장 9시 이전 매수 가능 여부 — 코드 근거

- `apps/dark-toss-api/src/trading/trading.service.ts:56` `REGION_PROFILES.KR`에 `openMin: 9*60(540), closeMin: 15*60+30(930)`이 하드코딩되어 있고, `isRegionMarketOpen()`(1624~1638줄)이 로컬(KST) 시계로 이 범위 안인지만 계산합니다. 토스 API에서 별도 세션 정보를 받아오지 않습니다.
- 이 "장 시간" 판정은 딱 두 곳에서만 쓰입니다.
  - `runCycle()`(852줄): `if (this.marketHoursOnly && !this.isMarketOpen())` → **그런데 `marketHoursOnly`는 `apps/dark-toss-api/src/config/trading.config.ts:210`에서 `false`로 하드코딩**되어 있어, 이 게이트는 사실상 항상 비활성 상태입니다. 즉 AUTO 루프는 기본 설정상 장 시간 여부와 무관하게 매 사이클(`intervalSec`)마다 계속 돌아갑니다.
  - `maybeAutoSwitchRegion()`(1647~1669줄): 국장·미장 모두 마감이면 `autoPausedByClose=true`로 AUTO를 멈추고, 둘 중 하나라도 열리면 재개합니다. 그런데 이 로직은 `autoRegionSwitch` 플래그가 켜져 있을 때만 호출됩니다(`autoRegionSwitch = false`가 기본값, 84줄/129줄). 즉 최근 커밋의 "장마감 시 자동판단 일시정지·개장 시 자동 재개" 기능은 **자동 장이동(KR↔US) 옵션을 사용자가 켰을 때만 작동**하는 부가 기능이며, 그렇지 않으면 이 게이트도 적용되지 않습니다.
- 매수 실행 함수 `executeBuy()`(1290~1330줄)에는 시간대 관련 체크가 **전혀 없습니다.** 최대 보유 종목 수, 재매수 쿨다운, 예산/수량 계산만 하고, `mode==='live'`면 곧바로 `this.toss.createOrder(...)`(1327줄)를 호출합니다.
- `manualBuy()`(757줄, controller 150줄)는 `runCycle`을 거치지 않고 `executeBuy`를 직접 호출하므로, marketHoursOnly 게이트 유무와 무관하게 애초에 시간 체크 경로 자체가 없습니다.

## 토스 API의 장전 시간대 필드 제공 여부

`apps/dark-toss-api/src/toss/toss.service.ts` 전체를 검색했으나 `status`, `session`, `PRE_MARKET`, `AFTER` 등 장 세션 구분 필드/enum이 코드에 전혀 없습니다. `createOrder()` 요청 바디(429~448줄)는 `symbol/side/orderType/quantity/price/clientOrderId`뿐이고, `getHoldings`/`getBuyingPower` 응답 파싱에도 market status 관련 필드 매핑이 없습니다. 즉 이 시스템은 토스 API가 세션 정보를 내려주는지 여부와 무관하게 **그 정보를 아예 사용/파싱하지 않고**, 자체 로컬 시계 하드코딩(09:00~15:30 KST)에만 의존합니다.

## 실제로 9시 이전 매수를 시도하는지

- 기본 설정(`marketHoursOnly:false`, `autoRegionSwitch:false`)에서는 **AUTO 루프도 시간 게이트가 없어** 08시대에도 사이클이 돌고, 매수 신호가 나오면 `executeBuy`→`toss.createOrder`(MARKET)까지 그대로 호출됩니다.
- `autoRegionSwitch`를 켠 경우에만 "양쪽 시장 모두 마감"일 때 AUTO가 일시정지되며, 이 경우도 국장 09:00 개장 시점에 자동 재개될 뿐, 08:00~09:00 구간에 대한 별도 차단 로직은 없습니다(그 구간엔 이미 마감 상태로 판정되어 일시정지 상태일 뿐).
- 수동 매수(`manualBuy`)는 어떤 설정에서도 시간 체크 없이 즉시 주문을 시도합니다.
- 결론적으로 코드는 "08:00~09:00 시간외/동시호가에 매수 주문을 막는 로직"을 갖고 있지 않으며, 주문이 실제로 체결되는지는 전적으로 토스 서버 측 검증(시간외 주문 허용 여부)에 달려 있습니다. 코드 상으로는 막지도, 시간외 주문임을 인지하지도 않습니다.

## assistant — 2026-07-15T23:06:42.976Z

조사 결과, 코드 자체는 9시 이전 매수를 **차단하지도, 허용을 판단하지도** 않습니다. 핵심만 정리하면:

**1. 토스 API는 장전 세션 정보를 안 씀**
`toss.service.ts`의 주문/시세 응답 파싱 코드 어디에도 `PRE_MARKET`, `session`, `status` 같은 장 세션 구분 필드가 없습니다. 즉 토스 API가 실제로 그런 정보를 내려주는지 여부와 무관하게, 이 시스템은 그걸 아예 확인하지 않습니다.

**2. 대신 로컬 시계로 09:00~15:30만 하드코딩**
`trading.service.ts:56` `REGION_PROFILES.KR`에 `openMin: 540(=9시), closeMin: 930(=15:30)`이 박혀 있고, `isRegionMarketOpen()`이 이 범위인지만 로컬 KST 시계로 계산합니다.

**3. 근데 이 게이트가 기본적으로 꺼져 있음**
- `marketHoursOnly` 옵션이 `trading.config.ts:210`에서 기본값 `false` → AUTO 루프가 장 시간 체크를 아예 안 하고 08시대에도 계속 돕니다.
- `autoRegionSwitch`(국장↔미장 자동 전환, 최근 커밋 기능)도 기본값 `false` → 켜져 있을 때만 "양쪽 다 마감이면 일시정지" 로직이 작동하고, 이때도 09:00 개장 시점에 자동 재개될 뿐 8~9시 구간을 별도로 차단하는 로직은 없습니다.

**4. 매수 실행 함수(`executeBuy`) 자체에 시간 체크가 전혀 없음**
보유 종목 수·쿨다운·예산 계산만 하고 바로 `toss.createOrder()` 호출. 수동 매수(`manualBuy`)는 이 게이트를 거치지도 않고 곧장 주문을 시도합니다.

**결론**: 9시 이전(장전 동시호가 등)에 실제로 매수가 되느냐는 **토스 서버 쪽에서 시간외 주문을 받아주는지**에 전적으로 달려 있고, 우리 코드는 이걸 막지도 인지하지도 않습니다. 기본 설정 그대로면 08시대에도 신호만 뜨면 주문을 쏘게 됩니다. 만약 실제로 장전 매수가 되는지 확인하고 싶으면 토스증권 API 문서(주문 가능 시간대 규정)를 직접 봐야 하고, 원치 않으면 `marketHoursOnly: true`로 켜서 9시 이전엔 아예 주문이 안 나가게 막는 게 안전합니다.

원하시면 `marketHoursOnly` 기본값을 `true`로 바꿔드릴까요?
