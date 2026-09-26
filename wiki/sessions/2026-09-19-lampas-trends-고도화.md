---
tags: [session, lampas-studio, trends, llm-inference, atlas-cloud, jev, deploy]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-19 — Lampas Trends 고도화 (제목 키워드 유추 + 토픽 단위 재구축, 배포까지)

`Tool: claude` 세션(작업 폴더 `lampas-system`, 2026-09-26 뒤늦게 ingest). `[[progdesigner]]`가
"기사 제목이 핵심 키워드 3개로 쓰인다"는 전제로 트렌드 수집기를 다시 만들어달라고 요청 → 토픽 단위
재구축·제목 키워드 유추·X/Threads 소스 추가를 한 세션에서 구현·검증하고, 두 번째 왕복("배포해줘")에서
운영까지 반영했다. `[[lampas-web-trends]]` · `lampas-trends-collector`가 이 위키에 처음 상세히
노출된 세션 — 기존엔 `[[lampas-studio]]` `AGENTS.md` 스냅샷의 앱 목록에 이름만 있었다.

## 요청 배경
사용자 요청 원문: "트랜드 키워드 수집 시 기사 제목이 3가지 키워드로 작성된다고 보고 그 제목을 바탕으로
키워드 3가지를 유추하고 그 유추된 키워드들까지 포함해서 트랜드를 다시 찾아줘." — 목적은 "요즘 기사
쓸 때 인기 있는 키워드가 뭔지" 찾는 것.

## 구현 내용 (1턴, 커밋·배포 없이 종료)

- **토픽 단위 수집으로 재구조화**: 기존엔 분야(스포츠·예능·뷰티·AI 등) 단위 상시 피드였는데, 분야
  안에 **토픽**(스포츠 MLB·NBA / 예능 메이드인코리아·술래게임·연애전쟁·키드냅게임 / 뷰티 피부·보습·
  윤곽 / AI 에이전트·GPT·클로드·반도체)을 두고 토픽마다 Google 뉴스 검색 RSS·X 인기글·Threads
  인기글 소스를 자동 생성. 분야 상시 피드는 제목에 토픽이 든 기사만, Google Trends 급상승은 토픽이
  든 것만 그 토픽에 합류. 순위·변동·추이는 토픽 안에서 매김. 토픽 목록은 수집기 `TOPICS` 한 곳에서
  관리.
- **제목·글마다 키워드 3개 유추**: 제목/인기글이 핵심 키워드 3개로 쓰인다는 전제로, 제목마다 3개를
  뽑아 기존 후보(별칭·이름·인용)와 합쳐 재집계. 제목에 문자 그대로 없어도 그 기사가 근거로 연결되고,
  2건 이상 반복돼야 순위에 오름(1건짜리 노이즈 배제). **Atlas 키가 있으면** `gemini-3.5-flash`에
  40개씩 배치로 묻고 제목당 24시간 캐시(1회 호출), **키가 없거나 실패하면** 조사·어미 제거 규칙으로
  대체. 토픽 이름 조각("메이드"·"코리아")과 범용어는 제외.
- **X·Threads 소스 추가**: 공개 웹 검색이 로그인 없이 안 보여 공식 API로 연동. X는 최근 검색
  좋아요+리포스트 상위 20건, Threads는 키워드 검색 TOP 순서. 30분 주기, 3일 창. 토큰 없으면 "토큰
  미설정"으로 표시하고 수집하지 않음.
- **웹**: 분야 탭 아래 토픽 칩, 보드 필터에 "제목 키워드" 보기(유추 건수 순), 출처 필터에 X·Threads,
  카드·상세에 인기글·제목 키워드 건수·근거 표시. 구 스냅샷 포맷도 하위 호환.
- **API 실시간 검색**(`GET /v1/trends/search`)에도 같은 방식의 제목 키워드 유추 적용(X·Threads는
  실시간 검색엔 미포함).
- **검증**: 수집기 python 테스트 24개, `lampas-api` jest(trends) 23개+tsc, `lampas-web-trends`
  vitest+tsc+vite build 13개 전부 통과. 실제 RSS 1회 실행 — 기사 775건, 토픽 13개, 키워드 258개
  (예: 윤곽→피부·시술·얼굴, 반도체→AI 반도체·SK하이닉스로 정리). 로컬은 Atlas 키·소셜 토큰이 없어
  규칙 유추·소셜 소스 off 상태로 검증됨(운영은 `lampas-api`의 Atlas 키를 공유해 모델 유추가 켜짐).
- 유추 규칙 사전(`INFER_STOP`·`TOPIC_CONTEXT`)이 수집기·API 양쪽에 중복 존재 — 고칠 때 함께 고쳐야
  하는 결합 지점으로 명시됨.

## 배포 (2턴, "배포해줘")

배포 전 `tools/check-lampas-api-drift.sh`로 운영 DB 스키마 드리프트 확인(→ [[prod-ddl-before-deploy-with-drift-check]]
같은 계열 절차 재사용) — 차이는 운영에만 있는 다른 작업본 여분 컬럼(clips·reel_exports workspaceId
등)뿐, 크래시 위험 없음, DB는 건드리지 않음. 배포 순서: 수집기 → API → 웹.

```
./scripts/deploy-api.sh lampas-trends-collector
./scripts/deploy-api.sh lampas-api
./scripts/deploy-web.sh lampas-web-trends
```

**배포 중 발견·수정한 버그**: 첫 배포 후 운영 로그에서 모델 유추가 전부 실패. 원인은 `gemini-3.5-flash`가
배치당 20~40초 걸려 **40개 배치가 타임아웃되거나 응답이 잘림**. 조정: 배치 20개·타임아웃 90초·압축
JSON·잘린 응답 복구 로직·사이클당 120건 상한. 추가로 **API는 요청 경로에서 모델을 기다리지 않고
백그라운드로 캐시를 데우도록 변경** — 첫 검색은 규칙 유추, 재검색부터 모델 결과가 붙는 구조로 전환.
→ 절차 스킬 [[llm-batch-inference-timeout-tuning]] 신설 계기.

**배포 결과** (재배포 반영, 재시작 0):

| 대상 | 버전 | 상태 |
| --- | --- | --- |
| lampas-trends-collector | 0.1.7 | PM2 online |
| lampas-api | 0.1.99 | PM2 online |
| lampas-web-trends | 새 번들 | trends.lampas.io 서빙 중 |

**운영 확인**: 수집기 첫 사이클 기사 1,332건·토픽 13개·키워드 254개·모델 유추 120건·실패 없음. API
실시간 검색은 1초 내 응답, 백그라운드 유추가 캐시를 데운 뒤 세 번째 검색에서 모델 키워드 76건 확인
(재검색 필요 구조가 실제로 동작함을 실증).

## 남은 일 (사용자 조치 필요, 세션 종료 시점 미해결)
- **Threads 소스 13개 오류** — 서버 `lampas-api` `.env`의 Threads 토큰이 9월 10일 만료, 만료 후
  자동 갱신 안 됨 → 재발급 필요.
- **X 소스 13개 "토큰 미설정"** — X 최근 검색은 Basic 이상 **유료 티어** 토큰 필요, 미설정 상태.
- 변경 사항은 **커밋하지 않음**(운영엔 반영됐지만 코드는 미커밋 상태로 남음 — 다음 세션에서 커밋
  범위 확인 필요). 진단용 스크립트 `tools/diagnose-trends-collector.sh`·`tools/diagnose-trends-llm-ua.sh`
  신규 추가.

## 관련
- 엔티티: [[lampas-web-trends]](신설) · [[lampas-studio]] · [[atlas-cloud]](gemini-3.5-flash 텍스트
  유추 경로)
- 스킬: [[llm-batch-inference-timeout-tuning]](신설) · [[prod-ddl-before-deploy-with-drift-check]](재사용)
- 원본: `raw/conversations/2026-09-19-lampas-trends-고도화.md` (source: `77b4d127-b037-49f5-a220-b3ba095e3eb7.md`)
