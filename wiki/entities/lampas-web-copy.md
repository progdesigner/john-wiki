---
tags: [entity, app, lampas-studio, sports-clip-pipeline, sns-copy, react]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-copy ("Copy")

`[[lampas-studio]]` 저장소(`lampas-system`)의 스포츠 클립 파이프라인 자매 앱 중 하나. 클립 근거로
SNS 카피·페르소나를 생성하는 웹. `[[lampas-agent]]`(스포츠 클립 라벨링 데몬)·`lampas-web-reels`(클립
편집)·`[[lampas-web-status]]`와 같은 계열 — 2026-09-25 세션들에서 처음 노출됐고, 저장소 루트
`AGENTS.md`(3라인 앱 목록: Lampas/Dalar/Talk)에는 등장하지 않는 앱이다(자세한 불일치는
`[[lampas-studio]]` "스포츠 클립 파이프라인" 절 참고).

## 관찰된 기능
- **페르소나·클립 목록**: 가로 스크롤 카드 스트립. 2026-09-25 세션([[2026-09-25-copy스크롤-fixs삭제-tools모델표시-영상재생버그]])
  에서 발견된 버그 — 목록 자체엔 가로 스크롤 CSS가 있었지만, **바깥 `fieldset`이 카드 전체 너비만큼
  늘어나 스크롤 컨테이너 폭이 콘텐츠에 종속**돼 스크롤이 생기지 않고 화면 오른쪽으로 계속 밀려나가는
  구조였다. 바깥 영역 너비를 제한 + 긴 페르소나 설명 줄바꿈 처리로 수정, v0.2.6 배포. 테스트 32개 통과.
  - `lampas-web-reels`의 페르소나 선택 UI(가로 스크롤 카드 스트립)가 이 Copy 앱 디자인을 그대로
    참조한다(같은 날 밤 별도 세션, `raw/conversations/2026-09-25-reels-페르소나-카드스트립-점수정렬.md`
    — 미정식 ingest, 상세 미확인).
- **경기 맥락 연동**([[lampas-agent]] "sports-wiki 경기 엔티티" 기능과 연결, 2026-09-25): 클립이 속한
  경기·팀·결정적 장면을 카피 프롬프트 끝에 추가하고, 결과 상단에 "이 클립의 경기"를 표시.

## 관련
- 상위 파이프라인: [[lampas-agent]](추출·라벨링) · `lampas-web-reels`(클립 편집·페르소나 선택) ·
  [[lampas-web-status]](시스템 상태)
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 세션: [[2026-09-25-copy스크롤-fixs삭제-tools모델표시-영상재생버그]] ·
  [[2026-09-25-스포츠위키-경기엔티티-설계구현]]
