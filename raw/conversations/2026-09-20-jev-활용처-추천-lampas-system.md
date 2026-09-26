# Lampas Agent 개선

Tool: codex
Started: 2026-09-20T11:38:22.168Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.codex/sessions/2026/09/20/rollout-2026-09-20T20-38-22-01a0be9c-314a-7591-989a-2c1659c80866.jsonl

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

지금까지 만든 여기 기능 들 중에 Jev 를 활용할게 있는지 찾아보고                                                                                                          
  https://github.com/superagents-lab/jev-search 라든지 활용 예시를 참고해서                                                                                                
  https://docs.typesafe.ai/introduction 에 문서를 참고해서                                                                                                                 
  lampas-system 에 개선할 수 있는것들을 추천해줘 (작업은 이후에)

## assistant

Jev 저장소와 Typesafe 문서를 확인하고, lampas-system의 검색·AI 연동 흐름과 비교해 적용할 만한 개선안을 추려보겠습니다. 코드 수정 없이 추천만 정리하겠습니다.


## assistant

현재 코드에서는 레퍼런스 검색 결과의 관련도 판단과 채팅의 `진행·질문·취소` 분류가 Jev에 잘 맞습니다. 다만 Jev는 정해진 후보를 선택하거나 점수를 매기는 모델이라, 검색어·광고 문구 생성과 이미지 분석은 기존 모델과 역할을 나누는 방향으로 검토하고 있습니다.


## assistant

**활용할 곳이 있습니다. 우선 추천은 ‘레퍼런스 검색 결과 재정렬’, ‘채팅 의도 분류’, ‘트렌드 관련 기사 선별’입니다.** Jev 문서·예제와 현재 API 구현을 비교한 결과이며, 코드 수정이나 설치는 하지 않았습니다.

Jev는 입력을 보고 **선택·판정·점수화하는 모델**입니다. `Choice`는 후보 선택, `Score`는 기준별 평가, `Noul`은 예/아니오 확률을 반환합니다. 생성형 모델과 역할을 나누면 기존 기능에 적용하기 좋습니다. [공식 소개](https://docs.typesafe.ai/introduction)

사용자가 보내준 **jev-search**는 이를 검색에 적용한 예시입니다. 검색은 Search1API가 수행하고, Jev는 검색 소스·기간·검색어 후보를 선택한 뒤 결과의 관련도를 평가합니다. 실제 구현에서도 검색어를 자유롭게 생성하지 않고 **주어진 후보 중 선택**하며, 결과 제목과 요약을 평가합니다. Lampas에서도 이 구조를 참고할 수 있고, Search1API 도입은 별도로 판단하면 됩니다. [예제 저장소](https://github.com/superagents-lab/jev-search), [판정 구현](https://github.com/superagents-lab/jev-search/blob/main/src/lib/typesafe.ts)

| 추천 순위 | 적용 기능 | 기대하는 개선 | 변경 범위 |
|---|---|---|---|
| **1** | 레퍼런스 탐색 | 사용자 의도에 맞는 결과를 먼저 표시 | 검색 결과 후처리 |
| **2** | 채팅 플로우 | 진행·질문·취소를 구분하고 불확실한 판단 처리 | 기존 Grok 역할 일부 분리 |
| **3** | 트렌드 검색 | 동음이의어·무관한 기사 혼입 감소 | 기사 관련도 판정 추가 |
| **4** | 광고 카피·마케팅 방향 | 생성 후보를 기준별로 평가하고 추천 | 생성 후 평가 추가 |
| **5** | 제품 리서치 | 외부 근거를 선별해 제품 분석에 연결 | 검색·근거 저장 기능 확장 |

**1. 레퍼런스 탐색: 가장 먼저 실험할 만합니다**

현재 [reference-explore.service.ts](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-api/src/modules/references/reference-explore.service.ts:217)는 검색 결과에서 URL 유효성과 이미 선택한 이미지 여부를 확인한 후, 앞쪽 결과를 정해진 개수만큼 사용합니다. 이 단계에 사용자 브리프와의 관련도 판단을 넣을 여지가 있습니다.

예를 들어 “차분한 프리미엄 스킨케어 광고, 욕실 배경 제외”라면 다음처럼 개선할 수 있습니다.

- Grok이 탐색 방향과 검색어 후보를 생성합니다.
- 기존 SerpAPI가 검색합니다.
- Jev가 검색 결과의 텍스트를 바탕으로 주제 관련도와 제외 조건 부합 여부를 평가합니다.
- 관련도가 높은 후보부터 보여주고, 판정 실패 시 기존 순서를 사용합니다.

**검색 결과 순서만 바꾸므로 실험 범위가 작고, 사용자의 선택률로 효과를 측정하기 좋습니다.**

단, Jev는 현재 **텍스트 입력만 지원**합니다. 이미지의 조명·구도·색감까지 평가하려면 Gemini/Grok Vision으로 설명을 먼저 만들어야 합니다. 따라서 첫 적용은 제목·설명 기반 관련도부터 시작하는 편이 적절합니다. [지원 입력](https://docs.typesafe.ai/models)

**2. 채팅 플로우: 정해진 선택지 판단을 분리**

현재 [analyzeFlowTurn](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-api/src/modules/orchestration/orchestration.service.ts:487)은 Grok에 다음 작업을 함께 요청합니다.

- `progress / clarify / cancel` 분류
- 사용자에게 보여줄 응답 작성
- 촬영 대상·이름·요청 샷 추출

여기서 **진행 상태와 정해진 대상 선택**은 Jev에 잘 맞습니다.

예를 들어 “그만 만들고 지금까지 한 것만 보여줘”라는 요청에서 플로우 중단 여부를 판단하거나, “인물 없이 제품만”에서 촬영 대상을 선택하는 역할입니다. 설명 문장과 자유로운 촬영 요청 추출은 기존 Grok이 담당하면 됩니다.

`Choice`의 confidence가 낮으면 기존 Grok 분석으로 넘기는 구조도 가능합니다. 다만 confidence는 **정답 보장 수치가 아니라 선택 확률 분포에서 계산한 확신도**이므로, 임계값은 실제 대화로 검증해야 합니다. [Confidence 문서](https://docs.typesafe.ai/confidence)

이 기능은 명확한 선택만 필요한 턴에서 효과가 클 것으로 봅니다. 모든 턴에 Jev와 Grok을 연속 호출하면 오히려 대기시간이 늘 수 있습니다.

**3. 트렌드: 키워드와 기사가 실제로 관련 있는지 판정**

현재 [트렌드 검색 로직](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-api/src/modules/trends/trends-search.lib.ts)은 분야별 사전·규칙을 사용하고, [트렌드 서비스](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-api/src/modules/trends/trends.service.ts:160)는 LLM으로 제목에서 키워드를 유추해 캐시합니다.

Jev는 이 사이에서 다음 판단을 맡길 수 있습니다.

- 이 기사가 사용자가 검색한 대상에 관한 기사인가?
- ‘스포츠·예능·뷰티·AI·기타’ 중 어느 분야인가?
- 유추한 키워드가 기사 제목으로 뒷받침되는가?

예를 들어 스포츠의 “한화”를 찾는데 기업 실적 기사가 섞이거나, “제미나이”의 다른 의미가 섞이는 경우를 줄이는 용도입니다.

**기사 관련도와 트렌드 인기는 별도로 유지하는 것이 좋습니다.** Jev는 포함할 근거를 선별하고, 관심 지수는 기존 기사 수·매체 수·시간 기반 계산을 유지하는 방향입니다. 제목에서 새로운 키워드를 만들어내는 작업 역시 기존 LLM이 담당해야 합니다.

**4. 광고 카피: 생성한 후보 중 무엇을 추천할지 평가**

현재 [product-insights.service.ts](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-api/src/modules/product-insights/product-insights.service.ts:189)에는 마케팅 확장·광고 카피·블로그 생성 흐름이 있습니다.

여기에 생성 후보별 평가를 붙일 수 있습니다.

- 제품 설명과 일치하는가?
- 지정한 타깃의 문제를 구체적으로 다루는가?
- 제공된 자료에 없는 효능이나 수치를 추가했는가?
- 요청한 브랜드 말투에 맞는가?

한 번에 “좋은 광고인가?”를 묻기보다 기준을 나눠 평가하고 코드에서 가중치를 합치는 방식입니다. 공식 문서의 **Composite Scoring**과 맞습니다. [설계 패턴](https://docs.typesafe.ai/patterns)

이렇게 하면 단순히 여러 안을 생성하는 것에서 **조건에 맞는 안을 우선 추천하는 기능**으로 확장할 수 있습니다. 다만 제품 정보 자체의 진실성을 검증하는 기능으로 해석해서는 안 됩니다.

**5. 제품 분석: 외부 근거를 붙인 리서치로 확장**

현재 [제품 분석 진입점](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-api/src/modules/product-insights/product-insights.service.ts:139)은 제품 이미지와 추가 컨텍스트를 Gemini에 전달합니다. 여기에 jev-search 방식의 검색 단계를 붙이는 확장도 가능합니다.

예를 들어 “이 선크림의 경쟁 제품과 소비자가 불편해하는 점을 찾아줘”라면:

1. 기존 모델이 검색어 후보를 만듭니다.
2. 검색 API가 공식 제품 페이지·리뷰·기사를 수집합니다.
3. Jev가 동일 제품 여부와 요청 관련도를 평가합니다.
4. Gemini/Grok이 선별된 근거와 링크를 이용해 분석합니다.

제품별 리서치 가치가 있지만, 검색·원문 처리·근거 보관까지 필요해서 **앞의 개선안보다 작업 범위가 큽니다.**

**도입한다면 1번부터 작은 비교 실험을 권합니다.**

레퍼런스 검색 요청 100~200개 정도로 기존 순서와 Jev 재정렬을 비교하고, 상위 결과 관련도·선택률·추가 지연·요청당 비용을 확인하면 됩니다. 채팅 분류는 별도로 한국어 대화를 평가해야 합니다. 공식 문서에서도 영어 정확도가 가장 높고 CJK 언어는 자체 검증이 필요하다고 명시합니다. 현재 문서상 직접 API 가격은 입력 100만 토큰당 **$0.042**, 출력은 무료지만, 검색 API와 이미지 설명 생성 비용은 별도입니다. [모델·가격·언어 지원](https://docs.typesafe.ai/models)

제 추천은 **레퍼런스 재정렬을 첫 실험으로 삼고, 한국어 판정 품질이 확인되면 채팅 분류와 트렌드 선별로 확대하는 순서**입니다.

## user

도입시 효과를 잘 모르겠다 표로 정리해서 추천 정도를 알려줘

## assistant

**지금 당장 필수로 도입할 정도는 아닙니다.** 현재 기능에 ‘판단 단계’를 추가하는 것이어서, 기존 결과에 불만이 있는 영역에만 적용하는 편이 좋습니다. 아래 효과는 코드 구조를 보고 예상한 것으로, 실제 성능은 아직 검증하지 않았습니다.

| 적용 영역 | 사용자가 체감할 변화 | 효과가 큰 상황 | 비용·단점 | 추천 정도 |
|---|---|---|---|---|
| **레퍼런스 검색 재정렬** | 원하는 분위기·주제에 가까운 결과가 먼저 나옴 | 무관한 검색 결과가 자주 섞일 때 | 추가 호출 지연. 이미지 자체의 분위기 판단에는 별도 Vision 분석 필요 | **★★★★☆ 우선 실험** |
| **트렌드 기사 선별** | 검색 대상과 관계없는 기사가 줄어듦 | 동음이의어·분야 혼동으로 결과가 지저분할 때 | 한국어 기사 판정 검증 필요. 트렌드 발견 자체가 늘지는 않음 | **★★★☆☆ 문제 있다면 추천** |
| **채팅 진행·질문·취소 분류** | 질문했는데 다음 단계로 넘어가는 등의 오작동 감소 기대 | 실제 대화에서 의도 오분류가 반복될 때 | 기존 Grok도 수행 중. 둘 다 호출하면 더 느려질 수 있음 | **★★★☆☆ 오류 확인 후 추천** |
| **광고 카피 후보 평가** | 여러 생성안 중 조건에 맞는 안을 먼저 추천 | 후보가 많아 고르는 데 시간이 걸릴 때 | 점수가 실제 광고 성과를 보장하지 않음 | **★★☆☆☆ 후순위** |
| **외부 근거 기반 제품 리서치** | 경쟁 제품·리뷰 근거가 붙은 분석 제공 | 제품 분석을 리서치 서비스로 확장할 때 | 검색·수집·근거 관리까지 개발 필요. 효과 대부분은 검색 기능 추가에서 나옴 | **★★☆☆☆ 별도 기능으로 검토** |
| **이미지·영상·카피 생성 대체** | 직접적인 생성 품질 개선 없음 | — | Jev는 생성 모델이 아니며 현재 텍스트 입력만 지원 | **☆☆☆☆☆ 부적합** |

Jev의 선택·점수화 기능과 텍스트 입력 제한은 [공식 소개](https://docs.typesafe.ai/introduction) 및 [모델 문서](https://docs.typesafe.ai/models)를 기준으로 했습니다.

| 지금 원하는 개선 | 제 판단 |
|---|---|
| **생성 이미지·영상 퀄리티를 높이고 싶다** | Jev 도입 우선순위가 낮습니다. |
| **검색 결과에 쓸모없는 것이 많이 나온다** | 레퍼런스 재정렬을 작게 실험할 가치가 있습니다. |
| **채팅이 내 의도를 자꾸 잘못 이해한다** | 실제 실패 대화로 기존 Grok과 비교해볼 가치가 있습니다. |
| **AI 비용이나 응답 시간을 줄이고 싶다** | 기존 호출을 대체할 수 있는지 검증해야 합니다. 단순 추가로는 절감되지 않습니다. |
| **현재 기능에 큰 불만이 없다** | **도입을 보류하는 것을 추천합니다.** |

**현재 제 추천은 ‘전면 도입 보류, 레퍼런스 검색만 필요할 때 비교 실험’입니다.** Jev 자체가 새로운 핵심 기능을 만들어주기보다는, 기존 검색·분류의 정확도를 다듬는 데 적합합니다.
