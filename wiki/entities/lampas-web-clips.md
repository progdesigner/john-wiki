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

## 최초 구현 — 분야(domain)·카테고리·스포츠 LLM-위키·훅 블렌드 슬라이더 (2026-09-12, 2026-09-26 뒤늦게 ingest)
이 앱에 "스포츠"라는 개념 자체가 처음 등장한 지점은 [[2026-09-18-lampas-clip-intelligence-brand-kit-대량구현]]
(아래 "5축 점수" 절)보다 **6일 앞선**
[[2026-09-12-lampas-copy페르소나-clips분야카테고리-스포츠위키-구축]] 세션이다. 이 세션이 처음 만든 것:
- `lampas-api` Prisma에 `ClipCategory`(시드 `sports/nba`·`sports/mlb`) 신설, `Clip`에 `domain`/
  `categoryId`/`playerScore`/`momentScore`/`playerRefs` 컬럼 추가.
- **`sports-wiki` 모듈** 신설(`SportsWikiSource`/`SportsWikiPage`/`SportsWikiLogEntry`, MySQL 3테이블 —
  이 [[john-wiki]] 저장소와 같은 "raw sources(불변)+LLM 유지 wiki+append-only log" 패턴을 참고하되
  markdown 파일 대신 **DB 테이블로 의도적으로 변형**, 이유: rsync+PM2 배포 모델에서 파일 손실 위험).
  `POST /v1/sports-wiki/ingest`(URL 또는 텍스트 직접 입력). 이후 2026-09-25 세션이 이 모듈에
  "경기(game)" 엔티티를 추가로 확장 → [[2026-09-25-스포츠위키-경기엔티티-설계구현]].
- 좌측 사이드바 상단 `DomainSwitcher`(일반/스포츠), 스포츠 선택 시 `FilterBar`에 카테고리 칩 +
  `hookBlend.ts`(선수 점수↔결정적 순간 점수 블렌드 슬라이더) + 위키 패널 버튼이 등장. `ClipCard`에
  선수 배지, `LabelEditor`에 스포츠 전용 필드 추가.
- **소스 분야 이동**(같은 세션 후속 요청): `POST /v1/clips/sources/:id/domain`로 소스+그 클립 전체를
  일괄 재분류, `FilterBar`에 "분야로 이동…" 셀렉트.
- **소프트 삭제 전환**(같은 세션 후속, 사용자 결정 "앞으론 소프트 삭제로 바꾸죠"): `Clip`/
  `ClipSource`가 원래 `deletedAt` 없는 하드 삭제(FK cascade + 즉시 S3 삭제)였던 것을 `deletedAt`
  소프트 삭제로 전환, `POST /clips/sources/:id/restore`·`/clips/:id/restore` 신설, 모든 조회 경로에
  `deletedAt: null` 필터, 특정 id 재조회용 `GET /v1/clips?ids=` 추가. 계기는 reels→clips→edit 삭제
  체인 조사에서 하드 삭제가 복구 불가·false-positive "삭제됨" 표시·깨진 edit 세션 세 가지 문제의
  공통 원인으로 지목된 것 → [[lampas-web-reels]] "지워진 클립 복구" 절.
- 라벨링 배치 504 오류의 근본 원인(AWS ALB 60초 유휴 타임아웃)을 규명하고 배치 크기 축소(8→4)+순차
  실행 전환(concurrency 6→1)으로 수정, 라벨링 직후 스포츠 클립의 선수/구단 데이터를 `sports-wiki`에
  자동 반영하는 **`wiki` 파이프라인 스테이지**를 신설(`lampas-agent-clips`) — 절차 →
  [[lb-idle-timeout-keepalive-streaming]], 상세는 [[lampas-agent]] 참고.
- **주의 — 이 세션 시점엔 인제스트 앱이 `lampas-agent-clips`라는 독립 앱**이었다(2026-09-18 세션에서
  `lampas-agent-pulse`와 병합돼 `apps/lampas-agent`가 됨). 아래 각 절이 "2026-09-18 세션 도입"이라
  기록한 5축 점수 등 기능들은 이 세션 **이후**에 추가된 것으로, 서로 다른 시점의 레이어다.
- 세션 전체 → [[2026-09-12-lampas-copy페르소나-clips분야카테고리-스포츠위키-구축]]

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
- 세션: [[2026-09-12-lampas-copy페르소나-clips분야카테고리-스포츠위키-구축]](**최초 구현**, 09-26
  뒤늦게 ingest — 분야/카테고리/sports-wiki/훅블렌드슬라이더/소스분야이동/소프트삭제 원출처) ·
  [[2026-09-18-lampas-clip-intelligence-brand-kit-대량구현]]
