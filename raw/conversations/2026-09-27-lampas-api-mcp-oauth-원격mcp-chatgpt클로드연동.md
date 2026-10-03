# Lampas MCP

Tool: codex
Started: 2026-09-27T02:54:10.736Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.codex/sessions/2026/09/27/rollout-2026-09-27T11-54-11-01a0e0c8-cf36-7420-8432-fe19cc1cda6b.jsonl

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
│   ├── lampas-web-status/     # 시스템 상태 페이지 (status.lampas.io · lampas-api status 모듈 프로브 결과)
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
| `lampas-web-status` | **8461** | `pnpm dev:lampas:web:status` |
| `lampas-web-fit` | **8462** | `pnpm dev:lampas:web:fit` |

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
  <current_date>2026-09-27</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

lampas-api-mcp 를 제대로 만들어서 GPT 나 클로드와 연계할 수 있게 해줘

## assistant

`lampas-api-mcp`의 현재 도구와 인증·실행 방식을 확인하고, GPT와 Claude에서 연결할 수 있도록 구현과 설정 문서까지 정리하겠습니다. OpenAI 연동 요건은 `openai-docs` 스킬을 적용해 확인하겠습니다.


## assistant

현재 서버는 로컬 `stdio`만 지원하고, 원격 연결과 자동 검증 테스트가 없습니다. Claude Desktop용 로컬 실행은 유지하면서 원격 MCP와 OAuth 연결을 추가하겠습니다. 원격 요청마다 사용자 인증을 분리해 다른 계정의 크레딧이 사용되지 않도록 처리하겠습니다.


## assistant

ChatGPT 웹 연결에는 OAuth가 필요합니다. Lampas 키를 연결 화면에서 검증한 뒤 MCP 전용 토큰을 발급하는 흐름을 구현 중입니다. 도구의 과금·변경 여부도 명시하고, 원격 도구가 서버의 로컬 파일을 읽지 못하도록 제한하겠습니다.


## assistant

OAuth 연결부터 공식 MCP SDK의 초기화·도구 호출까지 통합 테스트가 통과했습니다. 서로 다른 두 사용자의 동시 요청도 각자의 Lampas 인증으로 전달되는 것을 확인했습니다. 이제 재시작 후 인증 복원, 로컬 stdio 연결, 업로드·오류 처리를 검증하고 클라이언트별 설정 예제를 정리하겠습니다.


## assistant

로컬 stdio와 원격 OAuth/MCP를 포함한 16개 테스트, 타입 검사, 빌드가 모두 통과했습니다. ChatGPT·Claude 연결 방법과 두 공급사의 API 호출 예제도 추가했습니다. 실제 웹 계정 연결은 공개 HTTPS 배포 주소가 필요하며, 현재는 배포 설정 예제까지 준비된 상태입니다.


## assistant

`lampas-api-mcp` 연동 구현을 완료했습니다.

- Claude Desktop용 stdio, 원격 HTTP MCP 지원
- ChatGPT·Claude용 OAuth 인증과 사용자별 키 분리
- 인증 정보 암호화 저장, 업로드 제한, 오류 처리 보강
- 연결 가이드·API 예제·배포 설정 추가

**16개 테스트·타입 검사·빌드가 통과**했고, 독립 설치도 검증했습니다.

설정 방법: [README](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-api-mcp/README.md)

실제 ChatGPT·Claude 웹 연결은 **공개 HTTPS 주소로 배포한 뒤** 가능합니다. 운영 배포와 실제 계정 연결은 아직 수행하지 않았습니다.

## user

배포한 뒤에 적용하는 법 알려줘

## assistant

배포 후 필요한 설정과 ChatGPT·Claude 연결 순서를 확인하겠습니다. OpenAI Docs 스킬로 ChatGPT의 현재 연결 방식도 확인하겠습니다.


## assistant

배포 대상 설정이 아직 없어 기존 Lampas API 서버의 운영 구성을 확인하고 있습니다. 해당 서버에 MCP를 배포한 뒤, 실제 접속 주소를 기준으로 연결 방법을 안내하겠습니다.


## assistant

기존 HTTPS 주소를 활용해 연결 URL을 `https://api.lampas.io/mcp`로 구성하겠습니다. 사용할 경로가 기존 API와 충돌하지 않는 것을 확인했습니다. MCP 프로세스는 별도 포트로 실행하고, OAuth 연결 경로를 함께 연결하겠습니다.


## assistant

배포가 완료됐고, `https://api.lampas.io/mcp`의 인증 응답과 OAuth 연결 화면을 외부에서 확인했습니다. 이제 ChatGPT나 Claude에 이 URL을 등록하고, 연결 화면에서 본인의 Lampas Secret Key를 입력하면 됩니다.


## assistant

배포 완료했습니다. 서버 상태와 OAuth 연결 화면까지 확인했습니다.

**연결 URL**
```text
https://api.lampas.io/mcp
```

먼저 [Lampas 키 관리](https://platform.lampas.io/keys)에서 본인의 **Secret Key**를 준비하세요.

**ChatGPT에 연결**

1. 설정 → **Security and login → Developer mode**를 켭니다.
2. [플러그인 페이지](https://chatgpt.com/plugins)에서 `+`로 연결을 추가합니다.
3. 이름은 `Lampas`, URL은 위 주소를 입력합니다.
4. 인증은 **OAuth**, 등록 방식 선택이 나오면 **DCR**을 선택합니다. Client ID/Secret은 비워 둡니다.
5. Lampas 연결 화면에서 Secret Key를 입력하고 승인합니다.
6. 새 대화의 `+` 메뉴에서 Lampas를 활성화합니다.

**Claude에 연결**

1. **Customize → Connectors → + → Add custom connector**로 이동합니다.
2. 이름 `Lampas`와 위 URL을 입력합니다. 고급 설정의 Client ID/Secret은 비워 둡니다.
3. **Connect**를 눌러 Lampas 연결 화면에서 Secret Key를 입력합니다.
4. 대화의 `+ → Connectors`에서 Lampas를 활성화합니다.

연결 후 다음과 같이 확인하세요.

> Lampas의 이미지 생성 모델과 필요한 크레딧을 조회해줘.

이미지·비디오·오디오 생성에는 연결한 계정의 크레딧이 사용됩니다. 실제 ChatGPT·Claude 계정 연결은 위 절차로 마치면 됩니다.

[OpenAI 연결 안내](https://developers.openai.com/api/docs/guides/developer-mode) · [Claude 연결 안내](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp)

## user

<environment_context>
  <current_date>2026-09-28</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

마지막에 이렇게 되고 어떤 계정으로 연결되는지도 모르겠다 다시 살펴봐줘
첨부 이미지 경로:
"/Users/progdesigner/Works/lampas/lampas-system/.lampas-attachments/eed0288d-64bd-495f-920b-57d30fc7932d-image.png"

## assistant

첨부 화면과 현재 OAuth 연결 흐름을 확인하겠습니다. 마지막 단계에서 생긴 문제와 연결된 Lampas 계정을 확인할 수 없는 부분을 함께 살펴보겠습니다.


## assistant

원인을 재현했습니다. 제가 넣은 `Referrer-Policy: no-referrer` 설정 때문에 Chrome이 폼 제출 시 `Origin: null`을 보내고, 서버가 이를 거부하고 있었습니다. 앞선 검증에서 실제 브라우저 제출을 확인하지 못했습니다.

이 설정을 수정하고, 키 입력 후 바로 승인되는 흐름도 바꾸겠습니다. 연결할 Lampas 계정과 크레딧 사용 계정을 먼저 보여준 뒤 최종 승인하도록 하겠습니다.


## assistant

계정 확인 화면과 연결 후 계정을 조회하는 도구를 추가했습니다. 계정 정보는 실제 API의 인증 결과에서 가져오며, Secret Key를 사용하면 그 키 소유자의 계정과 크레딧 잔액을 표시합니다.

지금은 Chrome에서 계정 확인·변경·최종 승인 흐름을 검증하고 있습니다. API 배포는 현재 운영본을 기준으로 별도 작업 공간에서 준비해 진행 중인 다른 변경이 섞이지 않도록 하겠습니다.


## assistant

Chrome에서 잘못된 키 재입력, 계정 미리보기, 다른 계정으로 변경, 최종 승인 후 OAuth 토큰 교환까지 통과했습니다. 이제 이름·이메일·계정 ID·크레딧 잔액을 확인한 뒤 연결하게 됩니다. 운영 반영을 준비하고 있습니다.


## assistant

제 구현의 보안 헤더 설정 때문에 Chrome에서 `Invalid origin`이 발생했습니다. **수정 후 재배포했고, 실제 Chrome에서도 정상 제출을 확인했습니다.** 첨부 화면의 연결 시도는 완료되지 않은 상태였습니다.

연결되는 계정은 **입력한 Secret Key 소유자의 Lampas 계정**입니다. 이제 최종 승인 전에 이름·이메일·계정 ID·크레딧 잔액을 표시합니다.

다시 연결해주세요.

1. 기존 오류 창을 닫고 ChatGPT·Claude에서 Lampas 연결을 다시 시작합니다.
2. 키 입력 → **계정 확인**에서 표시된 계정을 확인합니다.
3. 맞으면 **이 계정으로 연결 허용**을 누릅니다.

연결 후에도 이렇게 확인할 수 있습니다.

> Lampas에 어떤 계정으로 연결되어 있고 크레딧은 얼마야?

## user

<environment_context>
  <current_date>2026-10-03</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

그래서 Chat GPT 에서 연동해서 쓰려면 어떻게 한다고 다시 알려줘
