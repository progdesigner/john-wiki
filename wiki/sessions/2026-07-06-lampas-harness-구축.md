---
tags: [session, lampas-harness, claude-agent-sdk, macos, launchd]
created: 2026-07-07
updated: 2026-07-07
---
# 2026-07-06 — lampas-harness 데몬화·채팅 UX 구축 세션

`[[progdesigner]]`가 `[[lampas-harness]]`(Claude Agent SDK 기반 웹 하네스)를 하루 동안 집중적으로 다듬은 세션.
데몬화부터 시작해 채팅 지속성·큐·모델 선택·작업 폴더 샌드박싱까지 반복적으로 기능을 쌓았다.
이 세션에서 에이전트가 **"람파스(Lampas)"**라는 이름을 부여받았다 → `[[lampas]]`.

기간: 2026-07-06 15:36 ~ 2026-07-07 02:58 (KST)
원본: [[raw/conversations/2026-07-06-lampas-harness-구축.md]]

## 흐름 (시간순)

1. **환경 확인** — API 키 복구 후 작동 확인. 작업 디렉토리 상위 폴더 접근 가능 여부 확인(가능, 단 권한/샌드박스 제약).
2. **데몬화** — `serve`로 포그라운드 실행 중이던 하네스를 macOS `launchd` LaunchAgent로 전환. 제어 스크립트 `harnessctl`(install/start/stop/restart/status/logs) 작성. → `[[macos-launchd-daemon]]` 스킬로 추출.
3. **라벨 리네임** — `com.lampas` → `io.lampas`. plist 파일명·내부 라벨·스크립트 참조 전부 치환.
4. **501 버그 수정** — `harnessctl`의 `LABEL="io.lampas.harness"`가 실제 설치 라벨 `io.lampas.harness.daemon`과 불일치 → `Could not find service ... 501`. 라벨과 로그 파일 경로(`daemon.out/err.log`) 수정으로 해결. (커밋 `3ecc049`)
5. **큐 기능** — ad-hoc 프롬프트 태스크 큐 + 세션 resume + 큐 상태 패널 (`src/queue.ts` 분리). (커밋 `2a3b6c1`)
6. **세션 영속화** — 서버 크래시 시 대화 맥락(sdkSessionId)이 인메모리 Map과 함께 소멸하는 문제를 사이드카 파일(`{chatId}.session.json`)로 해결. end-to-end 검증("바나나" 테스트). → `[[sdk-session-persistence]]` 스킬.
7. **채팅 UX 대개선** (커밋 `cc76704`) — 연결 상태 아이콘, thinking 스트리밍, FIFO 메시지 큐(busy 시 대기), 세션 중지, 모델 선택기(Claude+OpenAI 라이브 목록), jsonl 히스토리 영속화.
8. **네트워크 재시도/멱등성** — 대기 중 network error로 요청이 날아가는 문제. `msgId` 기반 중복 방지 + 클라이언트 자동 재전송(최대 5회) + "다시 시도" 버튼. (커밋 `3749e8a`) → `[[idempotent-message-retry]]` 스킬.
9. **작업 폴더 샌드박싱** — 메시지별 작업 폴더 선택 + 폴더 밖 접근 차단. 폴더 선택 시 `bypassPermissions` 대신 `default` 권한 모드로 전환해 도구 호출을 서버 가드로 검사. (커밋 `5cb33ae`) → `[[work-folder-sandboxing]]` 스킬.
10. **대화 목록 UI** — 햄버거 메뉴로 대화 목록/새 대화 버튼. 대화 보관(`chats/archive/`로 이동). (커밋 `155e641`)

## 핵심 결정사항

- **launchd LaunchAgent 채택** — pm2/tmux 대신. macOS 정석, `KeepAlive`+`RunAtLoad`. 단 **사용자 로그인 필요**(헤드리스면 자동 로그인 또는 LaunchDaemon+sudo 필요). 이 트레이드오프는 미해결 채로 남음.
- **네임스페이스 `io.lampas`** — `com.lampas`에서 변경.
- **기본 모델 `claude-opus-4-8`** — 코드에 모델이 고정돼 있지 않아 CLI 기본값을 따르던 문제(재현성 위험)를 지적하고, UI 모델 선택기로 해결. Claude 전 모델 + OpenAI 라이브 목록 노출. → `[[model-selection]]`
- **채팅 ≠ 큐** — 웹 채팅은 즉시 실행(라이브 표시만), `enqueue-task` 계열만 파일 큐에 적재. 이 구분이 사용자에게 혼란을 줬고 명시적으로 설명됨. → `[[harness-queue-vs-chat]]`
- **외부 접속** — Tailscale(사설, 권장) 또는 Cloudflare Tunnel. 이유: 하네스가 `bypassPermissions`로 맥 전체 제어 → 공개 노출 위험. 실제 설치는 진행 안 함(외부 나가는 작업이라 확인 대기).

## 반복된 문제 — 히스토리 소실

세션 내내 "대화가 자꾸 사라진다"가 반복 등장(새로고침 시, 서버 재시작 시). 여러 층위로 나뉨:
- 트랜스크립트(`.md`) → 항상 디스크에 append, 안 사라짐.
- sdkSessionId(맥락) → 인메모리 Map → 크래시 시 소멸 → 사이드카로 해결.
- 브라우저 화면 히스토리 → jsonl 블록 단위 저장 + 세션 자동 복구로 해결.

## 미해결 / 후속

- 포트 8787 충돌 — 수동 `serve`와 launchd 데몬이 동시에 8787을 노림(EADDRINUSE). 런타임 상태 이슈.
- 헤드리스 부팅 시 LaunchAgent 로그인 의존성 결정 대기.
- git 커밋 identity가 hostname 기반 자동값(`progdesigner@progdesigner-mac-mini.local`) — 정식 이메일 설정 권장됨.
- bash 샌드박싱은 명령 문자열의 경로 추출 기반이라 완전한 OS 샌드박스는 아님.
