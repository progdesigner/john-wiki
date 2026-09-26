# Dalar 인스타그램 관리

Tool: codex
Started: 2026-09-26T09:19:01.077Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.codex/sessions/2026/09/26/rollout-2026-09-26T18-19-02-01a0dd02-c97e-7f51-b7bc-0f7ad5bccb88.jsonl

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
  <current_date>2026-09-26</current_date>
  <timezone>Asia/Seoul</timezone>
  <filesystem><workspace_roots><root>/Users/progdesigner/Works/lampas/lampas-system</root></workspace_roots><permission_profile type="disabled"><file_system type="unrestricted" /></permission_profile></filesystem>
</environment_context>

## user

https://admin.first.dalar.ai/samples 에 샘플들을 
https://package.lampas.io/new 통해 올리려고 하는데 너무 패키징이 스포츠 느낌이라, 
페르소나에 따라 완전 달라질 수 있어야 하고. 
분야에 따라서 패키징을 다르게 할 수 있게 수정해줘 
그래서 first.lampas.io 에 맞게 게시물 정보를 입력해서 게시할 수 있게 해줘

## assistant

샘플 관리와 패키지 생성·게시 흐름을 확인하고, 분야와 페르소나에 맞춰 패키징과 게시물 정보를 설정할 수 있도록 수정하겠습니다.

## assistant

스포츠 느낌의 원인은 화면 예시뿐 아니라 AI 작성 규칙도 ‘하이라이트 소개 + 시청 안내’로 고정돼 있기 때문입니다. 이 규칙을 분야·페르소나·게시 목적에 따라 달라지도록 바꾸고, First 샘플을 보내면 반려동물 AI 영상 서비스에 맞는 소개와 `first.lampas.io` 안내가 미리 채워지도록 연결하겠습니다.

## assistant

First는 확인해 보니 아기 사진으로 돌잔치 영상을 만드는 서비스입니다. 앞서 말씀드린 반려동물 분야는 잘못 짚었습니다. 돌잔치를 준비하는 부모를 대상으로 따뜻한 샘플 소개와 제작 안내가 채워지도록 맞추겠습니다.

## assistant

분야별 템플릿과 직접 입력하는 페르소나·타겟 독자·게시 목적을 추가했습니다. First 샘플의 ‘Package로 보내기’는 영상과 장면 수를 전달하고, 돌잔치 영상용 설정·수정 가능한 게시 본문·제작 링크를 미리 채웁니다. 현재 테스트와 빌드를 확인하고 있습니다.

## assistant

수정했습니다.

- 스포츠 중심 작성 규칙을 없애고 **분야·페르소나·타겟·게시 목적**에 따라 문체와 본문 구성이 달라지도록 변경했습니다.
- First·뷰티·푸드·교육·스포츠 템플릿과 직접 입력을 지원합니다.
- 샘플에서 **‘Package로 보내기’**를 누르면 돌잔치 영상용 설정, 제목·본문, `first.lampas.io` 제작 링크가 채워집니다. 수정 후 기존 게시 흐름으로 진행할 수 있습니다.

관련 테스트 45개와 두 웹 앱 빌드를 통과했습니다. **운영 배포와 실제 게시물 발행은 아직 하지 않았습니다.**

## user

배포 해줘

## assistant

API와 Package, First 웹을 저장소 배포 스크립트로 배포하겠습니다. 배포 대상과 현재 변경 사항을 먼저 확인하겠습니다.

## assistant

API에 다른 작업의 미완료 변경이 있어 이번 패키징 수정만 분리해 배포하겠습니다. 또 `first.lampas.io`는 현재 DNS가 연결되어 있지 않습니다. 기존 First 운영 도메인 배포를 진행하면서 새 주소 연결 설정도 확인하겠습니다.

## assistant

분리한 API 소스의 전체 테스트 1,221개가 통과했고 운영 빌드가 진행 중입니다. First 주소 선택이 확인되면 게시물 링크를 맞춰 두 웹도 배포하겠습니다.

## assistant

API 배포를 완료했고 운영 서버의 데이터베이스 연결도 정상입니다. 웹에는 아직 연결되지 않은 `first.lampas.io` 제작 링크가 들어 있어, 앞서 드린 주소 선택 응답을 기다리고 있습니다.

## assistant

주소 선택 응답이 없어, 우선 정상 운영 중인 `first.dalar.ai`를 제작 링크로 사용해 웹 배포를 마무리하겠습니다. `first.lampas.io` 연결은 별도로 남겨두겠습니다.

## user

<send_user_message_question_reply>
[{"answer":"기존 first.dalar.ai 사용","question":"현재 First 운영 주소는 first.dalar.ai이고, first.lampas.io는 아직 연결되어 있지 않습니다. 게시물의 제작 링크를 어느 주소로 사용할까요?","questionItemId":"[\"request_user_input_async\",\"call_lLuOtCBqjN0bKSWK3QAgpAYC\",0]"}]
</send_user_message_question_reply>

## assistant

확인했습니다. 제작 링크는 `first.dalar.ai`로 반영했고 두 웹 배포도 완료했습니다. 운영 페이지에서 새 파일이 내려오는지 마지막으로 확인하겠습니다.

## assistant

배포 완료했습니다. API와 두 웹의 운영 응답·변경 반영을 확인했습니다.

[샘플 관리](https://admin.first.dalar.ai/samples)에서 **Package로 보내기**를 누르면 First용 설정과 게시 본문이 채워집니다. 분야·페르소나에 맞춰 수정한 뒤 게시하시면 됩니다.

제작 링크는 **first.dalar.ai**로 반영했습니다.

## user

first.lampas.io 는 잘못 말한거야 first.dalar.ai 만 하면됨

## assistant

네, `first.dalar.ai`만 사용하도록 이미 반영해 배포했습니다. `first.lampas.io`는 연결하지 않았습니다.
