---
name: macos-launchd-daemon
description: macOS에서 서비스(백그라운드 데몬·GUI Electron 앱 포함)를 launchd로 등록하고 명령어로 재시작하는 절차
created: 2026-07-07
updated: 2026-07-13
tags: [macos, launchd, daemon, devops, electron, gui]
---
# macOS launchd 데몬화 + 제어 스크립트

## 언제 쓰는가
포그라운드로 돌던 서버/서비스를 "재부팅해도 살아남고, 명령어로 재시작 가능한" 데몬으로 만들 때.
macOS에서 pm2/tmux/nohup보다 정석(자동 시작·자동 재시작·부팅 시 로드).

## 절차 (단계별)
1. **LaunchAgent plist 작성** — `~/Library/LaunchAgents/<라벨>.plist`.
   - 라벨 컨벤션: 역DNS (`io.lampas.harness.daemon`). **plist 파일명과 내부 `<string>` 라벨을 반드시 일치**시킬 것.
   - `RunAtLoad`(부팅/로그인 시 시작), `KeepAlive`(죽으면 자동 재시작), `StandardOutPath`/`StandardErrorPath`(로그 경로) 지정.
   - 작성 후 `plutil -lint <plist>`로 문법 검증.
2. **제어 스크립트 작성** (`harnessctl` 같은 래퍼) — 내부적으로 `launchctl` 호출:
   - install: `launchctl bootstrap gui/$(id -u) <plist>`
   - restart: `launchctl kickstart -k gui/$(id -u)/<라벨>`
   - stop: `launchctl bootout gui/$(id -u)/<라벨>`
   - status: `launchctl print gui/$(id -u)/<라벨>` (pid, 마지막 종료코드)
   - logs: `tail -f <StandardOutPath> <StandardErrorPath>`
3. **포트 충돌 확인** — 기존 포그라운드 프로세스가 같은 포트를 잡고 있으면 먼저 Ctrl+C로 종료 후 install. 안 그러면 데몬이 `EADDRINUSE`.
4. **헤드리스 여부 결정** — 아래 함정 참조.

## 주의사항 / 함정
- **라벨 불일치 = `Could not find service "..." in domain for user gui: 501`.** 스크립트의 `LABEL` 상수, plist 파일명, plist 내부 `<string>` 라벨이 **세 곳 모두** 같아야 한다. 이 세션에서 `io.lampas.harness` vs 실제 `io.lampas.harness.daemon` 불일치로 501 발생.
- 로그 파일 경로도 스크립트(`logs` 명령)와 plist(`StandardOutPath`)가 같은 파일을 가리켜야 한다. (`harness.out.log` vs `daemon.out.log` 불일치 사례)
- **LaunchAgent는 사용자 로그인 후에만 뜬다.** 헤드리스 서버면 둘 중 하나 필요:
  1. 자동 로그인 켜기(간단, 권장) — LaunchAgent 그대로 동작.
  2. 로그인 없이 부팅만 해도 뜨게 → `/Library/LaunchDaemons/`의 **LaunchDaemon**으로 만들고 `sudo` 필요.
- 라벨 변경 시(`com.*`→`io.*` 등) 기존 로드된 서비스를 `launchctl bootout` 후 새 라벨로 다시 bootstrap 해야 반영.
- **`~/Library/LaunchAgents`(사용자) vs `/Library/LaunchAgents`(root 소유).** 사용자 서비스는 반드시
  `~/Library/LaunchAgents`에 써라. root 소유 `/Library/LaunchAgents`에 sudo 없이 쓰면 **첫 줄부터 permission denied**.
  (2026-07-11 로컬 모델이 여기로 써서 실패.)
- **bootout에 도메인 필수** — `launchctl bootout <라벨>`이 아니라 `launchctl bootout gui/$(id -u)/<라벨>`.
  도메인을 빼면 서비스를 못 찾는다.
- **bootout 직후 bootstrap하면 오류 5** — 재배포(plist 교체) 시 `bootout` 직후 곧바로 `bootstrap`하면
  `Bootstrap failed: 5: Input/output error`가 난다. **몇 초 대기 후 재시도**. (2026-07-11 `[[rapid-mlx]]`
  `--host` 인자 변경 재배포에서 겪음.)

## GUI / Electron 앱을 launchd로 상주시킬 때 (2026-07-11 추가)
백그라운드 데몬과 달리 **GUI 앱(Electron 등)은 추가 함정**이 있다:
- **Electron은 전용 바이너리로 실행.** plist `ProgramArguments`에 `node main.js`를 넣으면 안 뜬다.
  `node_modules/.bin/electron` 또는 `.app/Contents/MacOS/<앱>` 같은 Electron 바이너리를 지정.
- **`LimitLoadToSessionType: Aqua`** — GUI 세션(Aqua)에서만 로드되게 지정. 없으면 GUI 컨텍스트 밖에서 문제.
- **크래시 시에만 재기동**: `KeepAlive = {SuccessfulExit: false}`. 이러면 정상 종료(Cmd+Q)엔 다시 안 뜨고
  크래시했을 때만 부활한다. GUI 앱은 사용자가 끄면 꺼진 채로 두는 게 맞으므로 `KeepAlive: true`(무조건 부활)는 부적절.
- **desktop만 갱신되는 self-update는 서버 재시작 없이** `launchctl kickstart -k gui/$(id -u)/<라벨>`로
  해당 앱만 재기동. "plist 다시 쓰면 launchd가 알아서 재시작한다"는 잘못된 전제 — kickstart를 명시적으로 호출해야 한다.
- **상태 출력 스크립트 주의**: 재시작 스크립트가 GUI 앱 실행 여부를 `pgrep`으로 판별할 때 프로세스명/도메인이
  틀리면 "항상 중지됨"으로 오표시된다(2026-07-11 `restart-lampas.sh` 버그로 원복).

## 출처: [[2026-07-06-lampas-harness-구축]] · [[2026-07-11-desktop-퀵채팅-설치-스크립트]] · [[2026-07-13-람파스-누적운영기억-이관]] ([[lampas-harness]])
