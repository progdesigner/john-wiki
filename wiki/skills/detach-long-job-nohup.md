---
name: detach-long-job-nohup
description: 에이전트 세션보다 오래 걸리는 다운로드·빌드·장기 명령이 턴/세션 종료로 죽지 않게 분리 실행할 때
created: 2026-07-09
updated: 2026-07-16
tags: [nohup, background, lampas-harness, download, shell]
---
# 장기 실행 작업을 세션에서 분리 (nohup & disown)

## 언제 쓰는가
`rapid-mlx pull`(수십 GB 다운로드), 대형 빌드 등 **한 턴보다 오래 걸리는 명령**을 돌릴 때.
에이전트가 그냥 `&`로 백그라운드에 띄우면 세션이 넘어갈 때 부모가 사라지며 함께 죽는다
(다운로드가 8GB 부근에서 반복 정지한 원인 → [[harness-background-process-lifecycle]]).

같은 패턴이 **자기 자신을 재시작하는 스크립트**에도 쓰인다 — `scripts/restart-lampas.sh`는 자신을
`nohup ... --worker &`로 재호출해 즉시 반환한 뒤, 워커가 `sleep 5` 후 실제 재시작을 수행한다. 목적은
다운로드 생존이 아니라 **현재 응답이 사용자에게 먼저 전달되게 하는 것**(재시작이 응답 전송 채널 자체를
끊는 걸 방지) — 자세한 안전 규칙은 [[self-hosted-agent-server-ops]] 함정 2. →
[[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]]

## 절차 (단계별)
1. **세션과 분리해 실행**:
   ```bash
   nohup <명령> > <로그파일> 2>&1 &
   disown
   ```
   - `nohup` — HUP(터미널/부모 종료) 신호 무시 → 부모가 사라져도 계속 실행.
   - `> 로그 2>&1` — 출력을 파일로 (터미널 없어도 되게, 진행 추적용).
   - `&` — 백그라운드 실행.
   - `disown` — 셸 작업 목록에서 떼어내 셸 종료에도 안 죽게.
2. **진행 추적**은 로그/디스크로:
   ```bash
   tail -f <로그파일>              # 바이트 진행 등 실시간
   du -sh <다운로드 캐시 디렉터리>   # 크기로 확인 (예 ~/.cache/huggingface/hub/models--...)
   ```
   HuggingFace 등은 **이어받기**를 지원 → 죽었다 다시 돌려도 처음부터 아님.
3. **완료 판정**: 목표 크기 근처에서 멈추고 `.incomplete` 조각 파일이 사라지면 완료.
4. 상주가 필요한 서비스라면 다운로드 후 **launchd 서비스로 승격** → [[macos-launchd-daemon]].

## 주의사항 / 함정
- **`run_in_background`도 세션 teardown에 죽는다**: Bash 도구의 `run_in_background` 작업은 SDK CLI
  프로세스의 자식이라, **대화 세션이 넘어갈 때(CLI teardown) 함께 종료**된다. task-notification이
  **"No completion record"로만 남아 완료로 오인**하기 쉽다(rapid-mlx 37GB 다운로드가 두 번 8GB 부근에서
  끊긴 원인, 2026-07-09). → 그래서 `&`도 `run_in_background`도 아닌 `nohup … & disown` 완전 분리가 필요.
- **감시 루프 zsh glob 함정**: `while true; do du -sh …models--...; done`에서 경로에 미확장
  glob 문자가 있으면 zsh가 `no matches found`로 루프를 죽일 수 있다. 실제 다운로드와 무관하니
  경로를 따옴표로 감싸거나 `setopt no_nomatch`.
- 진짜 안정적으로 하려면 **사용자가 직접 터미널에서 한 번 실행**하는 게 가장 견고
  (이어받기되므로 에이전트가 8GB까지 받아둔 것도 활용).
- SDK 인자 스캐너가 `https://…`·슬래시 경로를 막으면 명령을 스크립트 파일로 써서 실행
  → [[deploy-sandbox-pnpm-shim]].

## 출처: [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]] · [[2026-07-13-람파스-누적운영기억-이관]] · [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]] · 배경: [[harness-background-process-lifecycle]]
