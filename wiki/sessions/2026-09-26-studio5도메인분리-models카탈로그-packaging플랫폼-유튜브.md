---
tags: [session, lampas-studio, lampas-web-music, lampas-web-package, multi-domain, cloudfront, route53, models-catalog, atlas-cloud, youtube, admin-guard, concurrent-sessions, incident]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-26 — Studio 5도메인 분리 + models.lampas.io 일일 카탈로그 + Package 플랫폼명·유튜브

`Tool: claude` 터미널 세션, 작업 폴더 `lampas-system`(`[[lampas-studio]]`), 2026-09-26T06:38:11Z 시작.
한 세션 안에서 사실상 독립된 6개 요청이 연속 처리됐다 — 음악 플레이어 UI 수정 → 스튜디오 4도메인
분리 → 5번째 도메인(Transforms) 추가 → 5개 사이트 독립 제품화 → `models.lampas.io` 일일 자동
카탈로그(도중 ~5분 운영 장애) → Package 플랫폼명·미디어타입별 포맷·유튜브 추가 → Transforms 배포
라우팅 버그 수정 → 전체 커밋 푸시. 소스 → [[2026-09-26-lampas-system-수정사항]](raw).

## 요청 1 — `music.lampas.io` 모바일 오디오 플레이어 뭉침 수정

iOS Safari가 브라우저 네이티브 `<audio controls>`를 고정 크기로 그려 카드 안에서 재생 버튼·시간
표시가 잘리고 뭉개지는 문제(사용자가 스크린샷으로 제보). 커스텀 플레이어
`apps/lampas-web-music/src/components/AudioPlayer.tsx`(재생/일시정지·드래그/터치 탐색 진행바·
경과/전체 시간·로딩 스피너·재생불가 표시, 보라 액센트+플랫 버튼)로 교체하고 트랙 목록·스튜디오
결과 패널 둘 다 적용. 시간 표기·진행 비율·탐색 위치 계산은 순수 함수 `lib/player.ts`로 분리해
vitest 8개 추가. 카드 제목 옆 "게시됨 · music" 배지가 좁은 화면에서 제목을 밀어내지 않도록 줄바꿈
처리. 배포·커밋(`a946335a`, `apps/lampas-agent`의 무관한 env·package.json 변경은 제외). →
[[lampas-web-music]] 갱신.

## 요청 2 — Studio를 Work 전용으로, Actor/Object/Place를 별도 도메인으로 분리

사용자 요청: "studio.lampas.io는 Work에 집중, actors/objects/places.lampas.io에서 각 엔티티 전문
생성, resources는 제거(admin에서 볼 수 있으니)". 구현:

- **하나의 빌드, 호스트명으로 역할 분기** — 신규 순수 로직 `src/lib/appDomains.js`가 도메인·경로
  소유·내비·교차 링크를 정함(vitest 13개). `lampas-web-studio`(저장소 내 실제 이름, 코드 리네이밍
  없이 빌드 산출물만 4개 도메인에 서빙) 하나로 4개 호스트를 감당.
- `studio.lampas.io` — Work(노드 스튜디오)·Gallery·Transform·References만 남김. 상단 내비의
  Actor/Object/Place는 각 도메인으로 가는 **외부 링크**. `/actors/works/:key` 같은 엔티티 Work
  딥링크는 studio에 남음.
- `actors.lampas.io`/`objects.lampas.io`/`places.lampas.io` — 각자 엔티티의 목록·생성·상세(Actor는
  휴지통 포함)+공용 Gallery·약관만 서빙. 로고 "Lampas ACTORS/OBJECTS/PLACES".
- 다른 도메인 소유 경로로 들어오면 각 템플릿의 catch-all(`CrossAppRedirectPage`)이 그 도메인으로
  리다이렉트 — studio 노드 안 "액터 만들기" 링크, ActorView의 "스튜디오 열기" 버튼도 그대로 동작.
- Place는 API 엔티티가 Space라서 내부 URL은 `/spaces/*` 유지, `/places/*`는 별칭.
- 로컬 dev는 `?variant=actors|objects|places` + sessionStorage로 전환.
- Resources 화면·라우트·코드 완전 제거(admin에 이미 있어 중복). `sdk.lampas.io` 템플릿은 resources
  제거 외 무변화.
- **인프라**: 기존 studio CloudFront 배포(`E37EJWEMEOP61X`, 와일드카드 인증서)에 세 호스트를 별칭
  추가 + Route53 CNAME 신설 — 신규 CloudFront 배포 없이 `deploy-web.sh lampas-web-studio` 한 번으로
  네 도메인 전부 배포됨. `status.lampas.io`에 Actors/Objects/Places 등록, 동시에 등록이 빠져 API
  테스트를 깨뜨리던 `fit.lampas.io`도 함께 등록.
- 배포·커밋 `1699654e`.

### 사고 — 동시 세션의 미완성 `spot` 변경이 API 배포에 섞임
다른 세션이 같은 워킹트리에서 `spot` 모듈(API·admin·web-spot)을 작업 중이었는데, 이 세션의
`git add apps/lampas-api`가 그 미완성 변경을 함께 커밋에 담았고 `deploy-api.sh`도 그 상태로 빌드돼
운영 API에 **spot 스케줄러가 의도치 않게 배포**됨. API는 정상 기동했지만 `spot_places` 테이블이
없어 3초마다 오류 로그가 쌓이는 루프 발생.

**수습**: 커밋을 `git reset --soft`로 되돌리고 이번 세션 경로만 다시 커밋, 그 세션의 파일은 원래
상태(미추적·미스테이지)로 복원. 운영 DB에는 그 세션이 준비해 둔 멱등 DDL
`prisma/manual/2026-09-26-spot-places.sql`(빈 테이블 생성)을 적용해 오류 루프를 멈춤 — 코드를
되돌리지 않고 DB 쪽에서 안전하게 수렴시킨 선택. 결과적으로 spot 모듈이 운영에 미리 떠 있는
상태가 됐고, 해당 세션이 나중에 배포하면 자연히 덮어씀. → [[selective-hunk-commit-shared-file]]에
새 변형으로 추가(아래 "위키 반영" 참고).

## 요청 3 — Transforms를 6번째... 아니 5번째 도메인으로, Studio는 Work·Gallery만

후속 요청: "transforms도 transforms.lampas.io로 분리, studio는 Work·Gallery만". `transforms.lampas.io`
신설(Transform 목록·생성·편집·실행+공용 Gallery·약관), `studio.lampas.io`는 Work·Gallery만 남기고
Actor/Object/Place/Transform 내비 링크를 모두 외부 링크로 전환. `/references/search`는 내비 없는
숨은 라우트로 studio에 잔존. 랜딩 기능카드·푸터·`/playground` 퀵링크의 Transform 항목도 transforms
직링크로 교체. `appDomains.js`에 transforms 앱 추가, 테스트 243개 전체 통과. CloudFront 별칭+Route53
CNAME 추가(기존 studio 배포에 또 하나 얹음). **API는 재배포하지 않음** — 워킹트리에 다른 세션의
spot·packaging 진행 중 변경이 있어 함께 나가면 안 되기 때문(상태 페이지 레지스트리 등록만 커밋,
API 반영은 다음 API 배포에 자동 포함). 배포·커밋 `1450ff93`.

## 요청 4 — 다섯 사이트를 독립 제품처럼

"studio actors objects places transforms 사이트는 각각의 별도 사이트처럼, 지금은 다 연결된 사이트
같아". 내비에서 형제 링크를 모두 빼고 사이트마다 고유 정체성 부여:

- 워드마크가 "Lampas ACTORS" 공통 접두어 대신 **제품명만**(Studio/Actors/Objects/Places/Transforms)
  + 사이트 색 점 하나.
- **사이트별 액센트 색** — Studio 시안·Actors 주황·Objects 보라·Places 초록·Transforms 노랑.
  구현은 tailwind `primary` 토큰을 CSS 변수로, App이 `html[data-site]`를 찍는 방식이라 기존
  컴포넌트 무수정.
- 브라우저 탭 파비콘(사이트색 사각형+머리글자 SVG)·제품 우선 타이틀("Actors — 브랜드의 얼굴이
  될 AI 액터")도 사이트별로 다름.
- Actors/Objects/Places/Transforms의 `/`는 목록으로 안 튕기고 **자기 홈 화면**(헤드라인·설명·
  "Scout 시작" 등 주 CTA·기능 카드 3개, 다른 사이트는 "촬영은 Studio에서" 힌트 한 줄뿐).
- 형제 사이트 링크는 상단·모바일 내비에서 전부 제거, 유일한 흔적은 푸터의 "Lampas 제품군" 한 줄.
- Studio 랜딩에서 Scout/Register/Find/Transform 카드 제거, Studio 소유 기능(노드 촬영·모션 비디오·
  갤러리)만 남김.
- 검증: 헤드리스 크롬으로 네 variant의 제목·액센트·내비·파비콘 확인+스크린샷, 테스트 246개,
  빌드·dalar 동기화 드리프트 검사 통과. CTA 버튼 기본 밑줄 제거 등 마무리.
- 배포·커밋 `9d6255fd`. API는 건드리지 않음.

## 요청 5 — `models.lampas.io` 카탈로그 일일 자동 최신화 (+ 운영 API ~5분 장애)

"models.lampas.io 카테고리는 하루에 한번씩 최신 업데이트". 중간에 **이전 명령이 3분간 응답 없이
중단**되는 일이 있어("이어서 작업해줘" 요청으로 파일 상태 확인 후 재개), 이후 "3분간 응답이
준비되지 않았습니다" 재발 — API 프로세스 상태·로그·엔드포인트 응답을 직접 확인하며 진행.

**동작 방식**: `lampas-api`에 `ai/model-catalog/` 모듈 신설. 스케줄러가 30분마다 스냅샷 나이를
확인해 24시간(`MODEL_CATALOG_REFRESH_HOURS`) 경과 시 Atlas 전체 모델 목록+모델별 OpenAPI 스키마를
받아 `model_catalog_snapshots` 테이블에 저장(최근 14개만 보존). 부팅 직후 스냅샷이 없거나 오래됐으면
15초 뒤 즉시 한 번 받음. 매핑 규칙은 기존 `sync:atlas-pricing` 스크립트 규칙을 TypeScript로 옮긴
순수 함수, 50개 미만이거나 직전의 70% 미만이면 Atlas 장애로 보고 거부. `GET /v1/ai/models`는 이제
매 요청 Atlas를 안 부르고 과금 카탈로그 위에 스냅샷의 최신 모델·카테고리·이름·옵션을 얹어 응답
(신규 모델은 `source: atlas`, 내려간 모델은 `available: false`, 정가 변동은 `priceChanged`,
`catalogUpdatedAt` 포함). 관리자용 `GET/POST /v1/admin/ai/model-catalog[/refresh]`로 상태 조회·즉시
갱신 가능. **과금 단가는 바꾸지 않음** — 실제 청구는 여전히 정적 카탈로그 기준, 단가 자동 반영은
별도 확장 필요.

**화면**: 상단에 "Atlas 카탈로그 최신화 … · 매일 자동 갱신 · 신규 N · 내려감 N" 표시, 내려간 모델은
기본 숨김("N개 보기" 체크). 운영 첫 스냅샷에서 실제로 **신규 14개, 내려간 모델 4개** 확인.

### 장애 보고 — AdminGuard가 쓰는 JwtModule 미등록으로 운영 API 기동 실패 (~5분)
첫 배포(0.1.153) 직후 관리자 컨트롤러의 `AdminGuard`가 필요로 하는 `JwtService`가 `AiModule`에
등록되지 않아 Nest가 기동에 실패, PM2가 재시작을 반복하며 nginx가 404를 냄. **유닛 테스트·
타입체크로는 잡히지 않는 런타임 DI 오류**. `JwtModule` 등록 후 로컬에서 실제 기동시켜 확인하고
재배포(0.1.154)해 복구. 교훈("AdminGuard를 쓰는 모듈은 JwtModule 필수, 배포 전 40초 기동 확인")은
신규 스킬 [[nestjs-admin-guard-requires-jwtmodule]]로 추출. 상태 페이지 인시던트 목록 조회는 응답이
비어 있어 자동 인시던트가 열렸는지는 확인 못함.

테스트: API 매핑·목록합성·서비스 27개, 웹 vitest 3개, 전체 API 테스트 1,298개 통과. 커밋
`eeac24a4`(기능)·`7710eaa8`(수정)·`79ec2268`/`9e936310`(버전 bump).

## 요청 6 — Package 플랫폼명·미디어타입별 포맷 + 유튜브 추가

"package 할 때 미디어 타입에 따라 다르게 선택할 수 있게, 플랫폼명으로(블로그→네이버 블로그,
인스타그램 게시물/릴스 통일), 유튜브 추가 + 유튜브 업로드 방법 알려줘". 다른 세션이 packaging
영역을 건드리던 중이라 미커밋 변경 여부부터 확인하고 진행.

- 채널 이름이 **플랫폼 기준**으로 바뀜 — 블로그→"네이버 블로그", 인스타그램 게시물/릴스 둘 다
  "인스타그램"(형식은 작은 글씨로만), **유튜브 신규 추가**.
- "게시할 플랫폼" 단계는 네이버 블로그·인스타그램·유튜브 카드 3개. 카드 안 형식 버튼은 추가한
  자산의 **미디어 타입에 따라 활성화** — 이미지가 있으면 인스타그램 게시물, 영상이 있으면
  인스타그램 릴스+유튜브 동영상, 네이버 블로그는 항상 가능. 자산을 지워 조건이 깨지면 해당 형식은
  자동으로 선택에서 빠지고 "영상 자산을 추가하면 선택할 수 있어요" 안내.
- 유튜브 변형은 제목(100자)·설명·태그 편집기 — 복사·영상 내려받기·"유튜브 스튜디오 열기" 버튼
  (복사·열기는 내보내기 기록으로 남음). AI 생성(compose)도 유튜브용 제목·설명·태그 생성, 블로그
  프롬프트는 네이버 블로그 포스트 기준으로 교체.
- 서버는 유튜브 채널에 영상 자산 없으면 400 거부, 변형의 `mediaOrder`엔 영상만 포함. DB 채널 enum
  2테이블(`channel_variants`, `publish_records`) 확장(운영·로컬 모두).
- 규칙은 API `packages/lib/channel-media.ts`·웹 `lib/channel-media.ts` 순수 함수로 테스트 붙임.
  API 1,318개, 웹 11개 통과. 배포·커밋(`5e6c1ace`, `58b831a9`), 운영 API 재시작 없이 정상.

### 유튜브 업로드 방법 — 현재는 수동, API 연동 절차 안내
현재는 패키지에서 영상 다운로드 → "유튜브 스튜디오 열기" → 제목·설명·태그 붙여넣기 수동 업로드
(세로 영상 60초 이하는 자동 Shorts). 인스타그램처럼 버튼 한 번에 올리려면 YouTube Data API v3 연동
필요 — 절차를 안내만 하고 구현은 보류(사용자 요청 시 2~3단계 구현 가능, 검수 신청은 Google 계정
소유자 직접):
1. Google Cloud 콘솔에서 YouTube Data API v3 활성화 + OAuth 클라이언트(웹), 동의 화면에
   `youtube.upload` 스코프 등록.
2. "유튜브 채널 연결" → Google OAuth → refresh token을 인스타그램 계정처럼 `ChannelAccount`
   (provider YOUTUBE)에 저장.
3. 게시 시점 access token 갱신 → `videos.insert` resumable upload로 영상 업로드, snippet(제목·설명·
   태그)·status(공개범위·`publishAt`) 설정, 기존 게시 스케줄러에 YOUTUBE 분기 추가하면 예약 게시도 가능.
4. **제약 2가지**: 기본 쿼터 하루 10,000단위(업로드 1회 1,600단위 → 하루 약 6건), Google 앱 검수
   (OAuth 앱 인증+YouTube API 준수 감사) 통과 전까지 API 업로드 영상은 비공개 잠금(검수 보통 1~2주).

→ [[lampas-web-package]] 갱신.

## 요청 7 — Transforms "배포하기"가 엉뚱한 Work로 이동하는 버그

"transforms 배포하기 하면 `/transforms` 페이지로 와야 하는데 엉뚱한 Work로 간다". **원인**:
변환 생성·수정 화면이 dalar 시절 경로 `/studio/transforms`·`/studio/transforms/edit/:key`·
`/studio/transforms/run/:key`로 이동하고 있었음. `transforms.lampas.io`는 `/studio/…`를 studio 소유
경로로 보고 studio.lampas.io로 넘겼고, studio는 `/studio/:key`를 옛 Work 딥링크로 해석해 엉뚱한
Work 캔버스를 열었음 — 요청 2에서 만든 경로 소유권 분기 규칙과 요청 3 이전 레거시 경로가 충돌한
사례.

**수정**: 이동 경로 4곳을 `/transforms`·`/transforms/edit/:key`·`/transforms/run/:key`로 교체,
경로 정규화에 `/studio/transforms*` → `/transforms*` 별칭 규칙 추가(transforms 도메인에서는 내부
이동, studio 도메인에서 들어오면 transforms 도메인으로 리다이렉트). 테스트 272개, 빌드·dalar 동기화
드리프트 검사 통과, 다섯 도메인 재배포. 커밋 `c980e03d`·`4080f0b2`.

## 요청 8 — 전체 커밋 푸시

"지금까지 작업한 코드를 커밋하고 푸시해줘". `origin/main`을 `a9280ddc`→`75a3a6ab`로 푸시, 로컬
커밋 34개 전부 반영(이 세션의 작업 전체 + 다른 세션들이 커밋해 둔 fit·edit·admin·studio 관련
커밋도 함께). **푸시하지 않은 것**(다른 세션 진행 중, 손대지 않음): `apps/lampas-api-mcp`
(OAuth·HTTP 서버: `src/oauth*.ts`·`http.ts`·테스트·예제·env/process.json)·`apps/lampas-web-spot`
(`Spot.jsx`·`styles.css`·atoms 컴포넌트).

## 위키 반영

- [[lampas-studio]]: 5도메인 분리(요청 2~4, 7) + models.lampas.io 일일 카탈로그(요청 5) 절 신설,
  "관련" 세션·스킬 링크 추가.
- [[lampas-web-music]]: AudioPlayer.tsx 커스텀 플레이어 절 추가.
- [[lampas-web-package]]: 플랫폼명 통일·미디어타입별 포맷·유튜브 채널(요청 6) 절 추가, Instagram
  채널 연결 API 기록과 나란히.
- [[selective-hunk-commit-shared-file]]: "경로 단위 커밋이 형제 세션의 진행 중 변경을 쓸어가 **배포**
  까지 가는" 새 변형 추가(요청 2 사고) — 기존 변형들은 커밋 단계에서 발견됐지만 이번엔 운영 장애로
  먼저 드러남.
- 신규 스킬 [[nestjs-admin-guard-requires-jwtmodule]] — AdminGuard 의존 모듈에 JwtModule 등록 누락 시
  타입체크·유닛테스트를 통과한 채 운영 기동만 실패하는 패턴, 배포 전 로컬 실기동 확인 절차.
- 신규 스킬 [[multi-domain-single-build-variant-split]] — 호스트명 기반 variant 분기로 한 SPA 빌드를
  여러 독립 브랜드 서브도메인으로 서빙하는 절차(CloudFront 별칭 추가·catch-all 교차 리다이렉트·
  `data-site` CSS 변수 테마).

## 관련
- 저장소/제품: [[lampas-studio]]
- 앱: [[lampas-web-music]] · [[lampas-web-package]] · [[lampas-web-spot]](사고 당사자)
- 스킬: [[selective-hunk-commit-shared-file]] · [[nestjs-admin-guard-requires-jwtmodule]] ·
  [[multi-domain-single-build-variant-split]] · [[admin-guard-precedent-reuse]] ·
  [[prod-ddl-before-deploy-with-drift-check]]
- 외부 의존: [[atlas-cloud]](models 카탈로그 소스)
