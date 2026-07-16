---
tags: [entity, project, product, toktalk, nestjs, react, monorepo, ai-chat]
created: 2026-07-09
updated: 2026-07-16
---
# toktalk (TokTalk — AI 캐릭터/보이스 챗)

`[[progdesigner]]`의 AI 캐릭터/보이스 챗 제품. 도메인 `toktalk.ai`. `[[lampas-studio]]`(이미지 생성)와 **별개의 제품 코드베이스**이며,
`[[lampas-harness]]`/`[[lampas]]`(에이전트)가 개발·배포한다. 초기엔 단순 "Voice Chat" 프로토타입(web/·api/ 2폴더, 포트 3701/5174)이었으나
현재는 7개 앱 pnpm 모노레포로 확장(2026-07-08 문서 최신화 시점 확인).

## 앱 구성 (pnpm 모노레포, 관찰된 포트)

| 앱 | 역할 | 포트 |
|----|------|------|
| `apps/talk-api` | 메인 API (NestJS + Prisma/MySQL) | 3241 |
| `apps/talk-app-toss-api` | Toss 미니앱용 API | 3242 |
| `apps/talk-web-www` | 랜딩/웹 | 8241 |
| `apps/talk-web-app` | 사용자 플레이 웹앱 (React 19 + Vite, JS) | 8242 |
| `apps/talk-web-admin` | 어드민/스튜디오 (React 19, TS) | 8243 |
| `apps/toss-mina` | Toss 미니앱 | 8245 |
| `apps/toss-brainrot` | Toss 미니앱 | 8246 |

## 기술 스택 (관찰된 범위)
- 백엔드: NestJS 10 + Prisma 6.19(MySQL). API 도메인 `api.toktalk.ai`.
- 프론트: React 19 + Vite. 웹앱은 JS, 어드민은 TS.
- 실시간 채팅: Firebase. AI: OpenAI, Grok, DeepSeek, Gemini, ElevenLabs, AtlasCloud WAN. 음성/영상: Agora. 결제: Toss Payments.
- 각 앱 로컬 env: `env:local` 스크립트가 `env/.env.local → .env` 복사.

## 배포 (README 최신화 시점)
- API: rsync + PM2 (`micro.elevino.io`, 프로세스 `dbs-talk-api`/`dbs-talk-app-toss-api`).
- 웹 3종: S3(`s3://toktalk.ai/*`) + CloudFront 무효화.
- Toss 미니앱: `ait deploy`.
- 문서: 루트 `README.md`(2026-07-08 전면 재작성), `docs/ARCHITECTURE.md`(낡은 포트·env만 수정, DB 스키마·워크플로 본문은 2026-01 기준 잔존 — studio/play 모듈 미반영).

## 에피소드 플레이 시스템 (스튜디오 핵심)
캐릭터와의 **에피소드(beat 기반 시나리오)**를 재생하는 기능. 지문/대사가 순차 공개되고, 선택지·연출로 배경이 전환된다.
어드민 스튜디오(`talk-web-admin`)에서 제작, 웹앱(`talk-web-app`)에서 플레이. **플레이 로직은 두 앱에 중복 구현**(`beatPlay.js` / `beatPlay.ts`)되어 동시 수정 필요.
상세: → `[[episode-beat-play-system]]`

- 주요 파일: `talk-web-app/src/utils/beatPlay.js`, `.../pages/PlayEpisodePage.jsx`; `talk-web-admin/src/studio/beatPlay.ts`, `.../studio/types.ts`, `.../app/episodes/[id]/page.tsx`(편집기), `.../app/episodes/[id]/play/page.tsx`(테스트 플레이).
- API는 beats를 **자유 구조 JSON**으로 저장(`studio.dto.ts`, 스키마 검증 없음) → 클라 beat 구조 변경 시 서버 배포 불필요.

## 보안 주의 (관찰됨)
- `.env`(실제 API 키)와 Firebase 서비스 계정 JSON(`apps/talk-app-toss-api/config/toktalk-*-firebase-adminsdk-*.json`)이 **git 추적 중**이며 원격에 푸시됨(커밋 `0664ea9`, 이전 `b7fdf05` 패턴). 노출 상태 기록만.

## 참고 예시로 지목됐으나 미확인 (2026-07-16)
- `[[lampas-harness]]`에 ElevenLabs 실시간 TTS(`tts-stream`) 기능을 만들며 사용자가 `dbs/talk-system`
  (이 프로젝트)의 기존 구현을 참고 예시로 지목했으나, **작업 폴더 밖이라 하네스가 읽기조차 차단**해
  실제 구현(보이스 ID·모델·스트리밍 방식)은 끝내 확인되지 않았다. → [[work-folder-sandboxing]] ·
  [[2026-07-16-tts-stream-elevenlabs-구현착수]]

## 관련
- 세션: [[2026-07-08-toktalk-에피소드-배경전환-플레이]] · [[2026-07-16-tts-stream-elevenlabs-구현착수]](참고 예시로 지목, 접근 실패)
- 토픽: [[episode-beat-play-system]] / 스킬: [[loading-state-key-mismatch]]
- 개발/배포 주체: [[lampas]] on [[lampas-harness]] · 공급자: [[progdesigner]]
- 자매 제품(별개 코드베이스): [[lampas-studio]]
