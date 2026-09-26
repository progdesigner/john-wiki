---
tags: [session, lampas-studio, dalar, video-generation, ui, deploy, git]
created: 2026-09-26
updated: 2026-09-26
---
# Studio 개선 — Seedance 2.0 Mini 추가 + 노드 툴바 UX + 배포/멀티커밋 푸시

`Tool: claude` 세션. 작업 폴더 `/Users/progdesigner/Works/lampas/lampas-system`(=[[lampas-studio]]),
2026-09-24 22:13 UTC 시작. 3왕복: (1) 기능 구현, (2) 배포, (3) 전체 커밋·푸시.

## 요청 1 — 기능 2건 구현

> "studio.lampas.io 에 동영상 생성 시 Seed Dance 2.0 Mini 도 적용해주고 그리고 선택을 하면 오른쪽
> 왼쪽에 버튼이 둘다 나오게 해줘 오버할 때 나오지 말고 선택이 어려우니까 바꾸는 거야"

**1. Seedance 2.0 Mini 모델 추가** — SoT는 `[[dalar]]`의 `dalar-web-app`(Node Studio)이라는 점을
어시스턴트가 확인하고 그쪽에서 먼저 수정한 뒤 `pnpm sync:studio`로 `lampas-web-studio`에 반영,
**드리프트 0** 확인. [[dalar]] 엔티티가 이전엔 `AGENTS.md` 문서 인용만으로 "SoT가 dalar-web-app"이라고
추정 기록했던 것을, 이 세션이 **실제로 그 명령을 실행해 검증**했다 — 열린 질문 중 하나가 해소됨.

- 모델 피커에 "Seedance 2.0 Mini"(56cr/초, 시작 프레임 1장)와 "Seedance 2.0 Mini · 여러 이미지"
  (레퍼런스 최대 9장) 두 항목이 Fast와 2.0 사이에 추가.
- `modelCapabilities.js`에 정적 폴백(4~15초, 480p/720p, 원본 비율, 오디오 지원) 추가.
- `lampas-api`·`dalar-api`의 `video.dto.ts` 허용 목록 + `atlas-cloud` 서비스의 reference-to-video
  판별 정규식에 mini 포함 — **누락 시 서버 400** 이라는 점을 명시(허용 목록·정규식이 실제 게이트).
- `lampas-api` 크레딧 오버라이드에 mini image-to-video(56크레딧/초, 제품 `lampas-web-studio`) 추가.

**2. 노드 선택 시 좌우 + 버튼 동시 표시** (`NodeStudioCanvas.jsx`) — 포인터가 노드 중심선 어느
쪽에 있는지 추적하던 전역 pointermove 리스너 2개를 제거하고, **선택 시** 좌우 `NodeToolbar`가
동시에 뜨도록 변경(각 방향은 연결 가능한 노드가 있을 때만 표시, 메뉴는 한쪽만 열림). 기존엔 hover
방향에 따라 한쪽만 나와 선택이 어렵다는 사용자 피드백이 계기.

**부수 수정**: `lampas-web-studio`의 `src/lib/workDetailPath.spec.js`가 `node:test` 문법이라 vitest에서
실패 중이었고, 이 상태면 `deploy-web.sh`의 테스트 게이트가 막힘 — vitest 문법으로 교체. studio 테스트
228개 전부 통과, Vite 빌드 성공, lampas-api 타입체크·credits 스펙 통과. (`dalar-api`의 `tsc`가
`spaces.service.ts`의 `lampasGenerationJobId` 오류로 실패 중이었으나, 이번 변경과 무관한 기존 Prisma
클라이언트 공유 문제로 확인·불문에 부침.)

## 요청 2 — 배포

배포 전 운영 DB 드리프트 확인("empty migration", 깨끗함) + lampas Prisma 클라이언트 재생성 →
`lampas-api` 배포(0.1.140, micro 서버 PM2 online) → `lampas-web-studio` 배포(0.5.40, S3+CloudFront
무효화). 운영 `GET /v1/credits/pricing`에서 Seedance 2.0 Mini 56크레딧/초 노출 확인. 스모크 테스트는
`.env.smoke` 미설정으로 두 스크립트 모두 건너뜀.

**선택적 스테이징으로 커밋** — 이번 작업 파일만 커밋(`7b50335f`, 16개 파일). 공유 `atlas-cloud.service.ts`
두 파일은 다른 미커밋 수정이 섞여 있어 Mini 정규식 한 줄짜리 헝크만 골라 스테이징. `video.dto.ts`·
`modelCapabilities.js`·`studioModelCatalog.js`는 작업 전부터 있던 미커밋 변경(다른 모델 추가분)이
같은 파일에 섞여 있어 함께 커밋됨 — 이 코드는 방금 배포된 빌드에도 포함돼 있어 운영과 커밋은 일치.
절차 스킬 → [[selective-hunk-commit-shared-file]]. 나머지 미커밋 변경(copy·flow-works·pulse·voice
등)은 그대로 두고 푸시는 하지 않음.

## 요청 3 — 전체 커밋 후 푸시

작업 트리가 깨끗한 상태(위 커밋으로 정리됨)에서 원격의 새 커밋 10개를 병합하고 푸시.

**커밋 내역**(Seedance Mini 커밋 뒤로 4개 + 머지 1개):
- `9b844c99` feat(fixs): 앱 전반 fixs 오류 리포팅 + 에이전트 대시보드 (16 파일)
- `2d4934b4` feat(trends): flow 트렌드 작업, pulse 페르소나 트렌드, 에이전트 트렌드 리서치 (17 파일)
- `e07db82f` feat(spot): **`lampas-web-spot` 식당 지도 앱 신규** (27 파일) — 이 세션이 만든 게 아니라
  다른 세션의 작업이 원격에 이미 있던 것을 pull로 받아 로컬에 커밋한 것. → [[lampas-web-spot]]
- `0f0047c5` chore: 나머지 진행 중 작업 일괄 (159 파일 · api·agent·studio·pulse·copy·flow·talk·
  index.html 메타·배포 버전 범프)
- `755ab151` origin/main 머지

**머지 충돌 3건**은 모두 `talk-app-toss-samantha`(`[[toktalk]]` 소속) 관련 — 원격이 이미 0.1.7로
배포된 상태(`DEPLOYMENT.md` 테스트 링크·`package.json` 버전·`sync-from-web.mjs` 로그 문구)여서
로컬 0.1.6 대신 **원격 쪽을 그대로 채택**. [[toktalk]] 엔티티가 이전에 `AGENTS.md` 인용만으로 기록한
`talk-app-toss-samantha` 존재를 이 세션이 실제 git 충돌로 재확인.

**부수 관찰**: `lampas-web-spot`의 `.env.local`과 `env/.env.production`에 Naver 지도 클라이언트 ID가
평문으로 커밋됨 — 다른 앱의 env 커밋 관례와 동일하게 함께 올라갔고, 어시스턴트는 "프론트 공개 키라
문제는 없지만 비공개로 두고 싶으면 알려달라"고 병기.

## 관련
- 엔티티: [[lampas-studio]] · [[dalar]] · [[lampas-web-spot]] · [[toktalk]]
- 스킬: [[selective-hunk-commit-shared-file]] · [[multi-repo-bulk-commit-push]](유사 패턴, 이번은 단일
  저장소) · [[prod-ddl-before-deploy-with-drift-check]](배포 전 드리프트 확인 절차)
