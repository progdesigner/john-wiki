# 하네스 브라우저 만들기

Tool: codex
Started: 2026-09-25T14:21:45.948Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.codex/sessions/2026/09/25/rollout-2026-09-25T23-21-47-01a0d8f1-993e-7692-9b71-15d44d1e79e3.jsonl

## user

# AGENTS.md instructions for /Users/progdesigner/Works/lampas/lampas-system

<INSTRUCTIONS>
# lampas-system — AI/에이전트 가이드

모노레포(pnpm workspace) 기반의 Lampas 플랫폼 저장소. 프론트 작업 시 **`apps/lampas-web-studio`** 가이드를 우선 따른다.

---

## 저장소 구조

```
lampas-system/
├── apps/
│   ├── lampas-web-trends/     # 스포츠·예능·뷰티·AI 트렌드 보드 (trends.lampas.io)
│   ├── lampas-trends-collector/ # 공개 RSS 5분 주기 수집 (독립 PM2 워커)
│   ├── lampas-api/            # NestJS 백엔드 (Actor·Object·Space, 이미지·비디오, 결제, API Key 등)
│   ├── lampas-api-mcp/        # AI 게이트웨이 MCP 서버 (stdio · lampas_* tools)
│   ├── lampas-web-studio/     # Actor Studio Web SDK (React + Vite) ← 주요 프론트 · Node Studio
│   ├── lampas-web-music/      # 유튜브 레퍼런스 기반 AI 음악 생성 (music.lampas.io 상정)
│   ├── lampas-web-www/        # 공식 랜딩 사이트 (www.lampas.io)
│   ├── lampas-web-pay/        # 크레딧 결제 (pay.lampas.io · 토스페이먼츠 결제위젯)
│   ├── lampas-web-admin/      # Lampas 관리자 웹 (/admin API)
│   ├── photobooth-app-toss/ # 토스(Toss) 미니앱 · AI 포토부스
│   ├── talk-api/              # Talk NestJS 백엔드 (구 dbs/talk-system)
│   ├── talk-app-toss-api/     # Talk 토스 미니앱 전용 API
│   ├── talk-app-toss-samantha/    # Talk 토스 미니앱 (Samantha)
│   ├── talk-app-toss-brainrot/# Talk 토스 미니앱 (Brainrot)
│   ├── talk-web-www/          # Talk 랜딩
│   ├── talk-web-app/          # Talk 웹 앱
│   ├── talk-web-virtual/      # 사진 아바타·실시간 영상 대화 (virtual.toktalk.ai)
│   ├── talk-api-virtual/      # Tavus 영상 대화 전용 API (독립 Node 서버)
│   ├── talk-web-admin/        # Talk 관리자 웹
│   └── iileex-web-www/        # Iileex 웹
├── deploy/                    # 배포용 미러/설정
├── docs/                      # RELEASE.md, 설계 스펙 등
├── scripts/                   # deploy-api.sh, deploy-web.sh, deploy-app.sh
└── tools/                     # atlascloud, gemini, google, grok, video-edit, youtube 등 CLI·실험 도구
```

### 로컬 개발 포트

#### Lampas

| 앱 | 포트 | 루트 스크립트 |
|----|------|---------------|
| `lampas-web-www` | **8231** | `pnpm dev:lampas:web:www` |
| `lampas-web-pay` | **8232** | `pnpm dev:lampas:web:pay` |
| `lampas-web-cs` | **8233** | `pnpm dev:lampas:web:cs` |
| `lampas-web-studio` | **8236** | `pnpm dev:lampas:web:studio` |
| `lampas-web-music` | **8238** | `pnpm dev:lampas:web:music` |
| `photobooth-app-toss` | **8237** | `pnpm dev:lampas:web:photobooth` (Vite) / `pnpm dev:lampas:app:photobooth` (Granite) |
| `lampas-web-admin` | **8239** | `pnpm dev:lampas:web:admin` |
| `lampas-api` | **3133** | `pnpm dev:lampas:api` |
| `lampas-web-trends` | **8460** | `pnpm dev:lampas:web:trends` |

#### Dalar

| 앱 | 포트 | 루트 스크립트 |
|----|------|---------------|
| `dalar-web-root` | **8350** | `pnpm dev:dalar:web:root` |
| `dalar-web-www` | **8351** | `pnpm dev:dalar:web:www` |
| `dalar-web-cs` | **8352** | `pnpm dev:dalar:web:cs` |
| `dalar-web-app` | **8355** | `pnpm dev:dalar:web:app` |
| `dalar-web-admin` | **8359** | `pnpm dev:dalar:web:admin` |
| `dalar-api` | **3356** | `pnpm dev:dalar:api` |

#### Talk (구 dbs/talk-system 통합)

| 앱 | 포트 | 루트 스크립트 |
|----|------|---------------|
| `talk-web-www` | **8241** | `pnpm dev:talk:web:www` |
| `talk-web-app` | **8242** | `pnpm dev:talk:web:app` |
| `talk-web-virtual` | **8247** | `pnpm dev:talk:web:virtual` |
| `talk-web-admin` | **8243** | `pnpm dev:talk:web:admin` |
| `talk-app-toss-samantha` | **8245** | `pnpm dev:talk:app:samantha` |
| `talk-app-toss-brainrot` | **8246** | `pnpm dev:talk:app:brainrot` |
| `talk-api` | **3241** | `pnpm dev:talk:api` |
| `talk-api-virtual` | **3247** | `pnpm dev:talk:api:virtual` |
| `talk-app-toss-api` | **3242** | `pnpm dev:talk:api:toss` |

- Talk 전용 문서·스크립트·디자인은 `docs/talk/`, `scripts/talk/`, `design/talk/` 에 있고, 배포 미러·CLI 도구는 `deploy/talk/`, `tools/talk/`(비추적)이다. Prisma 는 `pnpm prisma:generate:talk` 등 `:talk` 접미 스크립트 사용.

### 자주 쓰는 명령

| 명령 | 설명 |
|------|------|
| `pnpm dev:lampas:web` | `lampas-web-*` 전체 병렬 개발 서버 |
| `pnpm dev:lampas:api` | API 개발 서버 (포트 3133) |
| `pnpm dev:lampas:web:www` | 공식 웹사이트 (포트 8231) |
| `pnpm dev:lampas:web:pay` | 크레딧 결제 웹 (포트 8232) |
| `pnpm dev:lampas:web:cs` | CS 웹 (포트 8233) |
| `pnpm dev:lampas:web:studio` | Web SDK / Node Studio (포트 8236) |
| `pnpm dev:lampas:app:photobooth` | AI 포토부스(토스 미니앱) 개발 서버 (포트 8237) |
| `pnpm dev:lampas:web:admin` | 관리자 웹 (포트 8239) |
| `pnpm dev:dalar` | `dalar-*` 전체 병렬 개발 서버 |
| `pnpm dev:dalar:web:www` | Dalar 랜딩 (포트 8351) |
| `pnpm dev:dalar:web:app` | Dalar 통합 앱 (포트 8355) |
| `pnpm dev:dalar:api` | Dalar API (포트 3356) |
| `pnpm dev:talk` | `talk-*` 전체 병렬 개발 서버 |
| `pnpm dev:talk:web:app` | Talk 웹 앱 (포트 8242) |
| `pnpm dev:talk:api` | Talk API (포트 3241) |
| `pnpm sync:studio` | dalar Node Studio → lampas-web-studio 이관 (apply) |
| `pnpm sync:studio:check` | 스튜디오 SoT 드리프트 검사 (exit 1 if drift) |
| `pnpm sync:atlas-pricing` | Atlas `/api/v1/models` → `model-pricing.generated.ts` (Standard/`origin` 단가만) |
| `pnpm --filter lampas-api run build` | API 빌드 |
| `pnpm prisma:generate` | Prisma 클라이언트 생성 |

### 배포 — 항상 `./scripts` 의 배포 스크립트를 사용한다

> 배포는 **반드시** 아래 스크립트로만 수행한다. 수동 빌드·S3 업로드·rsync·CloudFront 무효화를 개별 명령으로 재현하지 않는다. 스크립트가 프로덕션 env 적용(`env:production`), 빌드, 업로드, 원격 재시작, 캐시 무효화까지 일관되게 처리한다.

| 스크립트 | 대상 | 사용법 |
|----------|------|--------|
| `./scripts/deploy-api.sh <project>` | `apps/<project>/deploy.json` 에 `"type": "api"` 가 있는 API (원격 rsync + 재시작) | `./scripts/deploy-api.sh lampas-api` |
| `./scripts/deploy-web.sh <project>` | `apps/<project>/deploy.json` 에 `"type": "web"` 가 있는 웹 (S3 + CloudFront) | `./scripts/deploy-web.sh lampas-web-studio` |
| `./scripts/deploy-app.sh <target>` | 앱 (토스 미니앱 등) | `./scripts/deploy-app.sh photobooth-app-toss` |

- **배포 가능 여부**는 스크립트 하드코딩이 아니라 `apps/<project>/deploy.json` 존재 + `type` 일치로 결정한다. 새 프로젝트는 해당 파일만 추가하면 된다 (스키마: api=`remote`/`remoteApp`, web=`s3Path`/`cloudFrontId`/`awsProfile`).
- 스크립트는 `pnpm` 을 필요로 한다. `pnpm` 이 PATH 에 없으면 corepack 으로 임시 shim(`corepack pnpm@<packageManager 버전>`)을 만들어 PATH 에 넣고 스크립트를 실행하되, 스크립트 자체는 수정하지 않는다.

---

## `lampas-api` (요약)

NestJS + Prisma + **MySQL**. 외부 AI 연동은 `src/infrastructure/` 에 모듈화되어 있다. 기본 포트 `3133`.

| 경로 | 역할 |
|------|------|
| `src/modules/` | 도메인 모듈 (actors, objects, spaces, references, auth, images, transforms, resources, workspaces, credits, **payments**, **product-insights**, products, content-jobs, campaigns, instagram, presets, webhooks, admin, api-keys, api-clients, audit-logs …) |
| `src/infrastructure/` | atlas-cloud, email, gemini, grok, open-ai, serp-api, prisma, resource |
| `src/common/` | guards, decorators, helpers (`model-json.helper` 등) |

**주요 프론트 연동**

| 모듈 | 프론트 | 역할 |
|------|--------|------|
| `actors` / `objects` / `spaces` | `lampas-web-studio`, `dalar-web-app` (`/chat`) | 엔티티 CRUD·생성·스튜디오 합성 |
| `references` | `lampas-web-studio` | 레퍼런스 이미지 검색 |
| `payments` | `lampas-web-pay` | 토스 결제 패키지·주문·confirm |
| `product-insights` | `dalar-web-app` (`/discover`) | 제품 분석·마케팅 확장·광고 카피 이미지 |
| `admin` | `lampas-web-admin` | 관리자 JWT·유저·크레딧 |

**Actor 모듈** (`src/modules/actors/`) — Studio / AI와 직접 연동:

- `POST /actors/studio/rewrite-prompt` — TextToImage 5개 그룹 프롬프트 Grok 재작성
- `POST /actors/analyze-studio-reference` — 스튜디오 레퍼런스 분석
- `POST /orchestration/analyze-creation-input` — 채팅 입력에서 액터 생성 필드·취소 의도 추출 (Grok; `/actors/*` alias 유지)
- `POST /orchestration/classify-registration-photo` · `classify-register-image` — 등록 이미지 유형(Actor/Object/Space) 분류
- `POST /orchestration/analyze-flow-turn` · `marketing-consult` — 채팅 플로우 턴 판별·마케팅 상담
- `POST /actors/:key/generate-image` — 이미지 생성
- `POST /actors/:key/generate-motion-video` — 모션 비디오 생성

DTO·검증·Swagger는 각 모듈의 `dto/` 에 정의한다. 상세는 `apps/lampas-api/README.md` 참고.

### AI 모델 매핑 (`lampas-api`)

| 인프라 | 서비스 | API 키 / 설정 |
|--------|--------|----------------|
| **Gemini (Google AI)** | `GeminiService` | `GEMINI_API_KEY` |
| **Atlas Cloud** | `AtlasCloudService` | `ATLASCLOUD_API_KEY`, `ATLASCLOUD_API_URL` |
| **Grok (xAI)** | `GrokService` | `GROK_API_KEY`, `GROK_MODEL` |
| **SerpAPI** | `SerpApiService` | `SERP_API_KEY` (이미지 검색만, 생성 모델 없음) |

#### 환경 변수 — 기본 모델

| 변수 | 기본값 | 용도 |
|------|--------|------|
| `GEMINI_IMAGE_MODEL` | `gemini-3.1-flash-image-preview` | Gemini 직접 이미지 생성 |
| `GEMINI_TEXT_MODEL` | `gemini-3.5-flash` | Gemini 직접 텍스트·비전(JSON 분석) |
| `GEMINI_VIDEO_MODEL` | `veo-3.1-generate-preview` | Gemini Veo 비디오 생성 |
| `GROK_MODEL` | `grok-4.5` | Grok 채팅·비전 분석 |
| `GENERATE_IMAGE_MODULE` | `gemini` | Atlas 이미지 생성 시 provider 미지정 폴백 (`gemini` → nano-banana, `openai` → gpt-image-2) |
| `ATLASCLOUD_TEXT_MODEL` | `google/gemini-3.1-pro-preview` | Atlas OpenAI-compatible chat (현재 액터 플로우 미사용) |

#### Actor Creation (스카우트 / 생성 미리보기)

| 기능 | API | 서비스 | 모델 |
|------|-----|--------|------|
| Close up (프로필) | `POST /actors/generate/profile` | **GeminiService** | `GEMINI_IMAGE_MODEL` (`gemini-*`만 요청 모델로 전달; SDK의 `google/nano-banana-2/edit` 등 Atlas 형식은 기본값 폴백) |
| 레퍼런스 시트 (8프레임) | `POST /actors/generate/reference` | **GeminiService** | 동일 |
| 참조 이미지 → 폼 특성 | `POST /actors/analyze-reference` | **GeminiService** (vision) | `GEMINI_TEXT_MODEL` |
| 닮은 연예인 이미지 검색 | `GET /actors/reference-image-search` · `GET /references/image-search` | **SerpApiService** | — |

#### Actor Studio (Node Studio / TextToImage)

| 기능 | API | 서비스 | 모델 |
|------|-----|--------|------|
| 5그룹 프롬프트 재작성 | `POST /actors/studio/rewrite-prompt` | **GrokService** | `GROK_MODEL` |
| NSFW Custom Prompt 제안 | `POST /actors/suggest-nsfw-prompt` | **GrokService** | `GROK_MODEL` |
| 레퍼런스 → 5그룹+nsfw 분석 | `POST /actors/analyze-studio-reference` | **GrokService** (vision) 우선 → 실패 시 **GeminiService** | `GROK_MODEL` / `GEMINI_TEXT_MODEL` |
| 레퍼런스 계정(Instagram) 이미지 | `GET /actors/reference-account-images` | **InstagramService** (Business Discovery) → 실패 시 **SerpApiService** 폴백 | — |
| 텍스트 → 레퍼런스 샷 생성 | `POST /actors/generate-studio-reference-shot` | **AtlasCloudService** | 요청 `imageModel` 또는 `GENERATE_IMAGE_MODULE` 기반 (`google/nano-banana-2` 등) |
| 스튜디오 합성 이미지 | `POST /actors/:key/generate-image` | **AtlasCloudService** | 요청 `imageModel` (기본 `google/nano-banana-2/edit`); NSFW 시 `alibaba/wan-2.7-pro/image-edit` 고정 |
| 갤러리 프롬프트 편집 | `POST /actors/:key/source-assets/by-id/:assetId/edit-with-prompt` | **AtlasCloudService** | `imageProvider` → `openai/gpt-image-2/edit` 또는 `google/nano-banana-2/edit` |
| 스튜디오 프리셋 미리보기 | `POST /actors/:key/generate-studio-preset-preview` | **AtlasCloudService** | `GENERATE_IMAGE_MODULE` 폴백 |
| 모션 비디오 | `POST /actors/:key/generate-motion-video` | **AtlasCloudService** | `kwaivgi/kling-v2.6-pro/motion-control` |

#### Actor 에셋·기타

| 기능 | API | 서비스 | 모델 |
|------|-----|--------|------|
| 포즈·표정 풀세트 | `POST /actors/:key/generate-full-assets` | **AtlasCloudService** | `GENERATE_IMAGE_MODULE` 폴백 |
| 단일 포즈/표정 재생성 | `POST /actors/:key/generate-source-asset` | **AtlasCloudService** | 동일 |

#### Transforms · Images · Content Jobs · Product Insights

| 기능 | API / 모듈 | 서비스 | 모델 |
|------|------------|--------|------|
| Transform 이미지 실행 | `transforms.service` `run` | **AtlasCloudService** | Transform `imageModel` 또는 provider 기본 (`google/nano-banana-2/edit`) |
| NSFW 이미지 변환 | `POST /images/postprocess/nsfw` | **AtlasCloudService** | `alibaba/wan-2.7-pro/image-edit` |
| Content Job — IMAGE | `content-jobs` | **AtlasCloudService** | `gpt-image-2` → Atlas `openai/gpt-image-2` |
| Content Job — VIDEO | `content-jobs` | **GeminiService** | `GEMINI_VIDEO_MODEL` (Veo) |
| Content Job — COPY/SCRIPT/CTA | `content-jobs` | **GeminiService** | `GEMINI_TEXT_MODEL` |
| Product Insights 분석·카피 이미지 | `product-insights` | **GeminiService** / **AtlasCloudService** | 텍스트·이미지 각각 해당 파이프라인 |

#### Atlas Cloud 이미지 모델 선택 규칙 (요약)

SDK·DTO에서 넘기는 `imageModel` 값:

| 요청 모델 | Atlas 실제 모델 |
|-----------|-----------------|
| `openai/gpt-image-2/edit`, `gpt-image-2` | `openai/gpt-image-2(/edit)` |
| `google/nano-banana-2/edit`, `gemini-*` | `google/nano-banana-2(/edit)` |
| `xai/grok-imagine-image-quality/edit` | `xai/grok-imagine-image-quality/edit` |
| `alibaba/wan-2.7-pro/image-edit` | WAN image-edit (NSFW·이미지 편집) |
| 미지정 | `GENERATE_IMAGE_MODULE` (`gemini` → nano-banana, `openai` → gpt-image-2) |

> **구분 요약**: Actor Creation 미리보기(Close up·레퍼런스 시트)와 Vision 분석(`analyze-reference`)은 **Gemini API 직접** 호출. 스튜디오 합성·Transform·NSFW·포즈/표정 에셋·모션 비디오는 **Atlas Cloud** 경유. 프롬프트 재작성·NSFW 제안·스튜디오 레퍼런스 분석(1차)은 **Grok**.

---

## 프론트 앱 요약

| 앱 | 역할 |
|----|------|
| `lampas-web-studio` | 메인 SDK. **Node Studio**(`/studio`)가 생성·합성 허브. Actor/Object/Space/Gallery/Transforms/References |
| `lampas-web-pay` | 크레딧 패키지 결제 (토스 결제위젯). 성공/실패 리다이렉트 |
| `lampas-web-www` | 마케팅 랜딩 (Hero, Features, Products, FAQ …) |
| `lampas-web-admin` | 관리자 콘솔. `VITE_API_URL`로 `/admin/*` 직접 호출 (Vite 프록시 없음) |
| `photobooth-app-toss` | 토스 미니앱 포토부스 (Granite + Vite) |

---

## `lampas-web-studio` — 디렉터리 구조

```
src/
├── main.jsx                 # bootstrap → resolveDomainVariant → <App variant={…} />
├── App.jsx                  # variant별 CSS 로드, BrowserRouter, Toaster, TemplateApp
├── assets/figma-main/       # SVG 아이콘
├── components/
│   ├── atoms/               # 최소 UI 단위 (토큰·클래스·아이콘)
│   │   └── ds/              # fieldClasses, Icons, index
│   ├── molecules/           # 기능 단위 위젯·부분 UI (화면 전체 X)
│   │   ├── actor/           # Delete/Restore/Transfer, creation/ · studio/ 위젯
│   │   │   └── studio/node/ # Node Studio 캔버스·노드·엣지·엔진
│   │   ├── app/             # AppMainNav, AppFooter, Loading, ScopeSwitcher, Workspace* …
│   │   ├── auth/            # LoginRequiredPrompt 등
│   │   ├── forms/           # StudioField, StudioInput, StudioSelect …
│   │   ├── gallery/ object/ space/ references/ resources/ transform/
│   │   └── utils/           # ScrollToTop
│   ├── organisms/           # 화면 단위 컴포넌트 + 레이아웃
│   │   ├── actor/           # ActorSelection, ActorTrash, ActorView
│   │   │   └── studio/      # NodeStudio (메인), TextToImage, ImageToVideo, ReferenceToImage
│   │   ├── gallery/         # Gallery, GalleryView, InstagramPublishDialog
│   │   ├── home/            # Home (SDK), Playground (playground variant)
│   │   ├── layouts/         # Layout, LayoutPlayground
│   │   ├── legal/           # 약관·마케팅 동의 콘텐츠
│   │   ├── object/          # ObjectSelection, ObjectView
│   │   ├── space/           # SpaceSelection, SpaceView
│   │   ├── references/      # ReferenceSearch
│   │   ├── resources/       # ResourceList, ResourceView
│   │   └── transform/       # TransformsList, TransformRun
│   └── pages/               # 라우트 진입점 (얇은 wrapper → organism)
├── contexts/
│   ├── app.jsx / auth.jsx / scope.jsx   # 앱·인증·워크스페이스 스코프
│   ├── sdk.jsx              # SDKContext, SDKBridge, iframe postMessage
│   └── variant.tsx          # VariantProvider, useVariant()
├── services/
│   ├── api.js               # axios API 클라이언트
│   └── auth.js              # 인증 관련 API
├── templates/               # variant별 앱 셸 (라우트·홈·CSS만 분기)
│   ├── sdk/                 # index.jsx, index.css — sdk.lampas.io
│   └── playground/          # index.tsx, index.css — 로컬·playground
└── types/
    └── variant.ts           # Variant enum, resolveDomainVariant(host)
```

### Node Studio (메인 제작 플로우)

- **SoT:** 스튜디오 UI 기능은 `apps/dalar-web-app` (`src/apps/studio/.../actor/studio`)에만 추가한다. lampas 반영은 `pnpm sync:studio` (`scripts/sync-studio-from-dalar.mjs`). 스펙: `docs/superpowers/specs/2026-08-02-studio-sync-dalar-to-lampas-design.md`.
- 라우트: `/works` (목록), `/works/:workKey` (빈 캔버스), `/{actors|objects|spaces}/works/:entityKey` (엔티티 주인공).
- 구현: `organisms/actor/studio/NodeStudio.jsx` + `molecules/actor/studio/node/*`.
- 레거시 경로 `/actors/studio/:key`, `/objects/studio/:key`, `/spaces/studio/:key`, 생성·Transform 생성 URL은 **리다이렉트** (`StudioRedirects.jsx` / `Navigate`).
- 생성(스카우트)·등록·촬영·시나리오·Transform 등은 노드 그래프에서 처리. 구 `ActorCreation` / 독립 Creation 페이지 organism은 제거됨 (생성 UI 조각은 `molecules/actor/creation/` 등에 잔존·재사용).
- 시나리오 생성 API: `POST /actors/studio/generate-scenario` (lampas-api).

### Atomic Design 계층 규칙

| 계층 | 배치 기준 | 예 |
|------|-----------|-----|
| **atoms** | 스타일 토큰, 아이콘, 재사용 가능한 최소 단위 | `atoms/ds/Icons.jsx`, `fieldClasses.js` |
| **molecules** | 도메인·폼·기능 단위 위젯 (화면의 일부) | `molecules/actor/studio/node/nodes/ShootNode.jsx`, `molecules/forms/` |
| **organisms** | 화면 단위 컴포넌트, 레이아웃 | `organisms/actor/studio/NodeStudio.jsx`, `organisms/layouts/Layout.jsx` |
| **pages** | React Router 진입점만. 상태 전달·라우트 파라미터 처리 후 organism에 위임 | `pages/StudioPage.jsx` |

- 새 화면: **`pages/` 에 라우트 wrapper** → **`organisms/` 에 화면 UI** 추가, 화면을 구성하는 위젯은 `molecules/` 에 분리.
- 폼 필드는 `molecules/forms/` 재사용. DS 클래스는 `atoms/ds/fieldClasses.js`.
- **`components/ds/`, `src/pages/` 등 구 경로는 사용하지 않는다.**

### Variant · Template

호스트명으로 variant를 결정하고, variant마다 **템플릿(라우트·홈·CSS)** 만 분기한다.

| Variant | 도메인 | 홈 | SDKContext.mode |
|---------|--------|-----|-----------------|
| `PLAYGROUND` | 기본(로컬 등) | `Playground` | `playground` |
| `SDK` | `sdk.lampas.io` | `Home` | `sdk` |

- `main.jsx` → `resolveDomainVariant(host)` → `App` 에 `variant` 전달.
- `App.jsx` → variant별 `@/templates/{sdk|playground}/index.css` 동적 import.
- 라우트 정의는 `templates/sdk/index.jsx`, `templates/playground/index.tsx` 에 각각 있으며 경로는 동일 (`/`, `/studio`, `/actors`, `/gallery`, `/spaces`, `/references/search` …).
- variant 전역 접근: `useVariant()` (`contexts/variant.tsx`).
- iframe 임베드 SDK 통신: `useSDK()` (`contexts/sdk.jsx`).

### import · 경로

- **`@/`** → `src/` (`vite.config.js` alias, `jsconfig.json` paths).
- 상대 경로 대신 `@/components/...`, `@/contexts/...` 사용.
- barrel export: `atoms/ds/index.js`, `molecules/forms/index.js`.

---

## `lampas-web-studio` UI

### 버튼은 플랫(Flat) — 최우선

`<button>` 및 클릭 가능한 UI는 **단색 면 + 얇은 보더**로 만든다.

- **채우기**: `bg-primary`, `bg-surface-container-highest`, `bg-error` 등 토큰 단색. **`bg-gradient-*`로 버튼 면을 채우지 않는다.**
- **호버**: `transition-colors`로 `background-color` / `border-color` / `opacity`만 바꾼다. **글로우(`shadow-[0_0_…]`), `active:scale-*`는 쓰지 않는다.**
- **형태**: `rounded-xl` 또는 `rounded-lg` 위주. 아이콘-only는 `rounded-lg` + 명확한 `hover:bg-*`.
- **포커스**: `focus-visible:ring-*` 또는 `focus-visible:outline-none` + 링은 접근성용으로만 최소한.

### 그라데이션·유리 효과 (버튼 제외)

- **이미지 카드 오버레이**, 배너 배경 등에는 `bg-gradient-to-t …` 등을 **사용 가능**.
- **내비게이션 바**에 `backdrop-blur`·반투명 배경 사용 가능.
- 위 효과를 **버튼 배경**에 가져오지 않는다.

### 그 외 플랫 유지

- 본문·폼 필드는 단색·얇은 보더.
- 카드·이미지의 `hover:-translate-y-*`, 줌 등은 **목록 카드 등 의도된 곳**에만 제한.

### 스타일 파일

- 전역 CSS는 variant별 `templates/sdk/index.css`, `templates/playground/index.css` 에 둔다.
- **`src/index.css` 는 사용하지 않는다** (템플릿으로 이전됨).

---

## 작업 시 체크리스트

1. 컴포넌트 추가 위치가 Atomic Design 계층에 맞는지 확인.
2. import는 `@/` alias 사용.
3. 라우트 추가 시 **sdk·playground 템플릿 둘 다** 갱신.
4. 버튼·클릭 UI는 플랫 스타일 준수.
5. API 연동 시 `services/api.js` 패턴과 `lampas-api` DTO 필드명 일치 확인.
6. 로컬 포트·크로스앱 URL(`VITE_STUDIO_URL` 등)이 위 포트표와 일치하는지 확인.

</INSTRUCTIONS>
<environment_context>
  <cwd>/Users/progdesigner/Works/lampas/lampas-system</cwd>
  <shell>zsh</shell>
  <current_date>2026-09-26</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

lampas-browser 를 만들고 싶어, 크롬 기반으로 Aside 처럼 어떻게 만들어야 할까?

## assistant

가능해. **Lampas Browser는 Electron으로 Chromium 기반 MVP를 만들고, 브라우저 고유 기능이 더 필요해지면 Chromium 포크를 검토하는 방향**을 추천해.

다만 “Aside”가 어떤 제품인지에 따라 UI와 구현 범위는 달라져. 우선 **사이드바에 탭·워크스페이스·AI 도우미를 배치하는 브라우저**를 생각한 것으로 보고 설명할게.

### 1. Chromium을 사용하는 두 가지 방법

| 방식 | 장점 | 부담 | 추천 시점 |
|---|---|---|---|
| **Electron** | Chromium 포함, React 활용, 빠른 데스크톱 앱 개발 | Chrome 확장·프로필·동기화를 직접 보완해야 함 | MVP와 초기 제품 |
| **Chromium 포크** | 브라우저 엔진과 UI를 깊이 수정 가능 | 빌드·배포·보안 업데이트 유지 비용이 매우 큼 | 전담 브라우저 팀이 있을 때 |

Electron을 사용해도 웹사이트는 실제 Chromium 엔진으로 실행돼. 다만 **Chrome을 그대로 가져오는 것은 아니므로** Chrome 확장 전체 호환, Google 계정 동기화, 일부 DRM 콘텐츠 지원이 자동으로 따라오지는 않아.

### 2. Lampas에 맞는 구조

현재 모노레포에 데스크톱 앱을 추가하면 돼.

```text
apps/lampas-browser/
├── src/
│   ├── main/          # Electron: 창·탭·세션·다운로드 관리
│   ├── preload/       # UI에서 허용된 데스크톱 기능만 호출
│   └── renderer/      # React: 사이드바·주소창·설정·AI 패널
├── electron-builder.yml
└── package.json
```

화면은 이런 구성이 자연스러워.

```text
┌──────────────┬─────────────────────────┬────────────────┐
│ 워크스페이스 │ 주소창 / 이전 / 새로고침 │ Lampas AI      │
│              ├─────────────────────────┤                │
│ 고정 탭      │                         │ 페이지 요약    │
│ 열린 탭      │ 실제 웹페이지           │ 선택 영역 질문 │
│              │ Chromium WebContentsView│ 작업 실행      │
│ 방문 기록    │                         │                │
└──────────────┴─────────────────────────┴────────────────┘
```

핵심은 **웹페이지를 iframe으로 넣지 않고, 탭마다 `WebContentsView`를 사용하는 것**이야. React는 브라우저 UI를 담당하고 Electron 메인 프로세스가 웹페이지와 탭의 수명주기를 관리해.

### 3. 먼저 만들어야 할 MVP

첫 버전은 다음 정도로 좁히는 게 좋아.

- **브라우징:** 주소창, 검색, 이전·다음, 새로고침, 새 창 처리
- **사이드바 탭:** 생성·닫기·전환·고정, 마지막 세션 복원
- **워크스페이스:** 프로젝트별 탭 묶음
- **기본 사용성:** 다운로드, 방문 기록, 북마크, 단축키
- **Lampas AI:** 현재 페이지 요약, 선택 텍스트 질문, Lampas에 저장

워크스페이스는 먼저 **탭을 정리하는 단위**로 만들고, 로그인·쿠키를 분리하는 **프로필**은 별도 개념으로 두는 게 좋아. 프로젝트를 바꿀 때마다 로그인을 다시 해야 하는 문제를 피할 수 있어.

기존 `lampas-api`의 인증·워크스페이스·크레딧을 재사용하면 AI 기능을 기존 Lampas 서비스와 연결할 수 있어. 데스크톱 인증은 시스템 브라우저와 PKCE 또는 일회성 코드 교환으로 연결하는 편이 적합해.

### 4. 설계에서 중요한 부분

**웹사이트와 앱 권한을 철저히 분리해야 해.** 외부 페이지에는 Node.js나 파일 시스템 접근을 주지 않고, sandbox와 context isolation을 켜야 해. IPC도 앱 UI에서 허용한 명령만 실행하도록 제한해야 하고.

**AI가 페이지를 읽는 것과 조작하는 것은 별도 단계로 나누는 게 좋아.** 처음에는 사용자가 요청한 페이지의 본문·선택 영역만 전달하고, 이후 클릭·입력·탐색을 추가하면 돼. 페이지 안의 문구를 AI의 명령으로 취급하지 않는 경계도 필요해.

**탭이 많아지면 메모리 관리가 필요해.** 비활성 탭 휴면, 복원, 크래시 복구를 단계적으로 넣어야 하고, 배포 후에는 Electron/Chromium 보안 업데이트와 앱 자동 업데이트를 계속 유지해야 해.

내 추천은 **React + TypeScript + Electron + WebContentsView로 macOS 우선 MVP**를 만드는 거야. 첫 목표는 “일상적인 웹 탐색이 되는 사이드바 브라우저 + 현재 페이지와 대화하는 Lampas AI” 정도가 적당해.

말한 **Aside의 링크나 정확한 제품명**을 알려주면, 그 제품의 어떤 경험을 구현하려는지 기준으로 구조를 더 구체화할 수 있어.

## user

1. 일단 기본 브라우저를 먼저 만들고
2. ai 창을 클로드나 GPT 를 연동해서 화면에 보이는 데이터 수집이나, 영상 다운로드, 스프레드 시트 작업 같은 게 되어야 하고.
3. lampas-system 과 연계해서 브라우저로 ai 를 실행하고 컨트롤 할 수 있어야 하고, 다시 데이터를 보낼 수 도 있어야해.

## assistant

말한 범위라면 브라우저에 AI 패널을 붙이는 것뿐 아니라, Lampas가 작업을 보내고 결과를 돌려받는 실행 구조가 필요해. 저장소의 연동 지점과 OpenAI 문서 스킬을 확인해서 기본 브라우저부터 확장할 수 있는 구조로 정리할게.

## assistant

그 요구사항이면 **Lampas Browser를 “웹 탐색 + AI 작업 실행 + Lampas 양방향 연결”을 제공하는 데스크톱 앱**으로 설계하면 돼. 기본 브라우저를 먼저 만들되, 처음부터 AI가 사용할 브라우저 제어 기능을 분리해 두는 게 핵심이야.

**1. 기본 브라우저**

`apps/lampas-browser`에 **Electron + React + TypeScript**로 만드는 방향을 추천해.

- 사이드바 탭, 주소창, 이전·다음·새로고침
- 로그인 세션 유지, 탭 복원
- 북마크, 방문 기록, 다운로드
- 프로젝트별 워크스페이스
- 오른쪽 AI 패널

실제 웹페이지는 탭마다 `WebContentsView`로 실행하고, 탭 생성·페이지 이동·본문 읽기 등의 기능은 공통 제어 모듈로 만들어. 사람의 버튼 클릭과 AI의 명령이 같은 기능을 사용하도록 하는 거야.

**2. AI 패널과 작업 실행**

Claude/GPT에 **브라우저를 읽고 조작하는 도구**를 연결해야 해. 모델이 작업을 판단하고, 실제 실행은 Lampas Browser가 맡는 구조야. OpenAI도 [이런 실행 구조를 공식 지원](https://developers.openai.com/api/docs/guides/tools-computer-use)해.

| 원하는 기능 | 구현 방향 |
|---|---|
| 화면에 보이는 데이터 수집 | 본문·표·링크 추출, 화면 이미지 분석 보완 |
| 여러 페이지 조사 | 탭 이동·스크롤·페이지 넘김 후 결과 통합 |
| 영상 다운로드 | 직접 미디어 다운로드부터 구현하고, 사이트별 지원 확장 |
| 스프레드시트 작업 | CSV/XLSX 생성·편집, Google Sheets API 연동 |
| 웹사이트 작업 | 요소 클릭·입력·파일 업로드·실행 결과 확인 |
| Lampas AI 실행 | 기존 Lampas API 호출, 생성 상태 조회, 결과 저장 |

스프레드시트는 API나 파일 편집을 우선 사용하고, API가 없는 작업에 화면 조작을 쓰는 게 정확해. 영상 다운로드도 사이트마다 방식이 달라서 **지원 가능한 소스부터 늘려가는 기능**으로 잡아야 해. DRM 영상까지 범용으로 다운로드할 수 있는 건 아니야.

AI 패널은 자체 채팅 UI에 모델 API를 연결하는 방식을 기본으로 추천해. **Claude·ChatGPT 웹사이트를 직접 열어 조작하는 기능**이 필요하면 별도 사이트 연동으로 추가할 수 있지만, 로그인 상태나 사이트 UI 변경에 영향을 받아.

**3. Lampas와 양방향으로 연결**

연결은 두 방향 모두 지원해야 해.

```text
Lampas에서 작업 요청
  → 연결된 Browser가 작업 수신
  → AI가 페이지 탐색·수집·다운로드·편집
  → 진행 상황과 결과를 Lampas로 전송

Browser에서 작업 요청
  → Lampas API로 이미지·영상 등 생성
  → 생성 상태 확인
  → 결과를 Browser에서 사용하거나 Lampas에 저장
```

브라우저가 로그인 후 서버에 **인증된 WebSocket 연결을 먼저 맺도록** 만들면 돼. 서버가 사용자의 PC에 직접 접속할 필요 없이 그 연결로 작업을 전달할 수 있어.

작업에는 다음 정보를 붙여야 해.

- 어느 사용자·워크스페이스·브라우저가 실행하는지
- 작업 ID, 진행 상태, 중지·취소 상태
- 결과 데이터, 원본 URL, 생성·다운로드 파일
- 연결이 끊겼다가 복구돼도 같은 작업을 중복 실행하지 않는 처리

큰 영상이나 파일은 별도로 업로드하고, 작업 결과에는 파일 참조를 보내면 돼. 로컬 브라우저에서 실행하는 작업은 앱이 켜져 있고 연결되어 있어야 해.

저장소의 `lampas-api-mcp`에는 이미 채팅·이미지·영상 생성과 미디어 업로드를 호출하는 코드가 있어. **이 API 계약을 재사용하고, 서버에서 브라우저로 작업을 보내는 부분을 새로 추가**하는 구성이 적합해.

**개발 순서는 이렇게 잡으면 돼.**

| 단계 | 완료 기준 |
|---|---|
| 1. 기본 브라우저 | 여러 탭에서 탐색·로그인·다운로드하고 재시작 시 복원 |
| 2. AI 읽기 기능 | 현재 페이지의 내용을 질문하고 표를 CSV로 저장 |
| 3. AI 실행 기능 | 여러 페이지를 수집하고 시트·파일 작업 수행 |
| 4. Lampas 연결 | Lampas에서 작업 요청 → 브라우저 실행 → 결과 자동 등록 |

예를 들면 최종적으로 **“이 제품 페이지들의 정보를 시트로 정리하고, 이미지를 Lampas 레퍼런스로 저장한 뒤 광고 이미지를 생성해”**라는 작업을 한 흐름으로 실행하는 거야.

첫 구현 범위는 **기본 브라우저 + 공통 브라우저 제어 모듈**로 잡는 게 좋아. 그러면 이후 AI 패널과 Lampas 원격 실행을 같은 기반 위에 붙일 수 있어.

## user

구축해줘

## assistant

기본 브라우저부터 실제 실행 가능한 형태로 구축하고, AI 패널과 Lampas 연동을 붙일 수 있는 제어 모듈까지 연결할게. 먼저 저장소 지침과 기존 API·인증 구조를 확인하겠어.

## assistant

기존 Lampas AI 게이트웨이의 생성·업로드 API를 재사용할 수 있어. 이번 구현에는 탭·세션 복원, Claude/GPT 작업 패널, 페이지·표 추출, CSV 편집·저장, 직접 영상 다운로드와 양방향 작업 연결을 넣겠어. 원격 작업은 브라우저가 서버에 연결하는 방식으로 구성하고, 진행 상태와 결과를 다시 조회할 수 있게 만들게.

## assistant

브라우저 엔진과 AI 도구 실행 부분을 작성했어. 웹페이지는 앱 권한과 분리해서 실행하고, 수집한 표는 편집 가능한 시트로 보내도록 연결했어. 지금은 원격 작업의 인증·중복 실행 방지·결과 반환과 설정 화면을 붙이고 있어.

## assistant

연동 점검 중 기존 Lampas 게이트웨이가 AI의 도구 호출 응답을 누락하는 부분을 발견해서 함께 수정했어. 이제 화면 빌드와 타입 검사를 진행하고, 실제 Electron 실행에서 탭 이동·표 수집·세션 복원과 원격 작업 흐름을 검증하겠어.

## assistant

인증·원격 작업·AI 도구 호출 테스트와 게이트웨이 회귀 테스트는 통과했어. 실제 앱에서도 페이지 수집과 CSV 저장까지 확인했고, AI 응답 후 화면 전환에서 발생하는 오류를 잡고 있어. 이 부분까지 고친 뒤 세션 복원과 실행 명령을 최종 확인할게.

## assistant

실제 Electron 앱에서 탭 탐색, 표 편집·CSV 저장, 영상 파일 다운로드, 암호화된 설정 저장과 재시작 복원이 통과했어. 원격 서버에서 작업을 보내 브라우저가 실행하고 시트 결과를 돌려주는 흐름도 확인했어. 마지막으로 기존 Lampas MCP에서도 이 브라우저에 작업을 요청할 수 있게 연결하고, 로컬 앱 번들을 정리할게.

## assistant

MCP 작업 요청·조회·중지까지 연결했고, 타입 검사와 24개 테스트가 통과했어. macOS 앱 번들도 생성했어. AI API 호출은 비용 없이 모의 응답으로 검증했으므로, 실제 사용 시 설정에서 API 키와 모델 ID를 입력하면 돼. 영상은 직접 URL 다운로드, 스프레드시트는 CSV 편집·저장까지 지원해.

## assistant

실행 가능한 **Lampas Browser MVP**를 구축했어.

- Chromium 탭·워크스페이스·북마크·세션 복원
- GPT/Claude AI 패널과 페이지 수집·조작
- 시트 편집·CSV 저장·직접 영상 URL 다운로드
- Lampas 생성 API 연동과 Relay/MCP 양방향 작업 제어

실행:
```bash
pnpm dev:lampas:browser
```

설정에서 **API 키와 모델 ID**를 입력하면 돼. 실제 AI 호출은 모의 응답으로 검증했고, 타입 검사·빌드·24개 테스트·Electron 통합 테스트가 통과했어.

[실행·연동 가이드](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-browser/README.md)

Google Sheets 연동과 스트리밍/DRM 영상 추출은 아직 미구현이야.

## user

상위 폴더로 가서 
lampas-system 과 lampas-harness 가 있을꺼야 지금 구축한 브라우저는 
lampas-harness 로 옮겨주고 api 기능이 필요하다면 하네스 방식으로 바꿔죠
apps/browser 라고 해주고 harness 에 세 세션 선택시 브라우징 기능을 추가해서 browser 를 원격으로 실행하고 일을 시킬 수 있게 해줘 
어때? 이렇게 옮기고 위 작업도 할 수 있겠어?

## assistant

브라우저를 `lampas-harness/apps/browser`로 옮기고, Harness의 세션에서 브라우저를 실행하고 작업을 요청할 수 있게 연결할게. 먼저 두 저장소의 지침과 Harness의 세션·원격 실행 구조를 확인해서 API 연동도 그 방식에 맞추겠어.

## assistant

Harness는 이미 Codex CLI·Claude Code의 로그인과 세션을 사용하는 구조야. 브라우저의 별도 모델 API와 Relay는 이 구조로 바꾸고, 새 세션에서 **브라우징**을 선택하면 해당 세션 전용 브라우저와 MCP 도구가 연결되도록 만들겠어. 원격 화면 확인과 작업 승인도 Harness 화면에서 처리할 수 있게 할게.

## assistant

브라우저 소스는 `lampas-harness/apps/browser`로 옮겼고, Harness에 새 세션의 **브라우징** 선택과 원격 화면·표 수집·결과 파일 패널을 추가했어. 세션마다 브라우저 프로필과 제어 권한을 분리했으며, AI는 기존 CLI 로그인으로 작업하도록 바꿨어. 이제 세션 생성부터 브라우저 실행·승인·파일 반환까지 실제로 검증하고 있어.

## assistant

Harness 전체 테스트 76개가 통과했어. 지금은 실제 Electron 브라우저를 띄워 페이지 읽기, 승인 후 작업 실행, CSV·다운로드 파일 반환까지 연결되는지 확인하고 있어. AI 호출은 기존 Claude/Codex CLI 세션을 사용하도록 연결했어.


## user

작업 잘 되고 있나?

## assistant

응, 잘 진행되고 있어. `lampas-harness/apps/browser`로 이동했고, 새 세션에서 **브라우징**을 선택하면 전용 브라우저가 실행되도록 연결했어. Harness에서 화면 확인·작업 승인·결과 파일 다운로드도 할 수 있게 만들었어.

전체 테스트 76개는 통과했어. 지금 실제 브라우저로 수집과 파일 반환을 검증하고 있어. 실제 Claude/Codex 모델에 지시하는 테스트는 아직이라, 그 부분까지 확인된 것으로 말하진 않을게.


## assistant

브라우저와 Harness의 타입 검사·빌드도 통과했어. 실제 연결 검증에서는 테스트용 Chrome과 Electron을 따로 띄워 확인 중이야. 원격 실행은 **Harness가 돌아가는 컴퓨터에서 브라우저를 실행하고, 다른 기기의 Harness 화면에서 제어하는 방식**으로 구현했어.


## assistant

실제 브라우저 통합 검증도 통과했어. Harness에서 브라우징 세션을 만들고, 페이지의 표를 수집한 뒤 MCP로 시트를 수정하고, 승인 후 CSV·영상 파일을 받아오는 흐름까지 확인했어. 검증 중 처음 화면을 캡처할 때 간헐적으로 오류가 표시돼서, 시작 직후 화면 준비 처리도 보완하고 있어.


## user

다시

## assistant

이어서 진행할게. 화면 캡처와 종료 처리를 보완했고, 수정한 버전으로 빌드와 실제 브라우징 흐름을 다시 검증 중이야.


## assistant

이동과 Harness 연동을 완료했어.

- 위치: `lampas-harness/apps/browser`
- **새 세션 → 브라우징 → Claude/Codex 선택**으로 실행
- Harness에서 화면 확인, 페이지·표 수집, 작업 승인, CSV·직접 영상 파일 다운로드 지원
- 별도 AI API 대신 기존 CLI 로그인과 세션 전용 MCP 사용

실제 Electron·MCP·웹 화면 통합 검증과 빌드는 통과했어. 실제 모델 호출은 아직 검증하지 않았고, 전체 테스트는 79개 중 브라우저와 별개인 위키 테스트 1개가 실패했어.

실행 중인 Harness는 재시작하면 반영돼. [사용 안내](/Users/progdesigner/Works/lampas/lampas-harness/apps/browser/README.md)

## user

https://lampas-harness.tail43b73a.ts.net/wiki.html 로 가도 위키도 안 나오고 
새 세션을 열어도 브라우징이 없엇 확인해줘

## assistant

접속한 주소의 서버가 최신 빌드를 제공하는지 확인할게. `/wiki.html` 경로와 새 세션의 브라우징 옵션이 안 보이는 원인을 확인하고 반영하겠어.


## assistant

원인을 확인했어. 접속 주소는 9월 24일에 시작된 구버전 서버로 연결되고 있어. 최신 빌드 파일은 있지만 서버에 로드되지 않아서 `/wiki.html`은 404이고, 브라우징 기능도 제공하지 않는 상태야.

현재 실행 중인 CLI 세션이 3개 있어서 재시작하면 이 세션들이 중단돼. 먼저 최신 빌드의 위키 경로와 브라우징 옵션을 별도 검증 서버에서 확인할게.


## assistant

원인은 **구버전 서버가 계속 실행 중인 것**이야. 실제 주소에서 위키는 404, 브라우징 기능은 비활성인 걸 확인했어.

최신 빌드를 별도로 실행하면 **위키 186개 문서와 브라우징 선택 모두 정상**이야. 서버 재시작만 남았어.

**지금 재시작해도 될까?** 현재 실행 중인 CLI 세션 3개가 중단되기 때문에 확인이 필요해. 기존 자동 재시작도 이 세션들이 끝나기를 기다리고 있어.

## user

재시작해줘

## assistant

Harness 서버를 재시작하고, 실제 접속 주소에서 위키와 브라우징 옵션이 정상인지 확인할게.

