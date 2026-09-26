# Lampas System 개선

Tool: codex
Started: 2026-09-25T10:15:03.283Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.codex/sessions/2026/09/25/rollout-2026-09-25T19-15-04-01a0d80f-b9c7-74e2-9d94-2ce9ac04fe5d.jsonl

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
<environment_context>
  <cwd>/Users/progdesigner/Works/lampas/lampas-system</cwd>
  <shell>zsh</shell>
  <current_date>2026-09-25</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

copy 에 스크롤이 없어서 오른쪽으로 쭉 넘어갔다 확인해서 수정해줘
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/b59f407e-649a-4b0c-bba6-e03ddbf85083-image.png"

## assistant

첨부 화면에서 문제가 난 Copy 영역을 확인하고, 내용이 길어져도 화면 오른쪽으로 넘치지 않도록 수정하겠습니다.


## assistant

페르소나 목록에 가로 스크롤 설정은 있었지만, 바깥 `fieldset`이 카드 전체 너비만큼 늘어나 스크롤이 생기지 않는 구조였습니다. 해당 영역의 너비를 제한하고, 아래 긴 설명도 영역 안에서 줄바꿈되도록 수정하겠습니다.


## assistant

수정했습니다.

- 페르소나·클립 목록이 화면 너비 안에서 가로 스크롤되도록 수정
- 긴 페르소나 설명의 줄바꿈 처리
- 프로덕션 빌드 통과

배포는 아직 하지 않았습니다.

## user

배포해

## assistant

Copy 웹을 저장소 배포 스크립트로 배포하겠습니다.


## assistant

Copy 웹 **v0.2.6 배포 완료**했습니다.

- 스크롤 수정 반영
- 테스트 32개 및 빌드 통과
- CDN 캐시 갱신 진행 중

새로고침해서 확인하시면 됩니다.

## user

https://lampas-system.tail0e32ab.ts.net/fixs 에서 쌓인 작업을 제거할 수 도 있게 해줘

## assistant

Fixs에 쌓인 작업의 저장·표시 구조를 확인하고, 화면에서 작업을 삭제할 수 있도록 추가하겠습니다.


## assistant

작업 상세에 ‘작업 삭제’ 버튼을 추가했습니다. 대기·완료·실패한 작업은 삭제할 수 있고, 실행 중인 작업은 취소가 끝난 뒤 삭제하도록 처리했습니다. 삭제한 작업이 자동 새로고침으로 다시 나타나지 않는지도 함께 점검하고 있습니다.


## assistant

삭제 API의 인증, 실행 중 삭제 차단, 대기열 제거를 검증했고 전체 398개 테스트와 타입 검사가 통과했습니다. 지금 해당 Fixs 사이트에 배포하고 있습니다.


## assistant

[Fixs](https://lampas-system.tail0e32ab.ts.net/fixs)에 삭제 기능을 추가하고 배포했습니다.

- 작업 선택 → **작업 삭제** → 확인
- 실행 중인 작업은 취소 완료 후 삭제 가능
- 같은 오류가 재발하면 새 작업으로 접수

테스트 398개·타입 검사 통과, 배포 버전 `1.0.22` 정상 응답 확인했습니다.

## user

오류 수정해줘
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/5da1ab8c-3206-44fa-8663-6fb5eea7e8f6-image.png"

## assistant

첨부 화면의 오류를 확인하고 원인을 찾아 수정하겠습니다.


## assistant

업로드 응답에서 파일 주소를 읽는 코드에 누락이 있었습니다. 서버가 전달하는 `download_url`을 Tools 화면이 인식하지 못해, 업로드가 성공해도 실패로 처리할 수 있는 상태입니다. 해당 응답 형식을 지원하도록 수정하고 테스트하겠습니다.


## assistant

수정 후 테스트 39개와 타입 검사가 통과했습니다. Tools 웹에 배포 중이며, 이번 수정은 같은 업로드 코드를 쓰는 오디오·비디오 입력에도 적용됩니다.


## assistant

수정 후 **Tools v0.1.2 배포 완료**했습니다.

업로드 응답의 파일 주소(`download_url`)를 읽지 못해 실패로 처리하던 문제였습니다. 테스트 39개·타입 검사·빌드 모두 통과했습니다.

새로고침 후 다시 생성해 주세요. 실제 유료 영상 생성까지는 검증하지 않았습니다.

## user

https://tools.lampas.io/ 여기에 기능들에 모델들이 뭘 쓰이는 지 모두 표시해줘 
그리고 이거 여전히 생성 오류 나니 확인 후 수정해 
그리고 프론트에서 오류 나는 것도 fix 에 보낼 수 있게 해줘 
모든 프로제트에 대해서 말야
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/bebe6f88-1fd0-4511-b9fe-91668fe0acd9-image.png"

## assistant

첨부 오류를 확인해 생성 실패 원인을 추적하겠습니다. Tools의 모든 기능에 사용 모델을 표시하고, 전체 프로젝트에서 프론트 오류를 Fixs로 보내는 연결도 점검해 보완하겠습니다.


## assistant

운영 로그에서 이번 실패 원인을 확인했습니다. 업로드는 통과했고, Kling 모델이 `Failure to pass the risk control system`으로 생성을 거절했습니다. 입력 중 무엇이 거절 사유인지는 응답에 없습니다. 이 경우 이유가 화면에 남도록 개선하고, 실패 응답을 계속 대기하는 처리도 고치겠습니다.

현재 공통 Fixs 수집기는 HTTP 500 이상만 감지해 이번 400 오류를 놓쳤습니다. 전체 프론트에서 400대 응답·네트워크 오류·콘솔 오류도 수집하도록 확장하겠습니다.


## assistant

Tools 목록과 각 기능 화면에 실제 실행 모델을 표시했고, 여러 모델을 쓰는 기능은 단계별로 보이게 했습니다. 거절 사유도 사라지는 알림 대신 결과 영역에 남도록 바꿨습니다.

전체 서비스의 오류 수집 코드는 기존 배포 페이지에 별도로 반영하겠습니다. 서비스별 기능 변경을 함께 배포하지 않도록 배포 스크립트에 수집 코드만 갱신하는 경로를 추가하고 있습니다.


## assistant

브라우저에서도 확인했습니다. 13개 기능 모두 모델이 표시되고, 운영 로그와 같은 생성 거절 응답을 재현했을 때 오류 안내가 화면에 남으며 Fixs 보고도 발생했습니다.

웹 프로젝트들의 소스 연결을 갱신했고, 이제 배포 설정이 있는 서비스에 수집 코드를 반영하고 있습니다. Kling의 입력 검토 거절 자체는 클라이언트 수정으로 해제할 수 없어, 구체적인 안내와 실패 처리까지 수정한 상태입니다.


## assistant

Tools v0.1.3과 Fixs 에이전트 v1.0.23 배포가 완료됐고, 나머지 웹 32개에도 오류 수집 코드를 반영했습니다. 실제 Fixs 서버에 보고가 저장되는 것까지 확인했습니다.

프론트 36개 모두 소스에 연결됐습니다. 별도 배포 방식인 토스 미니앱 2개는 소스 반영까지 완료했으며, 이번 웹 배포에는 포함되지 않았습니다.


## assistant

반영했습니다.

- **모델 표시:** Tools 13개 기능의 목록·상세에 사용 모델 표시.
- **생성 오류:** 이번 오류는 Kling의 입력 검토 거절이었습니다. 거절 사유를 화면에 유지하고, 실패 상태·재시도·Fixs 보고 처리를 수정했습니다. 공급사의 거절 자체를 해제한 것은 아닙니다.
- **전체 오류 수집:** 프론트 36개에 연결. HTTP 400 이상, 네트워크·콘솔·실행 오류를 Fixs로 전송합니다.

**웹 33개와 에이전트 배포 완료.** 토스 미니앱 2개는 소스 반영 완료이며 별도 배포가 남아 있습니다. Tools를 새로고침해 주세요.

## user

이번엔 생성된게 안 나와
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/0ac81dfd-a4eb-450a-8a2f-b1411d994abe-image.png"

## assistant

첨부 화면을 확인하고, 생성 결과 응답이 화면에 표시되는 과정까지 추적하겠습니다.


## assistant

영상 생성은 성공했습니다. 다만 화면이 존재하지 않는 `/v1/ai/media` 주소로 영상을 요청해 404가 발생했습니다. 제가 앞선 검증에서 실제 영상 재생까지 확인하지 못했습니다.

원본 영상은 정상 응답하는 것을 확인했습니다. 원본 주소로 재생하도록 수정하고, 이미 생성한 결과를 다시 볼 수 있도록 처리하겠습니다. 다시 생성하실 필요는 없습니다.


## assistant

기존에 생성된 12.27초 영상을 브라우저에서 실제로 재생해 확인했습니다. 새 생성 요청 없이 저장된 결과를 복원하도록 수정했고, 테스트 45개와 타입 검사도 통과했습니다. 지금 배포 중입니다.


## assistant

**수정 후 v0.1.4 배포 완료했습니다.**

생성은 성공했지만 잘못된 재생 주소 때문에 영상이 표시되지 않았습니다. 기존 12.27초 영상이 실제로 재생되는 것까지 확인했습니다.

**새로고침하면 기존 결과가 복원됩니다. 다시 생성하지 않으셔도 됩니다.**

## user

여전히 생성하면 영상이 안 나온다.
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/0f717b03-15cf-4738-bf53-23994bed92c2-image.png"

## assistant

첨부 화면과 방금 생성한 결과의 요청을 확인하겠습니다. 이번에는 운영 화면에서 새 결과를 받는 흐름과 실제 재생까지 함께 검증하겠습니다.


## user

s3 업로드 해서 보일 수 있게 해라 api 연결해서 보이게 하지 말고

## assistant

네. 생성 결과를 S3에 저장하고, 화면에서는 S3/CloudFront 파일 주소로 직접 재생하도록 하겠습니다. 현재 로그에는 새 S3 영상도 이전 프록시 주소로 요청한 흔적이 있어, 저장 경로와 운영 화면의 버전 갱신 문제를 함께 확인하겠습니다.


## assistant

이번에 생성한 영상은 이미 S3에 저장되어 있고, CloudFront 원본도 정상입니다. 재생 실패 로그는 계속 이전 화면 코드의 `/v1/ai/media` 요청을 가리킵니다.

추가로 일부 임시 영상 주소가 S3 저장 대상에서 빠지는 경우도 막고 있습니다. S3 저장이 끝난 주소만 반환하고, HTML 캐시가 이전 코드를 재사용하지 않도록 배포 설정도 수정하겠습니다.


## assistant

S3 저장 대상 누락과 저장 실패 시 임시 주소를 그대로 반환하던 처리를 수정했습니다. 이전 프록시 형식으로 남은 저장 기록도 파일 주소로 복원하게 했습니다.

프론트 검사는 통과했고 배포도 끝났습니다. API 배포 완료와 함께, 운영 페이지가 실제 CloudFront 영상만 요청하는지 마지막으로 확인하고 있습니다.


## assistant

**S3 저장 → CloudFront 주소 직접 재생**으로 수정·배포했습니다. API 미디어 프록시는 사용하지 않습니다.

운영 화면에서 기존 12.27초 영상의 실제 재생을 확인했습니다. 이전 코드가 남아 있어 **한 번 강력 새로고침(`⌘⇧R`)**해 주세요. 다시 생성할 필요는 없습니다.

[이미 생성된 영상 바로 보기](https://d15vzo88v4tnb8.cloudfront.net/ai-gateway/predictions/23d4d214-02e0-4696-9957-77e49eca56ac.mp4)
