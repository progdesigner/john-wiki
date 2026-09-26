---
tags: [entity, app, lampas-studio, sports-clip-pipeline, clip-bank, react]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-clips ("클립 뱅크")

`[[lampas-studio]]` 저장소(`lampas-system`) 내 스포츠 클립 파이프라인 자매 앱. `[[lampas-agent]]`가
인제스트한 클립을 조회·큐레이션하는 **읽기/큐레이션 전용** 웹(인제스트 자체는 하지 않음). 클립 원본은
`lampas-api`의 `clips` 모듈(`ClipSource`/`ClipFolder`/`ClipCategory`/`Clip` Prisma 모델)에 저장되며,
이 앱은 `src/lib/clipsBankApi.ts` 등 순수 로직 라이브러리 위에 뱅크 UI를 얹는다. 2026-09-26 ingest에서
처음 정식 엔티티화됨(그 전엔 다른 페이지의 "관련 앱" 목록에서 코드체로만 언급).

## 다축(5축) 클립 점수 표시·정렬 (2026-09-18 세션 도입)
[[2026-09-18-lampas-clip-intelligence-brand-kit-대량구현]] 세션에서 `lampas-api`의 `clip-intelligence`
모듈(훅·확산성·정보성·감정·완결성 5축 채점)이 신설되면서, 이 앱의 클립 카드에도 5축 미니 바가
붙고 상단 "축 정렬" 셀렉트로 로드된 페이지를 복합 점수나 축별로 재정렬할 수 있게 됐다(가중치는
API와 동일). **주의 — 데이터 출처가 다음 날 바뀜**: 도입 당시엔 `lampas-agent`의 인제스트 라벨링
(비전 모델)이 4축 점수를 함께 냈으나, [[lampas-agent]] "훅 점수 — 절대 점수에서 상대 순위로 전환"
절이 기록한 대로 **다음 날(2026-09-19) 라벨링에서 축 점수가 전부 제거**됐다 — 이후 이 뱅크의 4축
미니 바는 라벨링이 아니라 reels의 AI 선별(`analyze`) 호출 결과로 채워지는 구조로 바뀌었다. 즉 UI
자체는 그대로지만 값을 채우는 파이프라인이 하루 만에 교체된 사례.

## 폴더 이름 변경 UI (2026-09-18 세션, 2회 반복 수정)
사용자 스크린샷 피드백에 따라 `FolderSidebar`의 이름 변경 진입점을 두 번 고침:
1. 1차: 활성 폴더 옆에 "이름" 버튼 추가 — 기존 더블클릭 방식이 발견하기 어렵다는 이유.
2. 2차: 사용자가 "제목 부분을 선택해서 수정하는 걸로 다시" 요청 → "이름" 버튼 제거, 대신 **선택된
   폴더의 상단 제목을 클릭하면 바로 인라인 입력**(Enter/포커스 이탈 저장, Esc 취소)으로 변경. "전체
   클립"·"폴더 없음"은 편집 대상에서 제외. 사이드바 자체는 클릭=선택/더블클릭=이름변경으로 원복.

두 수정 모두 기존 `PATCH /clips/folders/:id`만 사용, API 변경 없음.

## 관련
- 상위 파이프라인: [[lampas-agent]](클립 인제스트·라벨링 원천) · [[lampas-web-reels]](뱅크에서 클립을
  가져와 편집 그룹 생성) · [[lampas-web-copy]](뱅크 클립으로 카피 생성)
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 토픽: [[lampas-clip-intelligence]]
- 세션: [[2026-09-18-lampas-clip-intelligence-brand-kit-대량구현]]
