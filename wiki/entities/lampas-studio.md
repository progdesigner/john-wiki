---
tags: [entity, project, product, image-generation, nestjs, react, instagram]
created: 2026-07-09
updated: 2026-07-16
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

- **생성**: Gemini(기본, 멀티 이미지 그리드 직접 생성) / Atlas Cloud 경유 — `gpt-image-2`, `nano-banana-2`, `grok-imagine`, `wan-2.7`. Gemini/Atlas가 동일 `generateImage` 시그니처라 선택값으로 서비스만 분기.
- **분석**: Grok / Gemini.
- 갤러리 편집 기본 이미지 모델: `google/nano-banana-2/edit`.
- UI 선택은 localStorage로 기억(`lampas_gallery_edit_image_model`, `lampas_studio_analyze_model`). → `[[localstorage-ui-preference-persistence]]`

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

## 관련
- 세션: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
- 개발/배포 주체: [[lampas]] on [[lampas-harness]]
- 공급자: [[progdesigner]]
- 포트폴리오 배경: [[works-project-portfolio]]
