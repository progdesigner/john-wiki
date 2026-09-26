---
tags: [entity, app, lampas-studio, trends, llm-inference, atlas-cloud, social-media, x, threads]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-trends (`trends.lampas.io`) + lampas-trends-collector

`[[lampas-studio]]` 저장소(`lampas-system`)의 **트렌드 키워드 수집·분석 제품**. 세 부분으로 구성:
Python 수집기 `lampas-trends-collector`(주기 실행), `lampas-api`의 `trends` 모듈(실시간 검색·순수
로직), 프론트 `lampas-web-trends`. 저장소 `AGENTS.md` 스냅샷(2026-09-21부터 확인됨, →
[[lampas-studio]] "2026-09-26 저장소 구조 스냅샷" 절)에는 앱 이름만 있었다.

**시점 정정(2026-09-26 재확인)**: 이 위키에 상세가 처음 노출된 세션은
[[2026-09-19-lampas-trends-고도화]]로 기록돼 있었으나, 뒤늦게 ingest된
[[2026-09-15-lampas-web-trends-실시간검색-구현]] 세션이 나흘 앞서 같은 앱을 먼저 상세히 구현한
것으로 확인됨 — **실제 최초 상세 노출은 09-15**(실시간 검색 엔드포인트 최초 구현), 09-19는 그 위에
얹은 후속 고도화(토픽 재구축·제목 키워드 유추·X/Threads·배포)다.

## 실시간 검색 기원 (2026-09-15)
`GET /v1/trends/search?category=sports&q=MLB` 엔드포인트의 최초 구현. 분야를 고르고 세부 키워드를
검색하면 그 시점에 Google Trends 급상승 + 키워드 전용 Google 뉴스 검색 + 분야 상시 피드를 병렬
수집해 즉시 트렌드를 보여준다(검색 키워드가 1위 배지, 그 아래 세부 키워드). 공개·무상태·DB 없음 —
같은 분야+키워드는 **2분 메모리 캐시**, 동시 요청은 한 번만 수집, **IP당 분당 20회** 제한, 소스당
12초 타임아웃·3MB 제한(한 소스 실패해도 나머지로 결과). 프론트는 검색창이 폼이 되어 Enter/"지금
수집"으로 실행, 분야별 예시 칩(MLB·KBO·손흥민) 제공, 수집 중 스켈레톤+30초 타임아웃/429/네트워크
오류 메시지+"다시 수집" 버튼. 이 세션에서는 **구현·검증만 하고 배포하지 않음** →
[[2026-09-15-lampas-web-trends-실시간검색-구현]].
- **결합 지점(09-19 세션에서 재확인된 것과 동일 패턴)**: `trends-search.lib.ts`는 수집기
  `collector.py`의 사전·분류 정규식·점수식을 그대로 옮긴 것이라 **두 파일을 함께 고쳐야** 한다. 이
  세션에서 `마지막`을 양쪽 불용어 사전에 추가.

## 무엇을 하는가
"요즘 기사 쓸 때 인기 있는 키워드가 뭔지" 찾는 도구. 두 단계로 트렌드를 잡는다:
1. **원시 후보 수집** — 별칭·이름·인용 등 명시적 키워드를 분야별 상시 피드(Google 뉴스 RSS·Google
   Trends 급상승)에서 추출.
2. **제목 키워드 유추** — 기사 제목·인기 글은 핵심 키워드 3개로 쓰인다는 전제로, 제목마다 3개를
   유추해 원시 후보와 합쳐 재집계. 제목에 문자 그대로 없어도 그 기사가 근거로 연결되고, **2건 이상
   반복돼야** 순위에 오른다(1건 노이즈 배제).

## 토픽 단위 구조 (2026-09-19 재구축)
분야(스포츠·예능·뷰티·AI 등) 안에 **토픽**을 두고, 토픽마다 Google 뉴스 검색 RSS·X 인기글·Threads
인기글 소스가 자동 생성된다. 예: 스포츠 MLB·NBA / 예능 메이드인코리아·술래게임·연애전쟁·키드냅게임 /
뷰티 피부·보습·윤곽 / AI 에이전트·GPT·클로드·반도체. 분야 상시 피드는 제목에 토픽이 든 기사만, Google
Trends 급상승은 토픽이 든 것만 그 토픽에 합류. 순위·변동·추이는 토픽 안에서 매긴다. 토픽 목록은
수집기 `TOPICS` 한 곳에서 관리(추가·변경 시 여기만 수정).

## 제목 키워드 유추 엔진
- **Atlas 키가 있으면**: [[atlas-cloud]] 경유 `gemini-3.5-flash`에 제목을 배치로 묻는다(모델은
  텍스트 생성 전용 사용처 — Atlas Cloud가 이미지·영상·음악 외 텍스트 LLM 라우팅에도 쓰인다는
  근거, [[atlas-cloud]] 참고). 제목당 24시간 캐시해 한 번만 호출.
- **키가 없거나 실패하면**: 조사·어미 제거 규칙 기반 유추로 대체.
- 토픽 이름 조각("메이드"·"코리아" 등)과 범용어는 제외.
- 유추 규칙 사전(`INFER_STOP`·`TOPIC_CONTEXT`)이 수집기·API 양쪽에 **중복 존재** — 고칠 때 두 곳
  다 고쳐야 한다(결합 지점, 단일 소스화 안 됨).
- **배치 크기·타임아웃 함정** — 최초 배포 시 40개 배치로 `gemini-3.5-flash`를 호출했다가 배치당
  20~40초 걸려 타임아웃/응답 잘림으로 모델 유추가 전부 실패. 배치 20개·타임아웃 90초·압축 JSON·
  잘린 응답 복구·사이클당 120건 상한으로 조정, API는 요청 경로에서 모델을 기다리지 않고 **백그라운드로
  캐시를 데우는** 구조로 전환(첫 검색은 규칙 유추, 재검색부터 모델 결과) → 절차 스킬
  [[llm-batch-inference-timeout-tuning]].

## X · Threads 소스
공개 웹 검색은 로그인 없이 안 보여 공식 API로 연동:
- **X** — 최근 검색 결과에서 좋아요+리포스트 상위 20건. **Basic 이상 유료 티어** 토큰 필요.
- **Threads** — 키워드 검색 TOP 순서. `threads_keyword_search` 권한 토큰 필요.
- 둘 다 30분 주기 수집, 인기 글은 3일 창. 토큰이 없으면 소스가 "토큰 미설정"으로 표시되고 수집하지
  않는다(에러로 실패하지 않음, 조용한 옵트아웃).
- **운영 상태(2026-09-19 배포 시점)**: Threads 소스 13개가 오류 — 서버 `.env`의 Threads 토큰이
  9월 10일 만료됐고 만료 후 자동 갱신이 안 됨(재발급 필요). X 소스 13개는 토큰 자체가 없어 "토큰
  미설정" 상태.

## 웹 (`lampas-web-trends`)
분야 탭 아래 토픽 칩, 보드 필터에 "제목 키워드" 보기(유추로 뽑힌 건수 순 = 요즘 글에 자주 쓰이는
키워드), 출처 필터에 X·Threads, 카드·상세에 인기글·제목 키워드 건수와 글 근거 표시. 구 형식 스냅샷도
깨지지 않도록 하위 호환.

## API 실시간 검색
`GET /v1/trends/search`에도 같은 방식의 제목 키워드 유추를 넣었다. X·Threads는 실시간 검색에는
포함하지 않음(수집기 상시 피드에만 적용).

## 배포
- 배포 스크립트: `./scripts/deploy-api.sh lampas-trends-collector` → `./scripts/deploy-api.sh
  lampas-api` → `./scripts/deploy-web.sh lampas-web-trends`(수집기 → API → 웹 순서 고정).
- 2026-09-19 배포: collector 0.1.7·lampas-api 0.1.99(PM2 online, 재시작 0)·lampas-web-trends 새
  번들(trends.lampas.io 서빙). 운영 첫 사이클 기사 1,332건·토픽 13개·키워드 254개·모델 유추 120건.
- 배포 전 `tools/check-lampas-api-drift.sh`로 운영 DB 드리프트 확인 절차 재사용(→
  [[prod-ddl-before-deploy-with-drift-check]]) — 이번엔 DB 미접촉, 여분 컬럼 차이뿐이라 안전.
- **커밋 미완료**: 2026-09-19 세션 종료 시점까지 이 변경 전체가 커밋되지 않음(운영엔 반영됐지만
  코드는 미커밋) — 다음 커밋 세션에서 확인 필요. 진단 스크립트 `tools/diagnose-trends-collector.sh`·
  `tools/diagnose-trends-llm-ua.sh` 추가.

## 관련
- 엔티티: [[lampas-studio]] · [[atlas-cloud]](gemini-3.5-flash 텍스트 유추 경로) · [[lampas-web-flow]]
  ("트렌드 분석 Work" — 원본 영상 없이 키워드로 페르소나·카피를 실험하는 별개 기능, 이 앱과 목적은
  비슷하나 코드 경로는 다름)
- 스킬: [[llm-batch-inference-timeout-tuning]] · [[prod-ddl-before-deploy-with-drift-check]]
- 세션: [[2026-09-15-lampas-web-trends-실시간검색-구현]](최초 구현) · [[2026-09-19-lampas-trends-고도화]](후속 고도화)
