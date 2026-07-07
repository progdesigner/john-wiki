---
name: macos-launchd-daemon
description: macOS에서 서비스를 launchd 데몬으로 등록하고 명령어로 재시작할 수 있게 하는 절차
created: 2026-07-07
tags: [macos, launchd, daemon, devops]
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

## 출처: [[2026-07-06-lampas-harness-구축]] ([[lampas-harness]])
