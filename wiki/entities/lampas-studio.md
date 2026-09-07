---
tags: [entity, project, product, image-generation, nestjs, react, instagram, space, product-insights]
created: 2026-07-09
updated: 2026-09-07
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

## 관련
- 세션: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] ·
  [[2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계]] · [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]] ·
  [[2026-07-16-lampas-web-product-신규앱-구현]] · [[2026-07-17-works-저장소-일괄최신화-pull]] ·
  [[2026-07-18-works-전체저장]] ·
  [[2026-07-17-람파스-차별화전략-용어-works저장-quick]] · [[2026-07-18-web-ai-등록플로우-사진분류-배포]]
- 토픽: [[lampas-actor-object-space-positioning]]
- 앱: [[lampas-web-ai]]
- 외부 AI 프로바이더: [[gemini]] · [[atlas-cloud]] · [[grok]] · [[openai]] · [[higgsfield]](경쟁 비교)
- 개발/배포 주체: [[lampas]] on [[lampas-harness]]
- 공급자: [[progdesigner]]
- 포트폴리오 배경: [[works-project-portfolio]]
