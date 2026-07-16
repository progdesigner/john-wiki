---
tags: [entity, project, trading, toss-securities, web, dark-system]
created: 2026-07-16
updated: 2026-07-16
---
# dark-toss-web

[[dark-system]] 모노레포 안의 웹 프런트엔드 — [[dark-toss-api]]의 대시보드. [[dark-upbit-web]]과
CSS 구조(`.panel-wrap` 공통 컨테이너)를 공유하는 자매 앱. S3 업로드 + CloudFront 무효화로 배포.

2026-07-15 [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] 세션에서 처음 다뤄짐.

## 확인된 사항
- **표 모바일 대응**: [[dark-upbit-web]]과 동일하게 `.panel-wrap`에 `overflow-x: auto` + 720px 이하
  `min-width: 640px` 적용, `index.css`만 수정. 데스크톱 레이아웃은 그대로.
- **확대 방지**: `index.html` viewport 메타에 `maximum-scale=1.0, user-scalable=no` 추가(핀치 줌·
  더블탭 확대·iOS 입력 자동확대 차단).
- **장마감 자동판단 일시정지 배지**: [[dark-toss-api]]의 `autoRegionSwitch`(자동 장이동) 기능이 켜져
  있고 국장·미장 모두 마감이라 AUTO가 일시정지된 상태면 "AUTO ⏸ 장마감 · 개장 시 재개" 배지 표시.
  개장 시 자동 재개되면 배지도 사라짐.
- **모델 select**: [[dark-toss-api]]의 `fetchChatModelCatalog`가 내려주는 목록을 그대로 씀 — 날짜
  스냅샷 모델 필터는 서버 쪽에서 처리.

## 관련
- [[dark-system]] · [[dark-toss-api]] · [[dark-upbit-web]]
- [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]]
- [[deploy-sandbox-pnpm-shim]]
