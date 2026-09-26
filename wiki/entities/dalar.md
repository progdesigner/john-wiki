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

**2026-09-26 ingest 시 시점 정정(스텁 부분 해소)**: 위 "2026-09-26 처음 노출"은 이 위키의 관찰
시점일 뿐이다. 실제로는 그보다 6일 앞선 [[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]]
세션(`Tool: codex`, `lampas-system`)에서 Dalar 소속 실사용 소비자 제품 **`[[dalar-web-first]]`**
("First" — AI 돌잔치 인터뷰 영상 서비스, `first.dalar.ai`/`admin.first.dalar.ai`)가 대규모로
개발·배포됐음이 뒤늦게 ingest됨. 이는 `AGENTS.md` 인용만으로 존재를 추정했던 이전 기록과 달리
**실제 소스코드 경로(`apps/dalar-web-first/public/samples/...`)·운영 도메인·배포 커밋까지 확인된
첫 사례**다. 상세는 [[dalar-web-first]] 참고.

- **앱 목록 갱신**: 기존 6앱(`dalar-web-root/www/cs/app/admin`, `dalar-api`) 목록에 **7번째 앱
  `dalar-web-first`**가 추가된다.
- **`AGENTS.md` 모순**: `[[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]]` 세션
  시스템 프롬프트에 포함된 `AGENTS.md`(2026-09-20 시점)에는 **Dalar 앱이 하나도 열거되지 않는다**
  (Lampas 9앱+Talk 9앱+Iileex만 기재). 반면 하루 뒤인
  [[2026-09-21-lampas-agent-fixs-신설]] 세션의 `AGENTS.md`는 Dalar 6앱을 포함한다 — 같은 저장소의
  문서가 짧은 기간에도 버전이 다르거나 세션마다 다른 캐시를 인용받고 있다는 뜻. `dalar-web-first`는
  **두 버전 모두에 등재되지 않았다**(실사용 도메인·배포 이력이 있는데도 문서 목록 누락) — 문서가
  실제 앱 구성을 못 따라간다는 기존 관찰([[lampas-studio]] "구조 괴리" 절)의 세 번째 사례.

## 관찰된 정보 (AGENTS.md 인용, 미검증)
- `lampas-system` 모노레포 안에 Lampas·Talk와 나란한 세 번째 앱 그룹으로 존재.
- 앱 6개: `dalar-web-root`(8350), `dalar-web-www`(8351), `dalar-web-cs`(8352),
  `dalar-web-app`(8355), `dalar-web-admin`(8359), `dalar-api`(3356).
- **`apps/dalar-web-app`이 Node Studio(스튜디오 UI)의 SoT(Source of Truth)** — `[[lampas-studio]]`의
  `lampas-web-studio`는 `pnpm sync:studio`(`scripts/sync-studio-from-dalar.mjs`)로 여기서 동기화받는
  구조라고 `AGENTS.md`가 명시. **2026-09-24 갱신**: [[lampas-studio]]의 Seedance 2.0 Mini 추가 세션
  ([[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]])이 `dalar-web-app`을 먼저 수정하고
  `pnpm sync:studio`를 **실제로 실행**해 `lampas-web-studio`에 반영·드리프트 0을 확인했다 — 이 SoT
  관계가 문서 인용이 아니라 실행으로 검증된 첫 사례. 다만 그 세션도 Dalar 자체(`dalar-web-app` 내부
  구조, 다른 5앱)를 조사하진 않았으므로, `lampas-web-sdk` 자체 구현 서술과의 배치 여부·Dalar 소속
  자체는 여전히 다음 조사 필요.
- 스펙 문서 경로: `docs/superpowers/specs/2026-08-02-studio-sync-dalar-to-lampas-design.md`(내용 미확인).
- `pnpm sync:studio:check`로 스튜디오 SoT 드리프트를 검사하는 명령이 별도 존재.

## 열린 질문
- Dalar가 `[[lampas-studio]]`와 같은 회사/제품군인지, 별도 브랜드인지 이름만으로는 판단 불가.
- ~~`lampas-web-studio`가 Dalar에서 단방향 동기화만 받는지, 자체 변경분도 있는지 미확인.~~ →
  2026-09-24 세션에서 최소 1건은 단방향(dalar-web-app→sync:studio→lampas-web-studio) 동기화로
  확인됨. 다른 기능도 전부 단방향인지는 여전히 미확인.

## 관련
- 세션: [[2026-09-25-lampas-web-fit-구축-배포]] · [[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]] ·
  [[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]]
- 저장소: [[lampas-studio]] (같은 모노레포 `lampas-system` 안에 공존)
- 앱: [[dalar-web-first]]("First" AI 돌잔치 영상 서비스, `first.dalar.ai`)
