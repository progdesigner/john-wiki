---
tags: [entity, project, product, monorepo, stub]
created: 2026-09-26
updated: 2026-09-26
---
# Dalar

이 위키에 **2026-09-26 처음 노출**된 제품/서비스 라인. `lampas-web-fit` 구축 세션
([[2026-09-25-lampas-web-fit-구축-배포]], `Tool: codex`)에서 `[[lampas-studio]]` 저장소
(`lampas-system`) 루트 `AGENTS.md`를 통해서만 간접 확인됐고, 이 세션에서 Dalar 자체를 다루거나
조사한 적은 없다 — 아래는 문서 인용뿐이며 검증되지 않음.

## 관찰된 정보 (AGENTS.md 인용, 미검증)
- `lampas-system` 모노레포 안에 Lampas·Talk와 나란한 세 번째 앱 그룹으로 존재.
- 앱 6개: `dalar-web-root`(8350), `dalar-web-www`(8351), `dalar-web-cs`(8352),
  `dalar-web-app`(8355), `dalar-web-admin`(8359), `dalar-api`(3356).
- **`apps/dalar-web-app`이 Node Studio(스튜디오 UI)의 SoT(Source of Truth)** — `[[lampas-studio]]`의
  `lampas-web-studio`는 `pnpm sync:studio`(`scripts/sync-studio-from-dalar.mjs`)로 여기서 동기화받는
  구조라고 `AGENTS.md`가 명시. 이 위키가 기존에 기록한 "`lampas-web-sdk`가 스튜디오를 직접 구현"이라는
  서술과 배치되므로, 실제 관계는 다음 조사에서 재확인 필요.
- 스펙 문서 경로: `docs/superpowers/specs/2026-08-02-studio-sync-dalar-to-lampas-design.md`(내용 미확인).
- `pnpm sync:studio:check`로 스튜디오 SoT 드리프트를 검사하는 명령이 별도 존재.

## 열린 질문
- Dalar가 `[[lampas-studio]]`와 같은 회사/제품군인지, 별도 브랜드인지 이름만으로는 판단 불가.
- `lampas-web-studio`가 Dalar에서 단방향 동기화만 받는지, 자체 변경분도 있는지 미확인.

## 관련
- 세션: [[2026-09-25-lampas-web-fit-구축-배포]]
- 저장소: [[lampas-studio]] (같은 모노레포 `lampas-system` 안에 공존)
