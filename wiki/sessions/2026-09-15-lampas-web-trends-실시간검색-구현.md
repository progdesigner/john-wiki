---
tags: [session, lampas-studio, trends, real-time-search, deploy]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-15 — lampas-web-trends 실시간 세부 키워드 검색 구현

`Tool: claude` 세션(작업 폴더 `lampas-system`, 2026-09-26 뒤늦게 ingest). `[[progdesigner]]`의
요청: "`lampas-web-trends`에서 분야별로 세부 키워드로 검색하면 실시간으로 수집해서 결과 볼 수 있게
해줘 — 예를 들어 스포츠 분야에서 MLB 검색하면 해당 키워드까지 포함해서 트렌드 제공." 1왕복으로
구현·검증까지 끝냈다(배포는 안 함).

이 세션이 `[[lampas-web-trends]]`의 `GET /v1/trends/search` 실시간 검색 엔드포인트의 **최초
구현**으로 확인됨 — 기존 엔티티 페이지는 [[2026-09-19-lampas-trends-고도화]] 세션을 "이 위키에
상세가 처음 노출된" 세션으로 기록했으나, 실제로는 이 09-15 세션이 나흘 앞서 같은 앱을 먼저
상세히 구현했다(뒤늦은 ingest 순서 때문에 09-19가 먼저 위키에 들어간 것). 09-19 세션은 이
실시간 검색 위에 토픽 재구축·제목 키워드 유추·X/Threads 소스를 얹은 후속 고도화다.

## 구현 내용

- **동작**: 분야를 고르고 검색창에 세부 키워드(예: `MLB`)를 넣고 Enter/"지금 수집"을 누르면, 서버가
  그 시점에 공개 RSS를 즉시 모아 해당 키워드 범위의 트렌드를 보여준다. 검색 키워드 자체가 1위
  (`검색 키워드` 배지)이고 그 아래로 세부 키워드가 이어진다. 실검증: `MLB(98점, 기사 52건)` → 다저스,
  김하성, KBO, 이정후, 류현진, 오타니 순, 약 1초 소요.
- **수집 소스**: Google Trends 급상승 + 키워드 전용 Google 뉴스 검색 + 분야 상시 피드(Google 뉴스·
  연합·조선)를 병렬 수집, 제목에 키워드가 든 기사만 남겨 세부 키워드를 뽑고 관심 지수로 순위.
- **분야 전환 시** 같은 키워드로 새 분야에서 재수집, "전체 보드"로 5분 주기 보드에 복귀. 실시간
  결과는 순위 변동·추이 그래프가 없다는 안내를 붙임. 저장 키워드 id는 상시 수집기와 같은 해시
  규칙이라 보드와 호환.

### 백엔드 (`lampas-api`, 신규 `trends` 모듈)
- `GET /v1/trends/search?category=sports&q=MLB` — 공개·무상태·DB 없음.
- 같은 분야+키워드는 **2분 메모리 캐시**, 동시 요청은 한 번만 수집(중복 억제), **IP당 분당 20회**
  제한.
- 소스당 12초 타임아웃·3MB 제한, 한 소스가 실패해도 나머지로 결과를 낸다.
- `trends-search.lib.ts`는 수집기 `collector.py`의 사전·분류 정규식·점수식을 그대로 옮긴 것이라 **두
  파일을 함께 고쳐야** 한다(09-19 세션에서 발견된 `INFER_STOP`/`TOPIC_CONTEXT` 중복 결합 지점과
  같은 패턴). 이번에 `마지막`을 양쪽 불용어 사전에 추가.

### 프론트 (`lampas-web-trends`)
- 검색창이 폼으로 바뀌어 Enter 또는 "지금 수집" 버튼으로 실행, 분야별 예시 칩(MLB·KBO·손흥민 등)
  클릭도 가능.
- 수집 중 스켈레톤, 30초 타임아웃·429·네트워크 오류 메시지 + "다시 수집" 버튼, 결과 상단에 수집
  시각·기사 수·소스 상태·캐시 여부 한 줄 표시.
- `VITE_API_URL`을 env 두 파일에 추가. vitest 도입해 `src/lib/liveSearch.ts` 순수 로직 테스트.

## 검증

| 대상 | 결과 |
|---|---|
| `lampas-api` `src/modules/trends` jest | 19 passed |
| `lampas-web-trends` vitest + 수집기 python | 6 + 13 passed |
| api `tsc --noEmit`, web `tsc` + `vite build` | 통과 |

## 남은 일 (세션 종료 시점 미해결)
- 로컬 API 데몬(3133)은 구 코드가 떠 있어 로컬 확인엔 `pnpm dev:lampas:api` 또는 빌드 후
  `launchctl kickstart -k` 필요.
- 운영 반영 순서: `./scripts/deploy-api.sh lampas-api` → `./scripts/deploy-web.sh
  lampas-web-trends`. **이 세션에서는 배포하지 않음.**
- `pnpm install`로 `pnpm-lock.yaml`에 누락돼 있던 다른 앱 항목들도 함께 채워짐 — 커밋 시 참고
  필요.

## 관련
- 엔티티: [[lampas-web-trends]](이 세션 내용으로 갱신 — 실시간 검색 기원 절 추가, "첫 상세 노출"
  시점 09-19→09-15로 정정) · [[lampas-studio]]
- 세션: [[2026-09-19-lampas-trends-고도화]](같은 앱의 후속 고도화 — 토픽 재구축·제목 키워드 유추·
  X/Threads·배포)
- 원본: `raw/conversations/2026-09-15-lampas-web-trends-실시간검색-구현.md` (source:
  `7da906f9-9831-421a-bb43-5229b0ad3942.md`)
