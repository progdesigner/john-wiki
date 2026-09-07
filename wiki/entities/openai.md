---
tags: [entity, ai-provider, openai, external]
created: 2026-08-31
updated: 2026-09-07
---
# OpenAI

[[progdesigner]]의 제품군이 쓰는 외부 AI 프로바이더. Claude([[claude-model-pricing]])와 함께
[[lampas-harness]]의 두 축 모델 경로이자, [[toktalk]]·로컬 LLM 인프라의 기반 규격이다. 이 페이지는
위키 곳곳(20개+ 페이지)에 흩어진 OpenAI 사용처를 한곳에 모은다. (2026-08-31 lint 신설)

## 어디에 쓰이는가

- **하네스 채팅 모델 경로** — [[lampas-harness]] 모델 선택기가 Claude + OpenAI 두 계열을 노출(기본은
  `claude-opus-4-8`). `/api/models`가 계정 접근 가능한 OpenAI 모델을 라이브로 반환 → [[model-selection]].
- **음성 입력(STT)** — 전송 버튼 길게 누르기(push-to-talk)가 **OpenAI Realtime `gpt-4o-transcribe`**(한국어
  우선)로 실시간 전사. 서버가 **1분짜리 임시 키**를 발급해 브라우저가 OpenAI에 직접 WebSocket 연결(키
  노출 없음). `speech_started` 이벤트로 인식 시작 판정 → [[realtime-voice-mic-buffer-before-connect]] ·
  세션 [[2026-07-15-gpt-realtime-음성입력-길게누르기]].
- **로컬 LLM 규격** — [[rapid-mlx]]가 **OpenAI 호환 API**(`http://localhost:8000/v1`)를 제공해 하네스의
  "Local" 프로바이더로 붙는다 → [[local-llm-on-apple-silicon]]. 하네스의 모델 주입 경로도 "Claude Code
  SDK / 로컬·OpenAI 호환 / Gemini" 3갈래로 구분된다([[use-area-personas]]).
- **이미지 생성(간접)** — [[lampas-studio]]가 [[atlas-cloud]] 경유로 `gpt-image-2`를 생성 옵션에 포함.
- **[[toktalk]] 스택** — OpenAI가 [[grok]]·DeepSeek·[[gemini]]·[[elevenlabs]]와 함께 AI 계층에 포함.

## 자격증명

- `OPENAI_API_KEY`가 하네스 `.env`에 존재([[progdesigner]]가 OpenAI 키 직접 보유). 2026-07-17 `.env`
  평문 노출 사고 때 이 키도 채팅에 출력됨 → [[secrets-plaintext-exposure-pattern]].

## 관련
- [[gemini]] · [[elevenlabs]] · [[higgsfield]] (외부 AI 도구군)
- [[lampas-harness]] · [[toktalk]] · [[rapid-mlx]] · [[model-selection]]
