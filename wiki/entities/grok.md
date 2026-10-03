---
tags: [entity, ai-provider, xai, external]
created: 2026-09-07
updated: 2026-10-03
---
# Grok (xAI)

[[lampas-studio]]/[[lampas-web-ai]]에서 **텍스트 분석·필드 추출 1순위 모델**로 쓰이는 xAI의 Grok.
이미지 생성 쪽은 [[atlas-cloud]] 경유 `grok-imagine`으로 별도 접근한다. 위키 여러 페이지(7개+)에
흩어진 사용처를 모은다. (2026-09-07 lint 신설)

## 어디에 쓰이는가

- **[[lampas-studio]] 레퍼런스 분석** — 레퍼런스 이미지 분석은 **Grok 1차 → Gemini 폴백** 순서로
  모델이 배정된다. Space 기능의 연출 프롬프트 생성도 동일하게 Grok을 쓴다(기존 Object 패턴과 동일).
- **[[lampas-web-ai]] 필드 추출** — 액터 만들기 플로우에서 `analyze-creation-input`이 Grok으로
  자유 텍스트 입력에서 이름·성별·나이·인종·외모 등 여러 필드를 한 번에 추출, 이미 채워진 질문은
  건너뛴다.
- **이미지 생성** — [[atlas-cloud]] 경유 시 선택 가능한 모델 중 하나(`grok-imagine`).
- **[[lampas-web-music]] 유튜브 레퍼런스 분석**(2026-09-26 모델 질문 세션에서 처음 확인) —
  `apps/lampas-web-music/src/lib/analyze.ts:10`가 `xai/grok-4.5`로 유튜브 영상 제목·채널·길이
  메타데이터만 보고 새 곡 제목·영문 스타일 프롬프트·창작 가사·연주곡 여부를 JSON으로 추출(원곡 가사
  재현 금지, 파싱 실패 시 1회 재시도 후 수동 입력 폴백). 음악 **생성** 자체는 [[atlas-cloud]] 경유
  minimax라 Grok과 분리돼 있다. → [[2026-09-26-music모델질문-전체커밋푸시-env파일항상커밋정책]]
- **[[toktalk]] AI 스택** — OpenAI·DeepSeek·Gemini·ElevenLabs와 함께 AI 계층 목록에 등장(역할
  미상세).

## dark-system과의 무관한 언급 — 혼동 주의

[[dark-upbit-api]]의 모델 카탈로그 날짜 필터 로직에 `grok-2-1212`(연월일 패턴이라 걸러냄)·
`grok-4-latest`(통과)가 **정규식 테스트 예시 모델명**으로 등장한다. 이는 dark-system이 실제로
Grok API를 호출한다는 뜻이 **아니고**, 날짜 패턴 필터가 다양한 프로바이더의 모델명 포맷에 대해
올바르게 동작하는지 확인하는 예시일 뿐이다.

## 관련
- [[atlas-cloud]] · [[openai]] · [[gemini]] · [[elevenlabs]] (외부 AI 도구군)
- [[lampas-studio]] · [[lampas-web-ai]] · [[lampas-web-music]] · [[toktalk]]
