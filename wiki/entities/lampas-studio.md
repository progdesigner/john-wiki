---
tags: [entity, project, product, image-generation, nestjs, react, instagram, space, product-insights, lampas-web-fit, dalar, lampas-browser, lampas-web-music, atlas-cloud, wan, video-generation]
created: 2026-07-09
updated: 2026-09-26
---
# lampas-studio (Lampas 이미지 생성 스튜디오)

`[[progdesigner]]`의 AI 이미지 생성 제품. 인물 **레퍼런스 시트**로 신원(얼굴·헤어·의상)을 고정하고,
그 위에 다양한 연출·모델로 이미지를 생성하는 스튜디오. `[[lampas-harness]]`/`[[lampas]]`(에이전트)와는
**별개의 제품 코드베이스**다(하네스 에이전트가 이 제품을 개발·배포한다).

- 배포: 웹 `sdk.lampas.io` (S3 + CloudFront), API는 원격 서버 PM2(`lampas-api`).
- 모노레포(`~/Works/lampas` 하위): `apps/lampas-api`(NestJS, `nest build`), `apps/lampas-web-sdk`(Vite, `vite build`).
- **로컬 폴더명 `lampas-system`**(`~/Works/lampas/lampas-system`) — `[[lampas-harness]]`의 로컬 폴더명
  `lampas-harness`와 다른 형제 폴더. 2026-07-15까지 위키의 "인접 저장소" 메모로만 존재하다 이 세션에서
  본 엔티티와 동일 저장소임이 확인됨. → [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
- **앱 구성**(2026-07-15 구조 분석): pnpm 워크스페이스에 Lampas 6앱(`lampas-api`, `lampas-web-sdk`,
  `lampas-web-www`, `lampas-web-ai`, `lampas-web-admin`, `lampas-app-toss`) + Iileex 3앱(`iileex-api`,
  `iileex-web-www`, `iileex-web-admin`) 총 9개. `lampas-api`는 `src/modules/` 19개 도메인 모듈·Prisma 모델
  35개. **제품 CLAUDE.md 문서와 실제 코드 괴리 3건 확인**(미수정): ① 모듈 목록에 `admin`/`api-clients`/
  `audit-logs`/`email`/`products` 누락(실제 존재), ② `lampas-web-sdk` organisms의 `references/` 디렉터리가
  문서 구조도에 없음, ③ 루트에 `AGENTS.md`가 `CLAUDE.md`와 별도 존재. → [[2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계]]
  **2026-07-18: `lampas-app-toss` 디렉터리가 `lampas-app-photobooth`로 이름 변경**(토스 미니앱의 실제
  기능이 포토부스임을 명확화하는 리네이밍, 앱 자체는 동일) → [[2026-07-18-web-ai-등록플로우-사진분류-배포]]
- **`lampas-web-ai`가 2026-07-15부터 주요 앱**으로 승격 — 구조·기능 상세는 별도 엔티티 페이지 →
  [[lampas-web-ai]]
- **DB: PostgreSQL** (2026-07-15 확인 — 로컬 커밋이 잘못 가정했던 "MySQL collation" 로직을 rebase 중
  PostgreSQL 대소문자 무시 JS 비교 방식으로 교체하며 드러남).
- 배포 스크립트: `./scripts/deploy-api.sh`, `deploy-web.sh <project>`, `deploy-app.sh <target>` — **배포는 반드시 이 스크립트로**(제품 CLAUDE.md 명문화, 2026-07-08). → `[[deploy-sandbox-pnpm-shim]]`

## 스튜디오 구조 (관찰된 범위)

- **모드 2종** — `Text to Image`, `Reference to Image`. **둘 다 같은 훅 `useTextToImageStudio`** 사용.
- **레퍼런스 시트** — 기준 이미지에서 외형·스타일링(얼굴·헤어·의상)을 고정하는 시트를 생성. 생성 모델 선택 가능(Gemini 기본 / Atlas Cloud).
- **레퍼런스 영역**(`StudioReferenceSection` 공용 molecule) — 레퍼런스 이미지를 텍스트 생성/검색/Instagram/포스트 URL로 가져와 **분석**(`applyStudioReference`)하면 Direction·Camera·Environment가 Custom 프롬프트로 자동 채워짐. `embedded` prop으로 카드 안 삽입 지원(2026-07-08 추가).
- **Reference Shot**(`handleReferenceShot`) — 시트로 신원을 고정한 채, 분석이 채운 Direction·Camera·Environment 세 그룹을 읽어 생성. appearance·styling은 시트가 고정하므로 빈 값으로 전송(충돌 없음).
- **갤러리** — `/gallery/view/:id` 상세에서 프롬프트 편집·재생성.

## 이미지 모델 (관찰된 값)

- **생성**: [[gemini]](기본, 멀티 이미지 그리드 직접 생성) / [[atlas-cloud]] 경유 — `gpt-image-2`, `nano-banana-2`, `grok-imagine`, `wan-2.7`. Gemini/Atlas가 동일 `generateImage` 시그니처라 선택값으로 서비스만 분기.
- **분석**: [[grok]] / Gemini.
- 갤러리 편집 기본 이미지 모델: `google/nano-banana-2/edit`.
- UI 선택은 localStorage로 기억(`lampas_gallery_edit_image_model`, `lampas_studio_analyze_model`). → `[[localstorage-ui-preference-persistence]]`
- **주의 — CLAUDE.md 요약과 실제 경로가 다름** (2026-07-15 세션 확인): 제품 CLAUDE.md엔 "스튜디오 합성=Atlas
  Cloud"로 요약돼 있지만, **Object 단독 촬영은 실제로 Gemini 직접 경로**다(`objects.service.ts:794`).
  Atlas Cloud는 Actor/Actor+Object 촬영 쪽 경로. → [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]]

## 백엔드 (`lampas-api`, 관찰된 범위)

- `ActorsService` / `actors.controller.ts` — 레퍼런스 계정/포스트/프록시 엔드포인트.
  - `GET /actors/reference-account-images` — username 계정 이미지 (커서 페이지네이션, 캐러셀 children).
  - `GET /actors/reference-image-proxy` — IG CDN 핫링크 차단 우회 서버 프록시(`@Public`, 허용 호스트만, 6h 캐시).
  - `GET /actors/reference-post-images` — 포스트 URL/shortcode 이미지.
- `InstagramService` / `InstagramApiService` — Instagram 접근 래퍼. `getPublicPostMedia(shortcode)`(facebookexternalhit UA HTML 파싱), 연결 계정 토큰(Graph API) 관리, `exchangeForLongLived`.
- `InstagramService.get*` — 소스 3종: 연결 계정(Business Discovery, Graph `media.after`), 공개 프로필(`api/v1/feed/user/{id}/?max_id=…`), Google 검색 폴백.
- Instagram 통합 상세 → `[[instagram-reference-integration]]`

## 프론트엔드 (`lampas-web-sdk`, 관찰된 범위)

- 훅 `useTextToImageStudio` — 스튜디오 전 상태(레퍼런스 계정/포스트/시트/분석 모델, 계정 연결).
- `organisms/gallery/GalleryView.jsx`, `molecules/transform/libs/transformUtils.js`, `molecules/StudioReferenceSection.jsx`, `organisms/.../ReferenceToImage.jsx`, `studio/libs/utils.js`(`buildStudioRefThumbnailSrc`).

## 외부 의존 / 설정

- Instagram: 공식 Graph API는 **임의 게시물 조회 불가**(본인 미디어·Business Discovery만). oEmbed는 Meta "oEmbed Read" 앱 심사 필요. `.env`에 `META_APP_ID/SECRET`(장기 토큰 교환용, 현재 파싱 경로엔 미사용).
- 인프라: AWS S3 + CloudFront(웹), 원격 서버 PM2(API).

## 추가 기능·이슈 (2026-07-15 세션 — Works 최신화 중 lampas-system rebase)

원격 9개 커밋이 Instagram 레퍼런스 조회를 **커서 페이지네이션(`IgMediaPage`)·캐러셀 구조**로 크게 개편.
그 위에 로컬 미커밋 18개 파일을 rebase로 이식(2개 커밋으로 정리):

- **API** — `reference-account-images`에 `order=newest|oldest` 쿼리 재도입, 게시 시각(`takenAt`) 수집,
  본인 연결 계정만 공식 own-media API로 조회하는 `getOwnConnectedMedia` 분리. (2026-07-08엔 공개 프로필
  오래된순 정렬을 전부 폐기했던 이력과의 관계 → [[instagram-reference-integration]] 모순 병기 참고.)
- **web-sdk** — 스튜디오 `RegeneratePrompt` 버튼/모달 제거, Transform 예시 이미지를 캐시버스터 없는
  안정 URL로 저장. 원격이 새로 만든 레퍼런스 시트 모델 선택(`REF_SHEET_MODEL_OPTIONS`)·개편된 계정 검색
  UI는 원격 버전 채택.
- 검증: lampas-api `tsc --noEmit`(Prisma 클라이언트 재생성 후) 통과, lampas-web-sdk vite 프로덕션 빌드 통과.
- **알려진 기존 이슈(미해결, 이 세션이 만든 문제 아님)**: `pnpm install`이 저장소 전체에서 실패 —
  `apps/iileex-api`가 참조하는 워크스페이스 패키지 `@iileex/shared`가 저장소 어디에도 없음. `origin/main`도
  동일.
- `lampas-system` 저장소에 git user 설정이 없어 기존 커밋과 동일하게 `John <john@progdesigner.com>`으로
  로컬(저장소 한정) 설정.
- 절차 스킬 → [[rebase-local-feature-onto-refactored-remote]] · 세션 →
  [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
- **2026-07-17 후속 pull**: [[2026-07-17-works-저장소-일괄최신화-pull]] 세션에서는 로컬 미커밋 변경 없이
  깨끗하게 `main` 1커밋(배포 스크립트 추가)만 pull됨 — 07-15의 리베이스 필요 상태에서 정상화됨.

## Space(공간) 엔티티 — 신규 기능 (2026-07-15~16 세션에서 설계·구현)

Actor·Object처럼 "만들어서 저장해두고 촬영에 반복 사용"할 수 있는 세 번째 촬영 대상. 이전엔 `environment`
(자유 텍스트)와 `sceneReferenceImageData`(1회성, 저장 안 됨) 두 임시 형태로만 존재했고 **독립 엔티티는
없었음** — Actor·Object 파이프라인을 그대로 복제해 신설.

- **DB**: `Space` 모델(테이블 `spaces`) 신설, 기존 enum(`ActorStatus`/`ScopeArea`) 재사용.
- **API**: `modules/spaces/`(생성·목록·조회·수정·삭제·복원·Draft↔Workspace 이전·공개 이미지 스트림) —
  `modules/objects/` 복제.
- **web-sdk**: `/spaces`, `/spaces/create`, `/spaces/view/:spaceKey` 신설. 공간 카테고리 11종(Studio·Indoor·
  Outdoor·Urban·Nature·Cafe 등). 내비게이션에 Spaces 탭 추가(Objects-Gallery 사이).
- **촬영 합성 연결**:
  - Actor / Actor+Object 촬영(Atlas Cloud) — 기존 `background` 레퍼런스 슬롯 그대로 재사용, 거의 무개조.
  - Object 단독 촬영(Gemini 직접 경로) — 2슬롯(제품+씬레퍼런스) 하드코딩에 **3번째 슬롯**으로 Space 이미지
    추가(Gemini 최대 8장 지원이라 여유 있음).
- **web-ai 채팅 촬영 플로우**: 피사체 확정 직후 저장된 Space가 있으면 배경 선택 카드 그리드 제안(없으면
  기존 플로우 무변화). 이 세션(2026-07-15) 시점엔 **채팅에서 Space를 "만드는" 플로우는 없었고** 생성은
  SDK 웹에서만이었으나, **2026-07-18 갱신**: [[lampas-web-ai]] 채팅 "등록하기"에 🏞 Space 만들기 메뉴 +
  사진 우선 분류(Gemini Vision)로 Space 등록 경로가 추가·배포됨(14:04~14:06 UTC) → 이제 채팅에서도
  Space 생성 가능. → [[2026-07-18-web-ai-등록플로우-사진분류-배포]]
- **모델 배정**: 레퍼런스 분석 Grok(1차)→Gemini(폴백), 프로필 이미지 생성 Gemini, 연출 프롬프트 Grok —
  기존 Object 패턴과 동일.
- 이 세션 시점 **web-ai/API tsc 통과·SDK esbuild 검증까지 완료, 배포는 미진행**(3개 앱 모두 배포 필요).
- **2026-07-18 갱신**: `lampas-api`의 Space 모듈이 DTO·컨트롤러·서비스 구현이 추가되며 확장되고,
  이 확장분이 **API 배포로 프로덕션에 반영**됨(PM2 재시작 확인). 단 이번에 배포된 건 API뿐이고
  **Space 생성 UI가 있는 `lampas-web-sdk`는 이 배포·직전 git pull 요약에 등장하지 않아 여전히
  미배포로 추정**(확인 필요) — 위 "3개 앱 모두 배포 필요" 상태가 API 1개만큼만 진전된 것으로 보임,
  Space가 실제 사용자에게 완전히 열렸다고 보긴 어려움. `lampas-web-product`의 `SpaceCreation` 컴포넌트도
  같은 pull에 개선분이 포함됐으나 이 앱 자체가 배포 인프라 미비로 미배포 상태(아래 절 참고).
  → [[2026-07-18-web-ai-등록플로우-사진분류-배포]]
- 절차 스킬 → [[clone-sibling-entity-pipeline]] · 세션 → [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]]
- **선행 설계와의 차이**: 같은 날 11:39~12:26 세션([[2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계]])이
  먼저 Space를 설계했으나(스키마 마이그레이션 없이 `category:'space'`인 `StudioObject` 재사용 + spaceType·
  timeOfDay·lighting·mood·description 5필드 분할안) 권한 채널 장애로 구현 착수 전 중단됐다. 저녁 세션은
  이 선행 설계를 참조하지 않고 "Space가 코드베이스에 없다"는 결론까지 처음부터 재조사했고, 실제 구현은
  더 무거운 **독립 `Space` Prisma 모델 신설**로 갔다(위 필드 분할 대신 공간 카테고리 11종 열거형만 채택).
  두 세션 사이 논의는 이 위키 소스로 확인 불가 — timeOfDay/lighting을 구조화 필드로 분리할지는 열린 질문.

## `lampas-web-product` — 제품 이미지 분석·마케팅 앱 (신규 앱, 2026-07-16 세션)

Actor·Object·Space와 별개로, **회사(Actor/Object) 엔티티 시스템과 무관한 독립 무상태 서비스**로 신설. 제품
이미지 한 장을 올리면 Gemini vision이 제품 특성 분석 + 마케팅 방향(타깃·앵글·채널·광고 카피·해시태그)을
바로 생성해주는 단일 화면 앱.

- **프론트**: `apps/lampas-web-product`(신규, 포트 **8236**) — `lampas-web-ai`를 슬림 복사 베이스로 스캐폴딩
  (Vite 6 + React + Tailwind, 로그인 불필요). 이미지 업로드(`fileToDownscaledDataUrl` 재사용) → 분석 →
  결과 카드(요약·특징·강약점·타깃·브랜드 키워드·마케팅 앵글·채널 전술·광고 카피·콘텐츠 아이디어·해시태그).
  광고 카피는 **AIDA 프레임**(Attention/Interest/Desire/Action)으로 정규화, 카피·해시태그 개별/일괄 복사
  버튼 지원(반복 사용자 요청으로 후속 개선됨).
- **백엔드**: `apps/lampas-api`에 신규 무상태 공개 모듈 `src/modules/product-insights/` —
  `POST /api/product-insights/analyze`(`@Public()`, 로그인·DB 불필요). `GeminiService.analyzeProductMarketing()`
  신설 — 기존 `analyzeObjectTraits`(Object 분석)와 동일한 vision+JSON파싱 패턴을 재사용, 마케팅 전략가
  프롬프트만 교체.
- 기존 `products` 모듈(Prisma CRUD 엔티티)과는 성격이 달라 **의도적으로 분리**됨 — 섞으면 지저분해진다는
  판단.
- **배포 미완**: `scripts/deploy-web.sh`에 항목은 추가했으나 CloudFront ID가 `__SET_CLOUDFRONT_ID__`
  플레이스홀더 — 실제 배포 전 S3/CloudFront 인프라 발급 필요.
- 절차 스킬 → [[new-app-scaffold-from-slim-base]] · 세션 → [[2026-07-16-lampas-web-product-신규앱-구현]]

## 포지셔닝 전략 — Higgsfield 대비 차별화 (2026-07-17 상담)

`[[progdesigner]]`가 [[lampas]]에게 힉스필드(Higgsfield) 대비 차별화 전략을 상담. 결론은 "생성 품질
경쟁이 아니라 Actor(사람)/Object(상품)/Space(공간) 3축을 결합해 브랜드 콘텐츠 자산을 지속 운영하는
체계"로 포지셔닝하라는 것 — Space 엔티티 구현(위 절, 2026-07-15~16)과 별개로 진행된 사업 전략
논의이며, **채택이 확정된 기록은 아님**(상담 결과일 뿐). 개체 등록 동사도 Actor=스카우트/Object=선정/
Space=매핑으로 추천받음. 상세 → [[lampas-actor-object-space-positioning]] ·
세션 [[2026-07-17-람파스-차별화전략-용어-works저장-quick]].

## 2026-07-18 오전~오후 세션 — 배포 운영 + web-ai 등록 플로우 개편 → [[2026-07-18-web-ai-등록플로우-사진분류-배포]]

- **`lampas-web-www` landing 페이지 전면 재디자인** — 기존 컴포넌트 제거 → `landing/` 폴더 신규 컴포넌트로
  교체, S3+CloudFront 배포 완료(06:06~06:07 UTC).
- **설계 문서 신설**: `docs/superpowers/specs/`에 `2026-07-17-lampas-web-www-landing-redesign-design.md`·
  `2026-07-18-lampas-web-ai-onboarding-design.md`·`2026-07-18-lampas-www-entity-landing-design.md` 3건
  추가 확인 — 기능 구현 전 설계 문서를 먼저 커밋하는 절차가 이 제품에도 쓰이고 있음(문서 내용 자체는
  이 세션 소스로는 미확인).
- **web-ai**: 새 대화 시작 2칩화(등록하기/촬영하기) + 등록하기의 "사진 우선 Gemini Vision 분류" 플로우
  구현·배포. 상세는 [[lampas-web-ai]] 엔티티 페이지 참고.
- 배포 중 corepack pnpm shim이 `ln -sf`로 `.bin` 심볼릭 링크를 잘못 남기는 새 함정 발견·정리 →
  [[deploy-sandbox-pnpm-shim]].

## 기타 변경 (2026-07-18, 오후 works-전체저장 세션)
- 액터 플로우 및 gemini/actors API 확장 커밋·push. [[works-project-portfolio]] 일괄 저장 요청의 일부로,
  변경 세부 내용은 소스에 한 줄 요약만 있어 미상 — 위 오전~오후 세션에서 배포까지 된 변경분을 이 시점에
  비로소 커밋했을 가능성이 높음(정확한 대응 관계는 두 세션 소스만으론 확정 불가). → [[2026-07-18-works-전체저장]]

## Lampas Browser — 기획됐다가 `lampas-harness`로 이관됨 (2026-09-26 세션)

`apps/lampas-browser`(Electron 기반 AI 조작형 Chromium 브라우저)가 이 저장소(`lampas-system`) 아래에서
하루 만에 기획·1차 구축까지 됐으나, 같은 날 안에 소스 전체가 `[[lampas-harness]]`의 `apps/browser`로
이관되며 AI 실행 방식도 자체 API 키에서 하네스 CLI 세션 재사용으로 바뀌었다. 이 저장소엔 더 이상
남아있지 않을 가능성이 높음(이관 이후 재확인 안 됨) — 최종 상태·상세는 → [[lampas-browser]] · 세션:
[[2026-09-26-lampas-browser-구축-harness이전]]

## "Edit" 템플릿 에디터 — 앱 `[[lampas-web-edit]]`(edit.lampas.io)

> **2026-09-26 ingest 시 시점 정정**: 이 절을 처음 "2026-09-25 세션에서 신규 노출"로 기록했으나,
> [[2026-09-19-lampas-edit-자막-템플릿-대량기능개발]] 세션(같은 저장소, **6일 앞선** `Tool: claude`
> 세션)에서 이 앱 전체(자막·큐 편집·트랙·레이아웃 템플릿 등 20개 이상 기능)가 처음부터 구현·배포된
> 것이 뒤늦게 확인됐다. 09-25 세션은 그 계정별 템플릿 데이터의 후속 버그(아래)만 다룬 것이지 앱의
> 최초 등장이 아니다 — "최초 노출"은 이 위키가 그 세션을 먼저 읽은 관찰 순서일 뿐. 앱 상세는 이제
> 독립 엔티티 [[lampas-web-edit]] 참고, 이 절은 계정별 템플릿 데이터 버그만 남긴다.

사용자가 이미지 위에 템플릿(로고 등 이미지 슬롯 포함)을 골라 적용하는 에디터로, 템플릿은
**계정별로 서버에 저장**된다(예: SPOTV 템플릿이 `progdesigner7`·`rallycap.official` 두 계정에
각각 존재— 계정 종속화 자체는 [[lampas-web-edit]] 0.1.38에서 구현됨).

- **이미지 슬롯 스키마**: `fingerprint`(그 이미지를 만든 브라우저의 로컬 OPFS 저장 키, 기기 종속) +
  `url`(공개 S3/CloudFront 주소, 기기 독립 — 0.1.42에서 처음 도입). 로컬에 없으면 `url`로 폴백하는
  로직은 있으나, **드래그로 이미지를 넣어 템플릿을 만들면 `url` 없이 저장되는 경로**가 있어 다른
  브라우저에서 이미지가 안 붙는 문제가 재발할 수 있음(2026-09-25 세션은 코드 수정 없이 데이터만
  땜질, 근본 수정 미착수).
- 공용 로고 자산은 S3 `cdn/production/edit-templates/logos/`에 보관.
- 절차 스킬 → [[template-image-slot-fingerprint-vs-url]] · 세션 →
  [[2026-09-19-lampas-edit-자막-템플릿-대량기능개발]](원 구현) ·
  [[2026-09-25-edit-템플릿-이미지-s3-url-수정]](후속 계정별 데이터 버그)

## 2026-09-24 저녁 세션 — Seedance 2.0 Mini 추가 + 노드 툴바 UX 개선 + Dalar SoT 동기화 실제 확인

[[dalar]]가 Node Studio(스튜디오 UI)의 SoT라는 사실이 이전엔 `AGENTS.md` 문서 인용으로만 추정
기록돼 있었는데, 이 세션에서 **실제로 `dalar-web-app`을 먼저 수정하고 `pnpm sync:studio`로
`lampas-web-studio`에 반영해 드리프트 0을 확인**함으로써 처음 실증됨(문서 인용이 아니라 명령 실행
결과) → [[dalar]] 참고.

- **동영상 생성에 Seedance 2.0 Mini 추가**: 모델 피커에 "Seedance 2.0 Mini"(56cr/초, 시작 프레임
  1장)·"Seedance 2.0 Mini · 여러 이미지"(레퍼런스 최대 9장) 두 항목, `modelCapabilities.js` 정적
  폴백(4~15초, 480p/720p, 원본 비율, 오디오 지원), `video.dto.ts` 허용 목록·`atlas-cloud` reference-
  to-video 정규식에 mini 포함(누락 시 서버 400), 크레딧 오버라이드(56크레딧/초, 제품
  `lampas-web-studio`) 추가. 배포 후 운영 `GET /v1/credits/pricing`에서 반영 확인.
- **노드 선택 시 좌우 + 버튼 동시 표시**(`NodeStudioCanvas.jsx`): 기존엔 포인터가 노드 중심선 어느
  쪽에 있는지 추적하는 전역 pointermove 리스너 2개로 hover 방향에 따라 한쪽 버튼만 노출돼 선택이
  어렵다는 사용자 피드백 → 선택 시 좌우 `NodeToolbar` 동시 표시(각 방향은 연결 가능한 노드가 있을
  때만, 메뉴는 한쪽만 열림)로 변경.
- **부수 수정**: `lampas-web-studio`의 `workDetailPath.spec.js`가 `node:test` 문법이라 vitest에서
  실패 중이던 것(방치되면 `deploy-web.sh` 테스트 게이트가 막힘)을 vitest 문법으로 교체, 테스트
  228개 전체 통과.
- **배포**: `lampas-api` 0.1.140(micro 서버 PM2)·`lampas-web-studio` 0.5.40(S3+CloudFront). 배포 전
  운영 DB 드리프트 검사 "empty migration"으로 클린 확인.
- **선택적 헝크 커밋**: 공유 `atlas-cloud.service.ts`처럼 다른 미커밋 변경과 섞인 파일은 이번 작업
  헝크만 골라 스테이징(`7b50335f`, 16개 파일) → 절차 스킬 [[selective-hunk-commit-shared-file]].
- **이어진 멀티커밋 푸시**: 미커밋 4커밋(`fixs`·`trends`·**`lampas-web-spot`(식당 지도, 신규) →
  [[lampas-web-spot]]**·잔여작업 159파일)을 원격 10커밋과 병합해 push. 충돌 3건은 모두
  `talk-app-toss-samantha`([[toktalk]])였고 원격의 0.1.7이 이미 배포된 상태라 원격 채택 — 이전에
  `AGENTS.md` 문서 인용으로만 기록됐던 `talk-app-toss-samantha` 앱 존재가 실제 git 충돌로 재확인됨.
- 세션 → [[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]]
- **2026-09-26 갱신**: 이 세션의 병합 커밋에 포착됐던 `lampas-web-spot`을 실제로 만든 별도
  `codex` 세션이 확인됨 — `spot.lampas.io` 맛집 지도, 지도 프로바이더는 카카오→OpenStreetMap→
  네이버(401 실패)→**OpenStreetMap 확정**으로 3회 전환, 최종 738곳 중 502곳 지도 표시. 이 세션
  당시 env에 남아있던 네이버 키는 그 중간 시점의 스냅샷이었음 → [[lampas-web-spot]]·
  [[2026-09-24-spot-맛집지도-구축-지도전환-신고기능]] 참고.

## `lampas-web-trends` + `lampas-trends-collector` — 트렌드 키워드 수집 (2026-09-19 세션에서 첫 상세 노출)

`AGENTS.md` 앱 목록(아래 절)엔 이름만 있던 두 앱이 이 세션에서 처음 상세히 드러남 — 기사 제목·인기
글이 핵심 키워드 3개로 쓰인다는 전제로 제목마다 키워드를 유추해 재집계하는 트렌드 수집·분석 제품
(`trends.lampas.io`). 토픽 단위 재구축([[atlas-cloud]] 경유 `gemini-3.5-flash` 텍스트 배치 유추,
X·Threads 공식 API 소스 추가) → 배포 → 배치 크기·타임아웃 조정(40개→20개·90초·백그라운드 캐시
워밍) 순으로 진행, 운영 배포까지 완료(수집기 0.1.7·API 0.1.99). Threads 토큰 만료·X 토큰 미설정은
운영 조치 필요로 남음. 코드는 **미커밋 상태**. 상세 → [[lampas-web-trends]] ·
[[2026-09-19-lampas-trends-고도화]] · 절차 스킬 [[llm-batch-inference-timeout-tuning]].

## 2026-09-26 저장소 구조 스냅샷 (`AGENTS.md`) — 이전 기록과 모순 다수

`lampas-web-fit` 구축 세션(`Tool: codex`, 작업 폴더 `lampas-system`)의 시스템 프롬프트에 저장소
루트 `AGENTS.md` 전문이 포함되어, 이 위키가 마지막으로 기록한 2026-07-15/18 구조와 크게 달라진
2026-09-26 시점 상태가 드러남. **이 세션 자체는 구조를 조사한 것이 아니라 문서를 그대로 인용받은
것**이므로, 아래는 `AGENTS.md` 서술 기준이며 실제 코드 확인은 아님 — 다음 조사 시 검증 필요.

> **2026-09-26 ingest 시 시점 정정**: 아래 내용을 "2026-09-26 최초 노출"로 기록해뒀으나,
> [[2026-09-21-lampas-agent-fixs-신설]] 세션(같은 저장소, 5일 앞선 `Tool: codex` 세션)의 시스템
> 프롬프트에도 **동일한 `AGENTS.md` 전문**(Lampas 9앱+Dalar 6앱+Talk 7앱+Iileex 1앱 구조, MySQL 명시
> 포함)이 이미 포함돼 있었다. 즉 이 구조 자체는 최소 2026-09-21부터 존재했고, "최초 노출"은 위키가
> *이 세션에서 처음 관찰*했다는 뜻일 뿐 저장소 구조가 09-26에 바뀌었다는 뜻이 아니다 — 아래 소절
> 제목·서술은 그대로 두되 이 점을 참고할 것.

- **앱 목록이 3개 제품 라인으로 확장**: Lampas 9개(`lampas-web-www`/`-pay`/`-cs`/`-studio`/
  `-music`/`-admin`/`photobooth-app-toss`/`lampas-api`/`lampas-api-mcp` + `lampas-web-trends`·
  `lampas-trends-collector`) + **Dalar 6개(이 세션에서 최초 노출, 위키에 전무했던 형제 제품 라인)**
  → [[dalar]] + Talk 9개(**"구 dbs/talk-system"** 주석 — [[toktalk]]을 "별개 코드베이스"로 기록한
  기존 페이지와 모순, 아래 [[toktalk]] 절 참고) + Iileex 1개(`iileex-web-www`만 등재).
- **`lampas-web-sdk` → `lampas-web-studio`로 개명된 것으로 보임**(2026-07 기록엔 `lampas-web-sdk`,
  이번 `AGENTS.md`엔 동일 역할·포트 위치에 `lampas-web-studio`만 존재) — 리네이밍 확정은 아니고
  이름 변화만 관찰.
- **`lampas-web-ai`(2026-07-15부터 주요 앱으로 승격 기록됨) · `lampas-web-product`(2026-07-16 신규
  기록됨) · `iileex-api`/`iileex-web-admin`이 이번 앱 목록에서 빠짐** — 폐기·통합 여부 미확인, 단순
  `AGENTS.md` 요약 누락일 가능성도 있음.
- **Node Studio SoT가 `apps/dalar-web-app`으로 이동**했다고 명시 — `lampas-web-studio` 반영은
  `pnpm sync:studio`(`scripts/sync-studio-from-dalar.mjs`)로 동기화. 이 페이지의 기존 "스튜디오 구조" 절이
  기술하는 `lampas-web-sdk` 자체 구현 서술과 배치되므로, 이후 세션에서 실제 소스 위치 재확인 필요.
  **2026-09-24 갱신**: 위 절의 세션이 이 SoT 관계를 문서 인용이 아니라 실제 `pnpm sync:studio` 실행·
  드리프트 0 확인으로 실증함 — 이 모순은 사실상 해소(SoT는 `dalar-web-app`, `lampas-web-studio`는
  동기화 대상이 맞음). 다만 `lampas-web-sdk`라는 이름 자체가 `lampas-web-studio`로 리네이밍된 것인지,
  아니면 SoT 이전으로 자체 구현이 완전히 얇아진 것인지는 여전히 미확인.
- **DB 모순**: `AGENTS.md`는 "NestJS + Prisma + **MySQL**"이라 명시. 이 페이지의 기존 서술(2026-07-15
  rebase 세션에서 코드로 직접 확인한 **PostgreSQL**, 위 절 참고)과 정면으로 다름 — 어느 쪽이 최신·
  정확한지 이 세션 소스만으론 판별 불가, 다음 코드 접근 시 재확인 요망.
- **신규 앱 `lampas-web-fit`**(포트 **8462**, 도메인 `fit.lampas.io`) — 이번 세션에서 처음부터 구현·
  배포까지 완료. 음악 박자에 맞춘 운동 가이드 앱: 운동 타이머·음악이 같은 오디오 시계 공유(일시정지·
  탭 이탈 시 동반 정지), 다음 동작 8박 전 예고 + 마지막 4박 음성 카운트, 루틴 3종 + BPM·라운드·음악
  설정, 운동 기록 저장, 모바일 지원. 음악은 참고 영상(유튜브 쇼츠 "1분 플랭크 챌린지") 음원 대신
  **내장 오리지널 비트** 사용(라이선스 미확인 회피). 배포는 기존 `deploy-web.sh` 경로 + `fit.lampas.io`
  전용 CloudFront 신규 생성 + 기존 Lampas 와일드카드 인증서로 HTTPS 적용. → [[2026-09-25-lampas-web-fit-구축-배포]]

## Jev(TypeSafe) 도입 — 같은 날 세 세션이 서로 다른 결과 (2026-09-20)

2026-09-20 하루에 거의 같은 프롬프트("Jev 활용처 찾아서 lampas-system 개선 추천")로 시작된 세 세션이
있었다. 서로 다른 도구·다른 서베이·다른 결론을 냈으므로 혼동하지 않도록 나열:

1. **11:00:08Z, `Tool: claude`** — `lampas-api`/`dalar-api`/`lampas-agent`/`lampas-web-*`/`talk-api`
   전체 AI 호출 패턴을 조사(→ [[lampas-system-ai-call-architecture-audit]])하고 Jev 적용처 10개를
   추천, 사용자가 "#6" 지정 → **`dalar-api` 채팅 의도분류 + `lampas-api` sports-wiki ingest 게이트를
   실제로 구현·테스트**(코드 완료, 배포는 `TYPESAFE_API_KEY` 부재로 세션 종료 시점 보류).
   → [[2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현]]
2. **11:38:22Z, `Tool: codex`** — `references`·`orchestration`·`trends`·`product-insights` 모듈만
   훑어 별도로 5개 후보를 추천, **코드 수정 없이** "전면 도입 보류"로 종료. 우선순위 1위는 레퍼런스
   탐색 결과 재정렬(`reference-explore.service.ts:217`). → [[jev-typed-classification]] "미채택 후보
   5개" 절 · [[2026-09-20-jev-활용처-추천-lampas-system]]
3. **11:52:53Z, `Tool: codex`** — Flow 구축 세션의 "Jev 노드 자동화" 절에서 사용자가
   `TYPESAFE_API_KEY` 값을 직접 제공, `.env.production`에 반영 → **이 값이 1번 세션이 막아둔 배포
   전제조건을 사실상 해소**했을 가능성이 높다(5일 뒤 sports-wiki 세션이 게이트의 실제 프로덕션 동작을
   서술함, → [[jev-typed-classification]] "확인된 사용처 #1"). → [[2026-09-20-lampas-flow-만들기]]

세 세션의 소스만으로는 1번의 코드가 3번의 배포와 정확히 어떻게 합류했는지 확정할 수 없다 — 커밋
그래프 확인이 필요한 열린 질문으로 남긴다.

## 스포츠 클립 파이프라인 — 에이전트·위키·Copy·Reels·Status·Tools (2026-09-25 세션에서 첫 노출)

`AGENTS.md` 2026-09-26 스냅샷 절(위)의 3라인 목록엔 등장하지 않는 앱들이 같은 저장소 `lampas-system`에
따로 확인됨 — 스포츠 경기 영상을 클립화·라벨링·SNS 카피까지 만드는 별도 콘텐츠 파이프라인:

- **`lampas-agent`** — 맥미니 로컬 데몬, 스포츠 클립 라벨링·업로드(Clips·Pulse·Threads·Fixs 탭), 유튜브
  영상 수집. 상세·이름 충돌 주의(`[[lampas]]` 하네스 에이전트와 무관) → [[lampas-agent]]
- **`lampas-api`의 `sports-wiki` 모듈** — 선수/구단 페이지에 이 세션에서 **"경기(game)" 엔티티**가
  추가됨(LLM 없이 결정적 저장 + 짧은 서사 요약만 LLM). 진단·설계·구현 전체 → [[lampas-agent]] ·
  [[2026-09-25-스포츠위키-경기엔티티-설계구현]] · 패턴: [[deterministic-extraction-vs-llm-rewrite]]
- **[[lampas-web-flow]]**("Flow", `flow.lampas.io`, 2026-09-20 세션에서 첫 노출) — 위 파이프라인
  전체(영상 수집→Clips→Copy→Reels→Edit→Package)를 "Work" 노드 그래프로 연결하는 오케스트레이션 허브.
  Jev 기반 노드 자동화, Tailscale Funnel 대역폭 병목 우회(S3 직접 전송), 서브도메인 간 계정 이어받기
  (SSO)까지 이 세션에서 구현·배포됨 → [[2026-09-20-lampas-flow-만들기]]
- **[[lampas-web-pulse]]**("Pulse", `pulse.lampas.io`, 2026-09-19 세션에서 전면 개편·정식 엔티티화) —
  Google 로그인 세션 단위로 미션을 열람하고 **페르소나를 만드는 단일 출처**(이전엔 Copy가 생성했음).
  계정간 미션 보내기 기능도 이 세션에서 함께 구현.
- **[[lampas-web-edit]]**("Edit", `edit.lampas.io`, 2026-09-19 세션에서 첫 상세 구현 — 자막 편집·
  큐 타이밍·레이어 스택·계정 종속 레이아웃 템플릿까지 이 파이프라인에서 가장 기능이 많은 앱) ·
  **[[lampas-web-copy]]**("Copy") · **[[lampas-web-reels]]("Reels")** · **[[lampas-web-package]]**
  ("Package", `package.lampas.io`, 2026-09-20 세션에서 정식 엔티티화 — 이전엔 다른 페이지에서
  "소속 미상"으로만 언급) · **[[lampas-web-status]]**
  (`status.lampas.io`, 2026-09-25 status.claude.com 형태로 처음부터 구현·운영 배포 완료 —
  컴포넌트 40개 60초 프로브·자동 인시던트·90일 업타임 바) · **[[lampas-web-tools]]**(`tools.lampas.io`,
  AI 생성 도구 13개) — 각각 카피 생성, 클립 편집/페르소나 선택, 완성 영상 등록·SNS 게시 준비,
  시스템 상태 표시, 범용 AI 생성 도구 모음. Copy·Tools·Fixs 삭제기능 상세는
  [[2026-09-25-copy스크롤-fixs삭제-tools모델표시-영상재생버그]] 세션, Package의 Pulse 페르소나
  자동작성·톤 5종은 [[2026-09-20-lampas-package-pulse페르소나-릴스자동작성-톤선택]] 세션, status
  전체 구현·배포 절차는 [[lampas-web-status]] 엔티티 · [[2026-09-25-status-서비스-구축-배포]]
  세션 참고. 이 앱들은 위 `AGENTS.md` 목록(Lampas 9/Dalar 6/Talk 9/Iileex 1)에 없어 두 소스(코드
  스냅샷 vs 실제 세션 관찰)가 서로 다른 앱 부분집합만 비추고 있음을 시사 — `AGENTS.md`가 전체 앱을
  다 열거하지 않거나, 클립 파이프라인이 별도 워크스페이스일 가능성 병기.
- **배포 동시성 함정**: 같은 저장소를 두 세션이 몇 분 간격으로 배포하면 상대 세션의 미완료 중간 상태가
  빌드에 섞일 수 있음(Threads 탭 오노출 사례) → [[lampas-agent]] 참고.

## AI Clip Intelligence + Brand Kit — 숏폼 자동 제작 요구사항 대응 (2026-09-18 세션)

사용자가 "긴 영상→AI 숏폼 자동 제작" 제품 기획서를 붙여넣고 스포츠 클립 파이프라인에 없는 기능을
채워달라 요청. 구현 전 clips/에디터/AI 게이트웨이·ASR/reels·copy·packaging 4개 영역을 병렬 서베이해
갭을 확정(절차 → [[parallel-survey-before-feature-gap-analysis]], 갭 분석표 → [[lampas-clip-intelligence]]).
`lampas-api`에 신규 `clip-intelligence`(5축 클립 점수·시간 미겹침 상위 N 선택·15/30/60/90초 트림·
스타일별 훅 문구) + `brand-kits`(로고·폰트·자막 스타일·팔레트·CTA·워터마크 계정 프리셋) 모듈을
신설하고, `[[lampas-web-edit]]`에 단어 강조 자막·9:16 Auto Reframe(모션 무게중심 팬, 이후 수동
키프레임 조정·Contain 모드 버그 수정까지 3회 후속)·브랜드 킷 자동 적용을, `[[lampas-web-reels]]`에
AI 선별 패널을 추가했다가 곧바로 "UI/UX 복잡" 피드백으로 페르소나/카피 생성 UI 전체를 걷어내고 현재의
6단계 편집 그룹 보드로 재편, `[[lampas-web-clips]]`에 5축 점수 표시·정렬을 추가했다. 부수적으로
에디터 자막 생성이 긴 구간에서 중간에 끊기던 버그(청크 미분할)를 수정 → [[asr-long-audio-silent-truncation]],
`lampas-web-copy`에 추가 지시사항·톤 서버 프리셋(`copy-hints` 모듈)과 `?clip=` 딥링크를 추가했다.
**5축 점수·페르소나 기반 클립 선택은 오래가지 못했다** — 다음 날([[lampas-agent]] "훅 점수" 절)
비전 라벨링의 축 점수가 배치 대비 효과로 불안정하다는 게 드러나 텍스트 기반 상대 순위 방식으로
대체됐고, 릴스의 페르소나 UI도 같은 세션 안에서 이미 단순화됐다 — 상세 비교는
[[lampas-clip-intelligence]] "실제 구현 결과 — 원안과의 차이" 절 참고.
전체 배포(운영 DB DDL 선적용→API→에이전트→웹 3종)까지 완료. 세션 →
[[2026-09-18-lampas-clip-intelligence-brand-kit-대량구현]].

## `lampas-web-music`(`music.lampas.io`) — 모델 업그레이드 (2026-09-22 세션)

Lampas 앱 목록에 이름만 있던 음악 생성 앱의 첫 상세 노출. [[atlas-cloud]] 경유 minimax 음악 모델을
2.6 → **3.0**으로 업그레이드·운영 배포 완료(요청 필드 동일해 호출 코드 무변경, 곡당 150크레딧 유지).
`[[lampas-web-tools]]`의 `music-gen` 툴은 이 배포 이후에도 여전히 2.6 — 자매 앱 간 버전 불일치가
남음. 상세·엔티티 → [[lampas-web-music]] · 세션 → [[2026-09-22-music-lampas-io-minimax3.0-업그레이드-배포]]

## Edit 모델 2종·멀티이미지 영상·WAN 3.0·Qwen Edit 추가 + 영상 오류 반복 근본수정 (2026-09-21 세션)

`Tool: codex` 세션. 한 세션 안에서 이미지 편집 모델 추가 → 영상 멀티이미지 연결 → 영상 생성 오류
반복 진단·수정 → Draft 이름변경 버그 → WAN 3.0 교체 → 카탈로그 갱신 → Qwen Edit 추가까지 연속
진행·전부 배포. → [[2026-09-21-lampas-studio-edit모델-wan3.0-qwen이미지-멀티이미지영상]]

- **이미지 편집 모델 추가**: **GPT Image 2.5 Sunburst Edit**·**Flare Edit** — 촬영·보정에 추가, 이후
  `models.lampas.io` 정식 카탈로그 등록(각 6cr/장, 이전엔 "카탈로그 외 모델"로 임시가격).
- **영상 멀티이미지 입력**: 기존엔 연결된 이미지 중 **첫 장만 서버 전송**하던 제약을 확인, Seedance/
  WAN 3.0 레퍼런스 모델로 다중 이미지 입력 지원 추가.
- **영상 생성 "오류 반복" 근본 원인 2가지**(실행 자체는 문제 없었음): ① 서버 작업 ID가 아직 없는
  로딩 노드를 "중단된 생성"으로 오판하는 로직, ② 작업 ID 획득 후 **실행 중 폴링 + 복구용 폴링이
  동시에 붙어** 일시적 상태 조회 실패 한 번에도 실패 알림이 뜨는 구조. 둘 다 수정 → 절차 스킬
  [[false-abort-premature-status-check]].
- **Draft Work 이름 변경 불가 버그**: 브라우저 기본 `prompt()` 사용 부분을 앱 내부 입력창으로 교체,
  저장 실패도 그 입력창에서 확인 가능하도록 수정.
- **WAN 2.7 → 3.0**: Atlas 카탈로그에 WAN 3.0은 **영상 모델로만** 존재(이미지 3.0 없음) — **영상만
  3.0 교체**(50cr/초, 다중 이미지·오디오 지원, `models.lampas.io` 확정가), 기존 저장된 2.7 선택도
  실행 시 3.0으로 자동 승계. **이미지용 WAN은 2.7 Pro 유지**(3.0 미제공, 반복 확인된 제약).
- **`models.lampas.io` 카탈로그 508개 모델 동기화** — 스튜디오 모델 선택창 가격 표시가 이제 이
  카탈로그를 **유일한 소스**로 조회(이전엔 별도 하드코딩 가능성 시사).
- **Qwen Image 3.0 Pro Edit 추가**: 이미지 촬영·보정·Transform에 추가, 레퍼런스 최대 3장, 40cr/장,
  Pro 모델 ID가 그대로 호출되는지(기존 Qwen으로 안 바뀌는지) 별도 테스트.
- 검증: 스튜디오 테스트 225→231개, 모델 요청 테스트 6개, 전체 테스트 1,081개, 두 웹앱 빌드, Lampas
  API 타입검사 통과. Dalar API 전체 타입 검사는 기존 Prisma 타입 불일치로 제한(이 세션 이전부터의
  기존 이슈).

## 관련
- 세션: [[2026-09-18-lampas-clip-intelligence-brand-kit-대량구현]] ·
  [[2026-09-19-lampas-edit-자막-템플릿-대량기능개발]] · [[2026-09-20-lampas-flow-만들기]] · [[2026-09-21-lampas-agent-fixs-신설]] ·
  [[2026-09-21-lampas-studio-edit모델-wan3.0-qwen이미지-멀티이미지영상]] ·
  [[2026-09-25-lampas-web-fit-구축-배포]] · [[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]] ·
  [[2026-09-22-music-lampas-io-minimax3.0-업그레이드-배포]] ·
  [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] ·
  [[2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계]] · [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]] ·
  [[2026-07-16-lampas-web-product-신규앱-구현]] · [[2026-07-17-works-저장소-일괄최신화-pull]] ·
  [[2026-07-18-works-전체저장]] ·
  [[2026-07-17-람파스-차별화전략-용어-works저장-quick]] · [[2026-07-18-web-ai-등록플로우-사진분류-배포]] ·
  [[2026-09-25-edit-템플릿-이미지-s3-url-수정]] · [[2026-09-25-스포츠위키-경기엔티티-설계구현]] ·
  [[2026-09-25-copy스크롤-fixs삭제-tools모델표시-영상재생버그]] ·
  [[2026-09-24-spot-맛집지도-구축-지도전환-신고기능]] · [[2026-09-19-lampas-trends-고도화]]
- 토픽: [[lampas-actor-object-space-positioning]] · [[jev-typed-classification]] ·
  [[lampas-system-ai-call-architecture-audit]] · [[lampas-clip-intelligence]]
- 세션(추가): [[2026-09-20-jev-활용처-추천-lampas-system]] ·
  [[2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현]]
- 앱: [[lampas-web-ai]] · [[lampas-agent]](스포츠 클립 파이프라인) · [[lampas-web-pulse]] · [[lampas-web-copy]] ·
  [[lampas-web-reels]] · [[lampas-web-clips]] · [[lampas-web-edit]](`edit.lampas.io`) · [[lampas-web-package]](`package.lampas.io`) ·
  [[lampas-web-flow]](오케스트레이션 허브) · [[lampas-web-tools]] ·
  [[lampas-web-spot]](식당 지도, `spot.lampas.io`, OpenStreetMap 확정) · [[lampas-web-music]](`music.lampas.io`) ·
  [[lampas-web-trends]](`trends.lampas.io`)
- 외부 AI 프로바이더: [[gemini]] · [[atlas-cloud]] · [[grok]] · [[openai]] · [[higgsfield]](경쟁 비교)
- 개발/배포 주체: [[lampas]] on [[lampas-harness]]
- 공급자: [[progdesigner]]
- 포트폴리오 배경: [[works-project-portfolio]]
- 스킬: [[selective-hunk-commit-shared-file]] · [[nominatim-batch-geocode-progressive-rollout]] ·
  [[tailscale-funnel-large-payload-bypass]] · [[cross-subdomain-session-handoff]] ·
  [[execution-run-scoped-status-vs-stale-notification]] · [[accept-then-poll-for-slow-ai-jobs]] ·
  [[proxy-body-limit-413-appears-as-network-error]] · [[llm-batch-inference-timeout-tuning]] ·
  [[parallel-survey-before-feature-gap-analysis]] · [[asr-long-audio-silent-truncation]]
