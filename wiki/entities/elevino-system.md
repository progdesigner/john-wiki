---
tags: [entity, repository, cwc, nestjs, typeorm, nextjs, membership, credit]
created: 2026-07-16
updated: 2026-07-16
---
# elevino-system

`~/Works/cwc/elevino-system` — [[cwc-commerce]] 계열 서비스. `micro.elevino.io` 도메인이 [[toktalk]]
배포 대상과 겹쳐 계열 서비스로 추정([[works-project-portfolio]]). "미문서화"로만 등장했다가 2026-07-15
[[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]] 세션에서 크레딧/멤버십 기능 분석 목적으로 정밀 조사됨.

## 사실 (2026-07-15 조사 시점)
- pnpm workspace (`pnpm@9.14.2`), `apps/*`. docker-compose 없음(DB/Redis 외부 또는 로컬 직접 설치 전제).
- 앱 6개: `apps/api`(elevino-api, NestJS 10.3.x + **TypeORM 0.3.20** + mysql2), `apps/admin`(elevino-admin,
  Next.js 14.2.35 App Router + React 18 + MUI 5, 포트 8102), `apps/web`(elevino-web, Vue3+Vite, 8101),
  `apps/www`(elevino-www, Vue3+Vite, 8100), `apps/link`(elevino-link, Next.js 15), `apps/app`(Flutter).
  크레딧/멤버십 기능은 `apps/api` + `apps/admin`에만 존재.
- DB: MySQL(mysql2) + TypeORM 0.3.20, 엔티티 기반(별도 통합 스키마 파일 없음, `**/**.entity{.ts,.js}`
  자동 로드). 커스텀 `@Column` 데코레이터(`core/decorators/column`)로 표준 TypeORM Column을 래핑.

### 크레딧 시스템 (핵심 — [[credit-ledger-balance-pattern]] 스킬로 추출됨)
- 2종 크레딧 테이블: **`credit`**(사용자 단위, `user_id` 기준 — 관리자 지급/차감 UI가 실제 쓰는 KRC
  크레딧, 이식 핵심)과 **`branch_credit`**(멤버십 단위 `member_id` 기준 — "멤버십 포인트", 단순 CRUD만
  존재, 지급/차감 로직 없음).
- 잔액은 **원장(ledger) 방식** — 별도 잔액 컬럼 없이 `credit` 테이블 마지막 행(`id DESC`)의
  `credit_after`가 현재 잔액.
- **네이밍 역전 함정**: `POST /admin/credits/grant`(메서드명 `adminCreditControllerGrant`)는 실제로는
  **차감**(`amount * -1`). `POST /admin/credits/system-grant`가 실제 즉시 **지급**.
- `addCredit`(read-then-write)이 **트랜잭션/행 잠금 없이** 수행됨 — 동시 요청 시 레이스 컨디션 가능.
- "멤버십 검색" UI는 실제로 `member` 테이블이 아니라 **`user` 테이블**을 이름/닉네임/전화번호 LIKE로
  검색(이메일은 검색 대상 아님). `member`는 `user.member_id`로 user와 간접 연결.
- 인증: JWT(Bearer) + StaffGuard 2단, `@Route`/`@StaffInfo` 커스텀 데코레이터.

## 관찰
- 2026-07-14 CWC-FY Group 위스키분쟁 이메일 서명에 **"CWC ⎢ ELEMENTS ⎢ ELEVINO"**가 나란히
  표기됨 — micro.elevino.io 도메인 겹침으로 추정만 하던 [[cwc-commerce]] 계열 관계가 이메일
  서명으로 명시적으로 뒷받침됨.

## 관련
- 세션: [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]], [[2026-07-14-clara-바바라-위스키분쟁-번역]]
- 이식 대상: [[cwc-system]] (DB를 그대로 재사용해 멤버십 크레딧 기능 이식 중)
- 배경 회사: [[cwc-commerce]]
- 스킬: [[credit-ledger-balance-pattern]]
