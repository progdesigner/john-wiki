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
- **개명 추정 → 확정**: 기존 `apps/toss-mina`가 `talk-app-toss-samantha`로 이름이 바뀐 것(포트
  8245 동일 유지)은 **[[2026-09-20-talk속도개선-사만다전환-스튜디오개편]] 세션에서 확정**됨(아래
  절 참고). `apps/toss-brainrot`→`talk-app-toss-brainrot`은 여전히 미확인.
- **2026-09-24 재확인**: `lampas-system` 저장소를 멀티커밋 푸시하던 세션
  ([[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]])에서 `talk-app-toss-samantha`가
  `DEPLOYMENT.md`·`package.json`·`sync-from-web.mjs`를 가진 실제 앱으로 git 병합 충돌(로컬 0.1.6 vs
  원격 0.1.7, 원격 채택)을 통해 재확인됨 — `AGENTS.md` 문서 인용을 넘어선 두 번째 독립 확인.
- Prisma는 `pnpm prisma:generate:talk` 등 `:talk` 접미 스크립트를 쓴다고 명시 — 별도 스키마 유지
  정황(완전 병합이 아닐 가능성을 뒷받침).

## 2026-09-20~21 — Talk 속도개선·사만다 전환·스튜디오 개편 (원본 이벤트 확인)

이번 ingest([[2026-09-20-talk속도개선-사만다전환-스튜디오개편]])로, 09-24/09-26 세션이
"추정"·"정황"으로만 남겼던 `talk-app-toss-samantha`의 실제 개명·구축 과정이 확인됨.

- **텍스트 대화 지연 수정**: `talk-api`/`talk-web-app` 텍스트 경로가 매 턴 세션 갱신 요청 +
  추론량 미지정 Grok + 게이트웨이 경유였던 것을 제거·`grok-4.3`(저추론) 기본화·xAI 직접 연결로
  전환. 첫 글자 지연 46.6초 → 운영 실측 0.83~1.35초. 사용자가 직접 고른 모델은 보존. →
  [[llm-chat-latency-direct-connection]]
- **개명 확정**: `apps/toss-mina` → **`talk-app-toss-samantha`** 로 이름 변경 + 모바일 웹앱 전면
  재설계(한 손 사용 하단 메뉴, 토스 사용자 식별 연동, 아이보리·테라코타 자동 다크모드). Toss 콘솔
  등록용 로고 2종(600×600)·스크린샷 4장(636×1048) 제작, 테스트 번들 업로드. 이전 페이지의 "개명
  추정"은 이 세션으로 **확정**됨.
- **어드민 스튜디오 전면 재구축**: `admin.toktalk.ai`를 사만다 전용 에피소드 스튜디오로 개편.
  기존 스토리·캐릭터 화면 제거, **대사→선택지→대사** 구조의 에피소드를 한 번에 1개만 생성해
  공개, 마지막 대사 이후 선택 이력을 이어받아 자유 대화로 전환. Eve 공식 음성 자동재생 연결.
  → 토픽 [[episode-beat-play-system]]에 구조 변경 반영.
- **⚠️ 데이터 삭제 이벤트**: 사용자가 "저장된 기존 에피소드까지 삭제"로 범위를 명시적으로 확정한
  뒤, 기존 스튜디오 에피소드 10개 + 구 방식 에피소드 4개(총 14개)와 연결 진행·해금 기록 19개를
  **백업 후 삭제**. 기존 채팅 로그는 보존. 삭제 전 되돌리기 어려움을 이유로 범위를 먼저 되물은
  절차가 세션에 기록됨.
- **NSFW 시드 오디오 요청 거부**: 사용자가 "Eve와 최대한 비슷한 시드 오디오로 야한 음성도 생성할
  수 있게" 요청했으나, 노골적 성적 음성 생성·그 용도의 seed audio 제작은 명시적으로 거부하고
  공식 Eve 음성으로 비노골적 로맨스 대사만 지원하는 대안으로 진행. 실제 구현에도 노골적 콘텐츠는
  포함되지 않음(세션 마지막에 재확인 문구).
- **유저·채팅 관리 페이지 복구**: 스튜디오 개편 중 내비게이션에서만 빠졌던 "서비스 관리"(유저
  정보·이전 채팅 관리) 경로를 복구, 사만다 스튜디오와 동일 톤(크림·테라코타)으로 통일. 이어
  배우·갤러리·게시물 메뉴는 숨김 처리.
- **웹/Toss 디자인 분리 확정**: 모바일 재설계는 **Toss 미니앱 전용**이었고 `app.toktalk.ai` 웹은
  기존 테라코타 디자인·로그인·헤더로 원복, 하단 앱 메뉴 제거. 대화·에피소드 기능(백엔드)은 공유,
  프론트 디자인만 웹/Toss 전용 화면으로 분리해 이후 동기화에도 서로 디자인이 새지 않게 함.
- 전체 작업을 293개 파일 커밋(`ee49a624`)으로 origin/main에 push, 사만다 Toss 앱 v0.1.6 재배포.

## 참고 예시로 지목됐으나 미확인 (2026-07-16)
- `[[lampas-harness]]`에 ElevenLabs 실시간 TTS(`tts-stream`) 기능을 만들며 사용자가 `dbs/talk-system`
  (이 프로젝트)의 기존 구현을 참고 예시로 지목했으나, **작업 폴더 밖이라 하네스가 읽기조차 차단**해
  실제 구현(보이스 ID·모델·스트리밍 방식)은 끝내 확인되지 않았다. → [[work-folder-sandboxing]] ·
  [[2026-07-16-tts-stream-elevenlabs-구현착수]]

## 관련
- 세션: [[2026-07-08-toktalk-에피소드-배경전환-플레이]] · [[2026-07-16-tts-stream-elevenlabs-구현착수]](참고 예시로 지목, 접근 실패) ·
  [[2026-09-20-talk속도개선-사만다전환-스튜디오개편]](사만다 개명·스튜디오 재구축 원본 이벤트) ·
  [[2026-09-25-lampas-web-fit-구축-배포]](저장소 편입 모순 발견) · [[2026-09-26-virtual-toktalk-tavus-아바타-구축]](virtual.toktalk.ai 구축) ·
  [[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]](talk-app-toss-samantha git 충돌로 재확인)
- 토픽: [[episode-beat-play-system]] · [[realtime-photo-avatar-voice-ai-landscape]] / 스킬: [[loading-state-key-mismatch]] ·
  [[self-imposed-vs-provider-limit-diagnosis]] · [[llm-chat-latency-direct-connection]]
- 외부 AI 프로바이더(virtual): [[tavus]]
- 개발/배포 주체: [[lampas]] on [[lampas-harness]] · 공급자: [[progdesigner]]
- 자매 제품(2026-07 기준 별개 코드베이스로 기록, 2026-09-26 `lampas-system` 편입 정황과 모순 —
  위 절 참고): [[lampas-studio]]
