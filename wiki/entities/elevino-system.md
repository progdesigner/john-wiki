---
tags: [entity, repository, cwc, nestjs, typeorm, nextjs, membership, credit]
created: 2026-07-16
updated: 2026-09-26
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
- 2026-07-17 [[2026-07-17-works-저장소-일괄최신화-pull]]: 로컬 저장소가 **`dev` 브랜치**(main 아님)에
  있음이 처음 확인됨. 같은 pull에서 `_v2/api` 설정 파일 정리 커밋 1개 반영.

## 관리자 모임 예약 취소·크레딧 환불 기능 (2026-09-25 구현·배포)
[[2026-09-25-엘레비노-예약취소-크레딧환불-배포장애]] 세션에서 구현·배포. 어드민의 "모임 예약"은
`reservation`(공간 예약) 모듈이 아니라 `RESERVE_GATHERING` 티켓+영수증임을 확인.
- 예약 취소(`POST /admin/tickets/:id/gathering/cancel`): 미결제면 단순 취소, 결제완료면 새 트랜잭션
  타입 **`CREDIT_REFUND`**로 크레딧 환원 + 영수증 `REFUNDED`.
- 크레딧 환불(`POST /admin/tickets/:id/gathering/refund-credit`): 환원된 크레딧을 계좌로 출금 처리
  (`CREDIT_EXCHANGE` 티켓·영수증 생성, 기존 "크레딧 출금" 목록과 통합). 초과반환·잔액부족·중복환불 방지.
- DB 스키마 변경 없음(트랜잭션 타입 값 1개 추가). [[credit-ledger-balance-pattern]] 스킬의 원장 구조
  분석을 그대로 활용한 사례.

## 배포 경로 이중화 발견 (2026-09-25)
프로덕션이 **8월 3일부터 이 모노레포가 아니라 형제 저장소 [[cwc-system]]의 `apps/elevino-*`에서
수동 배포되고 있었음**이 이번 세션에서 처음 드러남 — 이 저장소의 `dev`를 그대로 배포하자 cwc-system
쪽에만 있던 기능(cwc 브리지·와인 모듈, 소장 신청 중단/취소, 모바일 관리자 메뉴 등)이 프로덕션에서
사라졌다가 3-way 머지로 복구됨. 같은 세션에서 오래된 `ServiceLoader.onModuleInit`의 잠재 버그
(provider 이름이 문자열이 아니면 TypeError로 부팅 실패)가 롤백 시도 중 드러나 방어 코드로 수정.
→ 절차는 [[prod-rollback-source-of-truth-verify]] 스킬 참고.

## 관련
- 세션: [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]], [[2026-07-14-clara-바바라-위스키분쟁-번역]], [[2026-07-17-works-저장소-일괄최신화-pull]], [[2026-09-25-엘레비노-예약취소-크레딧환불-배포장애]]
- 이식 대상: [[cwc-system]] (DB를 그대로 재사용해 멤버십 크레딧 기능 이식 중 / 프로덕션 배포 소스 겸용으로 확인됨)
- 배경 회사: [[cwc-commerce]]
- 스킬: [[credit-ledger-balance-pattern]], [[prod-rollback-source-of-truth-verify]]
