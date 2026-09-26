---
tags: [topic, ai-pattern, classification, cost-optimization, lampas, dalar]
created: 2026-09-26
updated: 2026-09-26
---
# Jev — typed 질문 기반 저비용 AI 분류

`[[lampas]]` 생태계 내부에서 "Jev"라고 부르는 패턴/게이트: 자유 텍스트 생성 대신 **typed(구조화)
질문**을 AI 엔드포인트(`POST /v1/ai/systemone`, 상류는 "TypeSafe"로 불림)에 던져 분류·판정만 받고
텍스트 생성은 하지 않는다. 텍스트 생성이 없으므로 **입력 토큰만 과금**돼 건당 비용이 거의 0에 수렴한다.

## 확인된 사용처
1. **`lampas-api`의 `sports-wiki` ingest 게이트** — 관련도·페이지별 "새 정보 있음"·새 인물 등장을 noul
   질문으로 물어 새 정보 없는 소스는 16k 토큰 재작성 호출 없이 skip(`ingest-skip` 로그). **2026-09-26
   ingest로 구현 시점이 정정됨**: 기존엔 "2026-09-25 스포츠 경기 엔티티 설계 세션에서 이름만 언급,
   구체적 형태 미문서화"로 기록돼 있었으나, 실제 구현(`lib/sports-wiki-gate.ts`)은 그보다 **5일 앞선
   2026-09-20** `[[2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현]]` 세션(`Tool:
   claude`)에서 코드·테스트까지 끝났다. 그 세션 종료 시점엔 `TYPESAFE_API_KEY` 부재로 배포가
   보류됐지만, 5일 뒤 2026-09-25 스포츠위키 세션(`raw/conversations/2026-09-25-위키개선-스포츠경기엔티티-설계.md:47-48`)이
   "Jev 게이트가 하이라이트 장면 묘사를 새 정보 없음으로 skip할 수 있고, System One 15초 타임아웃도
   찍혔다"고 **실제 프로덕션 동작**을 서술하므로, 그 사이 어느 시점(가장 유력한 후보는 같은 날 뒤이은
   [[2026-09-20-lampas-flow-만들기]] 세션에서 사용자가 `TYPESAFE_API_KEY`를 대화 중 제공한 시점)에
   배포가 풀린 것으로 보인다 — 다만 세 세션 소스만으론 정확한 배포 시점·커밋은 확정 불가.
   → [[2026-09-25-스포츠위키-경기엔티티-설계구현]].
2. **`lampas-agent`의 Fixs 오류 triage** (2026-09-25 업그레이드에서 구체적 형태가 처음 드러남) — 접수된
   오류마다 typed 질문 4개(유형 10종·수정 위치 5종·심각도 4단계·해결 가능 확률)를 한 번에 질의.
   건당 0.3~0.6초, 비용 42크레딧/M 토큰 수준. 5초 간격 데몬이 미분류 1건씩 처리(시도 2회 상한), 실패 시
   상세 화면에서 수동 재실행 가능 → [[2026-09-25-fixs-업그레이드-경로묶음-jev분류]].
3. **`[[dalar-web-first]]`("First" 돌잔치 영상 서비스)의 제작 워커 오류 판정** (시점상 2026-09-20으로
   앞의 두 사용처보다 이르지만, 이 위키엔 2026-09-26 뒤늦게 ingest) — 영상 생성 파이프라인에서
   오류가 나면 Jev가 "재시도·대기·관리자 확인" 중 하나를 판단. Jev 연결 자체가 실패하면 서버 기본
   복구 규칙으로 대체하고, 외부 생성 요청이 실제 접수됐는지 불명확해 **중복 제작비 발생 위험이
   있는 경우엔 자동 판단을 포기하고 관리자 확인으로 넘기는** 보수적 에스컬레이션 규칙이 함께
   확인됨 → [[dalar-web-first]] · [[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]].
   sports-wiki·Fixs 사례와 달리 이 세션 소스엔 typed 질문의 구체적 스키마(질문 개수·선택지)가
   드러나지 않음 — 판단 카테고리(재시도/대기/관리자 확인)만 확인됨.
4. **[[lampas-web-flow]]("Flow") 노드 자동화의 선택·평가**(2026-09-20 — 세 사례보다 시점상 가장
   이르다) — Flow가 노드마다 대표 작업을 자동 수행할 때, Jev가 **클립 선별·페르소나 추천·구성/템플릿
   선택**을 담당하고 실제 카피 생성·영상 편집 실행은 각 서비스의 기존 기능이 수행. 실 호출은
   `TYPESAFE_API_KEY`(운영/로컬 env에 부재)를 사용자가 직접 제공해 배포 전용 설정에 반영한 뒤 검증됨
   (테스트 장면 페르소나 선택 **약 0.5초**) — 다른 세 사례와 달리 상류 엔드포인트가 명시적으로
   "TypeSafe"라 확인됨(이 토픽 제목의 근거). → [[2026-09-20-lampas-flow-만들기]] "Jev 노드 자동화" 절 ·
   [[lampas-web-flow]]
5. **`dalar-api`/`dalar-web-app`의 채팅 의도 분류·플로우 턴 판별** (2026-09-20,
   `[[2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현]]` 세션, `Tool: claude`) —
   `POST /v1/orchestration/classify-intent`가 의도 `choice`(10종)+취소 `noul`을 한 호출로 판정,
   `dalar-web-app`의 기존 16종 한국어 정규식 분류기(`classifyIntent.ts`)와 confidence 게이팅(≥0.6
   채택, 취소 ≥0.8)으로 병합(`director/mergeIntent.ts`). 코드·테스트(lampas-api 1003·dalar-api 19
   스위트·dalar-web-app 469 테스트)까지 완료됐으나, **이 위키 소스로는 실 운영 배포·벤치 스크립트
   (`bench-intent-jev.ts`) 실행 여부가 확인되지 않는다** — 위 #1(sports-wiki 게이트)과 달리 후속
   세션에서 이 기능을 언급한 소스가 없음. `#1`과 같은 세션에서 함께 구현됨.
   - **혼동 주의**: 아래 "미채택 후보" #2("채팅 플로우 progress/clarify/cancel 분류",
     `lampas-api`의 `orchestration.service.ts:487` `analyzeFlowTurn`)와 이름은 비슷하지만 **다른
     세션·다른 저장소 대상**이다 — 그쪽은 `lampas-api` 자체의 오케스트레이션 모듈을 추천만 하고
     구현하지 않은 것이고, 이 항목은 `dalar-api`/`dalar-web-app`의 채팅 플로우를 실제로 구현한
     것이다. 서로 다른 두 2026-09-20 세션이 유사한 이름의 기능을 다뤄 헷갈릴 수 있어 명시해둔다.

## 미채택 후보 5개 (2026-09-20 자문 세션, 코드 수정 없음)
위 네 사용처와 별개로, `[[lampas]]`가 `[[lampas-studio]]`(`lampas-system`) 전체 기능을 훑어 Jev를
추가 적용할 만한 곳을 추천만 하고 **작업은 하지 않은** 세션이 있다. 우선순위·별점:

1. **레퍼런스 탐색 결과 재정렬** (`reference-explore.service.ts:217`) — ★★★★☆ 우선 실험 권장
2. **채팅 플로우 progress/clarify/cancel 분류** (`orchestration.service.ts:487` `analyzeFlowTurn`) — ★★★☆☆
3. **트렌드 기사·키워드 관련도 판정** (`trends-search.lib.ts`, `trends.service.ts:160`) — ★★★☆☆
4. **광고 카피 후보 기준별 평가**(Composite Scoring) (`product-insights.service.ts:189`) — ★★☆☆☆
5. **제품 리서치 외부 근거 추가**(jev-search 패턴 응용) (`product-insights.service.ts:139`) — ★★☆☆☆

최종 결론은 **전면 도입 보류** — 레퍼런스 재정렬만 필요시 소규모 비교 실험. 텍스트 입력만 지원(이미지
분위기 평가 불가), 가격은 입력 100만 토큰당 $0.042(출력 무료), 한국어 등 CJK는 자체 검증 필요(영어가
가장 정확). → [[2026-09-20-jev-활용처-추천-lampas-system]]

## 관찰된 함정
- 운영 API가 순간적으로 nginx HTML 404를 반환하는 경우가 있어, 이런 비정상 응답은 **재시도 대상**으로
  분류해 처리해야 한다(Fixs 세션에서 세 건 관찰·수정).
- 상류(TypeSafe)가 15초 타임아웃으로 502를 낼 수 있다 — `lampas-api` 자체 리포터가 이 502를 다시
  Fixs에 오류로 접수하는 자기 참조적 케이스가 실제로 발생함.
- 파트너 키 계정별로 과금이 집계되므로, 새 소비처를 붙일 때 전용 키 없이 다른 앱(`talk-api` 등)의 키를
  임시 차용하면 사용량이 섞인다 — Fixs 세션이 이 상태로 임시 배포됨(후속 과제로 남음).

## 관련
- 사용하는 앱: [[lampas-agent]] (Fixs, sports-wiki ingest 게이트) · [[dalar-web-first]] (제작 워커
  오류 재시도/대기/관리자 확인 판단) · [[lampas-web-flow]] (노드 자동화 클립·페르소나·템플릿 선택) ·
  [[dalar]] (`dalar-api`/`dalar-web-app` 채팅 의도 분류, 배포 미확인)
- [[lampas-system-ai-call-architecture-audit]] — Jev 도입 근거였던 전체 AI 호출 아키텍처 감사(7종
  JSON 파서 중복·앱 간 파일 포크 등, Jev와 무관하게도 유효한 기술부채 지도)
- [[deterministic-extraction-vs-llm-rewrite]] — Jev와 마찬가지로 "LLM은 판정/짧은 요약만, 저장은
  결정적 코드"라는 같은 계열의 비용·신뢰성 절충 패턴
- 미채택 후보(자문만, 미구현): [[lampas-studio]] 레퍼런스 탐색·채팅 플로우·트렌드·광고 카피·제품
  리서치 → [[2026-09-20-jev-활용처-추천-lampas-system]]
- 세션: [[2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현]] ·
  [[2026-09-20-lampas-flow-만들기]] · [[2026-09-25-fixs-업그레이드-경로묶음-jev분류]] ·
  [[2026-09-25-스포츠위키-경기엔티티-설계구현]] · [[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]] ·
  [[2026-09-20-jev-활용처-추천-lampas-system]]
