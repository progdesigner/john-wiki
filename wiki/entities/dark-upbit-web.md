---
tags: [entity, project, trading, upbit, crypto, web, dark-system]
created: 2026-07-16
updated: 2026-07-16
---
# dark-upbit-web

[[dark-system]] 모노레포 안의 웹 프런트엔드 — [[dark-upbit-api]]의 대시보드(보유 포지션, AI 판단
로그, 체결 내역, 파라미터 변경 이력, 스타일/모델 select). S3 업로드 + CloudFront 무효화로 배포된다.
[[dark-toss-web]]과 CSS 구조(`.panel-wrap` 공통 컨테이너)를 공유하는 자매 앱.

2026-07-15 [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] 세션에서 처음 다뤄짐.

## 확인된 사항
- **표 모바일 대응**: 모든 표가 `.panel-wrap` 컨테이너 안에 있어 `index.css` 한 곳만 고치면 전체
  반영됨. `overflow-x: auto`(iOS 관성 스크롤 포함) + 720px 이하에서 `min-width: 640px`로, 열이
  찌그러지거나 말줄임되는 대신 가로 스크롤로 원래 폭을 볼 수 있게 함. 데스크톱 레이아웃·"AI 판단 로그"
  세로 스크롤(헤더 고정)은 그대로 유지.
- **확대 방지**: `index.html` viewport 메타에 `maximum-scale=1.0, user-scalable=no` 추가. 핀치
  줌·더블탭 확대 차단 + iOS 사파리 입력 필드 포커스 시 자동 확대도 함께 막힘.
- **스타일 드롭다운 하드코딩 사고**: 한때 서버 `/trading/styles`를 안 쓰고 옵션을 하드코딩해서, 서버가
  이미 초단타를 거부 중인데도 목록엔 계속 노출되는 불일치가 있었음. 서버 API를 따르도록 수정,
  초단타는 이후 "초단타 (일봉·볼린저·60초)" 라벨로 수동 선택 항목으로 복원됨. → [[dark-upbit-api]]
  게이팅 구조 참고.
- **모델 select**: [[dark-upbit-api]]의 `fetchChatModelCatalog`가 내려주는 목록을 그대로 씀(자체
  하드코딩 없음) — 날짜 스냅샷 모델 필터는 서버 쪽(`llm-catalog.ts`)에서 처리.

## 관련
- [[dark-system]] · [[dark-upbit-api]] · [[dark-toss-web]]
- [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]]
- [[deploy-sandbox-pnpm-shim]]
