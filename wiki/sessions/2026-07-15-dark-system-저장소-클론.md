---
tags: [session, dark, dark-system, git-clone]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-15 — dark-system 저장소 클론

## 요약
[[progdesigner]]가 `~/Works`에 `dark` 폴더를 새로 만들어달라고 요청 → 하네스가
`/Users/progdesigner/Works/dark`를 생성. 곧이어 그 폴더에 `git@github.com:progdesigner/dark-system.git`을
클론해 `dark-system`을 만들어달라고 요청 → `/Users/progdesigner/Works/dark/dark-system`에 클론 완료.
클론 직후 pnpm 모노레포 구조(apps/, scripts/, pnpm-workspace.yaml)를 간단히 인지.

원격 저장소 소유자는 `progdesigner`(개인 계정) — [[cwc-commerce]]나 다른 계열사 org가 아님.

2왕복짜리 짧은 세션(2026-07-15T06:38 UTC)이며, 같은 날 10분 뒤(06:48)
[[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] 세션에서 방금 만들어진 `dark/dark-system`이
"이미 최신"으로 집계됐다. 즉 이 세션은 그 세션보다 앞서는 **dark-system의 로컬 최초 등장(폴더 생성 +
최초 클론) 이벤트**이며, [[2026-07-15-cwc-system-저장소-클론]](06:42)보다도 4분 앞선다 — 2026-07-15
오전 Works 일괄 최신화 직전에 최소 두 개의 신규 저장소(cwc-system, dark-system)가 막 클론되었음을 뜻한다.

dark-system 자체의 내용(제품 성격·스택 상세)은 이후 세션에서 아직 조사되지 않았다 — [[works-project-portfolio]]에
여전히 "미문서화"로 남아 있다.

## 시간 순서 (2026-07-15 오전, UTC)
1. 06:38 — 이 세션: `~/Works/dark` 폴더 생성 + `dark-system` 최초 클론
2. 06:42 — [[2026-07-15-cwc-system-저장소-클론]]: cwc-system 최초 클론
3. 06:48 — [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]: Works 12개 저장소 일괄 최신화(dark-system 포함, "이미 최신"으로 집계)

## 결정/사실
- `dark-system` 로컬 클론 경로: `/Users/progdesigner/Works/dark/dark-system`.
- 원격: `git@github.com:progdesigner/dark-system.git` (개인 계정 소유, org 아님).
- 클론 시점에 이미 pnpm 모노레포(apps/, scripts/, pnpm-workspace.yaml)임을 확인 — 세부 내용(제품 성격·스택)은
  미조사 상태로 남음.

## 관련
- [[works-project-portfolio]]
- [[2026-07-15-cwc-system-저장소-클론]]
- [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
