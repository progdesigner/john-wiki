---
tags: [entity, ai-provider, image-generation, video-generation, external, wan, qwen, seedance]
created: 2026-09-07
updated: 2026-09-26
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
- **음악 생성 — `[[lampas-web-music]]`** — minimax 음악 모델을 Atlas Cloud 경유로 호출(2026-09-22
  세션에서 첫 확인). 2.6→3.0 업그레이드 시 Atlas 스키마의 요청 필드가 두 버전 간 동일함을 먼저
  확인하고 모델 식별자만 교체 — 이미지·영상뿐 아니라 오디오/음악 생성도 이 애그리게이터를 거친다는
  근거.
- **CLAUDE.md 요약과 실제 라우팅 불일치** (2026-07-15 세션 확인, [[lampas-studio]]에 상세) — 제품
  CLAUDE.md엔 "스튜디오 합성=Atlas Cloud"로 뭉뚱그려 있지만, **Object 단독 촬영은 실제로 Gemini
  직접 경로**(`objects.service.ts:794`)다. Atlas Cloud는 Actor/Actor+Object 촬영 쪽에만 해당.

## 모델 카탈로그 업데이트 (2026-09-21 세션)

- **WAN 3.0** — 영상 모델로만 카탈로그에 존재(이미지 생성·편집용 3.0은 미제공). [[lampas-studio]]가
  일반 WAN 영상을 2.7→3.0으로 전량 교체(50cr/초, 다중 이미지 레퍼런스·오디오 지원), 이미지용 WAN은
  2.7 Pro 유지.
- **Qwen Image 3.0 Pro Edit**(`qwen-image-3.0-pro/edit`) 신규 — 레퍼런스 최대 3장, 40cr/장.
- **GPT Image 2.5 Sunburst Edit · Flare Edit** 신규 — 이미지 편집, 각 6cr/장.
- **Seedance 레퍼런스 모델** — 영상 생성 시 다중 이미지 입력을 지원하는 경로로 확인(기존엔 첫 장만
  전송하던 제약이 있었음).
- `models.lampas.io` 카탈로그가 **508개 모델**로 동기화, 스튜디오 모델 선택창 가격이 이 카탈로그를
  유일한 소스로 조회하도록 재연결. → [[2026-09-21-lampas-studio-edit모델-wan3.0-qwen이미지-멀티이미지영상]]

## 텍스트 LLM 라우팅 — `[[lampas-web-trends]]` 제목 키워드 유추 (2026-09-19 세션)

이미지·영상·음악 외에 **순수 텍스트 생성(LLM 추론)도 Atlas Cloud를 경유**한다는 첫 확인 사례.
`lampas-trends-collector`/`lampas-api`가 기사 제목마다 핵심 키워드 3개를 유추할 때 Atlas Cloud
경유 `gemini-3.5-flash`에 40개씩(이후 20개로 축소) 배치로 질의 — 이미지/영상 모델과 같은
애그리게이터 계층을 텍스트 추론에도 그대로 쓴다는 근거. 배치가 너무 크면 타임아웃·응답 잘림으로
전량 실패하는 문제가 있어 배치 크기·타임아웃·응답 압축을 함께 조정 → [[llm-batch-inference-timeout-tuning]].
상세 → [[lampas-web-trends]] · [[2026-09-19-lampas-trends-고도화]].

## 다른 제품에서의 언급

- **[[toktalk]] AI 스택** — "AtlasCloud WAN"이 OpenAI·Grok·DeepSeek·Gemini·ElevenLabs와 함께 AI
  계층 목록에 등장. lampas-studio와 같은 Atlas Cloud 계정/연동인지는 이 위키 소스로 미확인 —
  제품이 다르므로 별개 통합일 가능성이 더 높다.
- **[[openai]]** — OpenAI의 `gpt-image-2`가 Atlas Cloud를 통해 간접 노출되는 것으로 확인,
  Atlas Cloud가 다중 모델 애그리게이터 역할을 한다는 근거.

## 관련
- [[openai]] · [[gemini]] · [[grok]] · minimax(음악, [[lampas-web-music]] 경유) (Atlas Cloud가 라우팅하는 개별 모델 제공사)
- [[lampas-studio]] · [[toktalk]] · [[lampas-web-music]] · [[lampas-web-trends]]
- 세션: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]] ·
  [[2026-09-22-music-lampas-io-minimax3.0-업그레이드-배포]] · [[2026-09-19-lampas-trends-고도화]]
