---
tags: [entity, project, trading, upbit, crypto, dark-system]
created: 2026-07-16
updated: 2026-08-03
---
# dark-upbit-api

[[dark-system]] 모노레포 안의 앱(`apps/dark-upbit-api`) — Upbit(업비트, 코인) API를 연동한
**자동매매(트레이딩 봇) 서비스**. [[dark-toss-api]](토스증권)와 자매 앱 관계로, 스타일(초단타/단타/
스윙/장기투자) 개념·전략 스캔(LLM 기반 자동 전환)·수동 매매 구조를 공유한다. [[progdesigner]] 개인
GitHub 계정(`progdesigner/dark-system`) 소유.

2026-07-15 저녁(21:52~22:38 UTC) [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] 세션에서
처음 내용이 다뤄지며 정체가 드러났다 — 이전까지 [[dark-system]]은 `apps/dark-toss-api` 하나만
조사된 상태였다.

## 구조 (2026-07-15 세션 기준 확인된 범위)
- `src/config/trading.config.ts` — 매매 스타일 목록·활성/비활성 판정. 초단타(scalping)/단타(day)/
  스윙(swing)/장기투자 4종.
- `src/llm/llm.service.ts` — 전략 스캔(자동 전환) 프롬프트 구성, LLM이 반환한 스타일 유효성 검증.
- `src/llm/llm-catalog.ts` — LLM provider/model 카탈로그. [[dark-toss-api]]에 **동일 파일이 별도로
  존재**(공유 패키지 아님, 두 앱에 각각 복사돼 있음) — 수정 시 양쪽 다 반영해야 함.
- 웹은 [[dark-upbit-web]] — 스타일 select·모델 select 옵션은 서버 API를 그대로 따라야 하는데,
  한때 하드코딩돼 있어 서버-클라이언트 불일치가 있었다(아래 사고 이력 참고).

## 매매 스타일 게이팅 구조 (2026-07-15 세션에서 확립)
- **개념**: `DISABLED_STYLES`(전면 차단, 4경로 모두 막음) → 이후 `AUTO_SWITCH_EXCLUDED_STYLES`(자동
  전환만 제외, 수동 선택은 허용)로 교체. 초단타가 여기 포함.
- **구조적 LLM 차단**: 초단타 프리셋의 엔진을 `ai` → `signal`(볼린저 지표 기반)로 바꿔, 나중에 플래그를
  풀어도(`AUTO_SWITCH_EXCLUDED_STYLES`에서 제거) 후보별 판단 LLM(`llm.decide`)·AI 후보 추천이 아예
  호출되지 않도록 만듦. 플래그 하나에 의존하지 않는 이중 안전장치.
- **부작용**: `signal` 엔진은 캔들을 일봉으로 강제 조회하도록 짜여 있어, 초단타를 재활성화해도
  "일봉 볼린저 스퀴즈를 60초 주기로 판단"하는 동작이 됨(1분봉 지표가 아님) — 단타(day)와 판단 로직은
  같고 손절 1.2%·주기만 다름. 진짜 분봉 지표 매매가 필요해지면 signal 엔진에 분봉 지원 추가가 선행돼야 함.
- **수동 선택 보호**: AUTO 루프가 돌 때 수동으로 고른 스타일을 다음 스캔이 되돌리는 기존 구조가 있었음
  → 현재 스타일이 수동 전용(초단타)이면 스캔은 제안 로그만 남기고 전환하지 않도록 예외 처리(리스크
  보정 판단은 계속 동작).
- 이 패턴 일반화 → [[trading-feature-flag-auto-exclude-manual-allow]] 스킬.

## 사고 이력 — 서버 반영 후에도 UI에 초단타가 남아있던 사례
서버가 `DISABLED_STYLES`로 초단타를 거부하기 시작한 뒤에도 [[dark-upbit-web]]의 스타일 드롭다운에
계속 초단타가 보였던 원인: **웹 드롭다운이 서버의 `/trading/styles`를 쓰지 않고 옵션을 하드코딩**하고
있었기 때문. 선택하면 서버가 "비활성 스타일" 에러로 거부했지만 목록 자체는 갱신되지 않았다.
`getStyles()` API를 그대로 따르도록 고쳐 해결. → [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]]

## LLM 모델 카탈로그 — 날짜 스냅샷 모델 필터
`fetchChatModelCatalog`(→ `llm-catalog.ts`)에 `isDatedSnapshotModel` 규칙을 추가해 후보 판단·AI
추천·전략 스캔 select 3곳에서 날짜 붙은 모델명을 제외:
- 걸러냄: `gpt-4o-2024-08-06`(연월일), `gpt-4-0613`/`gpt-3.5-turbo-0125`/`grok-2-1212`(MMDD),
  `gemini-2.5-pro-preview-05-06`(프리뷰 날짜), `gemini-2.5-computer-use-preview-10-2025`(월-연도 —
  1차 배포 후 실서버 검증에서 빠져나간 걸 발견해 정규식 보강, 재배포로 해결).
- 통과시킴: `gemini-1.5-flash-002`(버전 번호), `grok-4-latest`, `gpt-4-32k`.
- [[dark-toss-api]]의 동일 파일에도 같은 필터 적용됨(두 API 각각 수정 필요).

## 배포·인프라
- pm2로 재시작하는 서버(`dark-upbit-api`). 배포 시 gitignore된 `env/.env.production`이 로컬에
  없어 중단된 적 있음 — 실서버에서 받은 `.env`로 복구. → [[deploy-sandbox-pnpm-shim]]

## `decide-signal.ts` 신호 판정 로직 + 스타일별 리스크 설계 (2026-07-17, 미검증)
토스와 공통으로 보고된 변경(신규 `decide-signal.ts`·설정/서비스 확대·웹 UI 개편·신규 엔드포인트) —
상세·미검증 경고는 중복 기재 대신 → [[dark-system-signal-risk-design-unverified]] 참조.

## 관련
- [[dark-system]] · [[dark-toss-api]] · [[dark-upbit-web]] · [[progdesigner]]
- [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]]
- [[2026-07-17-dark-system-신호리스크-설계-코드업데이트]] · [[dark-system-signal-risk-design-unverified]]
- [[trading-feature-flag-auto-exclude-manual-allow]] · [[deploy-sandbox-pnpm-shim]] · [[config-flag-gate-audit]]
