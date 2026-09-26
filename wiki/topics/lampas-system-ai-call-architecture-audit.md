---
tags: [topic, lampas-studio, dalar, ai-architecture, tech-debt, audit]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-system LLM 호출 아키텍처 감사 (2026-09-20)

`lampas-api`/`dalar-api`/`lampas-agent`/`lampas-web-*`/`talk-api`/collector 전체를 훑은 배경 조사
결과(85 tool-use·127,916 토큰) → [[2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현]].
Jev/[[jev-typed-classification]] 적용처 추천의 근거였지만, 그와 별개로 **저장소 전체 LLM 플러밍의
구조적 중복·취약점 지도**로서 재사용 가치가 있어 별도 페이지로 분리.

## 큰 그림
모든 LLM 호출은 결국 `POST /v1/ai/chat/completions`(lampas-api `ai.service.ts`) 하나로 모인다
(AtlasCloud로 passthrough+과금). 이 선 위로는 **공유 typing/validation 계층이 전혀 없다** — 호출부마다
프롬프트를 문자열 배열로 손수 작성하고, `string`을 돌려받아 `JSON.parse`+`typeof` 보정을 손수 짠다.
zod는 전체 저장소에서 `lampas-api-mcp` 딱 한 파일에만 쓰인다. tool/function calling·structured
output(`json_schema`)은 전무.

## 가장 큰 고통 10가지
1. **"LLM 텍스트에서 JSON 뽑기" 구현이 7개** — 제일 좋은 `model-json.helper.ts`(균형 괄호 추출+잘림
   복구)는 8곳만 쓰고, `actors`/`orchestration`/`objects`의 naive `lastIndexOf('}')` 버전은 그 헬퍼
   주석이 명시적으로 "쓰지 말라"고 경고하는 바로 그 버그를 그대로 갖고 있다.
2. **JSON을 세 가지 서로 다른 방식으로 요청**하고 강제하는 방법은 0가지 — `response_format`은 Atlas
   경로에만 있어 Grok 20개 호출부(시나리오·오케스트레이션·보이스 생성 등 고가치 출력 다수 포함)는
   프롬프트로 "부탁"만 할 수 있다.
3. **잘림이 프로덕션 1순위 실패 모드**이고 모듈마다 자기 방식으로 땜질 — sports-wiki의 JSON모드
   재시도(502 4건 중 3건이 이 원인), clip-intelligence의 "절반 이상 와야 통과", trends의 정규식
   구제, translateCues의 개수불일치 재시도, actors의 토큰 예산 분할.
4. **모양(shape)이 기능당 세 번 선언**된다 — 프롬프트의 JSON 예시 문자열 1회, TS `interface` 1회,
   수동 보정 함수 1회 — 서로 조용히 드리프트한다. 모듈마다 자기 `str(v,max)`/`clamp100`/`*ParseError`도
   재선언.
5. **폴백 루프가 7회 복붙** (`copy` ×4, `clip-intelligence` ×3), "추론 모델은 비기본 temperature
   거절" 우회 조건이 두 곳에서 서로 다르다(`copy`는 항상 제거, `clip-intel`은 `google/*`만 유지).
6. **`chatContentText()`류 언랩이 최소 5곳**, 프론트 `runChatCompletion` HTTP 클라이언트가 7개 앱에
   중복(3개는 바이트 단위로 동일).
7. **앱 간 파일째 포크**: `dalar-api`가 `lampas-api`의 `atlas-cloud.service.ts`(2244줄)·
   `gemini.service.ts`·`orchestration.service.ts`(183줄 diff)·`model-json.helper.ts`·`actors.service.ts`·
   `objects.service.ts`를 통째로 포크. `labelBatch.ts`는 `lampas-agent`(376줄)와 `lampas-web-clips`
   (74줄)로 갈라져 다르게 진화. trends 키워드 프롬프트+정규식 구제는 TypeScript와 **Python
   양쪽에** 포크되어 `lampas-api`의 CLAUDE.md가 손으로 동기화하라고 명시.
8. **Tool calling이 전혀 없어서** dalar의 "에이전틱" 채팅이 2,900줄짜리 손수 짠 클라이언트 상태머신
   (`dalar-web-app/src/apps/chat/chat/`)이다. 16종 한국어 정규식 의도 분류(`classifyIntent.ts`) +
   one-shot LLM "헬퍼" 엔드포인트(`analyze-flow-turn` 등)가 얹혀 있고, 같은 응답 모양이 클라이언트
   (`normalizeFlowTurn.ts`)와 서버(`analyze-flow-turn.normalize.ts`) 양쪽에서 **두 번** 정규화된다.
   `director/responsePolicy.ts:5`: *"프롬프트는 계약이 아니라 희망사항이다."*
9. **스트리밍이 뒤집혀 있다** — 게이트웨이가 Cloudflare 엣지 ~100초 타임아웃을 피하려고 항상
   업스트림에 `stream:true`를 걸고, 그 결과를 서버에서 다시 비-스트림 JSON으로 재조립해 클라이언트에
   준다. 클라이언트는 토큰을 점진적으로 받은 적이 없다.
10. **딱 하나 예외** — `lampas-agent/src/server/pulse/ai/gateway.ts`의 `AiBudget`(미션당 호출 한도+
    토큰 원장+타입드 `AgentError`+`withRetry` 정책)은 이 저장소에서 유일하게 잘 짜인 클라이언트지만,
    다른 어디에도 없다. lampas-api엔 호출 예산·타입드 오류 분류·요청당 사용량 원장이 크레딧 과금 외엔
    없다.

## 왜 이게 중요한가
[[jev-typed-classification]] 도입은 이 목록 중 "판정만 하는" 호출부를 걷어내며 1·2·3·4번 고통을
자연히 줄인다(파서·재요청 로직이 typed 응답으로 대체됨). 하지만 **생성이 필요한 호출부(카피·시나리오·
페르소나 문장 등)는 Jev 대상이 아니므로**, 5·6·7·8·9번(폴백 루프 통합, `chatContentText` 통합,
앱 간 포크 해소, tool-calling 도입, 스트리밍 정상화)은 Jev와 무관하게 별도로 정리해야 한다.

## 관련
- 세션: [[2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현]]
- 토픽: [[jev-typed-classification]]
- 엔티티: [[lampas-studio]] · [[dalar]] · [[lampas-agent]]
