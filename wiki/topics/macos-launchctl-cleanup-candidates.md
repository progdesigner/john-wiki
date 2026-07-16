---
tags: [topic, macos, system-maintenance]
created: 2026-07-16
updated: 2026-07-16
---
# macOS launchctl 정리 후보 (progdesigner 맥미니, 2026-07-16 시점)

`[[progdesigner]]`의 맥미니에서 `launchctl list` 전체 출력을 스캔해 나온 1회성 판단 기록.
실행 여부는 미확인 — 다음에 실제 정리할 때 이 목록을 출발점으로 재검토할 것.

## 🟡 제거 검토 대상

- **`com.github.facebook.watchman`** — 파일 시스템 감시 도구, 당시 상태 `-1`(비활성).
  개발 중 아니면 `brew services stop watchman`으로 제거 가능.
- **Adobe Creative Cloud 관련 에이전트 다수** (`com.adobe.*`, PID 다수 실행 중) — 디자인
  작업을 안 하면 Adobe CC 앱의 백그라운드 실행을 끄는 것으로 정리.
- **`com.microsoft.OneDriveStandaloneUpdater`** — 자동 동기화만 필요하면 업데이터 자체는 비활성화 가능.

## ✅ 필수 유지 확인됨

- `io.lampas.harness.desktop` — [[lampas-harness]] 데스크톱 앱 자신.
- `io.lampas.rapidmlx` — [[rapid-mlx]] 로컬 LLM 상주 프로세스.
- `homebrew.mxcl.redis`, `homebrew.mxcl.postgresql@15` — **[[lampas-harness]]가 실제로 쓰는지는
  미확인**. `grep -r "redis\|postgres" src/ --include="*.ts" --include="*.js" --include="*.json"`로
  확인 후 미사용이면 `brew services stop`으로 끄면 메모리/배터리 절약.
- 개발 도구(Docker, IDE 등), 커뮤니케이션 도구(Teams/Slack/WhatsApp/카카오톡 등, CWC 업무용)는
  기본 유지.

## 출처
[[2026-07-16-launchctl-불필요서비스-점검]] — `launchctl list` 원시 출력(약 560개 항목) 1회 분석.
Redis/PostgreSQL 실사용 여부 확인 결과는 아직 이 위키에 반영되지 않음(미해결).
