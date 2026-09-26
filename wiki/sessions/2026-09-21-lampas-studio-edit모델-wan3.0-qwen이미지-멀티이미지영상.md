---
tags: [session, lampas-studio, atlas-cloud, image-generation, video-generation, bugfix, model-catalog]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-21 — [[lampas-studio]] Edit 모델 추가·멀티이미지 영상·WAN 3.0·오류 반복 수정

`Tool: codex` 세션. 작업 폴더 `lampas-system`(=[[lampas-studio]]). 사용자가 여러 요청을 한 세션에 이어
붙여 진행(이미지 Edit 모델 2종 추가 → 영상 멀티이미지 연결 → 영상 생성 오류 반복 원인 규명·수정 →
Draft 이름 변경 버그 → WAN 2.7→3.0 → 카탈로그 갱신 → Qwen Image 3.0 Pro Edit 추가), 전부 배포까지 완료.
원본: `raw/conversations/2026-09-21-lampas-studio-edit모델-wan3.0-qwen이미지-멀티이미지영상.md`

## 요청 1 — 이미지 Edit 모델 2종 + 영상 멀티이미지 연결

- 사용자가 첨부 이미지로 지목한 두 모델은 조사 결과 **GPT Image 2.5 Sunburst Edit**·**Flare Edit**로 확인,
  이미지 촬영/보정에 추가.
- 영상 생성이 **연결된 이미지 중 첫 장만 서버로 전송**하던 제약을 확인 — 공식 입력 규격을 확인한
  **Seedance 레퍼런스 모델**로 다중 이미지 입력을 지원하도록 구현(이후 WAN 3.0 레퍼런스 모델도 추가).

## 요청 2 — 영상 생성 "오류 반복" 진단·수정 (핵심 버그)

증상: 영상 생성이 오류로 표시된 뒤 다시 정상 실행되는 일이 반복(실제 실행 자체엔 문제 없음).

원인 2가지가 겹쳐 있었음:
1. 영상 요청 직후 **서버 작업 ID가 아직 없는 로딩 노드**를 "중단된 생성"으로 오판하는 로직이 있었음.
2. 작업 ID를 받은 뒤엔 **실행 중 폴링과 복구용 폴링이 동시에** 같은 작업에 붙어, 상태 조회가 일시적으로
   한 번만 실패해도 실패 알림이 뜨는 구조였음(서버는 계속 생성 중이라 결국 완료되는 것과 맞물려 "실패
   떴다가 나중에 완료되는" 증상으로 나타남).

수정: 정상 요청(아직 작업 ID 없음)을 중단으로 오인하지 않도록 판정 조건을 고치고, 실행 중/복구 폴링
중복 부착을 제거. → 절차 스킬 [[false-abort-premature-status-check]] 신설.

## 요청 3 — Draft Work 이름 변경 안 되는 버그

Draft 상태의 Work는 이름 변경이 동작하지 않는다는 제보. 브라우저 기본 `prompt()` 대화상자를 쓰던 부분을
앱 내부 입력창으로 교체하고, 저장 실패 시에도 그 입력창에서 바로 확인할 수 있도록 수정.

## 요청 4 — WAN 2.7 → 3.0 교체

- Atlas Cloud 카탈로그 확인 결과 **WAN 3.0은 영상 모델로만 제공**, 이미지 생성·편집용 3.0은 없음.
- **영상**: 일반 WAN을 3.0으로 교체 + 다중 이미지 레퍼런스 영상 모델도 신규 추가. 기존 Work에 저장된
  구버전(WAN 2.7) 영상 선택도 실행 시 3.0으로 자동 승계.
- **이미지**: WAN 3.0 부재로 기존 **2.7 Pro 유지**(제약사항으로 명시 보고).
- 검증: 스튜디오 테스트 225개(이후 231개)·모델 요청 테스트 6개·두 웹앱 빌드·Lampas API `tsc` 통과.
  Dalar API 전체 타입 검사는 **기존 Prisma 타입 불일치로 막혀 있음**(이 세션이 만든 문제 아님, 변경분만
  별도 확인).
- 배포 완료, 실제 유료 생성 호출 테스트는 수행하지 않음.

## 요청 5 — `models.lampas.io` 카탈로그 갱신·재연동

- 운영 카탈로그에서 Sunburst·Flare가 아직 "카탈로그 외 모델"로 분류돼 **임시 가격**을 쓰고 있던 것을
  발견.
- **최신 508개 모델 동기화**, Sunburst·Flare Edit를 정식 카탈로그 등록(각 **6cr/장**), WAN 3.0
  **50cr/초**(다중 이미지·오디오 지원 명시)로 가격 확정.
- API 배포 전 전체 테스트 **1,081개** 통과 → API·스튜디오 배포 완료. 스튜디오 모델 선택창의 가격 표시가
  `models.lampas.io`와 **같은 카탈로그**를 조회하도록 재연결(카탈로그 API가 유일한 소스가 됨).

## 요청 6 — Qwen Image 3.0 Pro Edit 추가

`qwen-image-3.0-pro/edit` 입력 규격 확인 후 **이미지 촬영·보정·Transform**에 추가. 레퍼런스 **최대
3장**, **40cr/장**. 기존 Qwen 모델로 잘못 바뀌지 않고 Pro 모델 ID가 그대로 호출되는지 테스트, 웹·API
배포 완료.

## 관찰
- 이 세션은 [[dalar]]가 Node Studio SoT임을 별도로 언급하진 않았으나(모델·크레딧·버그 수정 위주라
  `dalar-web-app` 동기화 절차가 표면화되지 않음), 기존에 확정된 `pnpm sync:studio` SoT 관계와 배치되는
  서술은 없음.
- Atlas Cloud 경유 모델이 이 세션만으로 6종 늘거나 교체됨(Sunburst Edit·Flare Edit·Qwen Image 3.0 Pro
  Edit 신규, WAN 2.7→3.0 영상 교체, Seedance/WAN 3.0 레퍼런스 다중 이미지 신규) — [[atlas-cloud]] 갱신.

## 관련
- 엔티티: [[lampas-studio]] · [[atlas-cloud]]
- 스킬: [[false-abort-premature-status-check]]
