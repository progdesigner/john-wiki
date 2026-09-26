---
tags: [session, lampas-agent, lampas-harness, john-wiki, threads, wiki-browser, feature-removal]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-26 — Threads 기능 취소·제거 + llm-wiki 탐색기를 lampas-harness `apps/wiki`로 이전

`Tool: codex` 세션(작업 폴더 `lampas-system`, 시작 2026-09-25T22:06Z UTC = KST 09-26 07:06).
방향이 두 번 크게 바뀐 긴 세션 — 사서 관점에서 핵심은 **이 저장소(john-wiki)가 처음으로 다른
시스템(lampas-harness)의 실제 기능 데이터 소스로 연결됐다**는 사실.

## 1막 — lampas-agent에 "Threads" 글 자동생성 기능 요청 (취소됨)

- 요청: `[[lampas-agent]]`(스포츠 클립 맥미니 데몬) Fixs 탭 앞에 **Threads 탭**을 추가해, "지금까지
  작업 후 보관한 위키 데이터"를 바탕으로 Threads(SNS) 글 초안을 자동 생성 + 사용자가 수정·저장한
  글에서 **페르소나**(말투·의도)를 학습해 다음 글에 반영.
- **1차 오해**: 어시스턴트가 "보관 위키"를 `lampas-agent`에 이미 연결된 **sports-wiki**(클립 라벨링
  결과가 쌓이는 그 위키)로 오인하고 그 방향으로 구현 착수(브라우저 localStorage 저장, 계정별 분리).
- 사용자 확인 질문에 대한 답: "개발 작업 기록·보관 문서 위키" — 즉 **이 저장소(john-wiki)**를 가리킴이
  확정됨.
- **1차 정정**: llm-wiki 실체를 `~/Works/john-wiki`로 특정("기억에 보관"으로 쌓인 `wiki/sessions`,
  `entities`, `topics` 구조 확인)하고, 저장 대상도 브라우저 localStorage에서 **서버 DB**로 전환(다른
  기기/브라우저에서 이어서 사용, 충돌 검사 추가). 이 위키 **186개 페이지**를 연결했다고 보고.
- v1.0.26 배포. 배포 전 검사에서 **Clips 위키 처리 코드와 기존 테스트가 어긋나 중단**되었다가, 테스트를
  현재 동작에 맞춰 갱신한 뒤 통과(437개)시켜 배포 완료.
- **2막에서 전면 취소**: 사용자가 "Threads 만드는 기능은 취소할게, lampas-agent에서 제거해줘"로
  기능 자체를 폐기 지시. 코드에서 제거되어 v1.0.29로 재배포됨. → 절차적으로
  [[full-stack-feature-removal-audit]] 계열(탭 UI + API 양쪽 제거).

## 2막 — llm-wiki 탐색기를 lampas-harness `apps/wiki`로 이전

- 요청: john-wiki를 "보는" 기능은 `lampas-agent`가 아니라 **상위 폴더 `[[lampas-harness]]`**의
  **`apps/wiki`**로 옮기고, 하네스 설정(⚙) 아래 **"위키" 버튼**을 추가해 **위키피디아 사이트처럼
  탐색**할 수 있게 해달라는 것. 진입점 파일명 후보로 `wiki.html`을 사용자가 직접 제안(어시스턴트가
  기존 `quick.html` 패턴과 맞다고 판단해 채택).
- 구현: 화면 코드는 `apps/wiki`에 모으고, 하네스 기존 **인증**과 **`WIKI_DIR` 설정**(이 저장소 경로,
  `[[wiki-memory-provider-integration]]`에서 2026-07-11에 이미 배선됐던 그 설정값)을 그대로 재사용.
  서버가 위키 원본(markdown)을 읽어 본문 검색, 문서 목차, 분류, **위키링크·역링크(backlink)** 연결까지
  렌더링.
- 검증: 하네스 테스트 79개(검색·위키링크·역링크·접근 제한 포함) 통과, `tsc` 통과, Playwright로 **데스크톱·
  모바일** 양쪽에서 검색·문서 열기·목차·뒤로가기·위키링크 클릭까지 실브라우저 검증 완료.
- **배포 지연**: 하네스 자체가 이 작업을 수행 중인 서버라, 재시작 시점에 **실행 중인 터미널 세션
  3~6개**가 끊길 위험이 있어 즉시 재시작을 미루고 세션 종료 시 자동 재시작하도록 예약(지연 방식은
  기존에 규명된 `restart-lampas.sh` 동작과 일치 → [[self-hosted-agent-server-ops]]).
- 세션 종료 시점: Threads 제거·v1.0.29 배포는 완료, `apps/wiki` 구현·빌드·브라우저 검증도 끝났지만
  **하네스 서버 재시작(반영)은 대기 중**이었음 — 이 위키 ingest 시점에 실제로 반영됐는지는 후속 확인 필요.

## 왜 중요한가 (사서 메모)

- 이 사건은 **"저장 ≠ 조회"** 통찰([[long-term-memory-architecture]])의 새로운 변형이다: 이번엔 LLM이
  능동 조회(tool)하는 게 아니라 **사람이 위키 웹사이트처럼 브라우징**하는 소비 경로가 처음 생겼다.
  기존 방법 A/B/C(요약 붙여넣기/AI_CONTEXT 자동주입/LLM 검색 tool)와는 별개의 **"방법 D: 사람용
  읽기 전용 웹 UI"**로 봐야 한다.
- `WIKI_DIR` 배선 자체는 2026-07-11에 이미 존재했지만([[2026-07-11-기억-요약-wiki-경로-확인]]) 그때는
  "어시스턴트가 조회 못 함"이 문제였다. 이번은 그 배선을 **사람이 보는 화면**에 처음 연결한 사례 —
  둘은 다른 소비자(LLM vs 사람)를 위한 별개 기능이라는 점을 향후 혼동하지 말 것.
- 1차 오해(sports-wiki vs john-wiki)는 "위키"라는 말이 이 생태계에 최소 두 개(스포츠 클립 위키, 이
  저장소) 존재해서 벌어졌다. `[[lampas-agent]]` 엔티티의 이름 충돌 경고와 같은 계열의 용어 충돌.

## 관련
- [[lampas-agent]] — Threads 기능 추가 후 전면 제거(v1.0.26→v1.0.29)
- [[lampas-harness]] — `apps/wiki`(wiki.html) 신설, 설정 > 위키 버튼
- [[john-wiki]] — 처음으로 사람용 브라우징 UI의 데이터 소스가 됨
- [[long-term-memory-architecture]] · [[wiki-memory-provider-integration]] — 조회(recall) 경로 비교
- [[full-stack-feature-removal-audit]] · [[self-hosted-agent-server-ops]]
