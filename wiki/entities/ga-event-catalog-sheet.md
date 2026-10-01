---
tags: [entity, tool, analytics, ga4, google-sheets, lampas-studio]
created: 2026-10-01
updated: 2026-10-01
---
# GA 이벤트 카탈로그 구글시트 (`lampas-system`)

`lampas-system`([[lampas-studio]]) 저장소 전체의 GA4 이벤트 정의를 코드에서 스캔해 저장·관리하는
구글 스프레드시트. [[2026-09-29-분석사이트구축-ga4퍼널-이벤트카탈로그]] 세션에서 처음 구축됨.

- **시트**: https://docs.google.com/spreadsheets/d/1VpbhftSjfFyKsPH7PvHfaBXBsua6Qb0bNx87uSaZbx4/edit
- **접근**: `lampas-harness`의 구글 서비스 계정(`lampas-crawler@lampas.iam.gserviceaccount.com`)을
  편집자로 초대해 하네스가 직접 읽고 쓸 수 있게 구성.
- **내용(최초 적재, 2026-09-29 기준)**: 코드에서 발견한 **이벤트명 106개**, 앱별로 나눈
  **정의 382행**(같은 이벤트명이 여러 앱에서 각자 정의되는 경우가 많아 명·행 수가 다름).
- **탭 구성**: `GA 이벤트`(발생 조건·파라미터·코드 위치) · 앱별 계측 상태 · First 퍼널 ·
  동적 이벤트 검토 · GA 자동 이벤트 · 관리 안내.
- **보존 동기화**: `GA 이벤트` 탭의 **수집 상태·담당자·검증일·메모** 열은 사람이 직접 관리.
  재스캔 커맨드 `pnpm analytics:catalog:sync`를 다시 실행해도 이 열들은 덮어쓰지 않고 보존하도록
  설계됨 — 코드 구현 여부와 운영 실제 수신 여부를 별도 열로 구분해 표시.
- **한계**: 이 카탈로그는 **코드 기준 이벤트 정의**일 뿐이며, 실제 운영에서 각 이벤트가 정상
  수신되는지는 별도 확인 필요(이 세션에서는 열만 분리해두고 실데이터 대조는 하지 않음).

## 관련
- 세션: [[2026-09-29-분석사이트구축-ga4퍼널-이벤트카탈로그]](구축 원본, 같은 세션에서
  `first.dalar.ai`/`admin.first.dalar.ai` GA4 퍼널 수집 공백도 함께 조사) ·
  [[2026-09-28-google-analytics-설정-first전용퍼널]](+1일 전, 이 카탈로그가 스캔한 GA 이벤트를
  전사에 처음 적용한 선행 세션)
- 엔티티: [[lampas-studio]](저장소 전체 대상) · [[dalar-web-first]](First 퍼널 탭 포함) ·
  [[posthog]](같은 날 뒤이어 도입된 별도 분석 솔루션, 이 카탈로그와는 독립)
