---
tags: [entity, tool, local-llm, mlx, launchd, apple-silicon]
created: 2026-07-13
updated: 2026-07-13
---
# rapid-mlx

Apple Silicon용 로컬 LLM 서버(vllm-mlx 계열). `[[progdesigner]]`의 맥미니(M4 Pro / 64GB)에서
`[[lampas-harness]]`의 **비용 0 로컬 모델 프로바이더**로 상주하며, OpenAI 호환 API를 제공한다.

- 엔드포인트: `http://localhost:8000/v1` (무인증). 하네스 모델 선택기에 **"Local" 그룹**으로 노출 → [[model-selection]]
- 상주 모델: `qwen3.5-35b-8bit`(= `mlx-community/Qwen3.5-35B-A3B-8bit`, 약 37.7GB) — 64GB에 sweet spot.
- 설치 절차: [[local-llm-rapidmlx-install]] · 배경(양자화·사이징): [[local-llm-on-apple-silicon]]

## Auto 모델 난이도 판정의 실질적 1순위 경로 (2026-07-15 확인)
[[model-selection]]의 "Auto" 기능은 원래 Claude Haiku 4.5(API)를 판정 1순위로 설계했으나,
`ANTHROPIC_API_KEY` 크레딧 잔액이 0이라 API 판정이 항상 400으로 실패 → **rapid-mlx(Qwen3.5-35B)가
현재 실질적인 1순위 판정 경로**로 동작 중. 판정 속도 ~250ms, 비용 0원, 실사용 예시 7건 테스트에서
7/7 기대대로 분류(few-shot 보정 후). → [[2026-07-15-auto모델-기능-최초구현]]

## launchd 관리 (KeepAlive) — 2026-07-11 확인

rapid-mlx 서버는 LaunchAgent **`io.lampas.rapidmlx`**가 `KeepAlive`로 관리한다. 프로세스를 kill하면
launchd가 **즉시 옛 plist 설정 그대로 되살린다.** 따라서 실행 인자(예 `--host 0.0.0.0` 바인딩)를 바꾸려면
프로세스를 죽이고 수동 재실행하면 안 되고 — 그러면 launchd가 옛 설정 인스턴스를 또 띄워 **포트 경합**이
난다([[self-hosted-agent-server-ops]]의 stray-port와 같은 함정) — **plist를 고쳐 재배포**해야 한다.

- plist 원본: 레포 `launchd/io.lampas.rapidmlx.plist`
- 재배포: `~/Library/LaunchAgents/`로 복사 → `launchctl bootout gui/$(id -u)/io.lampas.rapidmlx`
  → `launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/io.lampas.rapidmlx.plist`
  - **bootout 직후 bootstrap하면 오류 5**가 나므로 몇 초 대기 후 재시도. → [[macos-launchd-daemon]]
- 로그: `logs/rapidmlx.out.log`, `logs/rapidmlx.err.log`
- **35B 모델 로드에 5분 가까이** 걸리니 포트 오픈 폴링은 여유 있게.

## 콘텐츠 생성 용도로 확장 — `branding.html` (2026-09-26~27)

기존 용도(Auto 난이도 판정)에 이어 **생성형 용도**로도 쓰이기 시작했다 — `[[lampas-harness]]`의
`branding.html`(위키 기반 Threads 글 생성)이 이 서버의 `qwen3.5-35b`(추론형)를 호출해 게시물 초안을
쓴다. 추론형 모델 특성상 **사고 과정이 출력 토큰 예산을 먼저 소진해 본문을 못 쓰는 문제**가 처음
드러나 "추론 출력 끄기"로 수정됨 → [[gemini-thinking-tokens-eat-maxtokens-budget]] (Gemini에서
먼저 관찰된 패턴의 로컬 모델 재현) · 세션: [[2026-09-26-브랜딩-threads콘텐츠생성-로컬llm-json검증버그수정]]

## 관련
- 호스트·소유자: [[progdesigner]] / 소비자: [[lampas-harness]] ([[model-selection]] 판정 ·
  `branding.html` 콘텐츠 생성)
- 스킬: [[local-llm-rapidmlx-install]] · [[macos-launchd-daemon]] · [[detach-long-job-nohup]] ·
  [[gemini-thinking-tokens-eat-maxtokens-budget]]
- 토픽: [[local-llm-on-apple-silicon]]
- 세션: [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]] · [[2026-07-13-람파스-누적운영기억-이관]] ·
  [[2026-09-26-브랜딩-threads콘텐츠생성-로컬llm-json검증버그수정]]
