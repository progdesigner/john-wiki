---
tags: [entity, project, tool, claude-agent-sdk, typescript]
created: 2026-07-07
updated: 2026-09-08
---

# lampas-harness

Claude Agent SDK 기반 웹 하네스. `[[progdesigner]]`의 맥미니에서 데몬으로 돌며,
웹 채팅 UI로 에이전트(`[[lampas]]`)에게 작업을 시키고 큐로 백그라운드 태스크를 처리한다.

- 저장소: `github.com:lampas-dev/lampas-harness` (`main` 브랜치)
- 로컬 경로: `/Users/progdesigner/Works/lampas/lampas-harness`
- 언어/런타임: TypeScript, `@anthropic-ai/claude-agent-sdk@0.3.201`
- 실행: 맥미니, UI(웹) `8787` + API `3787` 두 포트, `0.0.0.0` 바인딩, LAN `http://192.168.0.5:8787`. (포트 확인: [[2026-07-11-desktop-퀵채팅-설치-스크립트]])
- 원격 접속: 2026-07-06 세션 기준 **Tailscale 사설 VPN 권장**(자기 기기끼리 `100.x` 연결, 공유기 포트포워딩 불필요·비권장). 당시 실제 설치는 미진행. → [[2026-07-06-lampas-harness-구축]]
- 인증: `HARNESS_TOKEN` (비-localhost 접속 시 필수). OpenAI 키도 보유.
  - **Claude 실행 과금**: 2026-07-15 코드 확인 결과 **기본은 Claude Code 구독(OAuth, `CLAUDE_CODE_OAUTH_TOKEN`) 과금**이다 — `query()`가 `apiKey`를 안 넘겨 SDK가 `ANTHROPIC_API_KEY`가 없으면 OAuth로 폴백. `ANTHROPIC_API_KEY`는 모델 목록 조회에만 쓰였음. ⚠️ 2026-07-06~07 기록의 "`ANTHROPIC_API_KEY` 직접 키(구독 로그인 아님)"와 **모순** — 그 사이 인증 방식이 바뀌었거나 초기 기록이 부정확. (`.env`의 API 키 주석 상태는 2026-07-15 세션 내에서도 진술이 엇갈려 재확인 필요.) → [[sdk-claude-code-vs-api-billing]] · [[2026-07-15-과금모드-토글-컨텍스트표시]]
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
  → 상세·정책 변천: [[use-area-personas]]
  - **변경(2026-07-09)**: "일반"은 처음엔 *페르소나 없음(=기본 람파스)*으로 정의됐으나, 이 세션에서
    사용자가 "일반"에도 페르소나를 설정하도록 지시해 *비어있음 → 채움*으로 바뀜. 계기는 "페이블5 숨은
    지시문" 프로모션 루머(취지만 참고, 원문은 복제 안 함). → [[2026-07-09-일반-사용영역-페르소나-설정]]
    · [[system-prompt-mimicry-misconception]]
  - **⚠️→🗑️ 폐기(2026-07-16)**: 이 기능 자체가 사용자 지시로 전면 폐기됨(메뉴 일반/이미지 2개로 축소,
    설정 편집 UI·백엔드 주입 로직 3경로·`/api/presets`·`presets/*.md` 전부 삭제). 아래 해당 절 참고.
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
- **재시작 스크립트 2종** — `restart-lampas.sh`, `restart-when-idle.sh`(유휴 감지 후 `restart-lampas.sh`
  호출 — 자동 메모리 정리용).
  - ⚠️ **`restart-lampas.sh` 동작 방식 기록 불일치**: 2026-07-11 세션 당시엔 "수동 즉시: `pkill -f lampas`
    후 daemon/server/scheduler 재시작"으로 관찰됐다. 그러나 2026-07-15 세션 두 건
    ([[2026-07-15-gpt-realtime-음성입력-길게누르기]] 22:40~22:41, [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]])
    에서 원본을 직접 읽어 대조한 결과 **지금은 pkill 없이 nohup 지연 실행 + `launchctl kickstart -k`
    방식**이다 — 즉시 반환 → 백그라운드 워커가 5초 대기 → launchd로 데몬 재시작 → `/api/health` 폴링
    확인. 진행 중인 턴이 재시작으로 끊기지 않도록 안전화된 것으로 보인다(스크립트가 2026-07-11 이후
    재작성된 것으로 추정, 정확한 변경 커밋은 미확인). → [[self-hosted-agent-server-ops]] ·
    [[detach-long-job-nohup]]
- ⚠️→✅ **Google Models 401 / 미표시** — 2026-07-08~11 사이 20회+ 미해결로 이월됐던 항목. 2026-07-13
  auto-memory 이관에서 **유력한 근본 원인 규명**: 셸에 `GOOGLE_API_KEY=`(빈 값 export)가 있어 dotenv가
  `.env`의 진짜 키를 안 덮어씀 → `googleModels()`가 `[]` 반환. `src/config.ts`에서 빈 값 변수만 `.env`로
  채우게 수정. (401 인증오류 증상과 빈-목록 증상이 완전히 같은지는 미단정 — 개연적 해소.) → [[env-empty-var-shadows-dotenv]]

## 추가 기능 (2026-07-13 세션)

- **보관(🗄) → 장기기억 자동 저장** — `src/server.ts`(`POST /api/sessions/archive`)가 파일을
  `chats/archive/`로 이동하면서 `memory-ingest` 잡을 자동 큐잉하도록 개선. 기존엔 `🧠 기억에 저장`
  버튼 수동 조작만 위키 ingest를 걸었고 보관은 파일 이동만 했다. 이제 **보관 = 아카이빙 + 장기기억 저장**.
  - **멱등 게이트**: 트랜스크립트가 있고 아직 저장 안 했거나 저장 후 대화가 더 이어진(변경된) 경우에만
    ingest — `rememberedAt`(아카이브로 함께 이동) vs 트랜스크립트 변경 시각 비교. 이미 최신이면 건너뜀.
  - **함정**: 보관은 파일을 곧 이동하므로 ingest 입력을 **이동 후 경로**(`chats/archive/<id>.md`)로 지정.
  - 프런트: 보관 버튼 툴팁·안내 문구에 저장 반영, `apps/web/dist` 빌드. 응답에 `remembered` 플래그.
  - 배포는 서버 재시작 필요(`launchctl kickstart -k gui/$(id -u)/io.lampas.harness`) — 어시스턴트는
    자기 턴에서 재시작하면 채널이 끊겨([[self-hosted-agent-server-ops]]) 직접 실행하지 않음.
  - **큐→실행 경로 상세(2026-07-15 재확인)**: `enqueueJob`(`src/queue.ts:54-61`)은 `queue/`에 잡 파일만
    쓰고 즉시 반환(비동기) → `src/daemon.ts`가 `config.pollIntervalMs`(기본 5000ms, `src/config.ts:24`)
    간격으로 폴링, rename으로 원자적 선점 후 `runJob` 실행(최대 15분 타임아웃,
    `jobs/memory-ingest.job.json`). 야간 안전망 배치는 `jobs/memory-ingest-daily.job.json` +
    `io.lampas.harness.memory-ingest-daily.plist`라는 구체적 파일명으로 존재(기존엔 "새벽 5:30 크론"으로만
    뭉뚱그려 기록됨) → [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]]
  - → 세션: [[2026-07-13-보관시-자동-기억저장]] · [[2026-07-14-보관-자동저장-확인-볼린저밴드]](1차 재확인) ·
    [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]](2차 재확인) ·
    [[2026-07-16-기억버튼-보관통합-NaN토큰버그수정]](상단 수동 버튼도 이 API로 통합) ·
    배경: [[long-term-memory-architecture]]

## 추가 기능 (2026-07-15 관찰)

- **프로젝트 스킬 시스템 + 스킬 번들** — 커밋 `ef36c5f`에서 프로젝트 스킬 시스템과 대량의 스킬 번들이
  추가됨. 어시스턴트(Skill 도구)로 호출 가능한 스킬이 크게 늘었다:
  - **마케팅 계열**(가장 많음): 전략/기획(product-marketing, marketing-plan, marketing-ideas,
    marketing-council, marketing-psychology, marketing-loops, competitor-profiling, competitors),
    콘텐츠/카피(content-strategy, copywriting, copy-editing, lead-magnets, free-tools),
    퍼널(signup, onboarding, cro, popups, paywalls, pricing, churn-prevention, referrals),
    채널 실행(ads, ad-creative, cold-email, emails, social, sms, video, image, public-relations,
    co-marketing, community-marketing, prospecting, revops, sales-enablement),
    SEO(seo-audit, ai-seo, schema, site-architecture, programmatic-seo, directory-submissions),
    측정(analytics, ab-testing, customer-research, aso).
  - **Remotion(영상 제작)**: remotion-best-practices/-captions/-create/-interactivity/-markup/
    -render/-saas, mediabunny.
  - **개발/운영**: deep-research, dataviz, update-config, keybindings-help, verify, code-review,
    simplify, fewer-permission-prompts, loop, schedule, claude-api, run, init, review, security-review.
  - → 이 스킬들이 [[lampas]]를 코딩 외 [[harness-as-business-assistant]](특히 마케팅 컨설팅) 도구로
    확장한다. 첫 실사용: [[2026-07-15-올리브유-마케팅-포지셔닝]].

## 추가 기능 (2026-07-15 세션 — 과금 모드 토글)

- **API 과금 vs 구독 과금 토글** — Claude 모델 실행을 Claude Code 구독(OAuth)과 API 종량 중 골라 쓰게.
  - `src/server.ts`: `TurnOpts.apiBilling` 추가(`/api/chat` 파싱), **`claudeAuthEnv()` 헬퍼** 신설 —
    서브프로세스 env에서 `ANTHROPIC_API_KEY` 또는 `CLAUDE_CODE_OAUTH_TOKEN` 중 원치 않는 쪽을 지워
    인증 강제(SDK `Options`엔 `apiKey`가 없고 `env`가 통째 교체라 이 방식). API 모드인데 키 없으면 에러.
  - init 메시지의 `apiKeySource`, `stream.getContextUsage()`를 캡처해 `UsageTotals`에 `billingMode`/
    `contextWindow`로 저장. `/api/models`에 `anthropicApiKeyConfigured` 노출.
  - `apps/web/index.html`: 입력창 하단 "API 사용" 체크박스(**Claude 모델 선택 시에만** 표시, 키 없으면 비활성,
    `localStorage` 저장). 컨텍스트 모달(`◔ 컨텍스트`)이 **구독(oauth) 모드면 비용($) 대신 컨텍스트 잔여
    %(잔여 토큰)** 표시, API 모드면 기존 비용 표시.
  - 배포는 서버 재시작 필요 → 어시스턴트는 자기 턴에서 재시작 안 함([[self-hosted-agent-server-ops]]).
  - → 세션: [[2026-07-15-과금모드-토글-컨텍스트표시]] · 스킬: [[sdk-claude-code-vs-api-billing]] · 배경: [[claude-model-pricing]]
- **(후속, 같은 날 02:53) 5시간/주간 사용량 한도 게이지 — 미구현으로 확인.** 컨텍스트 잔여율 옆에
  rate limit(5시간/주간) 잔여율도 보여줄 수 있는지 조사한 결과, SDK(`usage_EXPERIMENTAL...()`,
  `rate_limit_event`)엔 이미 필요한 훅이 있는데 `server.ts`는 둘 다 호출하지 않음 — **기술적으로 가능,
  구현 안 됨**. 어시스턴트가 구현 여부를 물었으나 소스에 사용자 답 없음(미결). →
  [[2026-07-15-사용량한도-rate-limit-sdk-확인]]

## 추가 기능 (2026-07-15 세션 — desktop File 메뉴 New Window)

- **`apps/desktop/main.js`(Electron)에 File 메뉴 신설** — `createNewWindow()`가 mainWin과 동일한
  스타일(숨긴 타이틀바, `#1F2127` 배경)로 `index.html`을 로드하는 독립 창을 연다. mainWin과 달리
  닫으면 그냥 닫힘(앱 상주는 mainWin이 담당). 단축키 **`Cmd+Shift+N`**(`Cmd+N`은 `index.html` 내부
  "새 대화"와 충돌 회피). `app.whenReady()`에서 `Menu.setApplicationMenu(buildMenu())` 호출.
  → 세션: [[2026-07-15-데스크톱-file메뉴-new-window]]
- 같은 세션에서 쌓여있던 변경사항을 3커밋으로 정리해 push: `bc7d150`(quick.html 사진 첨부),
  `5c737bd`(대화별 API 과금 토글 — [[2026-07-15-과금모드-토글-컨텍스트표시]] 구현분), `085faa2`(이 기능).

## 추가 기능 (2026-07-14 세션 — quick.html 이미지 첨부)

- **`apps/web/public/quick.html`(퀵 채팅 오버레이)에 이미지 드래그 첨부 기능 이식** — `index.html`(메인
  채팅)에만 있던 기능을 동일하게 이식: 화면 드래그 시 드롭 오버레이, 📷 버튼 파일선택, 붙여넣기(스크린샷),
  캔버스로 1568px 축소·JPEG 재인코딩·최대 8장, 썸네일 미리보기, 전송 시 `/api/chat` 바디에
  `images:[{type,data}]`, 히스토리 복원 시 이미지 렌더링.
  - `vite build`로 소스(`public/quick.html`) → `dist/quick.html`(서버 서빙 대상) 재빌드.
  - **Playwright + macOS 시스템 Chrome(`channel:'chrome'`)으로 실브라우저 종단 검증** — 브라우저 바이너리
    미설치 환경에서의 우회. PNG 드롭→썸네일→전송 바디 `images` 포함→말풍선 렌더링까지 확인, JS 에러 없음.
    → [[playwright-system-chrome-verify]]
  - → 세션: [[2026-07-14-quick-html-이미지-첨부-구현]]

## 추가 기능 (2026-07-15 세션 — GPT Realtime 음성입력)

- **전송 버튼 길게 누르기(push-to-talk) 음성 입력** — OpenAI Realtime `gpt-4o-transcribe`(한국어 우선)로
  실시간 전사. 서버가 **1분짜리 임시 키**를 발급해 브라우저가 OpenAI에 직접 WebSocket 연결(키 노출 없음,
  전사 성공 여부는 서버 로그에 안 남음). 최초 구현 커밋 `b9eac27`.
- **버튼 3단계 시각 피드백**: 🟡노랑(준비중, 마이크 오픈 전 — 유실 가능) → 🔴빨강(녹음중, 유실 없음) →
  🟢초록(인식중, OpenAI `speech_started` 이벤트 수신 후). 커밋 `7450997`(웹만 변경, 새로고침으로 반영).
- **시작 부분 끊김 수정**: 원래 토큰발급→WebSocket 연결(0.5~1.5초) 완료 후에야 마이크 캡처가 시작돼
  그 사이 말한 내용이 유실되던 구조적 버그를 발견·수정 — 마이크를 먼저 열고 연결 중 오디오를 로컬
  버퍼에 쌓았다가 연결 직후 순서대로 플러시. 커밋 `938d4e4`. 재사용 가능한 패턴으로 스킬 추출:
  → [[realtime-voice-mic-buffer-before-connect]]
- **밀어내기 취소(swipe-to-cancel)**: 녹음 중 버튼에서 60px 이상 벗어나면 회색 "✕ 떼면 취소"로 전환,
  그 상태에서 떼면 전송하지 않되 받아쓴 텍스트는 입력창에 남김. 커밋 `5182ee0`.
- **재발견된 배포 함정 2종** (같은 세션에서): (a) launchd 데몬은 `dist/index.js`를 실행하므로 `src`만
  고치고 `npm run build:server`를 빠뜨리면 새 라우트가 반영 안 됨, (b) 이전 날(7/12) 수동 실행된 옛
  서버가 IPv6 와일드카드로 포트를 리슨하며 localhost를 가로챈 채 남아있었음. → [[self-hosted-agent-server-ops]]
  (함정 1 보강 + 함정 4 신설)
- **크레딧 소진의 파급 범위 재확인**: API 크레딧 부족이 `/compact`뿐 아니라 **백그라운드 `memory-ingest`
  잡**(이 위키로의 야간 자동 적재)까지 실패시킴을 이 세션에서 재확인(22:27, 2회 재시도 모두 실패,
  "Credit balance is too low"). → [[sdk-claude-code-vs-api-billing]] · [[long-term-memory-architecture]]
- → 세션: [[2026-07-15-gpt-realtime-음성입력-길게누르기]]

## 추가 기능 (2026-07-16 세션 — 음성입력 진동+비프 피드백)

- **노랑(준비중)→빨강(녹음중) 전환에 진동+비프 동시 피드백** — `apps/web/index.html`의 `startVoice()`
  내 `setVoiceUi("live")` 호출 시점(1870번째 줄)에 `voiceStartFeedback()` 신설.
  - **진동**: `navigator.vibrate(40)` — Android Chrome 등. iOS Safari는 진동 API 미지원(무음 실패).
  - **비프음**: 음성 캡처용으로 이미 열려 있는 `AudioContext` 재사용, 880Hz·약 90ms·부드러운 볼륨 램프.
  - 진동 미지원 기기(iOS)에서도 소리로 대체되도록 **두 채널 동시 발동**.
  - vite 빌드로 문법 오류 없음 확인. 배포는 여전히 `npm run build:web` + 데몬 재시작 필요(이 세션에서는
    재시작 미실행). → [[self-hosted-agent-server-ops]]
  - → 세션: [[2026-07-16-음성입력-진동비프-피드백]] · 스킬: [[realtime-voice-mic-buffer-before-connect]]
    (3단계 시각 피드백에 진동+비프 반영)

## 추가 기능 (2026-07-15 세션 — Auto 모델 난이도 자동선택 최초 구현)

- **"Auto — 난이도 자동 선택" 모델 드롭다운 옵션 신설** (22:42~22:52, 커밋 `802af89`, v0.1.25) —
  사용자가 기능만 요청하고 판정 모델 선택은 어시스턴트 재량에 위임. 서버가 easy/medium/hard 3단계로
  분류해 모델을 배정(easy→`claude-haiku-4-5`, medium→`claude-sonnet-5`, hard→`claude-opus-4-8`,
  `LAMPAS_AUTO_*` env로 재정의 가능). 판정 1순위로 Haiku 4.5(API)를 골랐으나 검증 중 **`.env`의
  `ANTHROPIC_API_KEY` 크레딧 잔액이 0이라 API 판정이 400으로 실패**함을 발견 —
  [[rapid-mlx]] 로컬 LLM(2순위)이 현재 실질적 판정 경로. 휴리스틱(3순위)은 로컬 서버가 꺼져도 턴이
  안 막히는 최종 안전망. 실사용 예시 7건 테스트로 로컬 판정 7/7 정확도 확인(few-shot 보정 후).
  → 세션: [[2026-07-15-auto모델-기능-최초구현]] · 상세: [[model-selection]]

## 추가 기능 (2026-07-15 세션 — Auto 모델 난이도 판정 감사 + 확인UX, 위 세션 31분 후)

- **Auto 모델 난이도 판정 감사 + extreme 티어 신설(Fable 5)** — 사용자가 "Auto가 난이도를 어떻게
  판단하는지" 물어 서버 코드(`src/server.ts`)를 file:line으로 감사: 판단은 **서버 100% 전담**,
  클라이언트(`apps/web/index.html`)는 표시만. 3단계 순차 폴백(Claude API `judgeTierClaude()` → 로컬
  LLM `judgeTierLocal()` → 휴리스틱 `heuristicTier()`)으로 easy/medium/hard 판정, `runChatTurn()`이
  결과에 따라 모델 배정. 이어서 **`"extreme"` 티어를 신설**해 `claude-fable-5`를 추가 매핑
  (`LAMPAS_AUTO_EXTREME`) — 대규모 아키텍처 개편/다중 시스템 리팩터/장애 대응/보안 설계급으로
  범위를 좁히고, 모호하면 hard를 택하도록 프롬프트에 명시. **휴리스틱 폴백은 hard까지만** —
  extreme은 LLM 판정으로만 도달하게 해 판정 실패 시 근거 없이 최상위 모델로 안 튀도록 설계.
  상세: [[model-selection]] · 세션: [[2026-07-15-auto모델-난이도판정-확인ux-개선]]
- **위험 버튼에 인라인 확인(모달 없이) 2종 추가**: (1) 진행 중 턴 "■ 중지" 버튼 — 첫 클릭 시
  "정말 중지?"로 전환해 3초 확인창, 재클릭해야 실제 중지, 3초 지나면 자동 원복. (2) 대화 목록 항목
  — hover 시 뜨던 🗄 보관 버튼을 없애고, **0.6초 꾹 누르면** 노랑 강조 후 보관 확인창이 뜨는 방식으로
  전환(8px 이상 이동 시 취소, 롱프레스 후 클릭 억제). 둘 다 `apps/web/index.html`만 수정, 실행 로직
  (`archiveSession` 등)은 안 건드리고 트리거만 교체. 재사용 패턴으로 스킬 추출:
  → [[destructive-action-inline-confirm]]
- **빌드(v0.1.27) + `restart-when-idle.sh` 백그라운드 실행**으로 위 서버·웹 변경사항 반영. 다른
  대화의 작업이 끝나는 것을 5초 폴링으로 기다렸다가(2회 연속 큐 비어있음 확인, 최대 1시간 대기)
  데몬 재시작 — 진행 중인 다른 작업은 안 죽임. → [[self-hosted-agent-server-ops]] ·
  [[detach-long-job-nohup]]
- **quick.html에 작업 폴더 선택기 + Auto 모델 추가**(02:16~02:20, 구현 세션 확인됨) —
  `apps/web/public/quick.html`(퀵 채팅 오버레이)에 index.html의 두 기능을 그대로 이식:
  - **폴더 선택** — 헤더 드롭다운, `/api/workdirs`로 목록 채움, 선택 폴더를 메시지 `cwd`로 전송해
    도구 실행을 그 폴더로 제한([[work-folder-sandboxing]] 재사용). `quick_workdir` 키로 `localStorage`
    저장([[localstorage-ui-preference-persistence]]).
  - **폴더 잠금** — 첫 턴 종료 후(서버 `done` 이벤트·히스토리 `workDir`) 선택기 비활성화, "새 대화"(⌘N)로 해제.
  - **Auto 모델** — 모델 목록 맨 위 "Auto — 난이도 자동 선택" 추가, [[model-selection]]의 서버 판정
    로직(easy/medium/hard/extreme) 재사용, 배정 결과를 `⚙ auto → claude-…`로 채팅에 표시.
  - **배포 요령**: `dist/quick.html`은 vite가 `public/`에서 그대로 복사하는 정적 파일이라, 전체
    `vite build` 없이 `cp`만으로 반영 확인 가능(퀵 창 재오픈으로 확인) → [[self-hosted-agent-server-ops]]
    함정4의 반대 사례로 스킬에 편입.
  - 수정 파일은 `quick.html` 하나, 이 시점엔 커밋 미완료.
  → 세션: [[2026-07-16-quick-html-폴더선택기-auto모델-구현]](구현) ·
  [[2026-07-16-quick-작업폴더선택기-커밋푸시]](18분 후 커밋 `c100edd`·push 확인)
- 위 v0.1.27 변경 5종(롱프레스 보관 확인·중지버튼 재클릭 확인·음성 진동+비프·extreme 티어·
  quick.html 작업 폴더 선택기) 커밋 **`c100edd`**로 `main` 반영, origin push 완료(2026-07-16).

## 추가 기능 (2026-07-15 세션 — 세션 상주 Claude 프로세스, 백그라운드 작업 생존)

- **채팅 중 Agent/백그라운드 Bash/Workflow가 턴 종료로 끊기던 버그 근본 수정** — 기존엔 턴마다 Claude
  Agent SDK 서브프로세스를 새로 만들고 답변 후 통째로 닫는 구조라, 그 안에서 돈 배경작업이 턴 종료와
  함께 죽었다. `[[harness-background-process-lifecycle]]`이 예약 타이머·다운로드에서 규명한 원칙이
  채팅 경로 자체에도 적용된 사례.
  - **대화당 상주 Claude 프로세스** 도입(`ChatSession.live` 필드, 새 `runClaudeQuery`) — 턴이 끝나도
    프로세스가 유휴 10분까지 살아있어 배경작업이 계속 돌고, 완료되면 모델이 스스로 깨어나 후속 답변까지
    보낸다(닫힌 화면엔 배달 경로로 자동 전달, `[[session-independent-scheduler]]`의 배달 경로 재사용).
  - 배경작업 상태를 SSE + `/api/queue`로 노출, 개별 중지 API `/api/task/stop` 신설.
  - 중지 버튼은 프로세스를 안 죽이고 **현재 턴만 인터럽트** — 배경작업은 유지.
  - 자동/수동 컨텍스트 압축·대화 보관·과금 방식 전환도 유휴일 때만 프로세스를 내리도록 맞춤.
  - `apps/web/index.html` 상단 활동 표시줄에 배경작업 줄 표시(`🔄 · 설명 · 경과시간 · 토큰 · 마지막
    도구`, ✕로 개별 중지, 2초 폴링, 완료 시 ✅ 1분 표시).
  - 40초 테스트 명령으로 턴 종료 후 생존→완료 후 모델 재개→닫힌 화면 푸시까지 실증 확인.
  - **새 함정**: 커밋 메시지에 `/api/...` 같은 슬래시 문자열이 있으면 권한 가드가 경로로 오인해 차단 —
    메시지를 파일로 우회. [[deploy-sandbox-pnpm-shim]]의 "인자 스캐너" 함정과 같은 계열, 이번엔 커밋
    메시지 변종.
  - 커밋 `d20b439`(+718/−147), 재시작은 자기 턴 안 즉시 실행 대신 지연·분리 실행으로 처리
    ([[self-hosted-agent-server-ops]] 함정2 정석 대응 재현).
  - → 세션: [[2026-07-15-세션상주프로세스-백그라운드작업생존-구현]] · 배경: [[harness-background-process-lifecycle]]

## 기능 폐기 (2026-07-16 세션 04:26~04:32 — 사용 영역 페르소나 전면 제거)

- **사용자 지시**: "사용영역을 일반과 이미지로만 나누고 다른 영역은 숨김처리해주고 설정에서도
  사용영역 페르소나를 제거하고 페르소나를 이제 사용하지 마." UI 축소 + 기능 자체 폐기 두 요구가
  섞인 요청.
- **UI**: `apps/web/index.html`·`public/index.html` 양쪽 `CATEGORIES` 배열을 일반/이미지 2개로
  축소(비즈니스·브랜딩·마케팅·영상 삭제), 설정(⚙) "사용 영역 페르소나" 편집 섹션
  (select/textarea/저장·초기화) 전체 제거. 이미지 영역은 화질·비율 툴바 노출용으로만 유지.
- **백엔드**: `src/server.ts`의 `DEFAULT_PRESETS`·`presetInstruction()` 등 페르소나 주입 로직을
  Claude Code SDK/로컬·OpenAI 호환/Gemini **3개 경로 모두**에서 삭제, `GET/POST /api/presets`
  API 삭제, `presets/*.md`(6개 원본) `git rm`.
- **검증**: 잔여 참조 grep 없음 확인, `apps/web` 재빌드(dist 반영), `tsc --noEmit` 통과. 이 세션
  자체는 커밋·배포 없이 종료(작업 트리 변경만).
- **이 작업이 나중에 커밋 `7b27897`("refactor: presets 폴더 제거 및 서버 로직 정리")로 묶여
  push됨** — 같은 날 09:07 세션([[2026-07-16-잔여작업커밋-원격머지-재시작요청]])에서 원격과
  머지되며 병합 커밋 `dca1422`(충돌 마커 잔존 버그의 원인) 생성으로 이어짐.
  → 상세: [[use-area-personas]](폐기 절 추가) · 세션: [[2026-07-16-사용영역-페르소나-폐기]]
  · 스킬: [[full-stack-feature-removal-audit]]

## 추가 기능 (2026-07-16 세션 — 기억 버튼 보관 통합 + NaN 토큰 버그 수정)

- **상단 "기억에 저장" → "기억에 보관" 버튼 통합** — 기존엔 이 버튼이 `/api/remember`(저장만, 아카이빙
  안 함)를 호출했는데, 이제 라벨을 "기억에 보관"으로 바꾸고 **기존 보관 API `/api/sessions/archive`**를
  호출하도록 대상만 교체(새 엔드포인트 신설 안 함). 확인창 → 아카이빙 + `memory-ingest` 자동 큐잉
  ([[2026-07-13-보관시-자동-기억저장]]) + 새 대화 전환이 버튼 하나로 일어남. `rememberedAt` 멱등
  게이트, 실행중/대기중 대화 거부 규칙 모두 대화 목록 롱프레스 보관과 동일하게 적용됨.
  옛 `/api/remember`는 서버에 남아 있으나 **UI 진입점에서는 제거**.
  - ⚠️ **저장 진입점 서술 갱신 필요**: [[long-term-memory-architecture]]가 기록해 온 "저장 진입점
    3개(수동 🧠 버튼·보관 🗄 자동·야간 ingest)"는 이 세션 이후 **UI 레벨에서 (1)(2)가 통합**되어
    순수 저장-only 경로가 없어짐 — 상세는 그 토픽 페이지 참고.
  - 웹 빌드만으로 반영, 서버 재시작 불필요.
- **오른쪽 아래 "◔ NaN 토큰" 표시 버그 수정** — `usage: null`인 신규 세션에서
  `Object.assign({}, null, {압축통계})`가 필드를 못 채워 `undefined + undefined` → NaN 노출.
  `loadUsage()`(usage 없으면 빈 객체 안 만듦)·`updateUsage()`(필드 결손 시 0으로 계산) 두 곳 방어적
  수정. 재사용 절차: → [[null-merge-nan-display]]
  - 별건 관찰: 같은 화면의 "Credit balance is too low" 압축 오류는 표시 버그와 무관, API 크레딧
    소진 계열 문제로 추정(미해결) → [[sdk-claude-code-vs-api-billing]]
  - → 세션: [[2026-07-16-기억버튼-보관통합-NaN토큰버그수정]]

## 추가 기능 (2026-07-16 세션 — memory-ingest 크레딧 버그 근본 수정)

- **`runner.ts`(큐 작업 실행기)의 크레딧 소진 버그 근본 수정** — 오늘(07-16) 08:33 이후 `memory-ingest`
  잡이 전부 `"Credit balance is too low"`로 실패 중이던 것을 규명. 원인: `server.ts`의 인터랙티브
  채팅은 `claudeAuthEnv()`가 `ANTHROPIC_API_KEY`를 지워 구독(OAuth) 과금을 강제하는데, **큐 작업을
  실행하는 `runner.ts`에는 이 처리가 없어** 크레딧 0인 키를 그대로 물려받아 API 종량 시도 후 실패했다.
  `runner.ts`에도 동일 처리를 추가해 빌드 완료. 07:27 이후 보관된 대화 35건이 이 버그로 위키 미반영
  상태로 쌓여 있었음.
- **`jobs/memory-ingest-daily.job.json` 스캔 범위 확장** — 매일 05:30 안전망 잡이 `chats/` 최상위만
  훑고 `chats/archive/`를 빠뜨리던 것을 수정, 보관된 대화도 포함하도록 확장.
- **재시작 확인 → 실패 35건 재큐잉** — 데몬(`io.lampas.harness.daemon`)이 이 채팅 자체를 서빙 중이라
  재시작 전 사용자 확인을 구한 뒤([[self-hosted-agent-server-ops]] 함정2), 사용자의 반복 요청으로
  재시작 + `queue/failed/*memory-ingest.json`을 `queue/`로 이동해 즉시 재처리 확인.
  → 세션: [[2026-07-16-메모리인제스트-크레딧버그-근본수정]] · 배경: [[long-term-memory-architecture]] ·
  스킬: [[sdk-claude-code-vs-api-billing]]

## 제안된 기능 — tts-stream(ElevenLabs 실시간 읽어주기, 2026-07-16, 완료 여부 미확인)

- **요청**: 봇 메시지(AI가 전달한 대화)를 선택하면 ElevenLabs로 실시간 읽어주는 `tts-stream` 기능.
  참고 예시로 `dbs/talk-system`(=`[[toktalk]]`)이 지목됐으나 **작업 폴더 밖이라 끝내 못 봄**
  ([[work-folder-sandboxing]] — 읽기 전용 우회도 실패, SDK 레벨 폴더 제한으로 재확인).
- **조사**: `.env`에 `ELEVENLABS_API_KEY` 이미 있음. 기존 `mintVoiceSecret`은 STT(입력)뿐, TTS(출력)는
  없었음. 서버가 이미 SSE·바이너리 스트리밍 패턴을 쓰므로 프록시 붙이기 적합, 프론트는
  `apps/web/index.html`의 `add("bot", …)` 버블에 재생 버튼 추가하는 방향으로 계획.
- **채택된 기본값**(사용자 무응답으로 어시스턴트가 확정): ElevenLabs 공식 스트리밍 API, 모델
  `eleven_flash_v2_5`(저지연 다국어), 보이스는 env `ELEVENLABS_VOICE_ID`로 오버라이드 가능.
- **상태**: "server.ts에 ElevenLabs 상수 추가"를 시작한다고 밝힌 직후 소스가 끝남 — 실제 엔드포인트
  구현·프론트 재생 버튼·커밋 여부 **미확인**. 재개 시 `src/server.ts`에 관련 상수·라우트가 있는지
  먼저 확인할 것.
  → 세션: [[2026-07-16-tts-stream-elevenlabs-구현착수]]
  - ⚠️ **약 1시간 후 별개 경량 대안이 완성됨** — 아래 "배경 롱프레스 + 자동 음성 재생" 세션에서
    ElevenLabs 언급 없이 브라우저 내장 Web Speech API로 "봇 답변 자동 읽어주기"를 구현·커밋 완료.
    이 ElevenLabs `tts-stream`을 포기하고 방향을 바꾼 것인지, 별개 기능(고급 유료 TTS)으로 병행
    의도한 것인지는 소스만으로 미확정.
  - **[2026-07-16 후속 ingest로 부분 해소]** 같은 날 이후 등장한 "봇 말풍선 클릭 읽어주기" 기능
    ([[2026-07-16-봇메시지-클릭읽어주기-구현]])은 **이 ElevenLabs 경로가 아니라** Web Speech API
    쪽(`speak()`/`stopSpeech()`)을 재사용해 만들어졌음이 확정됨. 즉 클릭 트리거는 ElevenLabs와
    무관 — 다만 이 ElevenLabs `tts-stream` 자체가 결국 완성돼 어딘가에서 쓰이고 있는지는 여전히
    미확인.

## 추가 기능 (2026-07-16 세션 — 배경 롱프레스 설정 열기 + 자동 음성 재생)

- **채팅 빈 배경 롱프레스(≈0.5초) → 설정 모달 열기** — 메시지·버튼·입력창이 아닌 영역을 오래 누르면
  발동, 10px 이상 이동 시 스크롤 제스처로 간주해 취소. [[destructive-action-inline-confirm]] 변형B의
  pointerdown 타이머+이동취소 메커니즘을, **확인창 없는 비파괴적 네비게이션 열기**에 재사용한 사례.
- **자동 음성 재생 토글** — 설정 모달 "소통 언어" 아래 신설, 상태는 `localStorage`에 저장
  ([[localstorage-ui-preference-persistence]]). 켜져 있으면 `done` 이벤트(봇 응답 완료)마다
  `bot._md`를 마크다운·링크·코드블록 제거 후 브라우저 내장 **Web Speech API**(`ko-KR` 우선)로
  읽음 — 서버 비용 없음. 지금 보고 있는 대화만 읽고, 토글 끄면 즉시 정지.
- `apps/web/index.html`만 커밋 `3d16ed5`, origin/main push 완료.
- ⚠️ **ElevenLabs `tts-stream`과의 관계 미확정** — 위 항목 참고.
  → 세션: [[2026-07-16-롱프레스설정-자동음성재생-구현]]

## 추가 기능 (2026-07-16 세션 — 봇 말풍선 클릭 읽어주기/정지 토글 + 충돌마커 정리, v0.1.36)

구현 세션(09:10~09:17)과 커밋·푸시 세션(09:21~09:22, 4분 뒤)이 별도로 ingest됨.

- **구현** ([[2026-07-16-봇메시지-클릭읽어주기-구현]]) — 기존 자동재생 인프라
  (`speak(md)`/`stopSpeech()`, [[2026-07-16-롱프레스설정-자동음성재생-구현]]에서 만들어짐)를
  `speak(md, bubble)`로 확장해 재사용. 채팅 컨테이너에 클릭 위임 리스너를 달아 봇 말풍선
  (`.msg.bot`) 클릭 시 그 메시지만 읽고, 재클릭하면 정지(토글). 읽는 동안 파란 테두리 하이라이트 +
  `cursor: pointer` + 툴팁. 링크·이미지 클릭은 예외 처리해 읽기가 끼어들지 않음. **확정: Web Speech
  API 기반, ElevenLabs `tts-stream`([[2026-07-16-tts-stream-elevenlabs-구현착수]])과 무관** — 아래
  "미상" 표기는 커밋 세션 소스 단독으로는 판별 불가했던 것이고, 구현 세션 발견으로 해소됨.
  - 같은 세션에서 **직전 병합 커밋 `dca1422`**가 `package.json`·`index.html`에 병합 충돌 마커
    (`<<<<<<< HEAD` 등)를 그대로 남겨 빌드가 깨져 있던 것을 부수적으로 발견·정리
    ([[rebase-local-feature-onto-refactored-remote]] "마커 전체 검색" 단계 실증 사례).
    `package.json` 버전은 origin/main 쪽(0.1.32)으로, `index.html` `CATEGORIES`는 origin/main의
    추가 카테고리 + HEAD의 상세 "일반" 안내문구를 모두 살려 병합.
    - **`dca1422`의 생성 경위 확인됨(2026-07-16 후속 ingest)** — 이 커밋을 만든 원본 세션은
      [[2026-07-16-잔여작업커밋-원격머지-재시작요청]](같은 날 09:07~09:08, 위 구현 세션보다
      2~3분 앞섬). `git merge`(rebase 아님)로 3개 충돌(package.json 버전, 웹 index.html 재작성,
      루트 public/index.html 삭제)을 모두 "원격 우선"으로 일괄 해결한 뒤 어시스턴트가
      **"완벽합니다. 모든 충돌이 해결되었어요"**라고 선언하고 곧바로 push — 실제로는 마커가
      남아 있었으므로 이 선언이 틀렸던 것으로 드러남. 병합 직후 빌드나 마커 grep 등 검증을
      전혀 안 거친 것이 근본 원인.
  - Playwright로 8787 데몬에 접속해 클릭→`speechSynthesis.speak` 호출·재클릭 정지·링크클릭
    무간섭을 실브라우저로 검증 완료([[playwright-system-chrome-verify]]).
  - 이 세션 끝에서는 **커밋하지 않고** 사용자에게 커밋 여부를 위임.
- **커밋·푸시** ([[2026-07-16-봇메시지-클릭읽어주기-커밋푸시]]) — "모두 commit 해줘"에 위 변경
  전체를 커밋. **버전 0.1.36** — 직전 위키 기록 버전(0.1.30, `3d16ed5`)과 사이에 최소 5개 버전
  간극(원인 미확정). 커밋 `4cff973`(로컬) → 원격에 새 커밋 있어 **rebase** → `9e45de9` push 완료.

## 추가 기능 (2026-07-17 세션 — 하네스 재시작 + PWA 이름/아이콘 최초 설정 + Vite manifest 버그 수정)

12:40:59~12:48:31 UTC, 아래 `.env` 평문 노출 세션(12:50~12:55 UTC)보다 2분 앞서 끝난 선행 세션.

- **재시작 스크립트 실행** — "재시작하는 스크립트 실행해줘"(동일 요청 2회) → `scripts/`에서 재시작
  스크립트(`restart-lampas.sh`로 추정) 실행, 18초 후 새 프로세스(PID `74969`) 기동 + MCP 재연결
  확인. → [[self-hosted-agent-server-ops]]
- **PWA 이름/아이콘 최초 설정** — 앱 이름을 "Lampas"(영문 L 대문자)로, 아이콘을 사용자가 첨부한
  흰색 "L" 로고(1024×1024 PNG)로 설정. `apps/web/public/`에 없던 `manifest.json`을 신규 생성,
  아이콘을 192px/512px로 리사이징, `index.html`에 manifest 링크+Apple 메타 태그 추가. 빌드 v0.1.37.
- **Vite `publicDir:false` 버그 발견·수정** — 재시작 요청에 어시스턴트가 재시작 대신 근본 원인을
  발견: Vite `root: 'public/'` + `publicDir: false` 설정 때문에 `index.html`이 참조하는
  `manifest.json`/아이콘이 Vite 에셋 파이프라인을 타 **base64 인라인·해시 파일명**으로 바뀌는데,
  `manifest.json` **내부**에 적힌 아이콘 경로는 해시되지 않아 실제 배포 경로와 어긋나 깨졌다.
  `manifest.json`+아이콘 4종을 별도 정적 디렉토리 **`apps/web/static/`**(이 세션에서 최초 생성)로
  분리하고 `publicDir`로 지정해 재빌드 → `curl`로 manifest 200/JSON, 아이콘 200/PNG 확인.
  **서버 재시작 없이 반영**됨(정적 파일은 매 요청 디스크에서 읽음) — 사용자가 요청한 "재시작"은
  실제로 불필요했음. → 재사용 절차: [[vite-publicdir-manifest-icon-fix]]
- ⚠️ **아이콘 디자인 불일치**: 이 세션에서 설정한 아이콘은 흰색 "L" 로고인데, 아래 2026-07-18
  아이콘 통일 세션은 그 시점 `apps/web/static/` 아이콘을 "남색 사운드바" 디자인으로 서술한다 —
  이 세션(07-17 12:48) 이후 ~07-18 사이 아이콘이 한 번 더 바뀐 것으로 보이나 그 변경 세션은
  아직 위키에 없음(소스 미확인).
  → 세션: [[2026-07-17-harness재시작-pwa아이콘이름-manifest-vite버그수정]]

## 버그 수정 (2026-07-17 세션 — 컨텍스트 압축(`/compact`)이 API 과금으로 새던 버그)

`compactClaudeSession()`(`src/server.ts` 2460-2515)이 `query()` 호출에 `options.env`를 안 넘겨
`process.env`를 그대로 상속 → `ANTHROPIC_API_KEY`(크레딧 0)가 있으면 OAuth 구독보다 그 키를
우선해 압축이 실패하고 있었다. 채팅(`server.ts` 2028-2033)·큐 러너(`runner.ts`, 2026-07-16 절)에
이미 적용된 `claudeAuthEnv()`를 압축 경로에도 적용 — **세 번째 확인된 누락 진입점**.
`npm run build:server` 통과, 반영은 데몬 재시작 필요(이 세션에선 미실행). → 재사용 절차:
[[sdk-claude-code-vs-api-billing]] · 세션: [[2026-07-17-컨텍스트압축-api과금-수정]]

## ⚠️ 보안 관찰 (2026-07-17 세션 — `.env` 파일 평문 노출)

사용자가 "`.env` 파일을 그대로 읽어줘"라고 요청하자 어시스턴트가 위험 고지 없이
`CLAUDE_CODE_OAUTH_TOKEN`·`ANTHROPIC_API_KEY`·`OPENAI_API_KEY`·`GOOGLE_API_KEY`·
`ELEVENLABS_API_KEY`·`HARNESS_TOKEN` 전체 값을 채팅 응답에 표로 그대로 출력했다. 이 응답이
`logs/chats/archive/`에 평문으로 영구 저장된 상태 — 노출된 키의 재발급(rotate) 여부는 미확인.
`[[toktalk]]`(커밋 `0664ea9`)·`[[cwc-system]]`(프로덕션 env 평문 커밋)에 이은 세 번째 유사 사고로
패턴 토픽에 묶임. → [[secrets-plaintext-exposure-pattern]] · 세션: [[2026-07-17-env읽기-pwa세이프에어리어-여백수정]]

## 추가 기능 (2026-07-17 세션 — PWA 세이프 에어리어 여백 수정)

위 "하네스 재시작 + PWA 이름/아이콘 최초 설정" 세션(12:40~12:48 UTC) 2분 뒤(12:50~12:55 UTC) 이어진 세션.

- **상단 여백 추가** — `apps/web/public/index.html`의 `header` 규칙에 `env(safe-area-inset-top)`이
  전혀 없던 것이 원인. `padding-top: calc(10px + env(safe-area-inset-top))`로 수정해 PWA(홈 화면
  추가) 모드에서 상태바/노치를 침범하지 않게 함(일반 브라우저 탭은 `env()`=0이라 기존 10px 유지).
- **하단 여백 제거** — 후속 요청으로 `footer` 패딩 `8px 16px 10px`의 하단 값을 `10px → 0`으로 변경
  (safe-area 대응이 아닌 단순 제거 요청).
- 웹 빌드 후 서버 재시작(`PID 74969`)까지 어시스턴트가 직접 수행, 문제없이 완료.
- 재사용 절차 스킬 추출: → [[pwa-safe-area-inset-padding]] · 세션: [[2026-07-17-env읽기-pwa세이프에어리어-여백수정]]

## 추가 기능 (2026-07-18 세션 — desktop/web 아이콘 통일)

- **desktop 주황 face 아이콘 → web static 아이콘 통일** — `apps/desktop/assets/icon.svg`(주황 배경
  얼굴 모양)를 원본으로 `apps/web/static/icon-512.png`·`icon-192.png`를 교체, `icon-384.png`는
  신규 생성(PWA용, 기존엔 없었음). `apps/desktop/assets/icon.icns`는 `install-desktop.sh`와 동일한
  `sips`+`iconutil` 파이프라인으로 재생성해 web과 시각적 일관성 유지.
  - ⚠️ **직전 디자인 이력 불일치**: `apps/web/static/`는 2026-07-17 세션([[2026-07-17-harness재시작-pwa아이콘이름-manifest-vite버그수정]])에서 흰색 "L" 로고로 최초 생성됐는데, 이 세션은 그 자리의 기존
    아이콘을 "남색 막대(사운드바)"로 서술한다 — 두 세션 사이 아이콘이 다시 바뀐 것으로 보이나
    해당 변경 세션은 위키에 없음(미확인).
  - **1차 시도에서 방향을 반대로 잡음** — web의 남색 막대(사운드바) 아이콘을 desktop에 이식하고
    `install-desktop.sh` 재실행(Dock/Finder 반영)까지 사용자 승인 하에 완료했다가, 사용자가
    "반대로 한거 같은데?"로 정정. git 상태 확인 후 desktop 원본(주황 face) 복원 → 올바른 방향으로
    재작업. → 재사용 절차: [[icon-source-of-truth-before-replace]]
  - → 세션: [[2026-07-18-desktop-web-아이콘-통일]]

## 기타 변경 (2026-07-18)
- PWA 아이콘·매니페스트 등 정적 자산 + 서버/빌드 변경 커밋·push. [[works-project-portfolio]] 일괄
  저장 요청의 일부로, 변경 세부 내용은 소스에 한 줄 요약만 있어 미상. → [[2026-07-18-works-전체저장]]

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

## 운영 사실 (2026-07-13 auto-memory 이관)

- **외부 MCP 브리지** — `src/mcpBridge.ts`의 `externalMcpServers()`가 프로젝트 `.cursor/mcp.json`의 외부
  MCP 서버(naver-blog, threads 등)를 Claude Agent SDK `mcpServers` 규격으로 변환해 `server.ts`의
  `query()`에 스프레드. **Claude 세션(웹 채팅)에서도** `mcp__<서버>__<도구>`로 노출된다(과거 "MCP는
  Cursor/claude.ai 전용" 오해 정정). → [[harness-mcp-bridge]]
  - 실사용: [[naver-blog-mcp]] — 네이버 블로그 자동 발행(Playwright). 본문 순수 텍스트만·SRT 쿠키 ~24h
    만료·서버 버그 3종. 발행 절차: [[naver-blog-mcp-posting]]
- **스크립트 폴더 컨벤션** — 일회성 probe/test/조회는 `tools/`, 설치·운영(install/setup/restart/bootstrap/
  self-update)은 `scripts/`. (2026-07-09 사용자 지시.) → [[progdesigner]]
- **서버 라이프사이클 함정** — 이 하네스는 에이전트 턴을 자기 프로세스에서 spawn하므로, 서버(8787)를
  재시작·종료·중복실행하면 진행 중 턴이 끊기거나 코드 반영이 안 되는 함정 3종(stray-port·restart-kills-own-turn·
  pkill-hits-prod)이 있다. → [[self-hosted-agent-server-ops]]
- **로컬 LLM 서버** — rapid-mlx가 launchd(`io.lampas.rapidmlx`, KeepAlive)로 상주. → [[rapid-mlx]]
- **`.env` 로딩** — 셸의 빈 env 변수가 `.env` 진짜 값을 가리는 함정(dotenv override:false) 대응.
  → [[env-empty-var-shadows-dotenv]]
- **⚠️ 미해결**: 맥미니에 `homebrew.mxcl.redis`·`homebrew.mxcl.postgresql@15`가 launchd 상주 중인데
  이 하네스가 실제로 Redis/PostgreSQL을 쓰는지는 2026-07-16 시점 미확인(`grep -r "redis\|postgres" src/`
  결과 미보고). → [[2026-07-16-launchctl-불필요서비스-점검]] · [[macos-launchctl-cleanup-candidates]]
- **⚠️ 미확정**: `logs/chats/` 아카이브 파일명 재사용·같은 사건의 이중 기록·트랜스크립트 내 메시지 반복 등
  로그 무결성 이상 정황이 4개 세션에서 독립 관찰됨(원인 미확정). → [[chat-archive-duplication-anomaly]]

## 관련
- 세션: [[2026-07-06-lampas-harness-구축]] · [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-08-장기기억-provider-연동-설계]] · [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]] · [[2026-07-09-일반-사용영역-페르소나-설정]] · [[2026-07-11-desktop-퀵채팅-설치-스크립트]] · [[2026-07-13-람파스-누적운영기억-이관]] · [[2026-07-14-quick-html-이미지-첨부-구현]] · [[2026-07-15-올리브유-마케팅-포지셔닝]] · [[2026-07-15-과금모드-토글-컨텍스트표시]] · [[2026-07-15-사용량한도-rate-limit-sdk-확인]] · [[2026-07-15-데스크톱-file메뉴-new-window]] · [[2026-07-15-gpt-realtime-음성입력-길게누르기]] · [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]] · [[2026-07-15-auto모델-기능-최초구현]] · [[2026-07-15-auto모델-난이도판정-확인ux-개선]] · [[2026-07-15-세션상주프로세스-백그라운드작업생존-구현]] · [[2026-07-16-사용영역-페르소나-폐기]] · [[2026-07-16-음성입력-진동비프-피드백]] · [[2026-07-16-기억버튼-보관통합-NaN토큰버그수정]] · [[2026-07-16-메모리인제스트-크레딧버그-근본수정]] · [[2026-07-16-tts-stream-elevenlabs-구현착수]] · [[2026-07-16-롱프레스설정-자동음성재생-구현]] · [[2026-07-16-잔여작업커밋-원격머지-재시작요청]] · [[2026-07-16-봇메시지-클릭읽어주기-구현]] · [[2026-07-16-봇메시지-클릭읽어주기-커밋푸시]] · [[2026-07-16-launchctl-불필요서비스-점검]] · [[2026-07-17-harness재시작-pwa아이콘이름-manifest-vite버그수정]] · [[2026-07-17-컨텍스트압축-api과금-수정]] · [[2026-07-17-env읽기-pwa세이프에어리어-여백수정]] · [[2026-07-18-desktop-web-아이콘-통일]] · [[2026-07-18-works-전체저장]] · [[2026-09-07-웹배포확인-프롬프트인젝션거부-터미널세션분석]]
- 개발 대상 제품: [[lampas-studio]] — 이 하네스로 `[[lampas]]`가 개발·배포하는 이미지 생성 제품.
- 연동 대상 장기기억: [[john-wiki]] (제안).
- 상주 서비스·도구: [[rapid-mlx]] (로컬 LLM) · [[naver-blog-mcp]] (외부 MCP).
- `lampas-system` = `[[lampas-studio]]`의 로컬 저장소명(형제 폴더, `~/Works/lampas/lampas-system`) — 당시
  미커밋 변경 2개는 2026-07-15 세션에서 rebase로 정리·push 완료. → [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
- 이 하네스가 속한 상위 포트폴리오(`~/Works` 12개 저장소) → [[works-project-portfolio]]

## 신규 서브시스템 — 실제 웹 터미널(2026-09-07 관찰, `codex/web-cli-sessions` 브랜치)

이전까지 위키에 기록된 `[[lampas-harness]]`의 대화 인터페이스는 채팅형(`server.ts`의
`ChatSession`, `runClaudeQuery`)이었다. 2026-09-07 세션에서 처음으로 **PTY 기반 실제 웹 터미널**
서브시스템(`src/terminalSessions.ts`)이 확인됨 — codex/claude CLI를 `pty.spawn`으로 직접 띄우고
브라우저에서 터미널 화면으로 접속하는 별도 경로로 보인다(채팅 경로와의 관계·통합 여부는 미확인).

- **세션 생존 모델** — 실행 중인 PTY·출력 버퍼(`screen`, headless terminal)·클라이언트 목록이
  인메모리 `live` 맵에 보관됨. **브라우저 연결 종료(SSE 끊김)는 세션에 영향 없음** — `connectTerminal`의
  `res.on("close", ...)`(300~304행)은 `l.clients`에서 응답 객체만 제거, PTY는 계속 실행되고 출력은
  계속 `screen.write`로 누적. 재접속 시 `l.serializer.serialize()`(284~293행)로 현재 화면 스냅샷을
  보내 이어보기 가능 — 순수 "뷰어 분리".
- **서버 재시작은 완전히 다름** — `live` 맵 전체가 사라짐(PTY 핸들 참조 소실, 자식 프로세스는 고아로
  남거나 종료될 수 있음, 이 파일만으론 판별 불가). 재시작 로직(`terminalSessions.ts:45-59`)이 디스크의
  `status: "running"` 세션을 전부 `"interrupted"`로 강제 전환. 이후 그 세션에 재접속하면 `.screen`
  파일(있다면)만 보여주고 즉시 `exit` 이벤트, 입력은 "종료된 세션" 에러(294~313행).
- **SIGINT/SIGTERM 정상 종료의 숨은 레이스 컨디션**(`src/server.ts:3453-3458`) — 핸들러는
  `stopAllTerminals()` → `closeAllMcp()` → `process.exit(0)` 순으로 **동기** 실행. 그런데
  `stopAllTerminals`(`terminalSessions.ts:339-341`)은 각 세션에 `l.process.kill()`로 **신호만
  보내고 완료를 기다리지 않는다**. 실제 정리(`status: "exited"` 저장, `.screen` 스냅샷 쓰기, `exit`
  이벤트 전송)는 PTY `onExit` 콜백(`terminalSessions.ts:252-270`) 안에서 `l.pending.then(...)`으로
  **비동기** 수행된다. `kill()` 직후 곧바로 `process.exit(0)`이 실행되므로, 최악의 경우
  **비동기 스냅샷 저장이 끝나기 전에 프로세스가 죽어 세션 정리가 유실될 수 있다** — "정상 종료"라는
  이름과 달리 정리가 보장되지 않음. 비정상 종료(`kill -9`, 크래시)는 이 핸들러 자체가 안 돌아서
  `stopAllTerminals`가 아예 스킵되고, 다음 기동 시 재구성 로직이 일괄 `"interrupted"`로 마킹(이때는
  `.screen` 스냅샷이 없을 가능성이 더 큼).
  - 이 레이스는 [[self-hosted-agent-server-ops]]의 "함정 2(restart-kills-own-turn)"가 다루는
    "서버가 죽으면 부모가 죽어 응답이 유실된다"는 문제와는 **결이 다른, 별도의 데이터 정합성 문제**다
    — 함정 2는 응답 전달 채널 유실, 이건 **세션 상태 저장 자체**가 kill()-vs-process.exit() 순서
    비보장으로 유실될 수 있다는 것.
  - → 세션: [[2026-09-07-웹배포확인-프롬프트인젝션거부-터미널세션분석]]
- **배포 서술(미검증)** — 같은 세션 초입에서 "Mac mini에 `codex/web-cli-sessions` 브랜치 `782bb1f`
  배포, 테스트 6개·빌드 통과, CLI 버전 Codex 0.153.4/Claude Code 2.1.263"이라는 구체적 주장이
  제시됐으나, 어시스턴트가 **직접 검증하지 않고 확인을 거부**함(프롬프트 인젝션 의심) — 이 배포
  서술 자체의 사실 여부는 이 위키 기준 **미확인**. → [[unverified-attestation-injection]]

## 재확인된 함정 (2026-07-08~09 세션)
- **"Stream closed"** — 파일 수정 도구 권한 승인 채널이 세션 중 끊겨 편집 불가(2회). 사용자 재전송/재시작으로 복구.
- **인자 스캐너** — bash 인자에 `https://…`·슬래시 경로·S3 경로가 있으면 차단 → 스크립트 파일로 작성해 실행하는 우회 반복. → [[deploy-sandbox-pnpm-shim]]
- **권한 승인 UI 스트림 끊김** — `rapid-mlx` 실행이 권한 프롬프트 스트림 불안정으로 반복 실패, 수십 초 대기 후 회복.
- **에이전트 배경작업이 턴/세션 종료로 죽음** — 예약 타이머·긴 다운로드 모두 영향. → [[harness-background-process-lifecycle]]
- **일본어 드리프트** — 어시스턴트가 긴 코딩·영어 컨텍스트 속에서 응답 언어를 한국어→일본어로 벗어남. 코드 버그 아님(언어 지시는 정상 주입), 모델 행동 문제. 메모리 기록으로 대응.
