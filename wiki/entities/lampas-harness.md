---
tags: [entity, project, tool, claude-agent-sdk, typescript]
created: 2026-07-07
updated: 2026-07-12
---
# lampas-harness

Claude Agent SDK 기반 웹 하네스. `[[progdesigner]]`의 맥미니에서 데몬으로 돌며,
웹 채팅 UI로 에이전트(`[[lampas]]`)에게 작업을 시키고 큐로 백그라운드 태스크를 처리한다.

- 저장소: `github.com:lampas-dev/lampas-harness` (`main` 브랜치)
- 로컬 경로: `/Users/progdesigner/Works/lampas/lampas-harness`
- 언어/런타임: TypeScript, `@anthropic-ai/claude-agent-sdk@0.3.201`
- 실행: 맥미니, `0.0.0.0:8787` 바인딩, LAN `http://192.168.0.5:8787`
- 인증: `HARNESS_TOKEN` (비-localhost 접속 시 필수), `ANTHROPIC_API_KEY` 직접 키(구독 로그인 아님). OpenAI 키도 보유.
- 권한 모드: 기본 `bypassPermissions`(맥 전체 제어) — 그래서 공개 노출은 위험.

## 아키텍처 (관찰된 범위)

- `src/server.ts` — 웹 채팅 서버. `sessions: Map<chatId, ChatSession>` 인메모리 세션, 트랜스크립트 append(`<logDir>/chats/{chatId}.md`), 사이드카 세션 영속화(`{chatId}.session.json`), 히스토리 jsonl.
- `src/queue.ts` — 큐 스키마/enqueue/status. `daemon.ts`에서 분리됨.
- `src/runner.ts` — 큐 태스크 실행기.
- `harnessctl` — launchd 제어 스크립트 (install/start/stop/restart/status/logs).
- `launchd/io.lampas.harness.daemon.plist`, `...memory-lint.plist` — LaunchAgent 정의.
- 로그: `logs/daemon.out.log`, `logs/daemon.err.log`.

## 주요 기능 (2026-07-06~07 세션에서 구축)

- launchd 데몬화 + 제어 스크립트 → `[[macos-launchd-daemon]]`
- ad-hoc 프롬프트 태스크 큐 + 세션 resume → `[[harness-queue-vs-chat]]`
- 서버 크래시 후 대화 맥락 복구(사이드카) → `[[sdk-session-persistence]]`
- 모델 선택기(Claude+OpenAI, 기본 `claude-opus-4-8`) → `[[model-selection]]`
- 네트워크 재시도/멱등성(msgId dedupe) → `[[idempotent-message-retry]]`
- 메시지별 작업 폴더 샌드박싱 → `[[work-folder-sandboxing]]`
- thinking 스트리밍, 연결 상태 아이콘, 세션 중지, 대화 목록(햄버거)/보관, FIFO 대기 큐.

## 추가 기능 (2026-07-08~09 세션)

- **세션 독립 예약 메시지 스케줄러** — `src/scheduler.ts`(신규). 예약을 `queue/scheduled/`에 파일 영속화,
  서버 0.5초 폴링, 도착 시각 기준 정확 전달. `schedule_message`/`list_scheduled`/`cancel_scheduled` MCP 도구.
  → `[[session-independent-scheduler]]` · 배경 `[[harness-background-process-lifecycle]]`
- **로컬 LLM 프로바이더** — `server.ts`에 로컬 프로바이더 추가. 로컬 Rapid-MLX 서버(`localhost:8000/v1`,
  launchd `io.lampas.rapidmlx` 상주) 감지 시 모델 선택기에 **"Local" 그룹**으로 `qwen3.5-35b-8bit` 노출.
  → `[[local-llm-rapidmlx-install]]` · `[[model-selection]]`
- **사용 영역 페르소나** — 6개 영역(비즈니스/브랜딩/마케팅/이미지/영상/일반) 전문가 페르소나 프롬프트.
  조회·수정 API + 설정(⚙) 편집 UI, 수정본은 `presets.json`에 저장(✏️ 표시·초기화). 기존 프롬프트 칩은 제거.
  - **변경(2026-07-09)**: "일반"은 처음엔 *페르소나 없음(=기본 람파스)*으로 정의됐으나, 이 세션에서
    사용자가 "일반"에도 페르소나를 설정하도록 지시해 *비어있음 → 채움*으로 바뀜. 계기는 "페이블5 숨은
    지시문" 프로모션 루머(취지만 참고, 원문은 복제 안 함). → [[2026-07-09-일반-사용영역-페르소나-설정]]
    · [[system-prompt-mimicry-misconception]]
- **구조적 한계 규명** — 에이전트가 턴 안에서 띄운 배경작업은 턴/세션 종료로 죽는다(스케줄러·다운로드 두 스레드 관통).
  → `[[harness-background-process-lifecycle]]`

## 추가 기능 (2026-07-11 세션)

- **desktop 퀵 채팅(Electron) 클라이언트 + launchd 상주** — `apps/desktop/main.js`(Electron), 전역 단축키
  **Option+Space**로 `apps/web/public/quick.html` 퀵 채팅 오버레이 소환. `scripts/install-desktop.sh`/
  `uninstall-desktop.sh`로 `~/Library/LaunchAgents` 사용자 LaunchAgent 등록(`LimitLoadToSessionType: Aqua`,
  `KeepAlive={SuccessfulExit: false}`, 로그 `logs/desktop.*.log`). `self-update.sh`는 desktop만 변경 시
  `launchctl kickstart -k`로 앱만 재기동. → GUI 앱 함정은 [[macos-launchd-daemon]]에 반영. 세션:
  [[2026-07-11-desktop-퀵채팅-설치-스크립트]]
  - **로컬 모델 결과물이 실행 불가**여서 상위 모델이 재작성한 사례(권한/Electron 바이너리/launchctl 순서 3중 오류).
    로컬→고급 모델 수동 에스컬레이션 → [[model-selection]] · [[local-llm-on-apple-silicon]].
- **git 도구 in-agent 노출** — `src/fsTools.ts`에 git 관련 도구 추가, `src/webTools.ts` 신규(웹 도구 유틸).
- **재시작 스크립트 2종** — `restart-lampas.sh`(수동 즉시: `pkill -f lampas` 후 daemon/server/scheduler 재시작),
  `restart-when-idle.sh`(유휴 감지 후 `restart-lampas.sh` 호출 — 자동 메모리 정리용).
- ⚠️ **Google Models 401** — 2026-07-08~11 사이 20회+. API 키/인증 문제 추정, 기본(Claude/Qwen) 동작엔 영향 없음(미해결).

## 커밋 이력 (2026-07-11 세션)

| 커밋 | 내용 |
|------|------|
| `7694af0`→`6f3704f` | fsTools에 git 도구 추가 + server.ts 문서 주석 |
| `6f3704f`→`9132930` | desktop 설치 스크립트 외 8파일(511+/18-): install/uninstall-desktop.sh, install.sh·self-update.sh 갱신, main.js·quick.html·server.ts·webTools.ts |

## 커밋 이력 (2026-07-06~07 세션)

| 커밋 | 내용 |
|------|------|
| `3ecc049` | launchd 라벨 com.lampas → io.lampas 리네임, harnessctl 추가 |
| `2a3b6c1` | ad-hoc 태스크 큐 + 세션 resume + 큐 상태 패널 |
| `cc76704` | 채팅 UX 개선 (모델 선택기, 히스토리 영속화, 메시지 큐, 중지/thinking) |
| `3749e8a` | 네트워크 재시도/멱등성 + 큐 패널 내 중지 버튼 이동 |
| `5cb33ae` | 대화별 작업 폴더 선택 + 폴더 밖 접근 차단 |
| `155e641` | 대화 보관, 대기 큐 표시/취소, 대화별 폴더 잠금 |

## 제안된 기능 — 장기기억 provider 연동 (2026-07-08, 미구현)

`[[progdesigner]]`가 "과거 작업 이력을 기억 못 한다"는 문제를 제기해, `[[john-wiki]]`를
**memory provider로 붙이는 설계**가 도출됨. 당시 어시스턴트에 파일 도구가 없어 **제안·스케치 단계**에 그침.

- `MemoryProvider` 인터페이스 + `JohnWikiMemoryProvider`(rglob·토큰 스코어링·발췌)
- `AI_CONTEXT.md` 자동 주입 + `search/read/list` tool 노출 + 안티할루시네이션 지침
- **주의**: 제안 스케치는 Python/OpenAI-style. 이 하네스는 TS/Claude SDK라 재작성 필요.
- → 상세·함정: [[wiki-memory-provider-integration]] · 배경: [[long-term-memory-architecture]] · 세션: [[2026-07-08-장기기억-provider-연동-설계]]

### 진행 관찰 (2026-07-11)
- `src/config.ts`에 `wikiDir: process.env.WIKI_DIR ?? path.join(os.homedir(), "Works", "llm-wiki")` 설정과 `WIKI_DIR` env 오버라이드가 들어옴 → wiki 연동 배선이 **코드 레벨에선 착수**됨(2026-07-08 "완전 미구현"에서 진전).
- 그러나 어시스턴트에 **조회 tool은 아직 미노출** — 채팅에서 "기억 요약"·wiki 위치 질문에 여전히 답 못 함.
- **⚠️ 경로 불일치**: 기본값 `~/Works/llm-wiki`인데 실제 저장소는 `~/Works/john-wiki`. `WIKI_DIR` env 미설정 시 없는 경로를 가리킴. → 수정 필요.
- → 세션: [[2026-07-11-기억-요약-wiki-경로-확인]]

## 관련
- 세션: [[2026-07-06-lampas-harness-구축]] · [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-08-장기기억-provider-연동-설계]] · [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]] · [[2026-07-09-일반-사용영역-페르소나-설정]] · [[2026-07-11-desktop-퀵채팅-설치-스크립트]]
- 개발 대상 제품: [[lampas-studio]] — 이 하네스로 `[[lampas]]`가 개발·배포하는 이미지 생성 제품.
- 연동 대상 장기기억: [[john-wiki]] (제안).
- `lampas-system` — 인접 저장소(이 세션과 별개, 미커밋 변경 2개 존재).

## 재확인된 함정 (2026-07-08~09 세션)
- **"Stream closed"** — 파일 수정 도구 권한 승인 채널이 세션 중 끊겨 편집 불가(2회). 사용자 재전송/재시작으로 복구.
- **인자 스캐너** — bash 인자에 `https://…`·슬래시 경로·S3 경로가 있으면 차단 → 스크립트 파일로 작성해 실행하는 우회 반복. → [[deploy-sandbox-pnpm-shim]]
- **권한 승인 UI 스트림 끊김** — `rapid-mlx` 실행이 권한 프롬프트 스트림 불안정으로 반복 실패, 수십 초 대기 후 회복.
- **에이전트 배경작업이 턴/세션 종료로 죽음** — 예약 타이머·긴 다운로드 모두 영향. → [[harness-background-process-lifecycle]]
- **일본어 드리프트** — 어시스턴트가 긴 코딩·영어 컨텍스트 속에서 응답 언어를 한국어→일본어로 벗어남. 코드 버그 아님(언어 지시는 정상 주입), 모델 행동 문제. 메모리 기록으로 대응.
