---
name: self-hosted-agent-server-ops
description: 에이전트 턴 자체를 호스팅하는 서버(람파스 8787 등)를 재시작·종료·디버그할 때의 안전 규칙과 포트 함정
created: 2026-07-13
tags: [lampas-harness, launchd, server, port, restart, operations, self-hosting]
---
# 자기 자신을 호스팅하는 서버의 운영 함정

## 언제 쓰는가
`[[lampas-harness]]`처럼 **에이전트(나)의 턴을 자기 프로세스에서 spawn하는 서버**를 다룰 때 —
코드를 고쳤는데 반영이 안 되거나, 서버를 재시작·종료해야 하거나, 테스트 인스턴스를 띄웠다 죽일 때.
운영 데몬은 launchd(`io.lampas.harness.daemon`, KeepAlive) 관리 → [[macos-launchd-daemon]].

## 함정 1 — 옛 프로세스가 포트를 점유 (stray-server-port)
"코드를 고쳤는데 동작이 안 바뀐다"면 **중복 serve 프로세스**를 먼저 의심.
- 증상: 수동 실행된 옛 프로세스가 8787을 점유 → launchd 데몬이 뜰 때마다 `EADDRINUSE`로 웹 리스너만
  죽고 큐 워처는 사는 **반쪽 상태** 반복. `logs/daemon.out.log`에 EADDRINUSE 줄.
- 진단·복구:
  1. `ps aux | grep 'index.js serve'` — 중복 확인
  2. `logs/daemon.out.log`에서 EADDRINUSE 확인
  3. 옛 프로세스 kill 후 `launchctl kickstart -k gui/$(id -u)/io.lampas.harness.daemon`
  - 단, **이 대화 자체가 그 서버로 실행되므로** 즉시 kill하면 진행 중 턴이 끊긴다(함정 2). →
    `nohup bash -c 'sleep 25; …' &`로 지연 실행하고 최종 답변을 먼저 마친다.

## 함정 2 — 서버를 죽이면 내 턴이 끊긴다 (restart-kills-own-turn)
이 세션이 람파스 웹 채팅으로 실행 중일 때(시스템 프롬프트에 imagegen/scheduler 지시가 주입돼 있으면 그
경우), `dist/index.js serve`를 kill/재시작하면 SDK 자식(나)은 고아로 살아도 **부모가 죽어**:
- 권한 프롬프트가 전부 **"Stream closed"**로 실패,
- SSE 응답·`appendTranscript`가 죽은 부모 몫이라 **사용자 화면·히스토리에 내 출력이 전달 안 됨**.
- 대응: 턴 안에서 재시작이 필요하면 **코드 수정·빌드까지만** 하고 재시작은 사용자에게 맡기거나,
  nohup 지연 스크립트로 분리 실행([[detach-long-job-nohup]])하고 **최종 답변을 먼저 마친 뒤** 재시작되게.
  재시작 직후엔 권한 필요 작업(curl, 새 파일 Write 등)이 모두 실패한다.

## 함정 3 — pkill -f가 운영 데몬까지 죽인다 (pkill-hits-prod-daemon)
테스트 인스턴스(`PORT=8899 npx tsx src/index.ts serve`)를 `pkill -f "tsx src/index.ts serve"`로 종료하면
**운영 launchd 데몬도 명령줄이 같아 함께 죽는다**(launchd KeepAlive가 즉시 재살리지만 진행 중 턴은 끊김).
- 대응: 테스트 인스턴스는 **`/usr/sbin/lsof -ti :8899`로 PID를 특정해 그 PID만 kill**한다
  (`lsof`는 PATH에 없어 절대경로 필요).
- 셸 상태가 Bash 호출 간 유지 안 되므로 `kill %1` 같은 **잡 제어는 무효** — 이전 인스턴스가 포트를 계속
  쥐면 함정 1과 같은 EADDRINUSE.

## 주의사항 / 함정
- 세 함정의 공통 원인: **운영·테스트·현재 턴이 같은 소스·같은 실행 명령·같은 포트군을 공유**한다는 점.
  프로세스를 구분할 땐 명령줄(-f 패턴)이 아니라 **포트→PID**(`lsof -ti`)로 특정하라.
- rapid-mlx도 같은 launchd 관리 함정을 공유 → [[rapid-mlx]].

## 출처: [[2026-07-13-람파스-누적운영기억-이관]] ([[lampas-harness]])
