# Lampas Trends 고도화

Tool: claude
Started: 2026-09-19T02:53:11.569Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.claude/projects/-Users-progdesigner-Works-lampas-lampas-system/77b4d127-b037-49f5-a220-b3ba095e3eb7.jsonl

## user

트랜드 키워드 수집 시 기사 제목이 3가지 키워드로 작성된다고 보고 그 제목을 바탕으로 키워드 3가지를 유추하고 그 유추된 키워드들까지 포함해서 트랜드를 다시 찾아줘. 그래서 요즘 기사 쓸때 인기있는 키워드 들이 뭔지를 찾아내는 거지

## assistant

트렌드 수집 코드(`lampas-trends-collector`·`lampas-api/trends`)를 먼저 살펴보고, 기사 제목→키워드 3개 추론→재집계 단계를 설계하겠습니다.

## assistant

다음으로 필요한 것: (1) API 모듈이 AtlasCloudService 를 직접 쓰는 방식, (2) 그 뒤 수집기·API·웹 코드 작성. 먼저 (1)만 확인합니다.

## assistant

이제 코드를 작성합니다. 순서: 수집기(Python) → 워커 env 전달 → API 순수 로직·서비스 → 웹 표시 → 문서.

## assistant

Now the collector: inference functions, aggregate linkage, collect wiring.

## assistant

수집기 테스트 18개 통과. 다음은 API 순수 로직(규칙 유추·유추 연결)과 서비스(Atlas LLM 유추 + 제목 캐시)입니다.

## assistant

수집기 테스트 23개 통과. 다음 병렬 작업: 워커에 소셜 토큰 전달·로그 추가, API jest 실행으로 TS 변경 확인.

## assistant

확인할 것: 실패한 API 스펙 원인 디버그. 그와 별개로 웹 코드 작성에 필요한 정보는 모두 확보했으니 디버그만 먼저 합니다.

## assistant

웹 작업에 필요한 정보는 모두 확보했습니다. 타입·순수 로직(lib+테스트)·컴포넌트·CSS 를 한 번에 작성합니다.

## assistant

이제 대시보드(토픽 칩·보기 모드·출처 필터)와 CSS 를 바꾸고 웹 빌드(tsc)로 검증합니다.

## assistant

트렌드 수집을 요청하신 대로 바꿨습니다. 커밋과 배포는 하지 않았습니다.

**무엇이 바뀌었나**

- **토픽 단위 수집.** 분야 안의 토픽(스포츠 MLB·NBA / 예능 메이드인코리아·술래게임·연애전쟁·키드냅게임 / 뷰티 피부·보습·윤곽 / AI 에이전트·GPT·클로드·반도체)마다 Google 뉴스 검색 RSS, X 인기 글, Threads 인기 글 소스가 자동 생성됩니다. 기존 분야 상시 피드는 제목에 토픽이 든 기사만, Google Trends 는 토픽이 든 급상승만 그 토픽에 합쳐집니다. 순위·변동·추이는 토픽 안에서 매깁니다. 토픽 목록은 수집기 `TOPICS` 한 곳에서 관리합니다.
- **제목·글마다 키워드 3개 유추.** 기사 제목과 인기 글은 핵심 키워드 3개로 쓰인다는 전제로 제목마다 3개를 뽑아 기존 후보(별칭·이름·인용)와 합쳐 재집계합니다. 제목에 글자 그대로 없어도 그 기사가 근거로 연결되고, 2건 이상 반복돼야 순위에 오릅니다. Atlas 키가 있으면 모델(기본 gemini-3.5-flash)에 40개씩 배치로 묻고 제목당 24시간 캐시해 한 번만 호출하며, 키가 없거나 실패하면 조사·어미 제거 규칙으로 뽑습니다. 토픽 이름 조각("메이드"·"코리아")과 범용어는 제외합니다.
- **X·Threads.** 공개 웹 검색은 로그인 없이 볼 수 없어 공식 API 로 붙였습니다. X 는 최근 검색 결과에서 좋아요+리포스트 상위 20건, Threads 는 키워드 검색 TOP 순서를 인기 순위로 씁니다. 30분마다 묻고, 인기 글은 3일 창을 씁니다. 토큰이 없으면 소스가 "토큰 미설정" 으로 표시되고 수집하지 않습니다.
- **웹.** 분야 탭 아래 토픽 칩, 보드 필터에 "제목 키워드" 보기(유추로 뽑힌 건수 순 = 요즘 글에 자주 쓰이는 키워드), 출처 필터에 X·Threads, 카드·상세에 인기 글·제목 키워드 건수와 글 근거 표시. 구 형식 스냅샷도 깨지지 않습니다.
- **API 실시간 검색**(`GET /v1/trends/search`)에도 제목 키워드 유추를 같은 방식으로 넣었습니다. X·Threads 는 실시간 검색에는 넣지 않았습니다.

**검증**

| 대상 | 결과 |
| --- | --- |
| 수집기 python 테스트 | 24개 통과 |
| lampas-api jest (trends) | 23개 통과, tsc 통과 |
| lampas-web-trends vitest + tsc + vite build | 13개 통과, 빌드 성공 |
| 실제 RSS 로 수집기 1회 실행 | 기사 775건, 토픽 13개, 키워드 258개. 윤곽→피부·시술·얼굴, 반도체→AI 반도체·SK하이닉스 로 정리됨 |

로컬 실행은 Atlas 키·소셜 토큰이 없어 규칙 유추와 소셜 소스 off 상태였습니다. 운영 서버는 lampas-api 의 Atlas 키를 공유하므로 배포하면 모델 유추가 켜집니다.

**운영에서 필요한 일**

- X·Threads 토큰을 서버 PM2 환경변수 또는 lampas-api `.env` 에 넣어야 소셜 소스가 켜집니다. X 최근 검색은 Basic 이상 유료 티어, Threads 는 `threads_keyword_search` 권한 토큰이 필요합니다.
- 배포 순서는 수집기 → API → 웹입니다.

```
./scripts/deploy-api.sh lampas-trends-collector
./scripts/deploy-api.sh lampas-api
./scripts/deploy-web.sh lampas-web-trends
```

- 유추 규칙 사전(`INFER_STOP`·`TOPIC_CONTEXT`)은 수집기와 API 양쪽에 같이 있으니 고칠 때 함께 고쳐야 합니다.

## user

배포해줘

## assistant

배포 전에 lampas-api 스키마 드리프트 메모리와 lampas-api 작업 트리 상태를 확인합니다(수집기 → API → 웹 순서).

## assistant

`tools/check-lampas-api-drift.sh` 가 있습니다. 내용을 읽고 실행해 운영 DB 와 현재 스키마의 차이를 확인합니다. 수집기 배포 로그도 같이 봅니다.

## assistant

수집기 배포 완료(v0.1.6 online). 이제 (1) 운영 DB 드리프트 확인, (2) 수집기 첫 사이클 로그 확인을 동시에 합니다.

## assistant

테스트·타입체크 통과. README 의 배치 수치를 맞춘 뒤 수집기·API 를 동시에 재배포합니다.

## assistant

배포가 도는 동안 이미 끝난 웹 배포와 S3 스냅샷(첫 수집기 배포 결과)을 확인합니다.

## assistant

수집기 재배포 완료. 새 버전의 첫 사이클(LLM 배치 포함, 약 2분)을 기다렸다가 로그를 확인합니다.

## assistant

모두 배포·확인됐습니다. 마지막으로 API 의 백그라운드 유추가 운영에서 캐시를 데웠는지 다른 검색어로 한 번 더 확인합니다.

## assistant

배포가 모두 끝났고 운영에서 동작을 확인했습니다.

**배포 결과**

| 대상 | 버전 | 상태 |
| --- | --- | --- |
| lampas-trends-collector | 0.1.7 | PM2 online, 재시작 0 |
| lampas-api | 0.1.99 | PM2 online, 재시작 0 |
| lampas-web-trends | 새 번들 | trends.lampas.io 서빙 중 |

**운영 확인**

- 수집기 첫 사이클: 기사 1,332건, 토픽 13개, 키워드 254개, 모델 유추 120건, 실패 없음. S3 스냅샷에 토픽·유추 메타가 실려 있고 웹이 이를 읽습니다.
- API 실시간 검색은 1초 안에 응답하고, 백그라운드 유추가 캐시를 데운 뒤에는 모델 키워드가 반영됩니다(세 번째 검색에서 모델 76건 확인).
- API 배포 전 운영 DB 드리프트를 확인했습니다. 차이는 운영에만 있는 여분 컬럼(다른 작업본의 clips·reel_exports workspaceId 등)뿐이라 크래시 위험이 없었고, DB 는 건드리지 않았습니다.

**배포 중 고치고 재배포한 것**

첫 배포 후 운영 로그에서 모델 유추가 전부 실패했습니다. 원인은 gemini-3.5-flash 가 배치당 20~40초 걸려 40개 배치가 타임아웃되거나 출력이 잘린 것이었습니다. 배치 20개·타임아웃 90초·압축 JSON·잘린 응답 복구·사이클당 120건으로 조정했고, API 는 요청 경로에서 모델을 기다리지 않고 백그라운드로 데우도록 바꿨습니다. 첫 검색은 규칙 유추, 재검색부터 모델 결과가 붙습니다.

**남은 일 (사용자 조치 필요)**

- Threads 소스 13개가 오류입니다. 서버 lampas-api `.env` 의 Threads 토큰이 9월 10일에 만료됐고 만료 후에는 갱신이 안 되어 재발급이 필요합니다.
- X 소스 13개는 토큰이 없어 "토큰 미설정" 상태입니다. X 최근 검색은 Basic 이상 유료 티어 토큰이 필요합니다.
- 변경 사항은 커밋하지 않았습니다. 진단용으로 `tools/diagnose-trends-collector.sh`, `tools/diagnose-trends-llm-ua.sh` 두 스크립트를 추가했습니다.
