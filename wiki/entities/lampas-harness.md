---
tags: [entity, project, tool, claude-agent-sdk, typescript]
created: 2026-07-07
updated: 2026-07-16
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
    [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]](2차 재확인) · 배경: [[long-term-memory-architecture]]

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

## 추가 기능 (2026-07-15 세션 — Auto 모델 난이도 판정 + 확인UX)

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

## 관련
- 세션: [[2026-07-06-lampas-harness-구축]] · [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-08-장기기억-provider-연동-설계]] · [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]] · [[2026-07-09-일반-사용영역-페르소나-설정]] · [[2026-07-11-desktop-퀵채팅-설치-스크립트]] · [[2026-07-13-람파스-누적운영기억-이관]] · [[2026-07-14-quick-html-이미지-첨부-구현]] · [[2026-07-15-올리브유-마케팅-포지셔닝]] · [[2026-07-15-과금모드-토글-컨텍스트표시]] · [[2026-07-15-사용량한도-rate-limit-sdk-확인]] · [[2026-07-15-데스크톱-file메뉴-new-window]] · [[2026-07-15-gpt-realtime-음성입력-길게누르기]] · [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]] · [[2026-07-15-auto모델-난이도판정-확인ux-개선]] · [[2026-07-16-음성입력-진동비프-피드백]]
- 개발 대상 제품: [[lampas-studio]] — 이 하네스로 `[[lampas]]`가 개발·배포하는 이미지 생성 제품.
- 연동 대상 장기기억: [[john-wiki]] (제안).
- 상주 서비스·도구: [[rapid-mlx]] (로컬 LLM) · [[naver-blog-mcp]] (외부 MCP).
- `lampas-system` = `[[lampas-studio]]`의 로컬 저장소명(형제 폴더, `~/Works/lampas/lampas-system`) — 당시
  미커밋 변경 2개는 2026-07-15 세션에서 rebase로 정리·push 완료. → [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
- 이 하네스가 속한 상위 포트폴리오(`~/Works` 12개 저장소) → [[works-project-portfolio]]

## 재확인된 함정 (2026-07-08~09 세션)
- **"Stream closed"** — 파일 수정 도구 권한 승인 채널이 세션 중 끊겨 편집 불가(2회). 사용자 재전송/재시작으로 복구.
- **인자 스캐너** — bash 인자에 `https://…`·슬래시 경로·S3 경로가 있으면 차단 → 스크립트 파일로 작성해 실행하는 우회 반복. → [[deploy-sandbox-pnpm-shim]]
- **권한 승인 UI 스트림 끊김** — `rapid-mlx` 실행이 권한 프롬프트 스트림 불안정으로 반복 실패, 수십 초 대기 후 회복.
- **에이전트 배경작업이 턴/세션 종료로 죽음** — 예약 타이머·긴 다운로드 모두 영향. → [[harness-background-process-lifecycle]]
- **일본어 드리프트** — 어시스턴트가 긴 코딩·영어 컨텍스트 속에서 응답 언어를 한국어→일본어로 벗어남. 코드 버그 아님(언어 지시는 정상 주입), 모델 행동 문제. 메모리 기록으로 대응.
