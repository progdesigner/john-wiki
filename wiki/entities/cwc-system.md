---
tags: [entity, repository, cwc, nestjs, prisma, react]
created: 2026-07-16
updated: 2026-09-26
---
# cwc-system

`~/Works/cwc/cwc-system` — [[cwc-commerce]] 소유 ERP 분석/관리 pnpm 모노레포. "ERP 데이터를 크롤링해
사업자(gateway)별로 분석·관리". 2026-07-15 06:42 UTC [[2026-07-15-cwc-system-저장소-클론]] 세션에서
`git@github.com:cwc-commerce/cwc-system.git`를 최초 클론(로컬에 처음 등장) → 06:48
[[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]에서 "이미 최신"으로 집계 → 06:51
[[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]] 세션에서 처음으로 내용이 깊이 조사됨.

## 사실 (2026-07-15 조사 시점)
- pnpm workspace (`apps/*`), `pnpm@10.30.1`, node >=22.
- 4개 앱:
  - **cwc-api**(`@cwc/api` v0.1.5) — NestJS 11 + **Prisma 6**(MySQL), 포트 3341(README엔 3000으로 오기재).
    크롤링 데이터 수신(ingest) + gateway별 분석 + 사용자/인증.
  - **cwc-web-admin**(`@cwc/web-admin` v0.1.6) — React 19 + Vite 6 + react-router-dom 7 +
    @tanstack/react-query 5 + recharts, 포트 8341. 관리자 대시보드 UI. CSS 프레임워크 없음(순수 CSS
    단일 파일 `styles.css`, CSS 변수 기반).
  - **cwc-crawler-erp** — Python 3.12 + Playwright, ERP 웹 크롤러(품목/매출/재고 다운로드 후 API POST).
  - **cwc-agent-docs** — Python, 문서/에이전트용 부수 앱.
- DB: Prisma 6, MySQL. 로컬은 docker-compose `cwc-mysql`(포트 3306, DB명 `cwc`, utf8mb4). 기존 모델
  `User`, `Gateway`, `Item`, `Sales`, `Inventory`, `IngestRun`, `InventoryMonthSnapshot` — 전부
  `@@map` 스네이크케이스, `id` cuid(), `createdAt/updatedAt` 관례.
- 인증: Google OAuth(GIS), `cwccommerce.com` 도메인 계정만 허용. API가 Google ID 토큰을 직접 검증 후
  자체 HS256 앱 세션 JWT(7일) 발급. 권한모델 `User.isAdmin`/`User.menus`/`User.gateways`.
- 라우팅(Admin): `App.tsx`에 `<Routes>` 정의(`/dashboard`, `/upload`, `/search`, `/users`), `Layout.tsx`에
  네비 `TABS` 배열. 신규 페이지 추가 시 3곳(페이지 파일, App.tsx 라우트, Layout.tsx 메뉴) 동시 수정 필요.
- Git: 브랜치 `main`, origin `git@github.com:cwc-commerce/cwc-system.git`. 커밋 스타일 Conventional
  Commits, `feat:`/`chore(release):` 프리픽스 + **본문 한국어**.
- ⚠️ 프로덕션 env(`apps/cwc-api/env/.env.production`)에 DB 비밀번호·JWT 시크릿·Google Client ID가
  **평문 커밋**되어 있음 (2026-07-15 발견, 미조치 보안 이슈). → 반복 패턴으로 묶임: [[secrets-plaintext-exposure-pattern]]

## 진행 중 작업
- [[elevino-system]] DB를 그대로 재사용해 "멤버십 검색 + 크레딧 지급/차감" 관리자 기능을 신규 이식하는
  작업이 설계까지 완료되고 구현 착수 직전 하네스 권한 장애로 중단됨 → [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]].
  구현 재개 필요.

## ⚠️ 모순 기록 — `apps/elevino-*` 존재 (2026-09-25 발견)
2026-07-15 조사 시점에는 이 저장소에 **4개 앱(cwc-api/cwc-web-admin/cwc-crawler-erp/cwc-agent-docs)**
만 있다고 기록했으나, [[2026-09-25-엘레비노-예약취소-크레딧환불-배포장애]] 세션에서 **`apps/elevino-*`
경로가 존재하며 8월 3일부터 [[elevino-system]] 프로덕션(API+어드민)의 실제 수동 배포 소스로 쓰이고
있었음**이 드러남. 즉 이 저장소는 (a) 자체 ERP 크롤링/분석 앱들과 (b) elevino-system과 내용이 겹치는
`apps/elevino-*`를 함께 담은 저장소로, 후자는 2026-07-15 조사 때 언급되지 않았다(당시 조사 범위가
cwc-api/cwc-web-admin 위주였을 가능성). `apps/elevino-*`의 상세 구조는 아직 미조사 — 다음에 이
저장소를 다룰 때 `apps/` 전체 목록을 다시 확인할 것.
- 2026-09-25 세션에서 이 괴리를 "elevino-system(모노레포) `dev`"와 "cwc-system `apps/elevino-*`"의
  공통 조상(`afd2085c`) 기준 3-way 머지로 해소, elevino-system `dev`가 이제 cwc-system보다 앞선
  상태 — 앞으로는 한쪽에서만 작업·배포하기로 함(어느 쪽인지는 미확정, 모노레포 권장 언급됨).
  → [[prod-rollback-source-of-truth-verify]]

## 변경 이력
- 2026-07-17 [[2026-07-17-works-저장소-일괄최신화-pull]]: `main`에서 1커밋 pull(주간 매출 리포트 도구
  추가) — Works 일괄 최신화 세션 중 미커밋 변경 없이 깨끗하게 반영됨.
- 2026-09-25 [[2026-09-25-엘레비노-예약취소-크레딧환불-배포장애]]: `apps/elevino-*`가 프로덕션 배포
  소스였음이 밝혀짐(위 모순 기록 참고).

## 관련
- 세션: [[2026-07-15-cwc-system-저장소-클론]] (최초 클론), [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]], [[2026-07-17-works-저장소-일괄최신화-pull]], [[2026-09-25-엘레비노-예약취소-크레딧환불-배포장애]]
- 관련 저장소: [[elevino-system]] (DB 소스 / `apps/elevino-*` 프로덕션 배포 상대)
- 배경 회사: [[cwc-commerce]]
- 스킬: [[credit-ledger-balance-pattern]], [[prod-rollback-source-of-truth-verify]]
