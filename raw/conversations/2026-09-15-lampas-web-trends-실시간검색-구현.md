# 트랜드 수집 방향 개성

Tool: claude
Started: 2026-09-15T12:33:44.338Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.claude/projects/-Users-progdesigner-Works-lampas-lampas-system/7da906f9-9831-421a-bb43-5229b0ad3942.jsonl

## user

lampas-web-trends 에서 분야별로 세부 키워드로 검색하면 실시간으로 수집해서 결과 볼 수 있게 해줘 예를 들어 스포츠 분야에서 MLB 검색하면 해당 키워드까지 포함해서 트랜드 제공

## assistant

lampas-web-trends 앱 구조와 트렌드 데이터 흐름(API·수집 방식)을 먼저 파악하겠습니다.

## assistant

구현이 끝났습니다. 분야를 고른 뒤 검색창에 세부 키워드를 넣고 **지금 수집**(Enter)을 누르면, 서버가 그 시점에 공개 RSS를 바로 모아 그 키워드 범위의 트렌드를 보여줍니다.

**동작 방식**

- 스포츠에서 `MLB`를 검색하면 Google Trends 급상승 + `MLB` 전용 Google 뉴스 검색 + 스포츠 상시 피드(Google 뉴스·연합·조선)를 병렬 수집합니다. 제목에 키워드가 든 기사만 남기고, 거기서 세부 키워드를 찾아 관심 지수로 순위를 매깁니다.
- 검색 키워드 자체가 1위(`검색 키워드` 배지)이고, 그 아래로 세부 키워드가 이어집니다. 실제 피드로 확인한 결과는 `MLB(98점, 기사 52건)` → 다저스, 김하성, KBO, 이정후, 류현진, 오타니 순이었고 약 1초 걸렸습니다.
- 분야를 바꾸면 같은 키워드로 새 분야에서 다시 수집하고, "전체 보드"로 5분 주기 보드에 돌아옵니다. 실시간 결과는 순위 변동·추이 그래프가 없다는 안내를 붙였습니다.
- 저장 키워드 id는 수집기와 같은 해시 규칙이라 보드와 호환됩니다.

**백엔드 (lampas-api, 새 `trends` 모듈)**

- `GET /v1/trends/search?category=sports&q=MLB` 공개·무상태·DB 없음. 같은 분야+키워드는 2분 메모리 캐시, 동시 요청은 한 번만 수집, IP당 분당 20회 제한.
- 소스당 12초 타임아웃·3MB 제한, 한 소스가 실패해도 나머지로 결과를 냅니다.
- `trends-search.lib.ts`는 수집기 `collector.py`의 사전·분류 정규식·점수식을 그대로 옮긴 것이라 두 파일을 함께 고쳐야 합니다. 이번에 `마지막`을 양쪽 불용어에 추가했습니다.

**프론트 (lampas-web-trends)**

- 검색창이 폼이 되어 Enter 또는 "지금 수집" 버튼으로 실행되고, 분야별 예시 칩(MLB·KBO·손흥민 등)을 눌러도 됩니다.
- 수집 중 스켈레톤 안내, 30초 타임아웃·429·네트워크 오류 메시지와 "다시 수집" 버튼, 결과 상단에 수집 시각·기사 수·소스 상태·캐시 여부 한 줄.
- `VITE_API_URL`을 env 두 파일에 추가했습니다. vitest를 도입해 `src/lib/liveSearch.ts` 순수 로직을 테스트합니다.

**테스트 결과**

| 대상 | 결과 |
|---|---|
| lampas-api `src/modules/trends` jest | 19 passed |
| lampas-web-trends vitest + 수집기 python | 6 + 13 passed |
| api `tsc --noEmit`, web `tsc` + `vite build` | 통과 |

**다음 단계**

- 로컬 API 데몬(3133)은 구 코드가 떠 있으므로 로컬 확인은 `pnpm dev:lampas:api` 또는 빌드 후 `launchctl kickstart -k`가 필요합니다.
- 운영 반영은 `./scripts/deploy-api.sh lampas-api` 후 `./scripts/deploy-web.sh lampas-web-trends` 순서입니다. 배포는 하지 않았습니다.
- `pnpm install`로 pnpm-lock.yaml에 누락돼 있던 다른 앱 항목들도 함께 채워졌습니다. 커밋 시 참고하세요.
