## user — 2026-07-15T06:51:22.925Z

elevino-system 처럼 cwc-system 에 다가 
멤버십 정보를 검색해서 멤버십에게 크레딧을 지급하거나, 차감 시키는 기능을 만들고 싶어 만들어줘 
db 는 elevino db 그대로 써서 만들어야해 다 구축하면 Commit & Push 해줘

## assistant — 2026-07-15T06:51:30.043Z

cwc-system과 elevino-system 구조를 먼저 파악하겠습니다.

## assistant — 2026-07-15T06:51:55.943Z

I'll analyze the elevino-system repository thoroughly. Let me start by exploring its structure.

## assistant — 2026-07-15T06:52:03.494Z

저장소를 체계적으로 분석하겠습니다. 먼저 전체 구조와 주요 설정 파일들을 병렬로 살펴보겠습니다.

## assistant — 2026-07-15T06:52:06.685Z

Let me read the CLAUDE.md and explore each app's setup.

## assistant — 2026-07-15T06:52:10.210Z

4개의 앱이 있습니다. 루트 설정과 각 앱의 구조를 병렬로 살펴보겠습니다.

## assistant — 2026-07-15T06:52:14.157Z

The CLAUDE.md is very detailed. Now let me dive into the actual credit and membership modules in the API.

## assistant — 2026-07-15T06:52:17.700Z

핵심은 `cwc-api`(NestJS)와 `cwc-web-admin`(React+Vite)입니다. 두 앱의 package.json과 소스 구조를 병렬로 파악하겠습니다.

## assistant — 2026-07-15T06:52:24.089Z

Let me read the core credit and membership entities and admin controllers/services in parallel.

## assistant — 2026-07-15T06:52:27.038Z

핵심 패턴 파일들을 병렬로 읽겠습니다. API의 라우팅/DB/인증과 Admin의 라우팅/네비/기존 관리 페이지를 확인합니다.

## assistant — 2026-07-15T06:52:31.977Z

두 분석 에이전트가 아직 작업 중입니다. 완료 알림이 오는 대로 elevino DB 스키마·크레딧 로직을 cwc-system에 이식하는 구현을 시작하고, 완료 후 커밋·푸시까지 진행하겠습니다.

## assistant — 2026-07-15T07:03:49.413Z

인증 구조와 프론트엔드 라우팅/네비/기존 관리 페이지를 병렬로 읽겠습니다.

## assistant — 2026-07-15T07:03:49.414Z

Now let me read the DTOs, member entities, membership admin controller/service, and auth/DB config.

## assistant — 2026-07-15T07:03:49.417Z

프론트엔드 API 클라이언트, 인증, 기존 관리 페이지, 스타일, env 파일을 병렬로 읽겠습니다.

## assistant — 2026-07-15T07:03:49.417Z

Let me examine the branch.credit module (membership-scoped credit by memberId), member types, member search DTO, and auth/DB config.

## assistant — 2026-07-15T07:03:49.418Z

대표 API 컨트롤러/서비스, 인증 서비스/모듈, 기존 검색 페이지, 그리고 스타일 앞부분을 병렬로 확인하겠습니다.

## assistant — 2026-07-15T07:03:49.420Z

Now let me examine the search infrastructure (PaginationDTO, SimpleRepositoryService), auth (Route decorator, StaffGuard), member types, and DB connection config.

## assistant — 2026-07-15T07:03:49.420Z

멤버십/크레딧 관련 기존 코드가 있는지 확인하고, 공통 CSS 클래스와 기존 검색 페이지 스타일을 확인하겠습니다.

## assistant — 2026-07-15T07:03:49.421Z

The bash call failed due to a permission stream issue. Let me retry the grep for membership/credit and CSS classes.

## assistant — 2026-07-15T07:03:49.421Z

Let me find the Route decorator, DB connection config in modules/index.ts, and the JWT/token middleware.

## assistant — 2026-07-15T07:03:49.422Z

Now let me explore the admin UI (apps/admin) for credit and membership pages, and read the DB config service.

## assistant — 2026-07-15T07:03:49.423Z

The `/credits/manage` page is likely the credit grant/deduct admin UI. Let me read it plus the users search page.

## assistant — 2026-07-15T07:03:49.423Z

Let me read the UserSelectorField (the search UI) and the AdminApi client auth mechanism.

## assistant — 2026-07-15T07:03:49.423Z

Let me read the UserSearchDialog (search conditions) and the AdminApi auth mechanism, plus the credit schemas.

## assistant — 2026-07-15T07:03:49.423Z

Let me examine the server-side user search (keyword) and the AdminApi auth mechanism.

## assistant — 2026-07-15T07:03:49.424Z

Let me check the AdminApi auth mechanism, the JWT guard (token payload source), credit response RO, and User entity key fields.

## assistant — 2026-07-15T07:03:49.424Z

Let me get the User entity key fields and the AdminApi auth token handling to finish.

## assistant — 2026-07-15T07:03:49.425Z

분석을 완료했습니다. 아래에 한국어로 정리해 보고합니다.

---

# CWC System 저장소 분석 보고

## 결론 요약
`멤버십 검색 + 크레딧 지급/차감` 관리자 기능은 **완전히 신규**입니다. 기존 코드에 membership/credit 회원 관리 모델은 없습니다(유일한 `membership` 언급은 `ingest.service.ts:141`의 매출 결제수단 필터일 뿐). 추가할 자리는 명확합니다: **API는 `apps/cwc-api/src/`에 새 NestJS 모듈**, **Admin은 `apps/cwc-web-admin/src/pages/`에 새 페이지 + 라우트/네비 등록**, DB는 **Prisma 스키마에 신규 모델** 추가. 기존 `users`(스탭 관리) 도메인이 그대로 참고 템플릿이 됩니다.

---

## 1. 모노레포 구조
- 루트: pnpm workspace (`pnpm-workspace.yaml` → `apps/*`), packageManager `pnpm@10.30.1`, node >=22. 루트 `package.json`에 통합 스크립트(`dev`, `build`, `prisma:*`, `api:dev`, `admin:dev`).
- `apps/` 하위 4개 앱:

| 앱 | 프레임워크/버전 | 역할 | 포트 | dev / build |
|---|---|---|---|---|
| **cwc-api** (`@cwc/api` v0.1.5) | NestJS 11 + Prisma 6 (MySQL) | REST API. 크롤링 데이터 수신(ingest) + gateway별 분석 + 사용자/인증 | **3341** (`main.ts:27`, 기본 3341; README엔 3000으로 기재됨) | `nest start --watch --debug` / `nest build` |
| **cwc-web-admin** (`@cwc/web-admin` v0.1.6) | React 19 + Vite 6 + TS + react-router-dom 7 + @tanstack/react-query 5 + recharts | **관리자 대시보드 UI** | **8341** (`vite.config.ts:7`) | `vite` / `tsc --noEmit && vite build` |
| **cwc-crawler-erp** | Python 3.12 + Playwright | ERP 웹 크롤러 (품목/매출/재고 다운로드 후 API로 POST) | - | (Python) |
| **cwc-agent-docs** | Python | 문서/에이전트용 (부수적) | - | (Python) |

→ 신규 기능 관련 앱은 **cwc-api**와 **cwc-web-admin** 두 개입니다.

## 2. DB 연결
- **ORM: Prisma 6** (`@prisma/client`), provider `mysql`. 스키마: `apps/cwc-api/src/../prisma/schema.prisma` (경로: `apps/cwc-api/prisma/schema.prisma`).
- 연결 env 키: `DATABASE_URL`(Prisma 전용). 프로덕션은 AWS RDS MySQL 사용. env 파일: `apps/cwc-api/env/.env.production` (스크립트 `env:local`/`env:production`이 `env/.env.*` → `.env`로 복사).
- `docker-compose.yml`: 로컬용 **MySQL 8.4** 컨테이너(`cwc-mysql`, DB명 `cwc`, 포트 3306, utf8mb4). 로컬 개발 DB용.
- 기존 모델: `User`, `Gateway`, `Item`, `Sales`, `Inventory`, `IngestRun`, `InventoryMonthSnapshot`. 모두 `@@map` 스네이크케이스 테이블명, id는 `cuid()`, `createdAt/updatedAt` 관례. → 크레딧용 신규 모델도 이 관례를 따르면 됩니다.
- Prisma 스크립트: 루트에서 `pnpm prisma:migrate`, `prisma:generate`, `prisma:studio`, `prisma:seed` 등.

## 3. 페이지/라우트 구조 (Admin)
- 라우팅 정의: `apps/cwc-web-admin/src/App.tsx` — `react-router-dom` `<Routes>`. 기존 라우트: `/dashboard`, `/upload`, `/search`, `/users`(관리자 전용). `RequireMenu`(메뉴권한 가드)와 `RequireAdmin`(관리자 가드) 래퍼 사용.
- 네비게이션/메뉴 정의: `apps/cwc-web-admin/src/components/Layout.tsx` — 상단 `TABS` 배열 + `user.isAdmin`일 때만 `/users`("스탭 관리") 링크 노출. `NavIcon` 스위치로 SVG 아이콘 추가.
- 레이아웃 파일: `Layout.tsx`(헤더+nav+`<Outlet/>`), 감싸는 인증 게이트: `apps/cwc-web-admin/src/components/AuthGate.tsx`.
- 페이지 파일들: `src/pages/DashboardPage.tsx`, `ItemSearchPage.tsx`, `UploadPage.tsx`, `UserManagementPage.tsx`.
- **신규 페이지 추가 시 손봐야 할 곳**: (1) `src/pages/MembershipPage.tsx` 생성, (2) `App.tsx`에 `<Route>` + 가드 추가, (3) `Layout.tsx`의 `TABS` 또는 admin 링크 블록에 메뉴 추가(+`NavIcon` case), (4) 메뉴 권한 방식으로 갈 경우 `access.ts`의 `MENUS`와 `auth.tsx`의 `MENU_LABELS`에도 등록.

## 4. API 라우트 패턴
- NestJS 모듈식: 각 도메인이 `*.module.ts` / `*.controller.ts` / `*.service.ts` 세트. 모듈은 `apps/cwc-api/src/app.module.ts`의 `imports`에 등록.
- 인증은 **전역 가드**(`AuthModule`에서 `APP_GUARD`로 `AuthGuard` 등록, `auth.module.ts:12`). 컨트롤러에서 `@Req() req: AuthedRequest` 받아 `assertAdmin(req)` / `assertGatewayAccess(req, code)` / `permittedGateways(req)` 헬퍼(`auth/auth.guard.ts`)로 권한 체크. 공개 엔드포인트는 `@Public()`.
- DTO 검증: `class-validator` 데코레이터 + 전역 `ValidationPipe`(`main.ts`, whitelist/forbidNonWhitelisted). 전역 prefix `api` → 실제 경로는 `/api/...`.
- **관리자 CRUD 대표 예시** (신규 기능의 최적 템플릿) — `apps/cwc-api/src/users/users.controller.ts`:
```ts
@Controller('users')
export class UsersController {
  constructor(private readonly users: UsersService) {}

  @Get()
  list(@Req() req: AuthedRequest) {
    assertAdmin(req);
    return this.users.list();
  }

  @Patch(':id')
  update(@Param('id') id: string, @Body() body: UpdateUserDto, @Req() req: AuthedRequest) {
    assertAdmin(req);
    return this.users.update(id, body);
  }
}
```
서비스는 `PrismaService` 주입해서 `this.prisma.user.findMany(...)` 형태(`users/users.service.ts`). 검색+지급/차감 API도 이 패턴(예: `@Get('membership/search')`, `@Post('membership/:id/credit')`)으로 만들면 스타일이 일치합니다.

## 5. UI 스타일
- **CSS 프레임워크 없음(Tailwind 아님)**. 순수 CSS 단일 파일 `apps/cwc-web-admin/src/styles.css`(1406줄) + CSS 변수(`--primary:#3b82f6` 등) 사용. 클래스 기반.
- 공통 클래스: `.section`/`.summary`, `.section-head`, `.table` + `.table-scroll`, `.btn` `.btn-ghost` `.btn-danger` `.btn-outline`, `.card`/`.cards`, `.perm-chip`/`.perm-group`, `.filter*`, `.search-bar`/`.search-input`, `.muted` `.error` `.empty` `.small`. 신규 페이지도 이 클래스들을 재사용하면 됩니다.
- 공용 컴포넌트: `src/components/`에 `Layout`, `AuthGate`, `ChartWithTableToggle`, `SummaryTable`, `UploadSlot` 등. 범용 Table/Button 컴포넌트는 없고 각 페이지가 `<table className="table">`를 직접 씀.
- 데이터 패칭은 **@tanstack/react-query**(`useQuery`/`useMutation`/`invalidateQueries`) 관례. API 호출은 전부 `src/api.ts`의 `api` 객체에 메서드로 정의(`get/post/patch/del` 헬퍼, Bearer 토큰 자동 첨부). **신규 엔드포인트도 `api.ts`에 메서드 추가**해야 합니다.
- **스타일 참고용 대표 페이지**: `src/pages/UserManagementPage.tsx`(관리자 테이블+체크박스+뮤테이션, 가장 유사) 및 `src/pages/ItemSearchPage.tsx`(검색바+필터+정렬 테이블). "멤버십 검색" 화면은 `ItemSearchPage`의 검색/필터 구조를, "크레딧 지급/차감"은 `UserManagementPage`의 mutation+테이블 패턴을 조합하면 됩니다.

## 6. 인증/권한
- **Google OAuth(GIS) 로그인** → `cwccommerce.com` 도메인 계정만 허용(`auth/access.ts` `ALLOWED_DOMAIN`). Google ID 토큰을 API가 직접 검증(`auth.service.ts`, JWKS)하고 자체 **HS256 앱 세션 JWT**(7일) 발급.
- 권한 모델: `User.isAdmin`(전체 접근+사용자 관리), `User.menus`(허용 메뉴 배열), `User.gateways`(허용 사업자). 관리자는 모든 메뉴/사업자 통과.
- 서버: 전역 `AuthGuard`가 Bearer 토큰 검증 + `INGEST_API_TOKEN`(크롤러 머신용) 우회 허용. 관리자 전용은 `assertAdmin(req)`.
- 프론트: `AuthGate`가 토큰 확인 후 `AuthProvider`로 `useAuth()` 제공. 라우트 가드 `RequireAdmin`/`RequireMenu`(`App.tsx`).
- **크레딧 지급/차감은 민감 작업이므로 컨트롤러에서 `assertAdmin(req)` 적용 권장**(기존 users 도메인과 동일).

## 7. Git 상태
- 브랜치: `main`. 리모트: `origin git@github.com:cwc-commerce/cwc-system.git` (fetch/push 존재).
- 최근 커밋(`git log --oneline`):
  - `1263ec6 chore(release): cwc-web-admin v0.1.6`
  - `00deded chore(release): cwc-web-admin v0.1.5`
  - `106ae41 chore(release): cwc-api v0.1.5`
  - `1b37e1e chore(release): cwc-web-admin v0.1.4`
  - `56c6fc0 feat: 재고 추이 스냅샷·매출 KPI 분석 및 대시보드 차트/표 UI 개선`
- **커밋 메시지 스타일**: Conventional Commits. `feat:`/`chore(release):` 프리픽스, **본문은 한국어**. 릴리스 커밋은 앱별 버전 태깅(`scripts/git-release-commit.sh`, `bump-version.sh` 존재). 작업 트리는 clean.

## 8. README 요약
루트 `README.md`: "ERP 데이터를 크롤링해 사업자(gateway)별로 분석·관리하는 pnpm 모노레포". 데이터 흐름 = [ERP 웹] → (Playwright) cwc-crawler-erp → (REST POST /ingest/*) cwc-api → (Prisma/MySQL 저장 + 분석) → (REST GET /analytics/*) cwc-web-admin. 사전 준비: Node>=22, pnpm10, Python3.12+, Docker(MySQL8). 시작: `pnpm install` → `pnpm db:up` → `prisma:migrate` → `pnpm api:dev`(문서상 :3000, 실제 기본 3341) / `pnpm admin:dev`(문서상 :5173, 실제 vite 설정 8341). 각 앱 상세는 하위 README 참조.

---

## 신규 기능 추가 시 건드릴 핵심 파일 정리
- DB: `apps/cwc-api/prisma/schema.prisma` (Membership/CreditLedger 등 모델 추가) → `pnpm prisma:migrate`
- API: `apps/cwc-api/src/membership/` 신규 모듈(`.module/.controller/.service.ts`) + `apps/cwc-api/src/app.module.ts`에 모듈 등록
- 프론트 API 클라이언트: `apps/cwc-web-admin/src/api.ts`에 메서드 추가
- 프론트 페이지: `apps/cwc-web-admin/src/pages/MembershipPage.tsx` 신규
- 라우트/네비: `apps/cwc-web-admin/src/App.tsx`, `apps/cwc-web-admin/src/components/Layout.tsx`
- (메뉴 권한제로 갈 경우) `apps/cwc-api/src/auth/access.ts`의 `MENUS`, `apps/cwc-web-admin/src/auth.tsx`의 `MENU_LABELS`
- 스타일: 기존 `apps/cwc-web-admin/src/styles.css` 클래스 재사용

참고: 프로덕션 env 파일(`apps/cwc-api/env/.env.production`)에 **실제 DB 비밀번호·JWT 시크릿·Google Client ID가 평문 커밋**되어 있습니다. 신규 작업과 직접 관련은 없지만 인지해 두시기 바랍니다.

## assistant — 2026-07-15T07:03:49.425Z

cwc-system 구조 파악 완료 — NestJS(cwc-api) + React/Vite(cwc-web-admin), Prisma/MySQL 구조입니다. 이제 elevino-system 분석 결과를 기다렸다가, elevino DB에 직접 연결하는 멤버십 크레딧 기능을 설계하겠습니다.

## assistant — 2026-07-15T07:03:49.426Z

Let me quickly read the two credit form schemas to document the admin form fields.

## assistant — 2026-07-15T07:03:49.426Z

분석을 완료했습니다. `elevino-system` 저장소의 "멤버십 검색 → 크레딧 지급/차감" 기능을 완전히 파악했습니다. cwc-system 이식에 필요한 모든 경로·스키마·핵심 로직을 아래에 정리합니다.

---

# Elevino 크레딧 지급/차감 기능 분석 보고서

## 0. 핵심 요약 (먼저 읽으세요)

- **크레딧 시스템은 2종류**입니다.
  1. **`credit` 테이블 (사용자 단위, `user_id` 기준)** — 실제 관리자 "지급/차감" UI가 사용하는 KRC 크레딧. **이식 대상의 핵심.**
  2. **`branch_credit` 테이블 (멤버십 단위, `member_id` 기준)** — "멤버십 포인트". 관리자 컨트롤러는 단순 CRUD만 있고 지급/차감 비즈니스 로직은 없음.
- **잔액은 원장(ledger) 방식**입니다. 별도 잔액 컬럼이 없고, `credit` 테이블의 마지막 행(`id DESC`)의 `credit_after` 값이 현재 잔액입니다.
- **네이밍 주의(중요)**: 엔드포인트 이름과 실제 동작이 반대입니다.
  - `POST /admin/credits/grant` (메서드명 `adminCreditControllerGrant`) → 실제로는 **차감(deduction)**. 양수 amount를 받아 `amount * -1`로 적용.
  - `POST /admin/credits/system-grant` → 실제 **즉시 지급(양수)**.
  - `POST /admin/credits/recharge/request` → 충전 **요청**(영수증+티켓 생성, 승인 필요).
- **멤버십 검색 UI는 실제로는 "사용자(user) 검색"**입니다. `member` 테이블이 아니라 `user` 테이블을 `keyword`(이름/닉네임/전화번호 LIKE)로 검색합니다. member는 `user_id`/`user.member_id`로 user와 연결됩니다.

---

## 1. 모노레포 구조

pnpm 워크스페이스(`pnpm@9.14.2`), 워크스페이스 멤버는 `apps/*`. 루트: `/Users/progdesigner/Works/cwc/elevino-system`

| 앱 | 패키지명 | 프레임워크 | 포트 |
|----|---------|-----------|------|
| `apps/api` | elevino-api | **NestJS 10.3.x** (TypeScript, TypeORM 0.3.20, mysql2) | `SERVER_PORT` env (고정 없음) |
| `apps/admin` | elevino-admin | **Next.js 14.2.35** (App Router, React 18, MUI 5) | 8102 (`pnpm dev -p 8102`) |
| `apps/web` | elevino-web | Vue 3 + Vite | 8101 |
| `apps/www` | elevino-www | Vue 3 + Vite | 8100 |
| `apps/link` | elevino-link | Next.js 15 | Next 기본 |
| `apps/app` | (Flutter) | Dart | - |

- **docker-compose 파일 없음** (검색 결과 존재하지 않음). DB/Redis는 외부 또는 로컬 직접 설치 전제.
- 크레딧/멤버십 기능은 **`apps/api`(백엔드) + `apps/admin`(관리자 UI)** 두 앱에만 존재.

---

## 2. DB / ORM / 연결 설정

- **DB: MySQL** (mysql2 드라이버), **ORM: TypeORM 0.3.20**. 마이그레이션은 거의 안 쓰고 엔티티 기반 + 시더.
- **연결 정의 파일**: `apps/api/src/modules/index.ts` (라인 94~106) — `TypeOrmModule.forRootAsync({ name: 'default', ... })`
- **연결 설정 구현**: `apps/api/src/core/config/database.config.service.ts` (`getMysqlConfig`, 라인 83~) — master/slave replication 지원, 엔티티 패턴 `**/**.entity{.ts,.js}` 자동 로드.
- **엔티티(=스키마) 위치**: 각 모듈의 `entities/*.entity.ts` 파일이 곧 스키마 정의. 별도 통합 스키마 파일(prisma 등) 없음. TypeORM 데코레이터 방식.
- **`.env` 키 이름** (Joi 검증, `apps/api/src/modules/index.ts` 라인 71~82):

```
DB_MYSQL_HOST, DB_MYSQL_PORT(=3306), DB_MYSQL_DATABASE(=elevino),
DB_MYSQL_USERNAME, DB_MYSQL_PASSWORD, DB_MYSQL_SLAVE,
DB_MYSQL_CHARSET(=utf8mb4), DB_MYSQL_TIMEZONE(=+09:00),
DB_MYSQL_SYNCHRONIZE(=false), DB_MYSQL_LOGGING, DB_MYSQL_DROP, DB_MYSQL_PREFIX
```
- env 파일: `apps/api/env/.env.local | .env.development | .env.staging | .env.production` → `pnpm env:local`이 `.env`로 복사.
- **커스텀 `@Column` 데코레이터** 사용: `apps/api/src/core/decorators/column` (표준 TypeORM `Column`을 래핑, `description`/`length` 등 메타 추가). 이식 시 이 데코레이터도 함께 가져오거나 표준 `@Column`으로 치환 필요.

---

## 3. 멤버십(회원) 테이블/모델 스키마

멤버십 모듈에는 3개 엔티티가 있습니다: `member`(핵심), `member_contract`, `member_pass`. 크레딧 이식의 핵심은 **`member`** 입니다.

**파일**: `apps/api/src/modules/membership/entities/member.entity.ts` — 테이블명 `member` 전문:

```ts
@Entity({ name: 'member' })
export class Member {
  @PrimaryGeneratedColumn() id!: number;
  @Column({ name: 'app_id', type: 'int', length: 11, nullable: false })        appId!: number;
  @Column({ name: 'user_id', type: 'int', length: 11, nullable: true, default: 0 })   userId?: number;      // 주 사용자 ID
  @Column({ name: 'branch_id', type: 'int', length: 11, nullable: true, default: 0 }) branchId?: number;    // 이용 지점 ID
  @Column({ name: 'staff_id', type: 'int', length: 11, nullable: true, default: 0 })  staffId?: number;     // 담당 Staff ID
  @Column({ name: 'type', type: 'varchar', length: 20, enum: MemberType })     type: string;               // 구분
  @Column({ name: 'code', type: 'varchar', length: 20, unique: true })         code: string;               // 멤버십 코드
  @Column({ name: 'level', type: 'int', length: 5, default: 0 })               level: number;              // 멤버십 Level
  @Column({ name: 'department', type: 'varchar', length: 50, nullable: true }) department?: string;
  @Column({ name: 'position', type: 'varchar', length: 50, nullable: true })   position?: string;
  @Column({ name: 'email', type: 'varchar', length: 50, nullable: true })      email?: string;             // 연락 이메일
  @Column({ name: 'phone', type: 'varchar', length: 50, nullable: true })      phone?: string;             // 연락 전화번호
  @Column({ name: 'actual_user_name', type: 'varchar', length: 50 })           actualUserName: string;     // 실사용자 명
  @Column({ name: 'joined_date', type: 'varchar', length: 10, nullable: true })joinedDate?: string;
  @Column({ name: 'joined_at', type: 'datetime', nullable: true })             joinedAt?: Date | string;
  @Column({ name: 'status', type: 'varchar', length: 20, enum: MemberStatus }) status: string;
  @Column({ name: 'memo', type: 'text', nullable: true })                      memo?: string;
  @Column({ name: 'extras', type: 'text', json: true, nullable: true })        extras: any;
  @Column({ name: 'resources', type: 'text', json: true, nullable: true })     resources: any;
  @CreateDateColumn({ name: 'created_at' }) createdAt!: Date | string;
  @UpdateDateColumn({ name: 'updated_at' }) updatedAt!: Date | string;
  @DeleteDateColumn({ name: 'deleted_at' }) deletedAt?: Date | string;
}
```

**Enum** (`apps/api/src/modules/membership/interfaces/member.types.ts`):
- `MemberType`: `GENERAL`, `METRE_CRU`, `PREMIER_CRU`, `EXCELLENCE_CRU`, `GRAND_CRU`
- `MemberStatus`: `READY`, `REQUESTED`, `ACTIVATED`, `EXPIRED`

**User 엔티티 연결 필드** (`apps/api/src/modules/user/entities/user.entity.ts`): `member_id`(int), `member_code`(varchar) — user가 member를 역참조. user는 `user_name`, `display_name`, `phone`, `email`, `app_id`, `user_type`, `status` 보유.

---

## 4. 크레딧 지급/차감 (핵심)

### 4-1. 원장 테이블 스키마

**파일**: `apps/api/src/modules/credit/entities/credit.entity.ts` — 테이블명 `credit` 전문:

```ts
@Entity({ name: 'credit' })
export class Credit {
  @PrimaryGeneratedColumn() id!: number;
  @Column({ name: 'app_id', type: 'int', length: 11, nullable: false })     appId!: number;
  @Column({ name: 'user_id', type: 'int', length: 11, nullable: false })    userId!: number;
  @Column({ name: 'receipt_id', type: 'int', length: 11, nullable: false }) receiptId!: number;   // 연결 결제영수증
  @Column({ name: 'credit_reason', type: 'varchar', length: 255, nullable: false })  creditReason!: string;
  @Column({ name: 'credit_applied', type: 'bigint', length: 14, nullable: false })   creditApplied!: number;   // +30, -20 등 증감분
  @Column({ name: 'credit_before', type: 'bigint', length: 14, nullable: false })    creditBefore!: number;    // 이전 잔액
  @Column({ name: 'credit_after', type: 'bigint', length: 14, nullable: false })     creditAfter!: number;     // 이후 잔액 (= 현재 잔액)
  @Column({ name: 'credit_cumulative', type: 'bigint', length: 14, nullable: false })creditCumulative!: number;// 누적 지급 총액
  @CreateDateColumn({ name: 'created_at' }) createdAt!: Date;
  @UpdateDateColumn({ name: 'updated_at' }) updatedAt!: Date;
  @DeleteDateColumn({ name: 'deleted_at' }) deletedAt?: Date;
}
```

> 참고: `credit_point`(테이블 `credit_point`, `member_id`/`staff_id` 포함, 적립/사용/만료 상태 관리)와 `branch_credit`(테이블 `branch_credit`, `member_id` 기준) 엔티티도 있으나, 관리자 지급/차감 UI가 실제 쓰는 건 위 `credit` 테이블입니다.

### 4-2. 잔액 계산 + 원장 기록 핵심 로직

**파일**: `apps/api/src/modules/credit/services/credit.service.ts`

잔액 조회 = 마지막 행의 `creditAfter`:

```ts
async getMyCredit(userInfo: { appId: number; userId: number }, manager?): Promise<CurrentCreditRO> {
  const res = await this.findOne({
    where: { appId: userInfo.appId, userId: userInfo.userId },
    order: { id: 'DESC' },
  }, manager);
  return plainToInstance(CurrentCreditRO, {
    creditCumulative: res?.creditCumulative ?? 0,
    credit: res?.creditAfter ?? 0,
  });
}
```

증감 기록(지급/차감 공통, 새 원장 행 insert):

```ts
async addCredit(dto: CreditUseDTO, manager?: EntityManager) {
  const { appId, userId, receiptId, amount, reason } = dto;
  if (amount === null) throw new CommonError(ERROR_CODE.INVALID_DATA, { message: '결제 금액이 없어요.' });

  const currentCredit = await this.getMyCredit({ appId, userId }, manager);

  const credit = this.createEntity({
    appId, userId, receiptId,
    creditReason: reason,
    creditBefore: currentCredit.credit,
    creditAfter: currentCredit.credit + amount,                    // amount가 음수면 차감
    creditApplied: amount,
    creditCumulative: amount > 0
      ? currentCredit.creditCumulative + amount
      : currentCredit.creditCumulative,                            // 누적은 지급(양수)만 증가
  });
  return await this.create(credit, manager);   // 새 행 INSERT
}
```

> ⚠️ **이식 시 주의**: `addCredit`은 "마지막 잔액 읽기 → 새 행 쓰기"를 **DB 트랜잭션/락 없이** 수행합니다(`manager` 인자는 옵셔널이고 호출부에서 전달 안 함). 동시 요청 시 race condition 가능. cwc로 옮길 때 `manager`(EntityManager 트랜잭션)로 감싸거나 SELECT ... FOR UPDATE를 추가하는 것을 권장.

### 4-3. 관리자 API 라우트 (컨트롤러)

**파일**: `apps/api/src/modules/credit/controllers/admin.credit.controller.ts` — `@Controller('admin/credits')`, 모든 라우트 `guards:[StaffGuard], roles:['staff']`. 실제 경로는 버전 프리픽스가 붙어 `/v1/admin/credits/...` 형태(프론트 호출부 확인).

| 메서드/경로 | 동작 | 요청 body | 응답 |
|---|---|---|---|
| `POST /admin/credits/grant` | **차감** (양수 amount → `*-1`) | `{ userId, amount, reason }` | `{ credit, receiptId, afterCredit }` |
| `POST /admin/credits/system-grant` | **즉시 지급** (양수) | `{ userId, amount, reason }` | `{ credit, receiptId, afterCredit, message }` |
| `POST /admin/credits/recharge/request` | 충전요청(승인필요, 무통장/바우처) | `{ userId, amount, paymentMethod, depositorName?, voucherCode?, voucherFaceAmount? }` | `{ receipt, ticket, message }` |
| `POST /admin/credits/balances` | userId 목록 잔액 일괄조회 | `{ userIds: number[] }` | `{ balances:[{userId,credit,creditCumulative}] }` |
| `GET /admin/credits/` | 크레딧 원장 리스트(+user 조인) | query(page,limit,...) | Pagination |
| `GET /admin/credits/:id` | 상세 | - | Credit |
| `POST/PUT/DELETE /admin/credits/:id` | 생성/수정/삭제 | - | - |

**차감(`grant`) 핵심 로직** (검증 → 영수증 생성 → addCredit):

```ts
async grant(@StaffInfo() staffInfo, @Body() dto: AdminCreditGrantDTO) {
  const appId = staffInfo.appId;
  if (!appId) throw new CommonError(ERROR_CODE.FAILURE_DATA, { message: '앱 정보를 확인할 수 없습니다.' });
  if (dto.amount < 1000) throw new CommonError(ERROR_CODE.FAILURE_DATA, { message: '차감 금액은 1000 원 이상이어야 합니다.' });

  const user = await this.userService.findOneById(dto.userId);
  if (!user) throw new CommonError(...);

  const currentCredit = await this.creditService.getMyCredit({ appId: user.appId, userId: user.id });
  if (currentCredit.credit < dto.amount)
    throw new CommonError(ERROR_CODE.FAILURE_DATA, { message: `크레딧이 부족합니다. 크레딧 잔액: ${currentCredit.credit.toLocaleString()} KRC` });

  const grantAmount = dto.amount * -1;                     // ← 차감이므로 음수화
  // ... paymentReceiptService.createReceipt(...) 로 영수증 기록 (orderType: ADMIN_CREDIT_GRANT, gateway: CREDIT) ...
  const creditRecord = await this.creditService.addCredit({
    appId: user.appId, userId: user.id, receiptId: receipt.id, amount: grantAmount, reason: dto.reason,
  });
  return { credit: creditRecord, receiptId: receipt.id, afterCredit: creditRecord.creditAfter };
}
```

**지급(`system-grant`)**은 동일 구조에 `grantAmount = dto.amount`(양수), 잔액 부족 검증 없음, `paymentGateway: NONE`.

> **의존성 주의**: grant/system-grant는 `PaymentReceiptService.createReceipt(...)`로 `payment_receipt` 행을 함께 생성합니다(부기 목적, `credit.receiptId`에 연결). recharge 플로우는 추가로 `TicketService`(`ticket` 테이블), `SlackService`, `NotificationService`에 의존. **최소 이식**이라면 `credit` 테이블 + `addCredit`만으로 충분하고, `receiptId`는 0 등으로 대체 가능. **완전 이식**이라면 payment/ticket 모듈까지 필요.

**DTO 파일**: `apps/api/src/modules/credit/dto/admin.credit.dto.ts`
- `AdminCreditGrantDTO { userId:number, amount:number, reason:string }`
- `AdminCreditSystemGrantDTO { userId, amount, reason }`
- `AdminCreditRechargeDTO { userId, amount, paymentMethod, depositorName?, voucherCode?, voucherFaceAmount?, reason? }`
- `AdminCreditBalancesDTO { userIds:number[] }`
- (DTO는 커스텀 `@Schema`/`@Property` 데코레이터 사용 — `apps/api/src/core/decorators/schema`)

**응답 RO**: `apps/api/src/modules/credit/responses/credit.response.ts` (`CurrentCreditRO { credit, creditCumulative }`, `CreditRO`, `CreditListRO`).

### 4-4. 관리자 UI (apps/admin)

- **탭 페이지**: `apps/admin/src/app/credits/manage/page.tsx` → `apps/admin/src/components/organisms/credits/credits-manage.tsx`
  - 탭 0 "지급" → `credits-recharge-admin.tsx` (무통장/바우처/시스템지급 선택)
  - 탭 1 "차감" → `credits-grant.tsx`
- **차감 컴포넌트** `.../credits/credits-grant.tsx`: `AdminApi.adminCreditControllerGrant({ userId, amount, reason })` 호출. 폼 스키마 `apps/admin/src/data/schemas/credit-grant-schema.ts` (필드: 대상 사용자[커스텀 검색], 차감 금액 KRW, 차감 사유[퀵필: "엘레망 광화문점에서 사용" 등]).
- **지급 컴포넌트** `.../credits/credits-recharge-admin.tsx`: `paymentMethod`가 `SYSTEM_GRANT`면 `POST /v1/admin/credits/system-grant` 즉시 지급, 아니면 `POST /v1/admin/credits/recharge/request` 충전요청 후 승인 페이지 이동. 폼 스키마 `credit-recharge-admin-schema.ts` (결제방법 SELECT: 무통장/바우처/시스템지급, 조건부 필드 `visibleWhen`).
- 기타 크레딧 페이지: `app/credits/users`(사용자별 잔액 목록), `app/credits/`(원장), `app/credits/recharges/[id]/approve`(충전 승인), `app/branches/credits`(멤버십 branch_credit). 컴포넌트는 `apps/admin/src/components/organisms/credits/*`, `.../credit-recharge/*`, `.../branch-credit/*`.

---

## 5. 멤버십(사용자) 검색 API/UI

### 검색 UI 흐름
`credits-grant.tsx` / `credits-recharge-admin.tsx`의 `userId` 필드 → **`UserSelectorField`** (`apps/admin/src/components/molecules/custom/user-selector-field.tsx`) → **`UserSearchDialog`** (`.../user-search-dialog.tsx`).

- 다이얼로그 placeholder: **"이름 또는 전화번호로 검색"**.
- 검색 호출: `AdminApi.adminUserControllerList({ page, limit, sortBy:'id', sortOrder:'DESC', keyword })` → `GET /admin/users?keyword=...`
- 검색 결과 각 사용자의 크레딧 잔액을 `POST /admin/credits/balances`로 일괄 조회해 테이블에 함께 표시(ID/이름/닉네임/전화번호/현재 크레딧/선택).

### 서버 검색 로직
**컨트롤러**: `apps/api/src/modules/user/controllers/admin.user.controller.ts` (라인 39~50) — `keyword`가 있으면 `userService.searchUsers(query)` 사용.

**서비스**: `apps/api/src/modules/user/services/user.service.ts` (`searchUsers`, 라인 409~441):

```ts
if (query.keyword) {
  queryBuilder.where(new Brackets((qb) => {
    qb.where('user.userName LIKE :keyword',   { keyword: `%${query.keyword}%` })
      .orWhere('user.displayName LIKE :keyword', { keyword: `%${query.keyword}%` })
      .orWhere('user.phone LIKE :keyword',       { keyword: `%${query.keyword}%` });
  }));
}
```

- **검색 조건: 이름(user_name) / 닉네임(display_name) / 전화번호(phone) LIKE**. **이메일은 검색 대상 아님.**
- `keyword` DTO: `apps/api/src/modules/user/dto/admin.user.dto.ts` (`AdminUserListDTO`, `PaginationDTO` 상속).
- 멤버(`member`) 자체 검색 API도 있으나(`GET /admin/members/` → `AdminMemberController.list` → `memberService.findAll`, `AdminMemberListDTO extends PaginationDTO`) **키워드 검색은 없고** 페이지네이션만. 멤버 관련 파일: `apps/api/src/modules/membership/controllers/admin.member.controller.ts`, `.../services/member.service.ts`.

### 공통 페이지네이션/검색 인프라
- `PaginationDTO`: `apps/api/src/core/dto/pagination.dto.ts` (`page`, `limit`(=take), `sortBy`, `sortOrder`; `skip` getter).
- `SimpleRepositoryService.findAll`: `apps/api/src/core/services/simple.repository.service.ts` (라인 145~) — QueryBuilder로 where/order/limit/offset 처리, `Pagination<T>` 반환. 대부분의 서비스가 이 베이스를 상속.

---

## 6. 인증 / 권한 처리 방식

**관리자 API 보호 = JWT(Bearer) + StaffGuard 2단**.

- **`@Route` 데코레이터** (`apps/api/src/core/decorators/route/index.ts`)로 라우트+가드를 선언. `roles:['staff']`를 주면 내부적으로 `options.auth='JWT'`가 되어 **`JwtAuthGuard`가 guards 맨 앞에 자동 추가**되고, `SetMetadata('roles', ...)`로 역할 메타 설정. `roles:['master']`면 `MasterAuthGuard` 추가.
- **JWT 검증**: `apps/api/src/core/guards/jwt.auth.guard.ts` — Passport 커스텀 Strategy. `Authorization: Bearer <token>` (또는 `x-access-token`, `admin-key` 헤더)에서 토큰 추출 → `jwt.verify(token, JWT_SECRET, {algorithm: JWT_ALGORITHM ?? 'HS256'})` → `req.tokenPayload = payload` 설정.
- **StaffGuard**: `apps/api/src/modules/staff/guards/staff.guard.ts` — `req.tokenPayload.staffId` 확인 → `staffService.getStaff()` 조회 → `staffService.hasPermission({ staff, roles })`로 권한 체크. 컨트롤러에서 `@StaffInfo()` 파라미터 데코레이터로 staff 페이로드(`staffId, appId, roleType` 등) 사용 (지급/차감 시 `appId` 검증에 사용).
- **관리자 프론트 토큰 주입**: `apps/admin/src/utils/axios/index.ts` — axios 요청 인터셉터가 `sessionStorage`의 access token을 읽어 `config.headers['Authorization'] = 'Bearer ' + accessToken` 설정. (CLAUDE.md는 Auth0로 표기하나, 실제 코드는 sessionStorage 기반 자체 JWT staff 토큰.) HTTP 클라이언트: `apps/admin/src/data/api/http-client.ts`, 생성 API 클라이언트: `apps/admin/src/data/api/AdminApi.ts`(swagger-typescript-api 자동 생성).

---

## 7. cwc-system 이식 체크리스트 (elevino DB 그대로 사용 전제)

**최소 이식 (사용자 크레딧 지급/차감만)** 필요 요소:
1. **엔티티**: `credit.entity.ts` (+ 필요시 `member.entity.ts`, `user.entity.ts`). 커스텀 `@Column` 데코레이터(`core/decorators/column`) 동반 or 표준 `@Column` 치환.
2. **서비스**: `credit.service.ts`의 `getMyCredit` + `addCredit` (원장 방식 잔액 로직). **트랜잭션/락 보강 권장**.
3. **컨트롤러**: `admin.credit.controller.ts`의 `grant`(차감)/`system-grant`(지급)/`balances`. `PaymentReceiptService` 의존을 제거하거나 receiptId=0으로 단순화 가능.
4. **검색**: `user.service.ts`의 `searchUsers`(keyword LIKE) + `admin.user.controller.ts` list.
5. **인증**: `JwtAuthGuard` + `StaffGuard` + `@Route`/`@StaffInfo` 데코레이터, `staff` 모듈.
6. **관리자 UI**: `credits-manage/grant/recharge-admin` 컴포넌트 + `user-search-dialog`/`user-selector-field` + schemas.
7. **DB 연결**: `DB_MYSQL_*` env + `database.config.service.ts`.

**완전 이식** 시 추가로 `payment`(payment_receipt), `ticket`, `notify`, `slack`, `app` 모듈 의존까지 포함해야 recharge/영수증/알림 플로우가 동작합니다.

**주의점 3가지**: (1) `grant`=차감/`system-grant`=지급 네이밍 역전, (2) `addCredit` 동시성 미보호, (3) 크레딧은 `user_id` 기준이며 멤버십 검색 UI도 실제로는 user 검색(이메일 미검색) — member는 `user.member_id`로 간접 연결.

## assistant — 2026-07-15T07:03:49.427Z

분석 완료. 이제 구현에 들어갑니다. 설계 방향:

- **DB**: cwc-api에 elevino DB 전용 Prisma 클라이언트를 하나 더 추가해 기존 elevino의 `user`·`member`·`credit`(원장) 테이블을 그대로 사용 (마이그레이션 없음, 읽기 + 원장 INSERT만)
- **API**: cwc-api에 `membership` NestJS 모듈 — 멤버십(사용자) 검색 + 잔액 조회 + 지급/차감 (elevino 원본의 동시성 문제를 트랜잭션+락으로 보강, `grant`=차감이던 네이밍 역전도 정리)
- **UI**: cwc-web-admin에 멤버십 크레딧 페이지 (검색 → 잔액 표시 → 지급/차감 + 내역)

먼저 수정할 cwc 파일들과 elevino DB 접속 정보를 확인합니다.

## assistant — 2026-07-15T07:03:49.428Z

파일 구조 파악 중입니다. 실패한 명령을 다시 실행합니다.

## assistant — 2026-07-15T07:03:49.429Z

구현을 시작하려는 시점에 하네스 권한 시스템이 막혀서 진행이 중단됐습니다. 현재 상태를 정리합니다.

## 현재 상황: 쓰기 작업이 전부 차단됨

읽기 전용 명령(`ls`, `git log`, 파일 읽기)은 정상 동작하지만, **파일 생성(Write)·수정·`/tmp` 쓰기를 포함한 모든 승인 필요 작업이 `Tool permission request failed: Error: Stream closed` 오류로 실패**합니다. 여러 번 재시도했지만 동일합니다. 권한 승인 채널이 끊긴 상태로 보여서(클라이언트 연결 끊김 또는 하네스 오류), 사용자 쪽에서 세션을 다시 열거나 승인 모드를 확인해 주셔야 진행할 수 있습니다.

## 분석·설계는 완료된 상태

두 저장소 분석을 마쳤고 구현 설계까지 확정했습니다. 권한이 복구되면 바로 코드 작성에 들어갈 수 있습니다.

**elevino 쪽 파악 결과** — 크레딧은 `credit` 테이블의 원장(ledger) 방식입니다. 잔액 컬럼 없이 마지막 행의 `credit_after`가 현재 잔액이고, 지급/차감은 새 행 INSERT입니다(`credit_before/after/applied/cumulative` 기록). 검색은 `user` 테이블을 이름·닉네임·전화번호 LIKE로 찾고, 멤버십(`member`)은 `user.member_id`로 연결됩니다. 참고로 elevino 원본은 `grant` 엔드포인트가 실제로는 차감이라는 네이밍 역전과, 잔액 읽기→쓰기 사이 트랜잭션 미보호(race condition) 문제가 있어서 이식하면서 바로잡을 예정입니다.

**cwc-system 구현 계획**:
1. **DB**: `apps/cwc-api`에 elevino DB 전용 Prisma 스키마(`prisma/elevino.prisma`)를 추가하고 `ELEVINO_DATABASE_URL` env로 연결 — 기존 `user`·`member`·`credit` 테이블을 그대로 사용하고 마이그레이션은 하지 않음
2. **API**: `src/membership/` NestJS 모듈 — 멤버십 검색(`GET /api/membership/search`), 크레딧 내역 조회, 지급/차감(`POST .../credits/grant`, `.../credits/deduct`, 차감 시 잔액 검증 + 트랜잭션·행 잠금 보강). 크레딧 조작은 민감 작업이라 기존 `assertAdmin` 패턴으로 **관리자 전용** 처리
3. **UI**: `cwc-web-admin`에 멤버십 크레딧 페이지 — 검색바 → 회원 목록(멤버십 코드·등급·현재 잔액) → 지급/차감 폼 + 최근 원장 내역. 기존 `styles.css` 클래스와 react-query 패턴 재사용, `App.tsx`/`Layout.tsx`에 관리자 메뉴 등록
4. 빌드 검증 후 `feat:` 컨벤션(한국어 본문)으로 커밋 & 푸시

세션을 새로 열거나 이 대화에 아무 메시지나 보내 권한 채널이 살아나면, 위 계획대로 즉시 구현을 이어가겠습니다.

