# Lampas System 개선

Tool: codex
Started: 2026-10-03T10:12:24.195Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.codex/sessions/2026/10/03/rollout-2026-10-03T19-12-25-01a10140-2b86-7cb1-91dc-21fc44c4970b.jsonl

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
  <current_date>2026-10-03</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

Lampas System 하네스에서 
특정 플레이리스트를 주고, 해당 플레이 리스트에 새로운 영상이 추가되면 
알아서 수집하는 기능을 구축해줘 
그리고 이 실행은 지금의 맥미니 하네스에서 실행되길 바래 
방향을 잘 설정해서 주기적으로 계속 수집할 수 있게 해줘 
1시간 단위로 체크해서 수집해줘 
그리고 이미 수집한 거면 또 수집할 필요 없거든, 
람파스 하네스를 다시 구조를 살펴보고 어떻게 만들지 먼저 플랜을 정리해보자
새로 만들어도 되니까 더 좋은 구조로 새로 다시 정리해보자

## assistant

람파스 하네스의 실행 구조와 기존 영상 수집 도구를 살펴보고, 맥미니에서 매시간 확인하고 중복 없이 수집할 수 있는 구조로 계획을 정리하겠습니다.

## assistant

현재 하네스에는 맥미니 상시 데몬과 SQLite 기반 주기 실행·재시도 구조가 이미 있습니다. 다만 Clips의 영상 처리와 체크포인트는 브라우저에 있어, 창을 닫아도 계속 수집하려면 수집 작업 상태와 실행을 서버로 옮기는 것이 핵심입니다. 기존 데몬을 확장하는 방향으로 검토하겠습니다.

## assistant

기존 구현에서 자동 수집에 영향을 주는 제한도 확인했습니다. 재생목록 조회는 최대 200개만 반환하고, 다운로드 작업은 메모리에 보관해 재시작 시 사라집니다. 클립 뱅크 등록에도 영구적인 중복 방지 키가 없습니다. 목록 확인과 영상 처리를 분리하고, 맥미니와 뱅크 양쪽에서 중복을 막도록 계획하겠습니다. 현재 맥미니는 시스템 잠자기가 1분으로 설정돼 있어 상시 운영 설정도 함께 다뤄야 합니다.

## assistant

추천 방향은 **기존 `lampas-agent`를 맥미니 상시 수집 하네스로 확장하고, 플레이리스트 감시와 영상 처리를 분리하는 것**입니다. 실행 상태를 서버에 저장해 브라우저를 닫거나 맥미니 프로세스가 재시작돼도 이어서 수집하도록 만들겠습니다.

현재 구조를 살펴보니 재사용할 기반은 충분합니다.

| 현재 구성 | 판단 |
|---|---|
| 맥미니 `launchd` 데몬 | Agent와 로컬 API 모두 실행 중 |
| SQLite 작업 원장 | 스케줄·재시도·리스·재시작 복구 기능 재사용 가능 |
| 플레이리스트 조회 | 구현돼 있지만 최대 200개 제한 보완 필요 |
| Clips 처리 | 브라우저에서 실행하고 OPFS에 상태 저장 → 서버 실행 필요 |
| 유튜브 다운로드 | 작업 상태가 메모리에 있고 임시 파일은 1시간 수명 |
| 클립 뱅크 등록 | 영구적인 중복 방지 보완 필요 |

구조는 [Agent 가이드](/Users/progdesigner/Works/lampas/lampas-system/apps/lampas-agent/CLAUDE.md)와 실제 서버·Clips 구현을 기준으로 확인했습니다.

계획은 다음과 같습니다.

1. **플레이리스트 구독을 등록합니다.**

   URL, 수집 계정, 저장 대상, 수집 범위, 분야·카테고리, 최초 수집 정책을 저장합니다. 확인 간격은 기본 **1시간**입니다.

   등록·일시정지·해제·지금 확인 기능과 마지막 확인 시각, 다음 확인 시각, 실패 사유를 하네스 화면에 표시합니다.

2. **목록 감시와 영상 처리 큐를 분리합니다.**

   매시간 맥미니에서 플레이리스트 전체 영상 ID를 조회하고, 이전 기록과 비교해 미수집 영상만 큐에 넣습니다. 새로 추가된 영상은 업로드 날짜가 오래됐더라도 수집합니다.

   ```text
   플레이리스트 등록
       → 매시간 전체 목록 확인
       → 미수집 영상만 작업 등록
       → 맥미니에서 영상 처리
       → 보관·뱅크 등록 확인
       → 수집 완료 기록
   ```

   긴 영상 처리가 한 시간을 넘겨도 목록 확인은 계속되어야 합니다. 현재 워커는 작업 실행을 기다리는 구조이므로, 스케줄 확인 루프를 긴 처리 작업과 분리합니다. 같은 플레이리스트의 확인 작업이 겹치면 하나로 합칩니다.

3. **중복 방지는 영상 ID를 기준으로 영구 저장합니다.**

   기본 식별 기준은 **저장 대상 + 소유 계정 + YouTube 영상 ID**입니다. 같은 영상이 여러 플레이리스트에 있어도 한 번만 수집하고, 어느 목록에서 발견됐는지는 별도로 기록합니다.

   발견·대기·진행·완료·실패 상태를 구분합니다. 큐에 넣었다는 이유로 수집 완료로 처리하지 않고, 필요한 결과가 저장된 뒤 완료를 기록합니다.

   뱅크 API에도 멱등키를 적용합니다. 업로드는 성공했지만 응답을 받기 전에 하네스가 꺼져도, 재시도 시 같은 소스나 클립이 중복 생성되지 않도록 합니다.

   기존에 수동 수집한 영상도 같은 계정·저장 대상에서 찾아 수집 기록에 연결합니다. 원본 등록만 끝난 작업과 처리까지 완료된 작업은 구분합니다.

4. **영상 처리를 서버 파이프라인으로 옮깁니다.**

   수집 범위가 전체 Clips 처리라면 다음 단계를 맥미니 서버에서 실행합니다.

   ```text
   다운로드 → 원본 보관 → 장면 분할·썸네일
            → 전사·라벨링 → 클립 뱅크 등록
   ```

   영상 처리는 우선 **동시 1개**로 시작합니다. FFmpeg는 맥미니의 네이티브 실행 파일을 사용하고, 기존 전사·라벨링 로직에서 공유 가능한 부분을 분리합니다. AI 호출과 저장은 지정한 로컬 또는 운영 뱅크를 따릅니다.

   단계별 파일과 체크포인트를 디스크·SQLite에 저장해 완료된 다운로드, 전사, 라벨링을 재실행하지 않습니다. 재개에 필요한 파일은 작업 완료 전까지 보존합니다.

5. **무인 운영과 복구를 갖춥니다.**

   - 재시작 후 만료된 작업 리스를 복구하고 미완료 단계부터 재개
   - 네트워크 오류는 백오프로 재시도, 인증·크레딧·접근 오류는 원인을 표시하고 대기
   - 비공개·삭제 영상은 상태를 기록하고 다른 영상 수집은 계속 진행
   - 목록 조회 실패나 일부 응답을 정상적인 전체 목록으로 확정하지 않음
   - 플레이리스트에서 영상이 빠져도 수집 완료 기록과 보관 영상은 유지
   - 디스크 여유 공간, 마지막 정상 확인 시각, 대기 작업 수를 상태 화면에 표시

   장기 실행 인증도 함께 설계해야 합니다. 현재 Clips API는 사용자 로그인만 허용하므로, 브라우저 JWT를 복사해 쓰는 방식 대신 **수집 계정에 귀속된 제한된 서버 인증 경로**가 필요합니다.

6. **맥미니 운영 설정과 실제 검증으로 마무리합니다.**

   현재 시스템 잠자기가 **1분**으로 설정돼 있습니다. 매시간 실행하려면 시스템 잠자기 방지 설정이 필요합니다. 또한 현재 데몬은 사용자 `LaunchAgent`이므로 재부팅 뒤 로그인 조건도 확인해야 합니다.

   검증 기준은 다음과 같습니다.

   - 새 영상 추가 후 다음 시간 확인에서 작업 생성
   - 같은 영상이 여러 목록에 있어도 한 번만 수집
   - 200개 초과 목록과 목록 중간에 추가된 영상도 발견
   - 브라우저 종료 후에도 처리 지속
   - 처리 중 데몬 재시작 후 완료 단계 재사용
   - 뱅크 저장 직후 연결이 끊겨도 중복 등록 없음
   - 하루 이상 연속 운영하며 실제 시간별 확인 기록 검증

새 앱을 따로 만들기보다는 `lampas-agent/src/server` 아래에 **구독 관리·목록 감시·영상 처리 모듈**을 추가하고, 기존 SQLite 원장과 데몬을 확장하는 편이 적합합니다. 이후 수집원이 늘어나도 감시 모듈만 추가할 수 있습니다.

구현 순서는 **구독·중복 기록 → 매시간 감시 → 서버 영상 처리 → 뱅크 멱등 등록·인증 → 관리 화면 → 맥미니 배포·연속 검증**으로 잡겠습니다.

수집 범위와 최초 등록 시 기존 영상 처리 여부는 앞서 제시한 선택에 따라 확정하면 됩니다. 이번에는 구조 검토와 계획까지 정리했으며, 코드나 실행 설정은 변경하지 않았습니다.

## user

1. 개별 영상을 등록 할 수도 있지만, 
이렇게 플레이리스트를 구독 등록하는 기능도 만들어서 처리해줘 
2. 응 분리해줘
3. id 기준으로 하고, 파일 업로드 기능은 잠시 제거해줘. 중복된 ID 는 재수집이 되어서 기존 클립과 다른 소스로 추가는 되게 (기존과 동일)
4. 영상 처리를 서버 파이프라인으로 하는게 더 나은거야? 더 빠르고 효과적으로 할 수 있는 방향이어야해 다시 확인해줘
5. 무인 운영/ 복구 기능은 필수야 적용해줘
6. 검증도 필수야

4번만 다시 확인해서 알려주고 나머지 적용해서 계획 다시 알려줘

## assistant

4번은 현재 브라우저 처리 방식과 맥미니의 네이티브 처리 방식을 비교해 다시 확인하겠습니다. 중복 ID는 자동 구독에서는 건너뛰고, 사용자가 개별 영상으로 다시 등록하면 기존처럼 별도 소스·클립을 만드는 것으로 이해해 계획에 반영하겠습니다.

## assistant

**4번은 맥미니에서 네이티브 FFmpeg로 처리하는 방향을 추천합니다.** 현재 코드를 다시 확인해보니, 무인 운영뿐 아니라 처리 효율을 개선할 여지도 큽니다.

현재 맥미니는 **M4 Pro·64GB RAM**이고 네이티브 FFmpeg가 설치돼 있습니다.

| 처리 | 현재 방식 | 변경 방향 |
|---|---|---|
| 원본 전달 | 맥미니 → S3 → 브라우저 수신 | 맥미니 파일을 직접 처리 |
| 장면 감지 | 브라우저에서 프레임마다 시킹·Canvas 비교 | FFmpeg로 순차 디코딩·프레임 분석 |
| 썸네일·키프레임 | 브라우저에서 반복 시킹 | 필요한 프레임을 묶어서 추출 |
| 오디오 추출 | FFmpeg WASM에서 직렬 실행 | 네이티브 FFmpeg로 추출 |
| 전사·AI 라벨 | 외부 API 호출 | 기존 API 유지, 제한된 병렬 처리 |

핵심은 **원본의 브라우저 재수신을 없애고, 반복 시킹과 WASM 처리를 줄이는 것**입니다. 전사·라벨링은 외부 API와 업로드 속도의 영향을 받으므로 서버로 옮기는 것만으로 빨라지지는 않습니다.

정확한 속도 개선 폭은 아직 측정하지 않았습니다. 동일 영상으로 단계별 시간·메모리·분할 품질을 비교하고, 장면 감지는 기존 기준을 먼저 유지해 처리 방식 변경으로 품질이 떨어지지 않도록 검증하겠습니다.

말씀하신 조건을 반영한 수정 계획은 다음과 같습니다.

1. **개별 영상 등록과 플레이리스트 구독을 함께 제공합니다.**

   개별 YouTube URL은 즉시 처리 큐에 등록합니다. 플레이리스트 URL은 구독으로 저장해 **1시간마다** 확인합니다. 구독에는 저장 대상, 소유 계정, 분야·카테고리, 기존 영상까지 수집할지 여부를 설정합니다.

   파일 선택·드래그앤드롭 업로드 기능은 화면과 신규 작업 등록 경로에서 잠시 제거합니다. 기존 파일 기반 소스와 클립은 보존합니다.

2. **목록 감시와 영상 처리를 분리합니다.**

   감시기는 전체 목록을 확인해 새 영상 ID를 기록하고 작업만 등록합니다. 처리 워커는 별도로 다운로드·분할·전사·라벨링·뱅크 등록을 수행합니다.

   긴 영상이 처리 중이어도 매시간 목록 확인은 계속됩니다. 현재 200개 조회 제한도 보완합니다.

3. **자동 중복 방지와 명시적인 재수집을 구분합니다.**

   | 상황 | 동작 |
   |---|---|
   | 구독에서 이미 처리한 영상 ID 발견 | 자동 재수집 생략 |
   | 같은 계정·저장 대상의 여러 구독에 동일 ID 존재 | 자동 수집 작업 하나로 연결 |
   | 사용자가 동일 영상을 개별 등록하거나 재수집 선택 | **새 소스·새 클립 생성** |
   | 실패·재시작으로 같은 작업 재시도 | 기존 작업을 이어서 실행 |

   따라서 영상 ID 자체에 소스 생성 금지 제약을 걸지 않습니다. **영상 ID는 자동 수집 이력 판단에 사용하고, 작업 ID는 재시도 중복 방지에 사용합니다.** 새 재수집 작업은 별도 작업 ID를 발급합니다.

4. **맥미니 서버 파이프라인으로 통합합니다.**

   ```text
   개별 등록 / 구독 감시
       → 영속 작업 큐
       → 다운로드
       → 장면 분석·썸네일·오디오 추출
       → 전사·라벨링
       → 원본 보관·클립 뱅크 등록
   ```

   브라우저는 등록·진행 확인·취소·재시도 화면을 담당합니다. 기존 원본 1개와 구간 메타데이터로 구성하는 가상 클립 방식은 유지합니다.

   초기에는 영상 처리 동시 1개로 운영하고, 측정 결과에 따라 단계별 병렬도를 조정합니다.

5. **무인 운영·복구를 필수로 적용합니다.**

   SQLite에 구독·작업·단계별 체크포인트를 저장합니다. 재시작하면 미완료 단계부터 재개하고, 완료된 다운로드·전사·라벨은 재사용합니다.

   네트워크 오류는 백오프로 재시도하며 인증 만료, 크레딧 부족, 접근 불가 영상은 사유를 표시합니다. 장기 실행용 서버 인증, 디스크 용량 관리, 시스템 잠자기 방지, 재부팅 후 실행 조건까지 함께 처리합니다.

6. **기능·복구·성능 검증을 배포 조건으로 둡니다.**

   새 영상 감지, 자동 중복 생략, 동일 ID 수동 재수집, 200개 초과 목록, 브라우저 종료, 처리 중 프로세스 종료, 저장 직후 응답 유실을 검증합니다.

   동일 영상의 기존·신규 처리 성능과 결과 품질을 비교하고, 맥미니에서 **24시간 이상 연속 운영**하며 시간별 확인·복구 기록까지 확인합니다.

구현 순서는 **작업·구독 모델 → 감시/처리 분리 → 네이티브 파이프라인 → 등록 화면 → 복구·인증 → 성능·연속 운영 검증**입니다. 기존 `lampas-agent`의 데몬과 SQLite 원장을 확장하는 구조로 진행하며, 이번 답변에서는 수정 계획까지 정리했습니다.

## user

이렇게 구축해서 다시 lampas-system 하네스를 재구축해줘

## assistant

합의한 구조로 구축하겠습니다. 먼저 현재 변경 사항과 하네스 연결 지점을 확인한 뒤, 서버 작업 큐·플레이리스트 감시·네이티브 영상 처리·관리 화면을 연결하고 맥미니 배포와 복구 검증까지 진행하겠습니다.

## assistant

중복 처리는 두 층으로 나눠 구현하겠습니다. 구독은 영상 ID로 자동 중복을 막고, 수동 재수집은 새 작업 ID를 발급해 별도 소스와 클립을 만듭니다. 서버 파이프라인은 기존 장면 분할·전사·라벨 해석 로직을 공유해 결과 형식을 유지하고, 파일 처리만 네이티브 FFmpeg로 교체하겠습니다.

## user

위 계획을 md 파일로 잘 남겨놨지?
