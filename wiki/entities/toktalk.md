---
tags: [entity, project, product, toktalk, nestjs, react, monorepo, ai-chat, avatar]
created: 2026-07-09
updated: 2026-09-26
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
- 실시간 채팅: Firebase. AI: [[openai]], [[grok]], DeepSeek, [[gemini]], [[elevenlabs]], [[atlas-cloud]] WAN. 음성/영상: Agora. 결제: Toss Payments.
- 각 앱 로컬 env: `env:local` 스크립트가 `env/.env.local → .env` 복사.

## 배포 (README 최신화 시점)
- API: rsync + PM2 (`micro.elevino.io`, 프로세스 `dbs-talk-api`/`dbs-talk-app-toss-api`). 배포 도메인이 [[elevino-system]]과 겹침(`micro.elevino.io`) — 같은 호스트 공유 정황.
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
  → 반복 패턴으로 묶임: [[secrets-plaintext-exposure-pattern]]

## 2026-09-26 모순 — `lampas-system` 저장소 편입 정황 (미확인)

`lampas-web-fit` 구축 세션([[2026-09-25-lampas-web-fit-구축-배포]], `Tool: codex`)의 시스템
프롬프트로 주입된 `[[lampas-studio]]` 저장소(`lampas-system`) 루트 `AGENTS.md`가 `apps/talk-api`,
`talk-app-toss-api`, `talk-app-toss-samantha`, `talk-app-toss-brainrot`, `talk-web-www`,
`talk-web-app`, `talk-web-admin`을 **`lampas-system`의 앱 목록 하위 항목**으로 열거하고,
`talk-api`엔 **"구 dbs/talk-system"** 주석이 달려 있다. 이 페이지가 2026-07 기준 기록한
"`[[lampas-studio]]`와 **별개의 제품 코드베이스**"라는 서술과 정면으로 모순된다.

- **미확인 사항**: 이 세션은 `lampas-system`의 `AGENTS.md` 문서를 그대로 인용받았을 뿐, 실제로
  두 저장소가 병합됐는지·`AGENTS.md`가 단순히 인접 작업 편의상 두 저장소를 함께 문서화한 것뿐인지는
  검증되지 않았다. 포트 번호(`talk-api` 3241 등)는 이 페이지의 기존 기록과 동일해 최소한 **런타임
  분리는 유지**되는 것으로 보인다.
- **신규 앱 2종 노출**: `talk-web-virtual`(포트 8247, `virtual.toktalk.ai` — 사진 아바타·실시간 영상
  대화) · `talk-api-virtual`(포트 3247, Tavus 영상 대화 전용 독립 Node 서버) — 이 페이지의 기존 7앱
  목록에 없던 기능. 관련 문서·스크립트·디자인은 `docs/talk/`·`scripts/talk/`·`design/talk/`에,
  배포 미러·CLI 도구는 `deploy/talk/`·`tools/talk/`(비추적)에 위치한다고 명시.
  **✅ 2026-09-26 확정**: 이 추측이 그대로 실사 구축·배포로 확인됨 → `[[2026-09-26-virtual-toktalk-tavus-아바타-구축]]`.
  `[[tavus]]` CVI(사진→아바타 Phoenix-4/4.5 + 실시간 한국어 영상통화 + 카메라 인식 Raven + 메모리)
  기반. 기본 캐릭터는 "하루"에서 "한소연"으로 교체(정면·배경제거 편집), 시작 화면은 한소연 단일
  선택으로 축소. 계정당/사이트 전체 일일 통화 횟수, 최대 통화 시간(요금제 상한 15분)은 **Tavus가
  아니라 이 서비스 자체 서버 env**로 제한 → `[[self-imposed-vs-provider-limit-diagnosis]]`.
- **개명 추정**: 기존 `apps/toss-mina`·`apps/toss-brainrot`이 `talk-app-toss-samantha`·
  `talk-app-toss-brainrot`으로 이름이 바뀐 것으로 보임(포트 8245/8246 동일 유지) — 확정은 아님.
- **2026-09-24 재확인**: `lampas-system` 저장소를 멀티커밋 푸시하던 세션
  ([[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]])에서 `talk-app-toss-samantha`가
  `DEPLOYMENT.md`·`package.json`·`sync-from-web.mjs`를 가진 실제 앱으로 git 병합 충돌(로컬 0.1.6 vs
  원격 0.1.7, 원격 채택)을 통해 재확인됨 — `AGENTS.md` 문서 인용을 넘어선 두 번째 독립 확인.
- Prisma는 `pnpm prisma:generate:talk` 등 `:talk` 접미 스크립트를 쓴다고 명시 — 별도 스키마 유지
  정황(완전 병합이 아닐 가능성을 뒷받침).

## 참고 예시로 지목됐으나 미확인 (2026-07-16)
- `[[lampas-harness]]`에 ElevenLabs 실시간 TTS(`tts-stream`) 기능을 만들며 사용자가 `dbs/talk-system`
  (이 프로젝트)의 기존 구현을 참고 예시로 지목했으나, **작업 폴더 밖이라 하네스가 읽기조차 차단**해
  실제 구현(보이스 ID·모델·스트리밍 방식)은 끝내 확인되지 않았다. → [[work-folder-sandboxing]] ·
  [[2026-07-16-tts-stream-elevenlabs-구현착수]]

## 관련
- 세션: [[2026-07-08-toktalk-에피소드-배경전환-플레이]] · [[2026-07-16-tts-stream-elevenlabs-구현착수]](참고 예시로 지목, 접근 실패) ·
  [[2026-09-25-lampas-web-fit-구축-배포]](저장소 편입 모순 발견) · [[2026-09-26-virtual-toktalk-tavus-아바타-구축]](virtual.toktalk.ai 구축) ·
  [[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]](talk-app-toss-samantha git 충돌로 재확인)
- 토픽: [[episode-beat-play-system]] · [[realtime-photo-avatar-voice-ai-landscape]] / 스킬: [[loading-state-key-mismatch]] · [[self-imposed-vs-provider-limit-diagnosis]]
- 외부 AI 프로바이더(virtual): [[tavus]]
- 개발/배포 주체: [[lampas]] on [[lampas-harness]] · 공급자: [[progdesigner]]
- 자매 제품(2026-07 기준 별개 코드베이스로 기록, 2026-09-26 `lampas-system` 편입 정황과 모순 —
  위 절 참고): [[lampas-studio]]
