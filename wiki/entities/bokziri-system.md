---
tags: [entity, project, works, welfare, benefits, toss-mini-app]
created: 2026-09-26
updated: 2026-09-26
---
# bokziri-system (복지리)

`~/Works/bokziri/bokziri-system` — [[progdesigner]]의 [[works-project-portfolio]] 중 하나. 2026-07-15
최신화 세션에서 이름만 노출된 뒤 2026-09-26 [[2026-09-21-복지리-로고-cdn이전-광고배너크기수정]] 세션에서
처음 상세 조사됨. "복지"(직장 복지/혜택) 정보 제공 서비스로 추정 — 회사(companies)·커뮤니티 게시글·
자료(resource)·문의(inquiry) 도메인 모델 보유.

## 구성 (확인된 범위)
- **웹**: `bokziri-web-www` (`apps/bokziri-web-www`). 배포는 S3 업로드+CloudFront 무효화. 버전
  0.8.35 → 0.8.37(2026-09-21 세션 중 3회 배포).
- **토스 미니앱**: 저장소 내 "toss" 앱, 토스(Toss) 슈퍼앱 플랫폼의 인앱("앱인토스") 미니앱으로 배포.
  배포 도구는 `ait deploy` CLI(툴 자체는 미조사) — 배포 후 토스 콘솔에서 별도 "릴리즈 반영"이 필요한
  2단계 구조. 버전 0.2.6 → 0.2.8(같은 세션 중 3회 배포).
- **API**: `company.service.ts`(회사 로고 URL 도메인 판별)·`company.seeds.ts`(시드 로고 URL). 이번
  세션에선 코드만 수정, API 서버 자체는 미배포(운영 API가 이미 신규 도메인을 허용하고 있어 무관).
- **운영 DB 테이블(확인분)**: `bokziri_company`(logo), `community_post`(content/resources),
  `resource`(resource_url/resource_thumbnail_url), `user`(resources), `inquiry`(resources).

## 인프라 이력
- **CDN 도메인 이전 (2026-09-21)**: 회사 로고 등 정적 자산이 죽은 외부 도메인 `cdn.dbs.best`를
  가리키고 있어 전부 깨짐 → S3 13,815개 객체(≈1.5GB)를 `s3://bokziri.com/cdn/production/`로 복사,
  신규 CloudFront(`E3VOBEVVHUEUN0`, 별칭 `cdn.bokziri.com`, 기존 `*.bokziri.com` 와일드카드 인증서
  재사용, OAC) 생성, DB 5개 테이블·13,443행 경로 일괄 치환. `bokziri.com` DNS가 Route53이 아니라
  **Squarespace**에 있어 CNAME 등록은 사용자가 직접 해야 했음. 절차는 [[dead-external-cdn-domain-migration]]
  스킬로 추출.
- **AdSense 배너 크기 버그 (같은 세션 후속)**: `apps/bokziri-web-www/src/components/ads/AdBanner.tsx`
  — 8월 말 커밋에서 광고 `ins`를 고정 높이 50px·너비 100%로 지정한 것이 원인. Google이 고정 크기
  모드에서 지정 높이(50px)에 맞는 표준 크리에이티브(320×50)만 채워, 넓은 화면에서도 항상 작게
  나옴 + `inline-block` 좌측 정렬로 더 작아 보임. 뷰포트 폭 기준 미디어쿼리(500px 미만 320×50,
  이상 468×60)+가운데 정렬+래퍼 높이 동기화로 수정, www 0.8.37 배포.

## 관련
- 세션: [[2026-09-21-복지리-로고-cdn이전-광고배너크기수정]]
- 스킬: [[dead-external-cdn-domain-migration]]
- 상위 목록: [[works-project-portfolio]]
