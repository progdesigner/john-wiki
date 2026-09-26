---
tags: [entity, app, lampas-studio, sports-clip-pipeline, video-editing]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-reels ("Reels", reels.lampas.io)

`[[lampas-studio]]` 저장소(`lampas-system`)의 스포츠 클립 파이프라인 자매 앱. 클립 편집 그룹 보드에서
페르소나·카피를 선택해 편집 그룹을 만들고, 갤러리에 완성된 영상을 Package로 보내는 역할을 한다.
`[[lampas-web-flow]]`의 4·6번째 노드(Reels·Package)가 이 앱을 가리킨다. 이전까지는 다른 엔티티 페이지
안에서 코드체(`lampas-web-reels`)로만 언급됐고 독립 페이지는 없었음 — 이 페이지가 최초 정식 엔티티화
(2026-09-26 ingest, 출처는 여러 날짜의 세션 조각).

## 관찰된 기능
- **편집 그룹 생성**: 클립·카피를 선택해 편집 그룹을 만드는 보드. 순수 로직은
  `apps/lampas-web-reels/src/lib/editGroup.ts`.
- **페르소나 선택 UI**: 가로 스크롤 카드 스트립 — `[[lampas-web-copy]]`의 카드 스트립 디자인을 그대로
  참조(2026-09-25 밤 별도 세션, `raw/conversations/2026-09-25-reels-페르소나-카드스트립-점수정렬.md`,
  미정식 ingest). 신뢰도/채점 점수 내림차순 정렬 기능 보유(같은 세션, 상세 미확인).
  - **데이터 출처 이관** (2026-09-19, [[2026-09-19-pulse-페르소나-단일출처-계정이관-신뢰도개선]]):
    이전엔 자체 `suggestPersonas`/`fetchLatestPersonaSet` API로 페르소나를 가져왔으나, Pulse 개편으로
    `fetchPulsePersonas`/`fetchPulseFields`(`[[lampas-web-pulse]]`, `pulse_personas` 테이블)로 교체됨.
    `personaOptions(personas, fields)`가 라이브러리 전체 기준으로 이름+타겟 중복 제거, 클립 범위였던
    `PersonaOption.clipIds`는 `scope` 라벨로 대체. "Pulse에서 페르소나 만들기 ↗" 링크
    (`VITE_PULSE_APP_URL/personas`)가 빈 상태에 추가됨.
- **Package 연동**: 갤러리에 있는 완성 영상을 선택해 [[lampas-web-package]]로 전송 (`[[lampas-web-flow]]`
  6번째 노드). Package 쪽은 2026-09-26 ingest에서 정식 엔티티화됨(이전엔 "소속 미상"으로 기록).
- **Jev 자동 실행과의 연결**(2026-09-20, [[2026-09-20-lampas-flow-만들기]]): Flow의 노드 자동화가
  편집 그룹을 저장하는 과정에서 **카피 연결 정보를 누락**하던 버그가 있었고 수정됨 — Reels 자체
  저장 로직의 결함이 Flow 자동화 검증 중에 드러난 사례.

## 관련
- 상위 파이프라인: [[lampas-agent]](영상 수집·클립) · [[lampas-web-pulse]](페르소나 라이브러리 출처) ·
  [[lampas-web-copy]](페르소나·카피, 카드 스트립 디자인 원본) · [[lampas-web-flow]](오케스트레이션)
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 세션: [[2026-09-19-pulse-페르소나-단일출처-계정이관-신뢰도개선]](페르소나 데이터 출처 Pulse로 이관) ·
  [[2026-09-20-lampas-flow-만들기]] (Jev 자동화 중 카피 연결 누락 버그 발견·수정)
