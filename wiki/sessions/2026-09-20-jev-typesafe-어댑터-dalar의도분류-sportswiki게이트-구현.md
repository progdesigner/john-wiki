---
tags: [session, lampas-studio, dalar, jev, typesafe, ai-architecture, deploy-blocked]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-20 — AI 호출 패턴 전체 조사 + Jev(TypeSafe) 어댑터 도입 + Dalar 의도분류·sports-wiki 게이트 구현

`Tool: claude`, 작업 폴더 `lampas-system`(`[[lampas-studio]]`), 세션 `2a32a383`, **11:00:08Z 시작**.
소스 제목은 "Lampas Agent 개선"이지만 실제 대상은 `lampas-agent`(맥미니 클립 데몬)가 아니라
`lampas-system` 저장소 전체다 — [[2026-09-20-jev-활용처-추천-lampas-system]] 세션과 같은 패턴의
제목 오인.

## 같은 날 세 개의 독립 세션 — 시간순
같은 사용자 프롬프트("Jev 활용처 찾아서 lampas-system 개선 추천")가 **거의 동시에 두 가지 도구로
실행**됐고, 세 번째 세션이 이어졌다. 이 페이지가 다루는 것은 첫 번째다.

1. **11:00:08Z — 이 세션 (`Tool: claude`)**: 전체 AI 호출 패턴 조사 → 10개 추천 → 사용자가 #6 지정 →
   #6·#7 실제 구현·테스트 → **배포는 `TYPESAFE_API_KEY` 부재로 세션 종료 시점까지 보류**.
2. **11:38:22Z — [[2026-09-20-jev-활용처-추천-lampas-system]] (`Tool: codex`)**: 별도로 5개 후보만
   추천, 코드 수정 없이 "전면 도입 보류"로 종료. 이 세션과 겹치는 대상(Jev 적용처)을 다루지만
   **서로 다른 서베이·다른 후보 목록**을 냈다 — 같은 프롬프트에 대해 두 도구가 독립적으로 다른
   결과를 낸 사례로 기록해둔다.
3. **11:52:53Z — [[2026-09-20-lampas-flow-만들기]] (`Tool: codex`)**: Flow 구축 중 "Jev 노드 자동화"
   절에서 사용자가 `TYPESAFE_API_KEY` 값을 대화 중 직접 제공해 `.env.production`에 반영 — **이 세션이
   막혀 있던 배포 전제조건을 우연히 다음 세션에서 해소**했을 가능성이 있으나, 이 셋의 소스만으로는
   #6·#7 코드가 그 배포에 실제로 포함됐는지 확정할 수 없다(아래 "배포 상태" 절 참고).

## 0. AI 호출 패턴 전체 조사 (배경 에이전트, 85 tool-use·127,916 토큰)
`lampas-api`/`dalar-api`/`lampas-agent`/`lampas-web-*`/`talk-api`/collector를 훑어 LLM 호출 아키텍처의
구조적 문제를 정리. 상세는 → [[lampas-system-ai-call-architecture-audit]] (별도 토픽 페이지로 분리 —
향후 리팩터 작업의 근거 자료로 재사용 가치가 큼).

## 1. Jev/TypeSafe 적용 추천 10개 (우선순위순)
survey 결과와 대조해 "판정만 하는 호출"을 골라 우선순위표로 제시 (생성이 필요한 곳은 LLM 유지):

1. clip-intelligence 5축 채점+페르소나 적합도
2. copy 교차 채점(hook/clarity/fit/natural)
3. Pulse 선별 교차 심사·검색어 계획
4. Pulse 페르소나 신뢰도·교차 심사
5. trends 제목 키워드 3개 유추 (TS·Python 이중 구현)
6. **dalar 채팅 의도 분류·플로우 턴 판별** ← 사용자가 선택, 아래 구현
7. **sports-wiki ingest 게이팅** ← 함께 구현
8. 클립 라벨링 중 닫힌 집합 필드
9. CS 문의 트리아지 (신규)
10. `/v1/ai/systemone`을 파트너 상품으로 노출

미채택: 카피 생성·콘티/대본·페르소나 문장·위키 본문·자막 번역·음성 디렉션·등록사진 분류(전부 생성
또는 비전이라 Jev 대상 아님).

## 2. 사용자 지시: "#6 을 적용해서 작업 후 배포해줘"

### 공통 기반 (`lampas-api`)
- `src/infrastructure/typesafe/`: Jev 직결 어댑터. Atlas 카탈로그에 없어 유일한 벤더 직결 — jev-search
  방식(TypeSafe 직결 → Cloudflare Workers AI → Vercel AI Gateway, 402·429·5xx에서만 다음 공급자)의
  폴백 체인.
- `POST /v1/ai/systemone` + `GET systemone/status`: 게이트웨이 passthrough. 검증·공급자 방언 정규화는
  순수 lib로 분리, 과금은 입력 토큰만(`typesafe/jev` 42크레딧/M, 출력 무료) 원장 기록. 미설정이면 503.

### #6 — dalar 의도 분류·플로우 턴 판별
- `dalar-api POST /v1/orchestration/classify-intent`: 의도 `choice`(10종) + 취소 `noul`을 한 호출로
  판정. `analyze-flow-turn`은 Jev 선판정으로 확신 취소(≥0.75)·shoot 이외 플로우 진행(≥0.85)을 Grok
  없이 확정.
- `dalar-web-app director/mergeIntent.ts`: 기존 정규식 분류기(`classifyIntent.ts` 16종 패턴)와
  confidence 게이팅(≥0.6 채택, 취소 ≥0.8, 사진 첨부는 규칙). 기존 Level 0 평가 세트 28건 그대로
  통과, 병합 로직 테스트 10건 추가.
- 벤치 스크립트 `apps/dalar-api/scripts/bench-intent-jev.ts`: 같은 평가 세트를 Jev에 직접 돌려 한국어
  정확도·게이팅 효과 측정용(아직 실행 안 됨, 키 필요).

### #7 — sports-wiki ingest 게이트
- `lib/sports-wiki-gate.ts`: 관련도·페이지별 "새 정보 있음"·새 인물 등장을 noul로 한 호출에 물어, 새
  정보 없는 소스는 16k 토큰 재작성 호출 없이 소스만 보관하고 `ingest-skip` 로그 남김. 진행 시 최신
  페이지 slug만 알려 중복 생성 방지. 판정 실패·미설정이면 기존 경로.

### 검증 결과 (배포 전)
| 대상 | 결과 |
|---|---|
| lampas-api jest | 114 스위트 1003 테스트 통과 |
| dalar-api jest | 19 스위트 전부 통과 |
| dalar-web-app vitest | 61 파일 469 테스트 통과 |
| tsc | lampas-api·dalar-api 변경 파일 오류 없음 |

## 배포 상태 — 이 세션 종료 시점엔 보류, 이후 상태는 소스 간 정황으로만 추정
- 이 세션 종료 시점: `TYPESAFE_API_KEY`가 어느 env에도 없어 **실 Jev 호출을 한 번도 못 돌려본 채**
  배포 보류. 키 없이 배포하면 세 앱 모두 기존 경로로만 동작하는 "빈 배포"가 된다고 명시.
- **#7(sports-wiki 게이트)은 이후 실제로 라이브였음이 별도 소스로 확인된다**: 5일 뒤
  `raw/conversations/2026-09-25-위키개선-스포츠경기엔티티-설계.md:47-48`이 "Jev 게이트가 하이라이트
  장면 묘사를 새 정보 없음으로 걸러 skip할 수 있고, 09-25에는 System One 15초 타임아웃도 찍혔습니다"
  라고 프로덕션 동작을 서술한다 — `POST /v1/ai/systemone` 호출이 실제로 나가고 있었다는 뜻이므로,
  이 세션이 막아둔 배포가 그 사이 어느 시점(가장 유력한 후보는 같은 날 뒤이은
  [[2026-09-20-lampas-flow-만들기]] 세션에서 사용자가 `TYPESAFE_API_KEY`를 제공한 시점)에 풀린 것으로
  보인다. → [[jev-typed-classification]] "확인된 사용처 #1" 절에 반영.
- **#6(dalar 의도분류)의 배포 여부는 이 위키 소스로 확인되지 않는다** — `classify-intent`·
  `mergeIntent.ts`·벤치 스크립트를 언급하는 후속 세션이 없다. 구현·테스트는 완료됐지만 실 운영 배포·
  벤치 실행 여부는 미확인으로 남긴다.

## 관련
- 토픽: [[jev-typed-classification]] · [[lampas-system-ai-call-architecture-audit]]
- 엔티티: [[lampas-studio]] · [[dalar]]
- 관련(같은 날, 다른 세션): [[2026-09-20-jev-활용처-추천-lampas-system]] · [[2026-09-20-lampas-flow-만들기]]
- 원본: `raw/conversations/2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현.md`
