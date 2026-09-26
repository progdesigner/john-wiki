---
tags: [session, lampas-studio, lampas-web-edit, image, track, s3, deploy]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-13 (06:10Z~) — Lampas Edit 이미지 미디어/트랙·텍스트 자간·원본 S3 백업 최초 구현

작업 디렉터리: `/Users/progdesigner/Works/lampas/lampas-system`(=[[lampas-studio]]). 소스:
`raw/conversations/2026-09-13-lampas-edit-이미지트랙-텍스트효과-원본백업-구현.md` (원본 아카이브
`c8f4e60e-2728-4d07-b392-8e032dd9608f.jsonl`, `Tool: claude`, 2026-09-13T06:10:08Z 시작).

**시점 정정(2026-09-26 ingest)**: 이 위키는 지금까지 `[[lampas-web-edit]]`의 "최초 상세 노출"을
[[2026-09-19-lampas-edit-자막-템플릿-대량기능개발]] 세션(버전 0.1.17→0.1.43)으로 기록해왔다. 이
세션은 그보다 **6일 앞선(2026-09-13 06:10Z, 그날의 다른 세션들 — CS/시나리오/dalar-web-first —
보다도 이른 시각)** 실제 최초 관찰 지점이다. 세션 시작 시점에 이미 `lampas-web-edit`은 완전히
영상 전용(mediabunny `VideoSampleSink` 디코딩, `<video>` 미리보기)이었다고 확인됐으므로, 이 세션
자체가 앱의 절대적 기원은 아니지만 — **이미지 미디어/트랙 지원, 트랙 순서 스왑, 텍스트 자간·장평·
기울임, 로그인 계정 원본 자동 S3 백업**의 원출처는 이 세션이다. 버전은 0.1.2(세션 시작 시점 기존
버전)에서 시작해 0.1.11 부근까지 여러 차례 배포되며 진행됐고(정확한 최종 버전은 소스에 명시 안 됨),
이후 [[2026-09-19-lampas-edit-자막-템플릿-대량기능개발]] 세션의 시작 버전 0.1.17과는 사이가 비어
있다(0.1.12~0.1.16 히스토리는 이 소스로 확인 불가).

## 기능 타임라인

| 버전 | 기능 |
|---|---|
| 0.1.2→0.1.3 | **이미지 미디어 추가**(`kind:'video'|'image'`, `probeImage`, 기본 표시 5초, 트림 상한 없음) + **이미지 트랙**(`TrackType:'image'`) + **트랙 행 전체 스왑**(`trackOrder`, 오디오 제외 ▲▼ 헤더 버튼) |
| 0.1.3→0.1.4 | 미디어 필터 칩 줄바꿈 수정 + 트랙 헤더 아이콘을 호버 시에만 노출되는 오버레이로 전환(라벨 잘림 해결) + **이미지 위치/크기를 텍스트 오버레이처럼**(중심 x/y 드래그, 휠 스케일, 최소 5%) |
| 0.1.4→0.1.5 | 이미지·텍스트 오버레이에 **투명도(opacity)** 추가 + 좌측 레일 "이미지" 탭 신설 + 트랙 이동 대상 없으면 새 트랙 자동 생성하도록 수정 |
| 0.1.5→0.1.6 | 이미지 스케일업 시 왼쪽으로 쏠리는 버그 수정 — 원인은 Tailwind Preflight `img{max-width:100%}` → [[tailwind-preflight-img-maxwidth-overrides-inline-scale]] |
| 0.1.6→0.1.7 | 클립을 드래그해 **같은 종류의 다른 트랙 행으로 세로 이동** 가능하도록 수정(기존엔 우클릭 메뉴로만 가능) |
| 0.1.7→0.1.8 | 하단 트랙이 스크롤해도 안 보이던 버그 수정 — 원인은 flex stretch + `overflow-y-hidden` 조합이 넘치는 트랙 내용을 잘라버린 것 |
| 0.1.8→0.1.9 | "화면 맞춤" 스케일 슬라이더 step 5%→1% |
| (중간) | 미디어 추가 시 소스/훅점수 필터 **가능성 조사**(구현 안 함) — 아래 "조사만 하고 미구현" 절 |
| 0.1.9→0.1.10→0.1.11 | 텍스트 오버레이에 **자간(letter-spacing)·장평(가로폭 scaleX)**, 이어서 **기울기(skewX, 체크박스로 12° 고정)** 추가 |
| (버전 미상, 같은 세션 후속) | **로컬 반입 원본 파일 S3 백업**(`EditSessionAsset` 테이블 신설) — 아래 절 참고 |

## 로컬 반입 원본 파일 S3 백업 — `EditSessionAsset` (신규 기능)

"다른 컴퓨터에서 편집 세션을 열면 로컬로 드래그해 넣은 영상·이미지가 '파일 없음'으로 뜬다"는 문제를
근본 해결. 로컬 파일은 브라우저 OPFS에만 존재해 기기 종속이었던 것을, 로그인 상태일 때 fingerprint
(`size-lastModified-name`) 기준 사용자당 1회 S3에 백업해두고, 세션을 열 때 파일이 없으면 서버에서
자동 재다운로드하도록 구현. `lampas-api`에 `EditSessionAsset` 테이블 + presign/confirm/조회 3개
엔드포인트 신설, `lampas-web-edit`의 `saveClipFile`/`addFiles`/`replaceClipMedia`/누락 파일 감지
지점에 백업·자동복구 로직 연결. 조사(서브에이전트)가 `scenario-sessions`의 기존 S3 영속화 패턴
(`persistImage`/`persistVideo`/`ResourceService.uploadBuffer`/`getPresignedPutUrl`)을 먼저 찾아
그대로 재사용했다. 테스트 25개 추가, 전체 스위트 798개 통과. 절차 일반화 →
[[local-asset-fingerprint-s3-backup-recovery]].

**배포 중 발견한 위험(건드리지 않고 보고만 함)**: `schema.prisma`에 다른 미커밋 작업이 `scopeArea`
컬럼(실데이터 있는 `clip_sources`·`clips`·`reel_exports`)을 지우려는 상태로 남아 있어, 통상적인
`prisma db push`를 썼다면 그 컬럼 데이터가 삭제될 뻔했다 — `prisma migrate diff`(오프라인)로
새 테이블만 골라낸 SQL을 만들어 운영에 직접 적용해 이 드리프트를 건드리지 않고 우회함 →
[[prod-ddl-before-deploy-with-drift-check]] "변형" 절에 추가.

## 조사만 하고 미구현 — 미디어 추가 시 소스/훅점수 필터

사용자가 "영상 편집에서 미디어 추가 시에도 소스 선택과 훅 점수를 가져와서 필터링해 볼 수 있게
할 수 있나?"라고 물어, 서브에이전트 2개를 띄워 `lampas-api` 클립 뱅크 API를 조사했다. 결론:
- **소스 필터**는 `GET /clips/sources` + `GET /clips?sourceId=`가 이미 있어 API 변경 없이 바로 이식
  가능(`lampas-web-clips`의 `<select>` 패턴 재사용).
- **훅 점수 최소값 필터**는 서버에 없음(정렬만 지원) — `lampas-web-reels`도 사실 서버 필터가 아니라
  받아온 목록을 클라이언트에서 `hookScore >= 기준값`으로 거르는 방식이라, 같은 패턴을 그대로 이식하면
  API 변경 없이 되지만 "받아온 페이지 안에서만 거른다"는 정확도 트레이드오프가 있다.
사용자에게 두 방향(빠른 클라 필터 vs 정확한 서버 필터 신설)을 제시하고 답을 받기 전에 다음 요청
(텍스트 자간·장평)으로 넘어가 **이 기능 자체는 이 세션에서 구현되지 않았다** — 후속 세션에서 재확인 필요.

## 특이사항 — 병렬 세션 인지, 충돌 없이 진행

작업 중 같은 파일들(`editorCommands.ts`, `TimelineStrip.tsx`, `ClipBankDialog.tsx`,
`ClipBankBrowser.tsx`)에 **다른 세션이 동시에 "클립을 다른 소스로 교체"(`replaceClipMedia`) 기능을
추가**하고 있는 것을 발견했으나, 겹치지 않게 잘 얹혀 있어 그대로 두고 배포마다 `git status`로 자기
파일만 스테이징됐는지 확인하는 방식으로 문제없이 진행됨 — [[selective-hunk-commit-shared-file]]에
새로 추가할 만한 사고는 없었음(기존 절차가 예방적으로 잘 작동한 사례).

## 관련
- 엔티티 갱신: [[lampas-web-edit]](이 세션이 실제 최초 관찰 시점으로 정정) · [[lampas-studio]]
- 스킬 신규: [[tailwind-preflight-img-maxwidth-overrides-inline-scale]] ·
  [[local-asset-fingerprint-s3-backup-recovery]]
- 스킬 갱신: [[prod-ddl-before-deploy-with-drift-check]](파괴적 드리프트 회피 변형) ·
  [[template-image-slot-fingerprint-vs-url]](0.1.42 "원본 자동 백업" 폴백 3단계의 실제 인프라 출처)
- 이후 세션: [[2026-09-19-lampas-edit-자막-템플릿-대량기능개발]](다음 관찰 시점, 0.1.17부터)
