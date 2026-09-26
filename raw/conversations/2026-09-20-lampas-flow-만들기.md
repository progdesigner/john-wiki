# Lampas Flow 만들기

Tool: codex
Started: 2026-09-20T11:52:53.379Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.codex/sessions/2026/09/20/rollout-2026-09-20T20-52-53-01a0bea9-7c4c-7110-a4b0-c760c89d0c15.jsonl

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
│   ├── talk-app-toss-mina/    # Talk 토스 미니앱 (Mina)
│   ├── talk-app-toss-brainrot/# Talk 토스 미니앱 (Brainrot)
│   ├── talk-web-www/          # Talk 랜딩
│   ├── talk-web-app/          # Talk 웹 앱
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
| `talk-web-admin` | **8243** | `pnpm dev:talk:web:admin` |
| `talk-app-toss-mina` | **8245** | `pnpm dev:talk:app:mina` |
| `talk-app-toss-brainrot` | **8246** | `pnpm dev:talk:app:brainrot` |
| `talk-api` | **3241** | `pnpm dev:talk:api` |
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
  <current_date>2026-09-20</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

1. 맥미니에 있는 lampas-agent 에게 특정 유투브 영상에 대해서 영상 수집을 tailscale 을 통해 요청
2. clips 에서 특정 소스의 특정 클립만 폴더링을 한뒤 
3. copy 에서 해당 클립들의 추천 페르소나로 copy 들을 만들고 
4. reels 에서 clips 과 copy 를 선택해서 편집 그룹을 만든 뒤 
5. edit 에서 clips 과 copy 를 바탕으로 특정 템플릿으로 작업을 끝낸 뒤 내보내기 하고
6. reels 에 갤러리에 있는 영상을 선택해서 package 로 보내고 

이런 작업들을 하나의 node 형태로 연결해서 작업 상태와 각각의 작업 공간으로 이동하여 업데이트 하고 동기화 하여 전체 flow 를 하나로 보고 진행 상황을 알 수 있게 하는 
flow.lampas.io 를 만들어줘 
그래서 Work 라는 개념으로 생성하면 각각 서비스별로 node 가 생성할 수 있어서 1~6번 까지 기능을 진행할 수 있게, 진행상황도 한눈에 알 수 있게 만들어줘

## assistant

기존 clips·copy·reels·edit 서비스와 lampas-agent 연동 구조를 확인하고, Work 안에서 서비스별 노드를 연결해 진행 상황과 작업 공간을 함께 관리하는 `flow.lampas.io`를 구현하겠습니다.


## assistant

기존 서비스가 클립·카피·편집 그룹·편집 세션·내보낸 영상을 각각 저장하고 있습니다. Flow는 이 실제 결과물을 Work 노드에 연결하고, 서비스에서 작업한 뒤 돌아오면 상태를 다시 조회하도록 만들겠습니다. 완료 여부는 연결된 결과물 기준으로 표시하겠습니다.


## assistant

Work 생성 시 6단계 기본 흐름을 만들고, 노드를 추가하거나 분기할 수 있게 구성하겠습니다. 각 노드에는 연결된 소스·폴더·카피·편집 세션·영상과 작업 공간 바로가기를 표시합니다. 현재 Agent의 클립 수집은 브라우저 작업 화면에서 실행되므로, Tailscale로 해당 화면에 유튜브 주소를 전달하고 결과 소스를 Work에 연결하는 방식으로 연동하겠습니다.


## user

> 맥미니 lampas-agent에 접속하는 Tailscale HTTPS 주소가 있으면 알려주세요. 주소가 아직 없으면 Flow의 Agent 연결 설정에서 입력할 수 있도록 만들겠습니다.

https://lampas-system.tail0e32ab.ts.net/

## assistant

Agent 기본 주소를 `https://lampas-system.tail0e32ab.ts.net/`로 설정하겠습니다. Flow에서 영상 수집 노드를 열면 이 주소의 수집 화면으로 이동하도록 연결합니다.


## assistant

Work 저장 API와 노드 캔버스를 추가했습니다. 다른 탭의 변경을 덮어쓰지 않도록 저장 충돌을 검사하고, 삭제된 결과물은 ‘확인 필요’로 표시하게 했습니다. 이제 각 서비스에서 생성한 결과를 원래 Work에 다시 연결하는 동작과 노드 상세 패널을 붙이고 있습니다.


## assistant

Clips에서 폴더로 이동한 클립, Copy에서 만든 카피, Reels에서 만든 편집 그룹이 Work로 돌아오도록 연결했습니다. 편집 세션과 내보낸 영상은 기존 연결 정보를 조회해 자동으로 따라갑니다. 결과물 저장 후 Flow 연결만 실패한 경우에는 재생성 없이 연결을 재시도할 수 있게 했습니다.


## assistant

실제 브라우저에서 캔버스 표시, 노드 저장, 분기 추가·연결, 모바일 화면을 확인했습니다. Agent의 `/healthz`도 HTTP 200으로 응답했습니다. 다만 로컬 MySQL은 저장소의 개발 계정으로 접속이 거부되어, DB 테이블 생성과 실제 데이터 연동 검증은 아직 완료하지 못했습니다.


## assistant

7개 앱의 빌드와 기존 관련 테스트 558개가 통과했습니다. 추가로 편집본이 마지막 내보내기보다 새로 바뀌면 완료 표시를 해제하고 재내보내기를 안내하도록 보완했습니다. Flow 도메인·배포 설정과 DB 적용 절차도 정리했으며, 운영 배포는 아직 하지 않았습니다.


## assistant

`lampas-web-flow`를 구현했습니다.

- Work 생성과 6단계 노드 연결·분기·드래그
- 서비스별 작업 공간 이동과 결과물 연결
- 진행률·오류·15초 자동 동기화
- 지정한 Tailscale Agent 주소 연동

7개 앱 빌드, 관련 테스트 578개, 브라우저 UI 검증을 통과했습니다.

[로컬 Flow 열기](http://127.0.0.1:8461)

**운영 배포는 아직입니다.** 로컬 MySQL 접속이 거부되어 DB 테이블 적용과 실제 전체 흐름 검증이 남았습니다. Agent 수집은 작업 탭을 유지해야 합니다.

[구현·배포 안내](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-web-flow/README.md)

## user

배포해줘

## assistant

Flow와 변경된 서비스들을 배포하겠습니다. 먼저 배포 설정과 현재 변경 사항을 확인하고, DB 적용 → API → 웹 서비스 → 맥미니 Agent 순서로 진행하겠습니다.


## assistant

AWS와 운영 API 서버 접속을 확인했습니다. 현재 작업 머신도 Agent가 실행되는 맥미니입니다. API는 Flow 변경만 담은 별도 체크아웃에서 배포하고, 운영 DB에는 `flow_works` 테이블만 추가하겠습니다.


## assistant

운영 DB 테이블 생성과 맥미니 Agent 배포를 완료했습니다. Clips·Copy 등 연동 웹앱도 순서대로 배포 중입니다. Flow의 CloudFront 배포가 생성되어, 현재 HTTPS 도메인 설정이 전파되기를 기다리고 있습니다.


## assistant

`https://flow.lampas.io`가 열리고, 운영 API에서 Work 생성·조회·수정과 저장 충돌 검사가 통과했습니다. 연동 서비스와 Agent 배포도 끝났습니다. 마지막으로 배포된 화면에서 로그인과 Work 표시를 확인하고 있습니다.


## assistant

운영 화면과 Work 저장은 정상입니다. 다만 Google이 새 Flow 도메인의 로그인을 허용하지 않는 것을 확인했습니다. 기존에 등록된 Reels 도메인에서 로그인한 뒤 인증 결과를 Flow로 전달하도록 보완하겠습니다.


## user

Flow 간의 작업 이동 시 지금 로그인된 세션으로 로그인 다시 되도록 수정해줘

## assistant

Flow에서 다른 작업 공간으로 이동할 때 현재 로그인 계정을 자동으로 이어받도록 수정하겠습니다. URL에는 로그인 토큰 대신 짧게 만료되는 일회용 인증 코드를 전달하고, 대상 서비스에서 코드 교환 후 해당 계정으로 로그인되게 하겠습니다. 수정 후 관련 서비스도 다시 배포하겠습니다.


## assistant

서비스마다 로그인 저장소가 분리되어 있어 현재 계정을 이어주는 처리가 필요합니다. Flow에서 작업 공간을 열거나 Work로 돌아올 때 같은 계정으로 연결하고, 대상에 다른 계정이 로그인되어 있어도 이동을 시작한 계정으로 전환되도록 수정하고 있습니다.


## assistant

웹 서비스 간 계정 전달 코드를 반영했습니다. Mac mini Agent는 운영 업로드 API와 로컬 처리 API의 로그인이 따로 필요해서, 운영에서 확인한 계정으로 두 세션을 연결하는 처리도 추가하고 있습니다. 이제 일회용 코드의 만료·재사용 차단과 실제 서비스 이동을 검증하겠습니다.


## assistant

코드 만료·재사용 차단과 다른 계정 세션을 교체하는 테스트가 통과했습니다. 관련 API와 웹을 배포 중이며, 배포가 끝나면 실제 브라우저에서 Clips → Flow 복귀와 Reels → Edit·Package 이동을 확인하겠습니다.


## assistant

운영 브라우저에서 Flow → Clips → Flow 이동과 Copy·Reels·Edit·Package의 계정 전환을 확인했습니다. 대상 서비스에 다른 계정이 로그인되어 있어도 Flow 계정으로 바뀌고, 로그인 코드는 주소에서 제거됩니다. Google 로그인 팝업도 정상 표시되며, 마지막으로 Agent의 로컬·운영 세션 연결을 확인하고 있습니다.


## assistant

Agent까지 검증을 마쳤습니다. Flow 계정으로 Tailscale Agent에 이동하면 로컬 처리용 세션과 운영 업로드용 세션이 모두 연결됩니다. 요청하신 수정은 운영에 반영됐고, 지금은 검증용 계정과 작업을 정리하고 있습니다.


## assistant

수정하고 [flow.lampas.io](https://flow.lampas.io)에 배포했습니다.

- 작업 공간 이동·Flow 복귀 시 현재 계정으로 자동 로그인됩니다.
- 대상에 다른 계정이 로그인되어 있어도 현재 계정으로 전환됩니다.
- Agent의 로컬 처리·운영 업로드 세션도 함께 연결됩니다.

실제 브라우저에서 계정 전환과 Agent 연결까지 검증했습니다.

## user

agent 에서 유투브 다운로드가 느려졌는데 확인해줘

## assistant

Agent의 최근 다운로드 기록과 실행 상태를 확인해서, 속도가 느려진 구간과 원인을 찾아보겠습니다.


## user

> 느려진 유튜브 영상 URL과, 느리게 느껴지는 구간(다운로드 시작 전 대기 / 다운로드 진행 중 / 100% 이후)을 알려주세요.

다운로드 중에 느려짐 그리고 재시도함 결국

## assistant

확인했습니다. 최근 영상은 Mac mini에 파일 저장까지 끝났지만, 그 파일을 브라우저로 보내는 연결이 아직 열려 있습니다. 지금은 유튜브 다운로드 자체보다 Mac mini → 브라우저 전송 구간이 느린지 확인하고 있습니다.


## assistant

병목은 **Mac mini → 브라우저 영상 전송 구간**으로 확인됐습니다.

- 영상 **122MB는 Mac mini에 저장 완료**됐지만, 브라우저 전송이 계속 진행 중입니다.
- 같은 376KB 파일 측정: 로컬 **0.04초**, Tailscale Funnel **6.1초**였습니다.
- 파일 수신 제한은 **10분**이고, 재시도하면 유튜브 다운로드부터 다시 시작하는 구조입니다.

현재 주소는 공개 Funnel을 사용하며, [Funnel에는 대역폭 제한이 있습니다](https://tailscale.com/docs/features/tailscale-funnel).

해결하려면 **영상은 Mac mini에서 S3로 직접 올리고 브라우저는 S3에서 받도록 변경**하는 게 좋습니다. 재시도도 이미 받은 파일을 재사용하도록 바꿔야 합니다.

## user

개선해줘

## assistant

Mac mini가 받은 영상을 S3로 직접 전송하고, 브라우저는 S3에서 받도록 개선하겠습니다. 전송 실패 시 이미 다운로드한 파일을 재사용하도록 수정한 뒤 검증·배포하겠습니다.


## assistant

현재는 브라우저가 큰 영상 파일을 받은 뒤 다시 업로드하고 있어 전송이 중복됩니다. 운영 계정의 S3 저장 경로로 Mac mini가 직접 업로드하도록 바꾸고 있습니다. 화면에서도 ‘유튜브 다운로드’, ‘영상 보관’, ‘브라우저로 가져오기’를 구분해 어디까지 진행됐는지 표시하겠습니다.


## assistant

직접 전송과 재시도 로직을 반영했습니다. 브라우저 수신에 실패해도 S3에 보관된 영상부터 다시 가져오며, 저장소 전송 실패 시에는 Mac mini의 파일을 재사용합니다. 소유권 검사, 중복 다운로드 방지, 실패 후 재개를 테스트하고 있습니다.


## user

배포해서 반영했어?

## assistant

아직 운영 배포 전입니다. 코드 수정과 기능 테스트는 통과했고, 마지막 타입 검사를 마무리하고 있습니다. 이어서 운영 API·Mac mini 처리 API·Agent에 배포하고 실제 S3 전송까지 확인하겠습니다.


## assistant

운영 API와 Mac mini 처리 API, Agent 배포를 진행 중입니다. 반영 후에는 Agent 탭을 새로고침해야 새 전송 방식을 사용합니다. 배포가 끝나는 대로 실제 업로드·수신 경로를 확인하겠습니다.


## assistant

배포는 완료됐습니다. 현재 운영 경로에서 실제 유튜브 다운로드와 S3 전송을 검증하고 있습니다. 이어서 128MB 테스트 파일로 전송 시간을 측정하겠습니다.


## assistant

실제 S3 전송 검증에서 서명된 URL과 중복되는 업로드 헤더 문제를 발견했습니다. 해당 헤더를 제거한 테스트 요청은 정상 업로드됐습니다. 이 보완 사항을 재배포한 뒤 전체 전송을 다시 확인하겠습니다.


## assistant

수정된 운영 경로에서 실제 유튜브 영상 전송이 정상 완료됐습니다. 테스트 영상은 다운로드 5초, S3 보관 약 1초, 브라우저 수신 0.1초였고 원본과 수신 파일의 해시도 일치했습니다. 128MB 대용량 검증을 마저 진행하고 있습니다.


## assistant

128MB 테스트도 통과했습니다. Mac mini의 테스트 브라우저 기준으로 S3 업로드 2.56초, 수신 2.23초였고, 완료된 전송을 재요청해도 같은 저장 파일을 재사용했습니다. 배포와 검증을 마쳤으며 테스트 데이터를 정리하고 있습니다.


## user

Flow 에서 다시 작업중인데, 확인 필요 상태로 뜸 확인해서 개선해줘
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/74ebf1bf-4a2d-4dfb-a027-72713028b320.png"

## assistant

첨부 화면과 Flow의 상태 판정 로직을 확인하겠습니다. 방금 개선한 다운로드·전송 단계가 Flow에 제대로 전달되는지도 함께 점검하겠습니다.


## assistant

화면에는 이전 실행의 ‘수집 취소’ 상태가 남아 있습니다. 재시작한 작업에 Flow 연결 정보가 누락될 수 있는 경로와, 늦게 도착한 취소 알림이 새 진행 상태를 덮어쓸 수 있는 문제를 확인했습니다. 재시작 시 연결을 이어받고, 실행별로 상태를 구분하도록 수정하겠습니다.


## user

전사에서 오래 걸린다 수정해줘
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/88230ef2-e406-4b80-a62a-d8908737fd15.png"

## assistant

전사 지연도 함께 확인하겠습니다. Flow의 재시작 상태 동기화 수정은 이어가면서, 전사가 오디오 추출·업로드·음성 인식 중 어디에서 지연되는지 나눠 점검하겠습니다.


## assistant

전사 시작 시 약 31MB짜리 오디오 처리 엔진도 Funnel로 받고 있어, 진행률이 0%인 채 오래 기다릴 수 있는 구조였습니다. 엔진은 S3에서 직접 받고 오디오는 운영 API로 직접 업로드하도록 바꾸겠습니다. 전사 중 세부 진행 상태를 표시하고, 완료된 구간은 재시도 때 재사용하도록 함께 수정하겠습니다.


## assistant

전사 구간을 최대 2개씩 처리하고, 완료된 구간은 저장해 재시도 때 재사용하도록 수정했습니다. Flow의 이전 취소 알림이 새 실행 상태를 덮어쓰지 않는 테스트도 통과했습니다. 배포 전 브라우저에서 오디오 엔진 로딩까지 확인하겠습니다.


## assistant

Agent 전체 테스트 369개가 통과했고 배포를 진행하고 있습니다. 전사 중에는 오디오 추출·업로드·음성 인식 대기 상태가 표시되며, Flow에도 해당 진행 상태가 전달됩니다.


## assistant

배포 후 브라우저에서 약 31MB 오디오 엔진을 S3로 직접 불러오는 것을 확인했습니다. 맥미니의 테스트 브라우저에서는 엔진 로딩부터 30초 샘플 오디오 추출까지 약 0.75초가 걸렸습니다. 운영 Flow에서도 늦게 도착한 취소 알림을 무시하고 새 실행의 ‘전사 진행 중’ 상태를 유지하는 것을 확인했습니다.


## assistant

수정 후 **배포까지 완료했습니다.** Agent 버전은 `1.0.16`입니다.

- 전사 엔진 로딩·오디오 업로드에서 느린 Funnel 경로 제거
- 최대 2개 구간 병렬 전사, 완료된 구간은 재시도 시 재사용
- 추출·업로드·음성 인식 대기 상태 표시
- 이전 취소 알림 때문에 Flow가 ‘확인 필요’로 바뀌는 문제 수정

테스트와 운영 확인을 마쳤습니다. **Agent 화면을 새로고침하고 기존 작업을 이어서 진행해주세요.**

## user

각 플로우에서 Jev 를 활용해서 대표적인 작업을 자동으로 수행 할 수 있게 해주고 그 작업에 대한 프롬프트를 정리해서 보여줘 그래서 필요하면 그 프롬프트를 수정해서 작업을 개선해서 할 수도 있게 해줘 
그리고 해당 플로우는 첫번째 노드에서 영상만 바꾸면 자동으로 쭉 이뤄질 수 있게 해줘

## assistant

각 노드에 Jev 자동 실행과 수정 가능한 작업 프롬프트를 연결하겠습니다. 기존 서비스의 실행 기능을 확인해서, 첫 노드의 영상만 바꾸면 저장된 설정으로 다음 단계까지 이어지도록 구현하겠습니다.


## assistant

Jev는 현재 저장소에서 선택·평가를 담당하는 모델로 연결돼 있습니다. 이를 클립 선별, 페르소나 추천, 구성·템플릿 선택에 활용하고, 실제 카피 생성과 영상 편집은 각 서비스의 기존 기능으로 실행하겠습니다. 노드마다 적용한 프롬프트와 Jev의 선택 결과를 확인하고 수정할 수 있게 하겠습니다.


## assistant

기본 설정은 대표 클립 3개를 묶어 한국어 카피가 들어간 세로 릴스 1개를 만들고 패키지에 저장하는 형태로 잡겠습니다. 전체 실행과 노드별 실행을 모두 제공하고, 중간에 멈추면 저장된 결과를 재사용해 이어서 실행하도록 연결하고 있습니다.


## assistant

전체 실행을 이어 주는 연결과 프롬프트 편집 UI를 추가했습니다. 기존 Edit에 저장한 계정 템플릿도 선택할 수 있게 연결했습니다. 지금은 재시도 시 카피나 결과물이 중복 생성되지 않는지, 다른 탭이나 이전 실행이 현재 작업을 덮어쓰지 않는지 검증하고 있습니다.


## assistant

자동 실행의 재개·중복 방지 테스트는 통과했습니다. Reels가 저장 과정에서 편집 그룹의 카피 연결 정보를 누락하던 부분도 함께 수정했습니다. 다만 실제 Jev 호출은 인증키 설정을 기다리고 있으며, 그동안 브라우저 렌더링과 배포 검증을 계속 진행하겠습니다.


## user

> 자동 제작의 기본 결과물은 어떤 형태로 할까요? 노드별 프롬프트와 설정은 이후에도 수정할 수 있게 만들겠습니다.

추천 클립 3개 · 한국어 카피 · 세로 9:16 릴스 1개 · 패키지 저장

## assistant

말씀하신 기본값으로 반영했습니다: **추천 클립 3개 → 한국어 카피 → 9:16 릴스 1개 → 패키지 저장**입니다. 실제 Chrome 렌더러로 자막이 포함된 MP4 생성과 갤러리 업로드 요청까지 확인했습니다.


## user

> 운영 API와 로컬 설정에 Jev 인증키가 아직 없습니다. `apps/lampas-api/env/.env.production`에 `TYPESAFE_API_KEY`를 설정해 주시면 실제 Jev 실행까지 검증하겠습니다. 키를 보관한 다른 환경 파일이 있다면 경로만 알려주세요. 키 자체를 대화에 보내실 필요는 없습니다.

apikey_24121f4f7bfea91458c8ef6d1e1d97028c8_8049cf5cbd4f8722a33367ab1b271a340785f42d3729e406b1ab0b83b3fe5a5b

## assistant

전달해 주신 키를 운영 설정에 반영하고 Jev 실제 응답까지 확인하겠습니다. 키는 화면이나 로그에 표시하지 않겠습니다.


## assistant

Jev 실제 API 호출이 성공했고, 테스트 장면에 맞는 페르소나를 약 0.5초 만에 선택했습니다. 인증키는 Git에 포함되지 않는 배포 전용 설정으로 저장했습니다. 이제 운영 화면에서 프롬프트 저장과 자동 실행 시작·정지까지 마지막으로 확인하겠습니다.


## assistant

운영 API에서 Jev 연결, 6개 노드의 프롬프트 저장, 실행·일시정지를 확인했습니다. 화면 검증 중 기본 프롬프트를 늦게 불러오면서 입력 내용을 덮어쓸 수 있는 문제도 발견해 수정했고, 마지막 배포에 반영했습니다.


## assistant

[Flow](https://flow.lampas.io)에 구현·배포했습니다.

- 노드별 **Jev 작업 프롬프트 확인·수정·저장**
- **전체 자동 실행 / 선택 노드 실행 / 일시정지·재개**
- 실행별 프롬프트와 Jev 판단 결과 기록
- 기존 Edit 계정 템플릿 선택 지원

기본 흐름은 **영상 수집 → 추천 클립 3개 → 한국어 카피 → 9:16 릴스 1개 → 패키지 저장**입니다.

첫 Agent 노드에서 **영상 URL을 변경·저장하고 ‘영상으로 전체 자동 실행’**을 누르세요. 자동으로 이동하는 Agent·Edit 화면은 열어 두어야 합니다.

Jev 실제 응답, 프롬프트 저장, MP4 렌더링을 검증했습니다. 실영상으로 전체 6단계를 연속 실행하는 통합 검증은 아직 진행하지 않았습니다.

## user

<environment_context>
  <current_date>2026-09-21</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

이런 오류가 나면 lampas-harness 에 요청해서 개선을 하는 작업도 할 수 있게 하면 좋겠고 Flow 통해서 
일단 이런 오류가 안나게 개선해줘
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/9da49f33-ef22-4892-8cb2-d712fa8c2814.png"

## assistant

첨부한 오류를 확인해 원인을 수정하겠습니다. Flow에서 오류와 실행 정보를 담아 lampas-harness에 개선 작업을 요청할 수 있는 연결 방식도 함께 확인하겠습니다.


## assistant

원인은 자동 실행 요청이 AI 작업이 끝날 때까지 응답을 기다려, Flow의 30초 제한에 걸리는 구조였습니다. 실행 접수는 즉시 응답하고 진행 상태를 따로 조회하도록 바꾸겠습니다. 일시적인 연결 지연은 작업 실패와 구분하고, 실제 오류에는 Harness로 전달할 진단 요청문을 만들 수 있게 하겠습니다.


## assistant

느린 AI 응답을 대기시켜도 실행 접수는 바로 반환되고, 반복 조회가 AI 작업을 중복 실행하지 않는 테스트를 통과했습니다. 배포를 진행하면서 오류 화면의 요청문 생성도 확인하겠습니다. Harness에는 기존 인증 화면을 통해 요청문을 전달하도록 연결해 두었습니다.


## assistant

배포 후 운영 API에서 실행 접수가 **29ms** 만에 반환되고, Jev 작업은 백그라운드에서 완료되는 것을 확인했습니다. 오류 옆에는 **‘Harness에 개선 요청’** 버튼을 추가했습니다. 실행·노드·오류 정보를 담은 요청문을 수정·복사하고 Harness를 열 수 있으며, 실제 전송은 Harness에서 합니다.


## assistant

수정·배포했습니다.

- AI 작업은 백그라운드에서 실행하고 즉시 응답하도록 변경해 **30초 타임아웃 원인 제거**
- 연결 지연 시 자동 재확인하고, 복구되면 이전 오류 표시 해제
- 느린 응답에서도 중복 실행되지 않는 테스트 통과

오류 옆에 **‘Harness에 개선 요청’**도 추가했습니다. 진단 요청문을 수정·복사하고 Harness를 열 수 있습니다. **실제 전송은 Harness에서 붙여넣어 진행**하는 방식입니다.

Flow를 새로고침해 주세요.

## user

pulse 에서 트랜디한 페르소나가 만들어져서 트랜디한 카피가 만들어지게 되어야 하는 데, 페르소나가 트랜디 하지 않는다. 이를 근본적으로 개선해줘

## assistant

Pulse가 페르소나를 만드는 근거와, 그 페르소나가 Copy에 전달되는 과정을 함께 확인하겠습니다. 최신 트렌드 정보가 빠지는 지점과 일반적인 말투로 바뀌는 원인을 찾아, 실제 반응 근거가 페르소나와 카피에 반영되도록 개선하겠습니다.


## assistant

원인을 확인했습니다. Pulse가 게시일 없이 누적 조회수를 주로 보고 있었고, 모델이 인용한 실제 댓글도 저장 과정에서 사라지고 있었습니다. Flow는 페르소나가 하나뿐이면 이를 무시하고 기본 캐릭터를 사용했습니다. 최신 근거·구체적인 말투·카피 예시가 끝까지 전달되도록 이 세 경로를 함께 수정하겠습니다.

## assistant

이제 게시일과 최근 7일간 조회수 증가 속도를 함께 보고, 수집한 날짜만으로 최신 자료라고 판단하지 않도록 바꾸고 있습니다. 페르소나에는 문장 리듬·훅 구조·카피 예시·피해야 할 표현을 저장하고, Copy 생성과 채점에도 같은 정보를 전달합니다. Pulse 화면에서도 최신 근거가 충분한지, 다시 수집해야 하는지 확인할 수 있게 하겠습니다.

## assistant

수정한 경로의 테스트 101개와 Pulse 화면 테스트 28개가 통과했습니다. 댓글 인용과 말투 정보가 실제 JSON 응답 처리 후에도 카피 생성·채점까지 유지되는지 확인했고, 오래된 고조회수 영상보다 최신 근거가 우선되는 것도 검증했습니다. 기존 운영 변경분을 보존한 상태로 API와 Pulse를 배포하고, 운영 반영을 확인하겠습니다.

## assistant

API와 Pulse 배포가 완료됐습니다. API 전체 테스트 1,078개도 통과했고, 운영 화면에 최신성 표시와 말투·카피 예시가 포함된 것을 확인했습니다. 마지막으로 운영 모델을 직접 호출해 새 페르소나가 생성·저장되는지 확인 중입니다. 기존 페르소나는 유지되며, 최신 수집 자료로 다시 생성하면 새 기준이 적용됩니다.

## assistant

개선해서 **Pulse와 API에 배포했습니다.**

- 누적 조회수 대신 **게시일·최근 상승 속도·반응률**로 근거를 우선 선정합니다.
- 페르소나에 **문장 리듬·훅 구조·카피 예시·피할 표현**을 저장하고, Copy 생성과 채점에 전달합니다.
- 실제 댓글 근거가 사라지던 오류와 Flow가 기본 캐릭터로 대체하던 문제를 수정했습니다.
- Pulse에서 최신 근거의 충분 여부와 갱신 필요 상태를 표시합니다.

자동 테스트와 운영 모델의 페르소나 4개 생성·저장까지 확인했습니다.

**기존 페르소나는 최신 수집 클립을 선택해 다시 생성해 주세요.** 새로 생성하는 페르소나부터 개선된 기준이 적용됩니다.

## user

트랜드 분석이라는 워크를 추가했는데, 
이번엔 Pulse 로 특정 키워드의 유투브 영상을 수집하고, 이를 기반으로 pulse.lampas.io 에서 페르소나를 만들고 copy.lampas.io 에서 페르소나별 copy 를 만들어보는 기능 만들어주고
각 페르소나와 copy 에 대해서 평가하는 기능을 넣어서 점점 더 잘 만들 수 있게 해줘
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/7af9723f-363f-494d-abfa-25f1e8929d86.png"

## assistant

추가하신 ‘트랜드 분석’ Work에 키워드 수집 → Pulse 페르소나 생성 → 페르소나별 Copy 생성을 연결하겠습니다. 페르소나와 카피에 남긴 평가가 다음 생성에도 반영되도록, 현재 Flow 구조와 평가 데이터 저장 경로부터 확인하겠습니다.

## assistant

이번 Work에는 `Pulse 키워드 수집 → 페르소나 4개 → 페르소나별 카피 비교` 흐름을 추가하겠습니다. 평가는 AI 점수와 사용자 평점·수정 의견을 구분해 저장하고, 다시 생성할 때 이전 평가와 좋은 예시를 참고하도록 하겠습니다. 원본 영상을 다운로드하지 않아도 수집한 제목·캡션·댓글 근거로 카피를 시험할 수 있게 만들겠습니다.

## assistant

키워드와 생성 프롬프트를 Work에 저장하고, 실험 회차별로 페르소나·카피·평가를 남기는 구조를 연결했습니다. Flow, Pulse, Copy에서 같은 실험 기록을 열 수 있게 작업 중이며, 긴 생성 요청은 백그라운드로 처리해 화면 이동이나 새로고침에도 진행 상태를 확인할 수 있도록 하고 있습니다.

## assistant

평가가 다음 생성 프롬프트에 반영되는 테스트와, AI 채점 실패 시 이미 만든 카피를 보존하고 채점만 재시도하는 테스트가 통과했습니다. Agent에는 최근 7일의 키워드 영상을 수집하는 실행 버튼을 추가했고, 완료된 미션을 해당 Work에 연결하도록 구현했습니다. 이제 실제 화면에서 설정·생성·평가 저장 흐름을 검증하겠습니다.

## assistant

화면에서 빈 Work에 노드를 추가하고, 미션 연결 → 페르소나 표시 → 평가 저장까지 확인했습니다. Flow에는 전체 진행 상태를 보기 쉽도록 결과 목록을 접어 두고, Pulse와 Copy에서는 같은 실험의 상세 결과를 비교할 수 있게 했습니다. API와 Agent부터 배포한 뒤 세 웹 화면을 반영하고, 운영 연결을 점검하겠습니다.

## assistant

배포 후 운영 API를 확인하던 중, 수집 전에도 다음 노드가 ‘시작 가능’으로 표시되는 문제를 발견했습니다. 실행 자체는 차단되고 있었지만 진행 표시도 정확해야 해서 함께 수정하고 있습니다. Pulse 로그인 연동과 평가·결과 데이터 보호도 운영에서 이어서 확인하겠습니다.

## assistant

Flow·Pulse·Copy와 Agent 배포는 완료됐고, 진행 상태 표시 수정본을 API에 반영 중입니다. 이제 테스트 계정으로 운영 Flow에서 Pulse와 Copy를 직접 열어, 재로그인 없이 같은 Work가 표시되는지 확인하고 있습니다. 실험 결과와 평가는 회차별로 남아 다음 결과와 비교할 수 있습니다.

## assistant

구현해서 **Flow·Pulse·Copy·Agent에 배포했습니다.**

‘트랜드 분석’ Work를 열고 **키워드·분야·프롬프트를 저장**하면 시작할 수 있습니다.

1. **Pulse 수집**: 최근 7일 유튜브 영상을 수집하고 Work에 연결
2. **페르소나 생성**: 수집 근거로 4개 생성·AI 평가
3. **카피 실험**: 페르소나별 10개씩 생성·독립 채점
4. **개선 반복**: 각각 평점과 수정 의견을 저장하고, **‘평가 반영 · 다음 실험 생성’**으로 이전 평가를 반영

회차별 결과와 평가를 비교할 수 있고, 생성 프롬프트도 수정할 수 있습니다. 운영에서 **Flow → Pulse → Copy 로그인 유지와 동일 Work 연동**까지 확인했습니다.

## user

# AGENTS.md instructions for /Users/progdesigner/Works/lampas/lampas-system

<INSTRUCTIONS>
These AGENTS.md instructions replace all previously provided AGENTS.md instructions.

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
| `talk-web-admin` | **8243** | `pnpm dev:talk:web:admin` |
| `talk-app-toss-samantha` | **8245** | `pnpm dev:talk:app:samantha` |
| `talk-app-toss-brainrot` | **8246** | `pnpm dev:talk:app:brainrot` |
| `talk-api` | **3241** | `pnpm dev:talk:api` |
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

## user

<environment_context>
  <current_date>2026-09-25</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

시작 버튼이 없다
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/638306c2-ca6b-4e5f-a318-9c13751d3029-image.png"

## assistant

첨부 화면에서 시작 버튼이 보이지 않는 이유를 확인하고, 바로 작업을 시작할 수 있도록 수정하겠습니다.

## assistant

Pulse 노드가 ‘시작 가능’이라고 표시되지만, 오른쪽에는 수집 화면이 아닌 Pulse 작업 공간으로 이동하는 버튼만 있었습니다. 노드 상세에 수집·페르소나 생성·카피 생성 시작 버튼을 넣고, 실행할 수 없는 단계에는 필요한 선행 작업을 표시하겠습니다.

## assistant

노드 상세의 시작 버튼을 확인했습니다. Pulse 버튼은 저장된 키워드를 Agent 수집 화면으로 전달하고, Personas 버튼은 해당 Work의 생성을 실행합니다. 테스트와 브라우저 확인을 마쳤으며, 이번 Flow 변경만 분리해서 배포하겠습니다.

## assistant

수정해서 배포했습니다. 새로고침하면 노드 오른쪽에 **‘키워드 수집 시작’** 버튼이 표시됩니다.

누르면 키워드가 입력된 Agent 화면이 열립니다. 그곳에서 **‘키워드 수집 시작’**을 누르면 됩니다. 페르소나·카피 노드에도 각각 시작 버튼을 추가했습니다.
