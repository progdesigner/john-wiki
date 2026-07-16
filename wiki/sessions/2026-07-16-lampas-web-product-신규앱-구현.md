---
tags: [session, lampas-studio, new-app, gemini, nestjs, react, marketing, git]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-16 — [[lampas-studio]] "제품 이미지 분석 + 마케팅 방향" 신규 앱(`lampas-web-product`) 구현

`[[progdesigner]]`가 "제품 분석 및 마케팅 방향을 알려주는 서비스를 람파스 프로덕트로 만들려고 해. 앱스에
하나 추가해서 이미지만 올리면 제품을 분석해주는 서비스를 만들어봐"로 시작한 세션(01:40~01:59 UTC).
`[[lampas-studio]]`(로컬 저장소 `lampas-system`)에 완전히 새로운 독립 앱과 백엔드 모듈을 스캐폴딩부터
구현·검증·배포 배선까지 한 턴에 마쳤다. 소스: `[[raw/conversations/2026-07-16-lampas-web-product-신규앱-구현]]`.

## 1. 조사 — 두 병렬 에이전트로 프론트 베이스·백엔드 패턴 동시 조사

기존 앱 두 개(`lampas-web-ai` vs `lampas-web-www`)의 package.json·vite.config·구조를 비교해 복사 베이스를
정하는 조사와, 기존 vision 분석 엔드포인트(Actor/Object) 코드 흐름·인증 가드 패턴을 조사하는 에이전트를
병렬로 돌렸다.

**프론트 베이스 선정 — `lampas-web-ai` 압도적 우세**:
- Vite 6 + `@vitejs/plugin-react` + TS 5.6 (WWW는 Vite 5 + SWC + lovable-tagger, 구식·특수 생성물)
- 의존성 5개뿐(axios/react/react-dom/sonner/google-oauth) — WWW는 shadcn/ui 풀스택 20여 개
- **재사용 가능한 기성품**: `src/lib/image.ts`의 `fileToDownscaledDataUrl(file, maxDim)`(다운스케일 data URL
  변환), `src/lib/api.ts`의 `VITE_API_URL` 기반 axios 인스턴스 + Bearer 인터셉터 + 잡 폴링 패턴
- 라우터 없는 단일 화면 SPA(App→Shell)라 결과 표시 UI로 개조하기 쉬움
- 포트: AI가 8232 사용 중이라 새 앱은 **8236**

**백엔드 패턴 — vision 분석은 전부 JSON base64/URL, multipart 없음**:
- 모노레포 전체에서 `FileInterceptor`/`@UploadedFile` 사용처 0건 — 이미지 입력은 JSON body에
  `referenceImageUrl?`/`referenceImageData?`(base64 or data URL)/`fallbackUrl?`/`resultLanguage?` 관례
- 가장 가까운 선례는 사람이 아닌 **Object(사물) 분석** —
  `objectsService.analyzeObjectReference(dto)` → `geminiService.analyzeObjectTraits({imageUrl,
  imageBase64, fallbackImageUrls, resultLanguage})` → flat JSON 정규화 반환
- **전역 APP_GUARD 없음** — 인증은 컨트롤러 클래스 레벨 `@UseGuards(JwtAuthGuard)`. `@Public()` 데코레이터를
  핸들러에 붙이고 `UserAuthGuard`를 붙이지 않으면 완전 공개(비로그인) 엔드포인트가 됨(`request.user =
  {id: GUEST_USER_ID, authType:'guest'}` 주입)
- 이미 존재하는 `products` 모듈은 Prisma CRUD 엔티티라 마케팅 분석용 무상태 공개 서비스와 성격이 달라
  **독립 신규 모듈 `product-insights`**로 분리하기로 판단

## 2. 구현

**백엔드 (`apps/lampas-api`)**
- `GeminiService.analyzeProductMarketing()` 신규 — `analyzeObjectTraits`와 동일한 vision 패턴(내장 JSON
  파싱 재사용)에 마케팅 전략가 프롬프트.
- 신규 무상태 공개 모듈 `src/modules/product-insights/` — `POST /api/product-insights/analyze`
  (`@Public()`, 로그인 불필요, DB 미사용).
- `app.module.ts`에 모듈 등록.

**프론트엔드 (`apps/lampas-web-product`, 신규 앱, 포트 8236)**
- `lampas-web-ai`를 슬림 베이스로 스캐폴딩(Vite 6 + React + Tailwind, 로그인 불필요).
- `src/App.tsx`: 이미지 드래그&드롭/클릭 업로드 → (선택) 추가 정보 입력 → "제품 분석하기" → 결과 카드 뷰.
  업로드 이미지는 `fileToDownscaledDataUrl`로 다운스케일 후 base64 전송.
- 결과 화면: 제품 요약·핵심 가치 제안, 주요 특징, 소재/디자인, 강점/약점, 타깃 고객(핵심/인구통계/
  라이프스타일), 브랜드 키워드, 마케팅 앵글, 추천 채널·전술, 광고 카피 초안, 콘텐츠 아이디어, 해시태그.

**배선**
- 루트 `package.json`에 `dev:lampas:web:product` 스크립트 추가.
- `scripts/deploy-web.sh`에 `lampas-web-product` 항목 추가 — **단, CloudFront ID는
  `__SET_CLOUDFRONT_ID__` 플레이스홀더**. 실제 배포 전 S3/CloudFront 인프라 발급이 선행돼야 함(코드에
  TODO 표시, 미해결 상태로 남음).

**검증**: 백엔드 `tsc` 신규 파일 에러 0(기존 tsc 에러는 `spaces`/`actors` 모듈의 Prisma 클라이언트
미재생성 문제로 무관 — `[[lampas-studio]]` 참조). 프론트 `tsc -b` + `vite build` 통과(gzip JS 74KB).
**실제 API 서버 기동으로 end-to-end 확인은 안 함** — 워킹트리에 커밋 안 된 `spaces` 모듈 + Prisma 스키마
변경이 있어 서버 기동엔 `prisma generate`가 선행돼야 하는데 작업 범위 밖이라 건드리지 않음.

## 3. 사용자 요청 파편화 — 3회 명확화 후 위임

사용자가 "출력 항목을" → "카피 해시태그 같은 특정 입력 항목을 더 선보였다" → "하키의 해체 같은 특정 입력
항목을 더 손봐달라고 작업해달라고?" 순으로 음성입력 오인식으로 추정되는 불완전한 문장을 세 번 연속 보냈다.
어시스턴트는 매번 **추측해서 진행하지 않고** 구체적 선택지를 제시하며 명확화를 요청했고, 사용자가 결국
"너가 알아서 손을 봐줘"로 판단을 위임하자 그때 자율적으로 개선안을 정하고 실행했다.

**개선 내용**:
1. 광고 카피 → **AIDA 프레임**(Attention/Interest/Desire/Action)으로 재구성, 실제 판매 카피처럼 복사
   가능한 형태로.
2. 해시태그 → 채널별(Instagram/Naver/TikTok) 전술 반영, 한/영 혼합 최적화, 개별 클릭 복사 + 전체 일괄 복사.
3. 프론트에 카피·해시태그 **복사 버튼** 추가(toast 피드백).
4. Gemini 프롬프트에 "commercially usable, not generic" 강조 추가.

빌드 재검증(프론트 타입체크·빌드, 백엔드 타입체크) 모두 통과.

## 4. 커밋·푸시 (2회 요청)

1차 요청("지금까지 작업을 커밋앤푸시해줘")에는 **이번 세션에서 만든 파일만** 스코프로 커밋 —
`8f1b066` "feat: 제품 분석 + 마케팅 방향 AI 서비스", 22개 파일, 1,164줄 추가.

2차 요청("나머지 파일들도 커밋할 게 있으면 커밋하고 푸시해 줘")에는 이전 세션([[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]])의
잔여 미커밋 변경(`spaces` 모듈, `objects`/`web-ai`/`web-sdk` 수정)을 별도로 정리해 커밋 —
`d4c4ffd` "feat: Space 모듈 추가 및 관련 프론트엔드 UI 개발", 22개 파일, 1,831줄 추가/수정. 두 커밋 모두
`main`에 push 완료.

## 남은 것

- 배포 인프라 미비 — S3 버킷/CloudFront 배포 ID(`__SET_CLOUDFRONT_ID__` 플레이스홀더), 도메인 연결 필요.
- API 서버 기동 필요(로컬 `.env.local` + `GEMINI_API_KEY`)해야 실제 분석 동작 확인 가능 — 이 세션에서는
  미실행.

## 관련
- [[lampas-studio]] (product-insights 섹션 신설)
- [[new-app-scaffold-from-slim-base]] (신규 스킬 — 이 세션에서 추출)
- [[clone-sibling-entity-pipeline]] (전날 세션의 형제 패턴, 엔티티 복제 vs 이번 앱 복제)
- [[deploy-sandbox-pnpm-shim]] (설치 실패 우회 세부 갱신)
- [[progdesigner]]
