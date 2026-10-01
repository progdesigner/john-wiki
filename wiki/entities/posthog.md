---
tags: [entity, tool, analytics, external-service, lampas-studio, dalar-web-first, toktalk]
created: 2026-10-01
updated: 2026-10-01
---
# PostHog

외부 제품 분석(product analytics) 서비스(`us.posthog.com`, 미국 리전). [[lampas-studio]] 저장소
(`lampas-system`) 산하 서비스 전체의 사용자 여정·결제 전환 분석 용도로 2026-09-29 세션
([[2026-09-29-posthog-구축-연동]])에서 처음 도입됨.

## 연동 범위
- **프로젝트**: `us.posthog.com` 프로젝트 ID `636309`, 세션 시작 시점엔 이벤트가 전혀 없던 새
  프로젝트.
- **계측 대상**: `deploy.json`(배포 설정 파일)이 있는 웹 **36개 전체** — Lampas·Dalar·
  [[toktalk]](Talk)·Iileex 전 라인. 공통 계측 모듈 하나에 PostHog 연결 코드를 추가하는 방식으로
  일괄 반영(처음엔 Dalar·Lampas 웹 30개로 시작해 Talk·Iileex까지 확장).
- **제외**: Toss 내부 미니앱 3개(`talk-app-toss-*` 등)는 이번 웹 전용 적용 범위에 포함되지 않음.
- **사용자 식별 연결**: [[dalar-web-first]]와 결제 페이지(`pay.lampas.io`)에서는 서버가 확인한
  동일 사용자 ID로 로그인 전후 여정을 연결. 로그아웃 시 식별 해제도 보완.

## 대시보드
- [First 대시보드](https://us.posthog.com/project/636309/dashboard/2150784) — [[dalar-web-first]]
  전용 구매·제작 퍼널(3개), 단계별 이탈, 매출 차트.
- [전체 서비스 대시보드](https://us.posthog.com/project/636309/dashboard/2150812) — 전 서비스
  방문·이동 경로·결제 전환·결제 실패·매출 비교.

## 알려진 한계 (2026-09-29 세션 시점)
- **실제 결제 완료 이벤트는 미검증** — 결제 완료는 서버 승인 성공을 기준으로 수집하도록 설계만
  됐고, 실제 유료 결제를 발생시켜 테스트하지는 않았다. 데이터는 세션 종료 시점부터 쌓이기 시작.
- PII·토큰이 담긴 URL 파라미터 제거, 중복 구매 집계 방지는 테스트 10개로 검증됨(구현 상세는 이
  세션 소스로는 코드 없이 서술만 확인).
- Toss 미니앱 3개는 별도 작업 필요.

## PostHog 로그인 — Google OAuth 장벽 2회 (브라우저 자동화 일반 한계)
설정 전 PostHog에 Google 계정으로 로그인하는 과정에서 브라우저 자동화 도구(`Tool: codex` 자체
브라우저 기능)가 두 차례 막혔다 — ① 로그인된 Google 계정이 없어 비밀번호 입력이 필요했던 1차,
② Gmail 로그인은 됐으나 Google이 패스키·휴대전화 승인·OTP 중 하나의 추가 본인 인증을 요구한 2차.
두 경우 모두 "브라우저 도구는 비밀번호 입력을 지원하지 않는다"는 이유로 사용자에게 직접 완료를
요청하고 그동안 연동 코드를 먼저 준비하는 식으로 진행됐다 — [[lampas-browser]]에서 두 차례(Meta·
카카오 개발자 콘솔) 확인된 [[browser-automation-human-handoff-for-blocked-ui]] 패턴의 **세 번째
독립 재현**이며, 이번엔 `lampas-browser`가 아닌 Codex 자체 브라우저 도구에서 일어나 이 한계가
특정 구현이 아니라 AI 조작 브라우저 자동화 전반의 구조적 한계임을 도구를 바꿔서도 재확인했다.

## 관련
- 세션: [[2026-09-29-posthog-구축-연동]](도입 원본) ·
  [[2026-09-29-분석사이트구축-ga4퍼널-이벤트카탈로그]](33분 전 세션 — GA4 경로로 First 퍼널
  수집 공백을 먼저 조사·관리자 전환 분석 화면을 권고했으나, 이 PostHog 도입으로 대신 해결됨) ·
  [[2026-09-28-google-analytics-설정-first전용퍼널]](+1일 전, GA4를 전사 적용하고 First 전용
  퍼널까지 만들었으나 미배포로 남았던 선행 세션 — 그 수집 공백이 다음날 PostHog 도입의 계기가 됨)
- 엔티티: [[dalar-web-first]](결제 퍼널 분석 대상) · [[lampas-studio]](36개 웹 공통 계측) ·
  [[dalar]] · [[toktalk]] · [[ga-event-catalog-sheet]](같은 날 별도로 구축된 GA4 이벤트 카탈로그,
  PostHog와는 독립)
- 스킬: [[browser-automation-human-handoff-for-blocked-ui]]
