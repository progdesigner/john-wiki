---
name: self-hosted-agent-server-ops
description: 에이전트 턴 자체를 호스팅하는 서버(람파스 8787 등)를 재시작·종료·디버그할 때의 안전 규칙과 포트·빌드 함정
created: 2026-07-13
updated: 2026-07-16
tags: [lampas-harness, launchd, server, port, restart, operations, self-hosting, build]
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
- **변종 (2026-07-15 관찰)**: `EADDRINUSE`로 죽지 않고 **떠돌이 프로세스가 IPv6 와일드카드로 리슨**해서
  `localhost` 접속을 조용히 가로채는 경우도 있다 — 새 데몬은 정상 기동(포트 충돌 에러 없음)하는데도
  브라우저는 계속 옛 코드를 받는다. 증상이 EADDRINUSE 없이 "새 코드가 하나도 안 먹힌다"로만 보여
  함정 4(빌드 누락)와 헷갈리기 쉽다. 진단은 동일(`ps aux`로 중복 프로세스 확인) + `lsof -iTCP -sTCP:LISTEN`
  으로 실제 리스너 주소(IPv4 전용인지 IPv6 와일드카드인지)까지 확인하는 게 확실하다. →
  [[2026-07-15-gpt-realtime-음성입력-길게누르기]]

## 함정 2 — 서버를 죽이면 내 턴이 끊긴다 (restart-kills-own-turn)
이 세션이 람파스 웹 채팅으로 실행 중일 때(시스템 프롬프트에 imagegen/scheduler 지시가 주입돼 있으면 그
경우), `dist/index.js serve`를 kill/재시작하면 SDK 자식(나)은 고아로 살아도 **부모가 죽어**:
- 권한 프롬프트가 전부 **"Stream closed"**로 실패,
- SSE 응답·`appendTranscript`가 죽은 부모 몫이라 **사용자 화면·히스토리에 내 출력이 전달 안 됨**.
- 대응: 턴 안에서 재시작이 필요하면 **코드 수정·빌드까지만** 하고 재시작은 사용자에게 맡기거나,
  nohup 지연 스크립트로 분리 실행([[detach-long-job-nohup]])하고 **최종 답변을 먼저 마친 뒤** 재시작되게.
  재시작 직후엔 권한 필요 작업(curl, 새 파일 Write 등)이 모두 실패한다.
- **`scripts/restart-lampas.sh`가 이 패턴을 내장**: 인자 없이 실행하면 자신을 `--worker` 모드로
  `nohup` 재호출 후 즉시 "예약됨" 반환(현재 응답 전달 보장) → 워커가 `sleep 5` 후
  `launchctl kickstart -k gui/$(id -u)/io.lampas.harness.daemon` → `/api/health`를 최대 10초 폴링해
  성공/실패를 `logs/server.log`에 남김. pkill은 쓰지 않는다(함정 3과 무관). 2026-07-11 세션 당시엔 이
  스크립트가 "수동 즉시(pkill 기반)"로 기록됐었는데, 2026-07-15 세션에서 원본을 다시 읽어보니 이미
  지연 방식으로 바뀌어 있었다 — 스크립트 자체가 이 함정에 대한 대응으로 재작성된 것으로 보인다.
  → [[lampas-harness]] · [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]]

## 함정 3 — pkill -f가 운영 데몬까지 죽인다 (pkill-hits-prod-daemon)
테스트 인스턴스(`PORT=8899 npx tsx src/index.ts serve`)를 `pkill -f "tsx src/index.ts serve"`로 종료하면
**운영 launchd 데몬도 명령줄이 같아 함께 죽는다**(launchd KeepAlive가 즉시 재살리지만 진행 중 턴은 끊김).
- 대응: 테스트 인스턴스는 **`/usr/sbin/lsof -ti :8899`로 PID를 특정해 그 PID만 kill**한다
  (`lsof`는 PATH에 없어 절대경로 필요).
- 셸 상태가 Bash 호출 간 유지 안 되므로 `kill %1` 같은 **잡 제어는 무효** — 이전 인스턴스가 포트를 계속
  쥐면 함정 1과 같은 EADDRINUSE.

## 함정 4 — dist 빌드 누락으로 새 코드가 반영 안 됨 (stale-dist-build)
launchd 데몬은 소스(`src/`)가 아니라 **컴파일된 `dist/index.js`를 실행**한다. `src`만 고치고
`npm run build:server`(또는 해당 빌드 스텝)를 빠뜨리면, 코드는 커밋까지 됐는데 실행 중인 서버엔
새 라우트/로직이 전혀 없는 상태가 된다.
- 증상: 새로 만든 API(예 `/api/voice/token`)가 404/미동작, 재시작해도 안 고쳐짐 — **함정 1(특히 IPv6
  변종)과 증상이 똑같아 헷갈리기 쉽다.** 실제로 2026-07-15 세션에서 이 둘이 같은 배포에서 동시에 발생했다
  (빌드 누락 + 떠돌이 옛 서버).
- 구분법: `dist/`의 해당 파일을 grep해 새 코드가 실제로 컴파일 결과물에 있는지 먼저 확인 — 없으면
  함정 4(빌드), 있는데도 안 되면 함정 1(옛 프로세스가 가로챔).
- 대응: 서버(`src/`) 관련 변경은 재시작 전에 **반드시 build:server를 먼저 돌렸는지 체크리스트화**한다.
  웹(UI)만 바뀐 경우는 반대로 재시작 불필요(새로고침만으로 반영).
- → [[2026-07-15-gpt-realtime-음성입력-길게누르기]]
- **반대 사례 — `public/` 정적 파일은 build 없이 cp만으로 반영 (2026-07-16)**: `apps/web/public/quick.html`
  같은 파일은 vite가 빌드 시 `public/`에서 **그대로 복사**만 하는 정적 자원이라, 전체 `vite build`를
  안 돌리고 `cp apps/web/public/quick.html apps/web/dist/quick.html`만으로도 즉시 반영 확인이 가능하다
  (해당 정적 파일을 서빙하는 창을 새로 열면 보임). 소스가 번들링 대상(`src/`, 컴포넌트 등)이 아니라
  `public/`에 있는지 먼저 확인하면 불필요한 전체 빌드를 건너뛸 수 있다. → [[2026-07-16-quick-html-폴더선택기-auto모델-구현]]

## 주의사항 / 함정
- 네 함정의 공통 원인: **운영·테스트·현재 턴이 같은 소스·같은 실행 명령·같은 포트군을 공유**한다는 점.
  프로세스를 구분할 땐 명령줄(-f 패턴)이 아니라 **포트→PID**(`lsof -ti`)로 특정하라.
- rapid-mlx도 같은 launchd 관리 함정을 공유 → [[rapid-mlx]].

## 출처: [[2026-07-13-람파스-누적운영기억-이관]] · [[2026-07-15-gpt-realtime-음성입력-길게누르기]] · [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]] ([[lampas-harness]])
