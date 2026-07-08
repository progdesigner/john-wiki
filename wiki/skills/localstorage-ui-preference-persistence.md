---
name: localstorage-ui-preference-persistence
description: 마지막 선택한 UI 값(모델·정렬 등)을 새로고침·전환에도 유지하도록 localStorage에 저장할 때
created: 2026-07-09
tags: [frontend, localstorage, ux, persistence]
---
# localStorage UI 선택 기억

## 언제 쓰는가
드롭다운·셀렉트 등에서 **사용자가 마지막에 고른 값을 새로고침·화면 전환 후에도 유지**하고 싶을 때.
(예: 이미지 생성 모델, 분석 모델, 갤러리 편집 모델.)

## 절차 (단계별)
1. **read/store 헬퍼 한 쌍** — 유틸 파일에 `readStored…()` / `store…()`를 만든다. 키는 접두사 규칙으로(`lampas_gallery_edit_image_model`, `lampas_studio_analyze_model`).
2. **읽을 때 반드시 검증·폴백** — `readStored…()`는 기존 값 resolver(`resolveTransformImageModelValue()` 등)를 거쳐, 저장값이 **잘못됐거나 옵션 목록에서 빠졌으면 기본값으로 폴백**. 저장값을 무검증으로 신뢰하지 말 것.
3. **초기값을 저장값에서 복원** — 컴포넌트/훅의 초기 state를 `readStored…()`로 시작.
4. **변경 시 즉시 저장** — 셀렉트 onChange에서 `store…()` 호출.
5. **불필요한 리셋 로직 제거** — "에셋/화면 전환 시 기본값으로 리셋" 같은 코드가 있으면 제거해야 유지가 실제로 동작한다(프롬프트 등 진짜 초기화가 필요한 값만 리셋).
6. **전역 스코프** — 액터·에셋과 무관하게 전역으로 하나만 기억(요구가 그럴 때).

## 주의사항 / 함정
- **임베드·프라이빗 환경** — localStorage 접근이 실패할 수 있으니 read/store 모두 **try/catch로 조용히 무시**(기존 패턴 따르기). 저장 실패가 기능을 깨뜨리면 안 된다.
- **저장값 유효성** — 옵션 목록은 배포마다 바뀔 수 있다. 항상 현재 옵션에 대해 검증 후 폴백.
- 유지가 안 되면 십중팔구 어딘가의 **리셋 로직**이 초기값을 덮어쓰고 있는 것 — 그 지점을 먼저 의심.

## 출처: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] ([[lampas-studio]])
관련: 하네스의 모델 선택기 → [[model-selection]]
