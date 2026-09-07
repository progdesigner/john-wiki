---
tags: [entity, ai-provider, image-generation, video-generation, external]
created: 2026-09-07
updated: 2026-09-07
---
# Atlas Cloud

[[lampas-studio]]가 [[gemini]] 직접 생성과 나란히 쓰는 **이미지/영상 생성 대행 서비스** — 모델을
직접 호출하지 않고 Atlas Cloud를 경유해 여러 외부 모델에 접근한다. 위키 여러 페이지(6개+)에
흩어진 사용처를 모은다. (2026-09-07 lint 신설)

## 어디에 쓰이는가 ([[lampas-studio]] 중심)

- **이미지 생성 — Gemini와 동일 시그니처로 분기** — Gemini(기본, 멀티 이미지 그리드 직접 생성)와
  Atlas Cloud 경유(대안)가 동일한 `generateImage` 시그니처를 공유해, 선택값 하나로 서비스만 바뀐다.
  Atlas Cloud 경유 시 `gpt-image-2`([[openai]]) · `nano-banana-2` · `grok-imagine`([[grok]]) ·
  `wan-2.7` 중 선택.
- **레퍼런스 시트 생성** — 시트 생성 모델 선택지도 Gemini 기본 / Atlas Cloud 대안 구조 동일.
- **Actor / Actor+Object 합성 촬영** — 기존 `background` 레퍼런스 슬롯을 그대로 재사용하는 경로.
  Space 기능(2026-07-15~16 신설)도 이 경로를 거의 무개조로 재사용.
- **CLAUDE.md 요약과 실제 라우팅 불일치** (2026-07-15 세션 확인, [[lampas-studio]]에 상세) — 제품
  CLAUDE.md엔 "스튜디오 합성=Atlas Cloud"로 뭉뚱그려 있지만, **Object 단독 촬영은 실제로 Gemini
  직접 경로**(`objects.service.ts:794`)다. Atlas Cloud는 Actor/Actor+Object 촬영 쪽에만 해당.

## 다른 제품에서의 언급

- **[[toktalk]] AI 스택** — "AtlasCloud WAN"이 OpenAI·Grok·DeepSeek·Gemini·ElevenLabs와 함께 AI
  계층 목록에 등장. lampas-studio와 같은 Atlas Cloud 계정/연동인지는 이 위키 소스로 미확인 —
  제품이 다르므로 별개 통합일 가능성이 더 높다.
- **[[openai]]** — OpenAI의 `gpt-image-2`가 Atlas Cloud를 통해 간접 노출되는 것으로 확인,
  Atlas Cloud가 다중 모델 애그리게이터 역할을 한다는 근거.

## 관련
- [[openai]] · [[gemini]] · [[grok]] (Atlas Cloud가 라우팅하는 개별 모델 제공사)
- [[lampas-studio]] · [[toktalk]]
- 세션: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]]
