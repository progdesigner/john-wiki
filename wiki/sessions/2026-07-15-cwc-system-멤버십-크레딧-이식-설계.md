---
tags: [session, cwc-system, elevino-system, membership, credit, prisma, typeorm, nestjs]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-15 — cwc-system 멤버십 크레딧 지급/차감 기능 이식 설계

## 요청
"elevino-system 처럼 cwc-system 에다가 멤버십 정보를 검색해서 멤버십에게 크레딧을 지급하거나
차감시키는 기능을 만들고 싶어. DB는 elevino DB 그대로 써서 만들어야해. 다 구축하면 Commit & Push."
→ elevino DB(기존 스키마·데이터)를 그대로 두고, cwc-system(관리자 대시보드)에서 조회/조작하는
"관리자 기능 이식" 작업.

## 진행 경과
1. **[[cwc-system]] 저장소 분석** — 구조·DB 연결·라우트 패턴·UI 스타일·인증 방식을 병렬 서브에이전트로 조사.
2. **[[elevino-system]] 저장소 분석** — 크레딧 시스템 2종(`credit` vs `branch_credit`), 원장(ledger) 방식
   잔액 계산, `grant`/`system-grant` 네이밍 역전, `addCredit` 동시성 미보호, 사용자 검색 로직(`user` 테이블
   keyword LIKE, member는 간접 연결)을 정밀 분석.
3. **구현 설계 확정**:
   - DB: cwc-api에 elevino DB 전용 Prisma 스키마(`prisma/elevino.prisma`) 추가, `ELEVINO_DATABASE_URL` env로
     연결. 기존 `user`·`member`·`credit` 테이블 그대로 사용, **마이그레이션 없음**(읽기 + 원장 INSERT만).
   - API: `apps/cwc-api/src/membership/` 신규 NestJS 모듈 — 검색(`GET /api/membership/search`), 잔액/내역
     조회, 지급/차감(`POST .../credits/grant`, `.../credits/deduct`). elevino의 `grant`=차감 네이밍 역전을
     바로잡고, 트랜잭션+행 잠금으로 동시성 문제 보강 예정. `assertAdmin` 패턴으로 관리자 전용.
   - UI: `cwc-web-admin`에 멤버십 크레딧 페이지 — 검색바 → 회원 목록(멤버십 코드·등급·잔액) → 지급/차감
     폼 + 최근 원장 내역. 기존 `styles.css`/react-query 패턴 재사용, `App.tsx`/`Layout.tsx`에 메뉴 등록.
4. **구현 착수 직전 하네스 권한 채널 장애로 중단**: 읽기 전용 명령은 정상 동작했으나, Write/파일 생성 등
   승인 필요 작업이 전부 `Tool permission request failed: Error: Stream closed` 오류로 실패. 재시도해도
   동일. 실제 코드 작성·커밋·푸시는 **이 세션에서 이루어지지 않았다.**

## 결정/사실
- 이식 방식: elevino DB에 **직접 연결**(별도 복제·마이그레이션 없이), cwc-system에서 새 Prisma 클라이언트로
  같은 테이블을 읽고 쓰는 구조로 확정.
- elevino 원본의 알려진 결함 2가지를 이식 시 고치기로 결정: (1) `grant`/`system-grant` 네이밍 역전,
  (2) `addCredit`의 read-then-write 사이 트랜잭션 미보호(레이스 컨디션).
- 재사용 가능한 설계 패턴은 [[credit-ledger-balance-pattern]] 스킬로 별도 추출.

## 미해결 / 다음 단계
- **실제 구현은 진행되지 않음.** 하네스 세션을 다시 열거나 승인 모드를 점검한 뒤, 위 설계안 그대로
  cwc-api `membership` 모듈 + cwc-web-admin 페이지 구현 → 빌드 검증 → `feat:` 컨벤션(한국어 본문)
  커밋 & 푸시가 남아있다.
- 프로덕션 env 파일(`apps/cwc-api/env/.env.production`)에 DB 비밀번호·JWT 시크릿·Google Client ID가
  평문 커밋되어 있음이 부수적으로 발견됨 — 신규 작업과 무관하지만 별도로 인지 필요(보안 이슈, 미조치).

## 관련
- 엔티티: [[cwc-system]], [[elevino-system]], [[cwc-commerce]]
- 스킬: [[credit-ledger-balance-pattern]]
- 이전 세션: [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] (이 세션에서 cwc-system·elevino-system이
  "미문서화" 저장소로 처음 목록에만 등장 → 이 세션에서 최초로 내용 조사됨)
