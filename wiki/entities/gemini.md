---
tags: [entity, ai-provider, gemini, google, external]
created: 2026-08-31
updated: 2026-09-07
---
# Gemini (Google)

[[lampas-studio]]의 **핵심 이미지·비전 엔진**이자 [[lampas-harness]]의 3대 모델 주입 경로 중 하나.
위키 여러 페이지(14개+)에 흩어진 Gemini 사용처를 모은다. (2026-08-31 lint 신설)

## 어디에 쓰이는가 ([[lampas-studio]] 중심)

- **이미지 생성 기본 경로** — 멀티 이미지 그리드를 직접 생성. [[atlas-cloud]] 경유(`gpt-image-2`·
  `nano-banana-2`·`grok-imagine`·`wan-2.7`)와 동일 `generateImage` 시그니처라 선택값으로 서비스만 분기.
  최대 8장 입력 지원.
- **레퍼런스 시트/분석** — 레퍼런스 시트 생성 모델 선택지(Gemini 기본 / [[atlas-cloud]]). 분석은
  [[grok]] / Gemini (모델 배정: 레퍼런스 분석 Grok 1차 → **Gemini 폴백**, 프로필 이미지 생성 Gemini).
- **Object 단독 촬영 직접 경로** — CLAUDE.md엔 "Atlas Cloud"로 요약돼 있으나 실제로는 Gemini 직접
  경로(`objects.service.ts:794`).
- **Gemini Vision — 제품 특성 분석** — [[lampas-web-ai]] Object 만들기(`/api/objects/analyze-reference`) 및
  `lampas-web-product`의 `GeminiService.analyzeProductMarketing()`(제품 이미지→마케팅 방향)에 사용.
- **Gemini Vision — 등록 사진 분류(2026-07-18)** — `GeminiService.classifyRegistrationSubject`
  (`POST /actors/classify-registration-photo`)이 인물/제품/공간을 분류해 등록 플로우를 자동 라우팅 →
  세션 [[2026-07-18-web-ai-등록플로우-사진분류-배포]].

## 자격증명
- `GOOGLE_API_KEY`가 하네스 `.env`에 존재. 2026-07-17 평문 노출 사고에 포함 →
  [[secrets-plaintext-exposure-pattern]].

## 관련
- [[openai]] · [[elevenlabs]] · [[higgsfield]] · [[atlas-cloud]] · [[grok]] (외부 AI 도구군)
- [[lampas-studio]] · [[lampas-web-ai]] · [[toktalk]] · [[model-selection]]
