---
name: macos-app-complete-uninstall
description: macOS에서 LaunchAgent 데몬을 동반한 앱(CLI+GUI+데몬 번들)을 흔적 없이 완전히 제거하는 절차
created: 2026-09-26
updated: 2026-09-26
tags: [macos, launchd, uninstall, system-maintenance, cleanup]
---
# macOS 앱 완전 제거 (LaunchAgent 데몬 동반)

## 언제 쓰는가
`~/.앱이름`류 자체 디렉터리에 CLI/GUI 바이너리와 자격 증명을 함께 설치하고, LaunchAgent로
상주 데몬까지 띄우는 유형의 앱(예: [[omnara]])을 "완전히" 지워 달라는 요청을 받았을 때.
단순히 `.app`을 휴지통에 넣는 것만으로는 데몬·PATH·캐시가 남는다.

## 절차 (단계별)
1. **설치 형태 먼저 조사** — 삭제 전에 전부 찾는다:
   - 실행 중 프로세스(`ps`/`launchctl list`)로 데몬 존재·라벨 확인.
   - 본체 디렉터리(`~/.앱이름` 같은 숨김 폴더) 위치와 용량.
   - `.zshrc`/`.bashrc` 등 셸 rc 파일의 PATH·alias 추가 여부.
   - `~/Library/Caches`, `~/Library/Application Support`, `~/Library/Logs` 아래 관련 캐시.
   - Homebrew/pipx/uv/npm 등 패키지 매니저에 등록됐는지, `/Applications`에 `.app`이 따로 있는지.
   - 앱 디렉터리 안에 **git worktree**가 있으면 커밋 안 된 작업이 있는지 먼저 확인(빈 디렉터리면
     안전하게 삭제 가능 — 이 판단을 건너뛰고 바로 지우면 작업물 유실 위험).
2. **데몬 중지 → LaunchAgent 등록 해제** — `launchctl bootout gui/$(id -u)/<라벨>` 후
   `~/Library/LaunchAgents/<라벨>.plist` 삭제. plist를 먼저 지우고 bootout을 건너뛰면 프로세스가
   좀비로 남을 수 있으니 순서를 지킨다 (→ [[macos-launchd-daemon]]의 역방향).
3. **본체 디렉터리 전체 삭제** — 앱 바이너리 + CLI + 자격 증명 + 로그가 보통 한 폴더에 몰려
   있으므로 통째로 제거.
4. **셸 rc 파일 정리** — PATH 추가 줄만 정확히 제거(다른 앱의 설정과 섞여 있을 수 있으니
   전체 초기화가 아니라 해당 줄만 골라 지운다).
5. **잔여 캐시 삭제** — 앱 이름 또는 앱이 참조하던 경로(예: 워크트리 경로) 기준으로 검색.
6. **사후 검증 (생략 금지)** — 삭제로 끝내지 말고 다시 조회해 확인:
   - 패키지 매니저(Homebrew/pipx/uv/npm) 목록에 없는지.
   - `/Applications`에 없는지.
   - 프로세스·`launchctl list`에 더 이상 안 뜨는지.
   - 앱이 남긴 파일 경로들을 다시 `ls`/`find`로 재조회해 전부 사라졌는지.

## 주의사항 / 함정
- **다른 도구의 자체 기록과 혼동하지 말 것** — 예: Claude Code CLI가 그 앱의 워크트리 경로에서
  세션을 실행한 적이 있다면, `~/.claude.json`의 프로젝트 항목이나 `~/.claude/projects`의 세션
  기록에 그 경로가 남는다. 이건 **삭제 대상 소프트웨어가 아니라 다른 도구의 대화 기록**이므로
  "완전 제거" 범위에서 제외하고 사용자에게 별도로 지울지 물어본다.
- **위키·설치 감사 기록에 이미 흔적이 있는지 먼저 확인** — 과거 `launchctl list` 전수조사
  같은 시스템 점검 기록이 있다면, 이번에 지운 데몬이 그 원시 로그에 이미 찍혀 있었는데
  분석 단계에서 누락됐을 수 있다(2026-09-26, [[macos-launchctl-cleanup-candidates]]에서
  `com.omnara.daemon`이 이 패턴으로 누락돼 있었음). 발견하면 해당 기록도 갱신해 둔다.

## 출처: [[2026-09-26-omnara-완전제거]] ([[omnara]] 완전 제거)
