---
tags: [session, cwc, cwc-system, git-clone]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-15 — cwc-system 저장소 클론

## 요약
[[progdesigner]]가 `~/Works/cwc` 폴더에 `git@github.com:cwc-commerce/cwc-system.git`을 클론해달라고
요청. 하네스가 `cwc` 폴더를 확인해 기존 프로젝트([[elevino-system]] 등)는 있지만 `cwc-system`은 아직
없음을 확인하고 `/Users/progdesigner/Works/cwc/cwc-system`에 클론 완료. 클론 직후 pnpm 모노레포
구조(apps, scripts, docker-compose 포함)를 간단히 인지.

1왕복짜리 짧은 세션(2026-07-15T06:42 UTC)이며, 같은 날 9분 뒤(06:48) [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
세션에서 방금 클론된 이 저장소가 "이미 최신"으로 집계됐고, 다시 3분 뒤(06:51)
[[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]] 세션에서 이 저장소의 실제 내용(스택·모델·인증 방식 등)이
처음 깊이 조사됐다. 즉 이 세션은 그 두 세션보다 앞서는 **cwc-system의 로컬 최초 등장 이벤트**다.

## 시간 순서 (2026-07-15 오전, UTC)
1. 06:42 — 이 세션: cwc-system 최초 클론
2. 06:48 — [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]: Works 12개 저장소 일괄 최신화(cwc-system 포함, "이미 최신"으로 집계)
3. 06:51 — [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]]: cwc-system 내용 첫 조사 + 멤버십 크레딧 이식 설계

## 결정/사실
- [[cwc-system]] 로컬 클론 경로: `/Users/progdesigner/Works/cwc/cwc-system` (기존 엔티티 페이지의 경로 표기와 일치).
- 원격: `git@github.com:cwc-commerce/cwc-system.git`.
- 클론 시점에 이미 pnpm 모노레포(apps, scripts, docker-compose)임을 확인 — 세부 내용은 후속 세션에서 조사.

## 관련
- [[cwc-system]]
- [[works-project-portfolio]]
- [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
- [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]]
