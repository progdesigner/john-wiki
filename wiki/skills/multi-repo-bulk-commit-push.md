---
name: multi-repo-bulk-commit-push
description: 여러 git 저장소를 한 번에 "저장"(commit+push)해달라는 요청을 받았을 때, 변경 있는 저장소만 골라 커밋·푸시하고 push 거부는 rebase로 해소하는 절차
created: 2026-07-19
tags: [git, ops, bulk-update, works-portfolio]
---
# 다중 저장소 일괄 커밋·푸시

## 언제 쓰는가
"Works(또는 특정 상위 폴더)의 모든 저장소를 저장해줘" 같은 일괄 커밋·푸시 요청. [[multi-repo-safe-bulk-update]]가
pull(원격→로컬) 방향이라면, 이 스킬은 그 반대 방향(로컬→원격)이다.

## 절차 (단계별)
1. 대상 상위 폴더 하위의 `.git` 디렉터리를 찾아 전체 저장소 목록을 만든다.
2. 저장소마다 미커밋 변경(untracked/modified/staged) 여부를 확인한다 — 변경 없는 저장소는 건드리지 않는다.
3. 변경 있는 저장소만 각각 diff를 확인하고 내용에 맞는 커밋 메시지로 커밋한다.
4. push한다. 거부되면(원격에 로컬이 모르는 새 커밋 존재) `git pull --rebase`로 원격 커밋을 먼저 통합한 뒤
   재push한다.
5. rebase 중 충돌이 나면 단순 fast-forward 문제인지, 같은 파일의 구조적 리팩터 충돌인지 구분한다 — 후자면
   [[rebase-local-feature-onto-refactored-remote]] 절차(원격 새 구조를 기준선으로 로컬 기능만 이식,
   빌드/타입체크 검증, 충돌 마커 grep 확인)를 따른다.
6. 결과는 저장소별 한 줄 표(변경 내용·결과)로 요약해 보고한다.

## 주의사항 / 함정
- **push 거부를 항상 "구조적 충돌"로 확대 해석하지 않는다** — 단순히 원격에 새 커밋이 쌓였을 뿐인 경우
  `pull --rebase` → push로 바로 끝나는 경우가 대부분이다. 실제 같은 파일을 다르게 고친 경우에만
  [[rebase-local-feature-onto-refactored-remote]]의 무거운 절차로 넘어간다.
- 변경 없는 저장소까지 커밋 시도하지 않는다 — 빈 커밋 생성 금지.
- 여러 저장소를 다루므로 저장소별 커밋 메시지는 각 저장소의 실제 diff에 맞게 따로 작성한다(전체를 하나의
  메시지로 뭉치지 않는다).
- [[multi-repo-safe-bulk-update]](pull 방향)와 짝을 이루는 루틴 — 같은 사용자가 "최신화"(pull)와
  "저장"(commit+push)을 번갈아 요청하는 패턴이 관찰됨 → [[works-project-portfolio]].

## 출처: [[2026-07-18-works-전체저장]]
