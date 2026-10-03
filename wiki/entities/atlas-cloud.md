---
tags: [entity, ai-provider, image-generation, video-generation, external, wan, qwen, seedance, model-catalog, lampas-web-scenario, lampas-web-edit, video-edit, dalar, motion-video, aspect-ratio]
created: 2026-09-07
updated: 2026-10-03 (ingest: 모션 컨트롤 모델 확장·비율 선택)
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

## ⚠️ 가격 카탈로그는 요청 body 스키마를 기록하지 않는다 (2026-09-13 세션, [[lampas-web-scenario]])

`apps/lampas-api/src/modules/credits/model-pricing.generated.ts`(`pnpm sync:atlas-pricing`으로
기계 생성, 사람 오버라이드는 `model-pricing.overrides.ts`)의 각 항목은 `modelId`/`atlasName`/
`category`/`unit`/`usdPerUnit`·`creditsPerUnit`/`usedFor`(설명 문장)/`products`/선택적
`capabilities`(`durationSeconds`·`durationDefault`·`resolutions`·`resolutionDefault`·
`aspectRatios`·`supportsAudio`)만 갖는다 — **`prompt`/`image`/`audio` 같은 요청 필드 존재 여부는
어디에도 저장되지 않는다.** 동기화 스크립트(`scripts/lib/atlas-model-schema-capabilities.mjs`)가
Atlas의 모델별 OpenAPI `Input` 스키마를 가져오긴 하지만 duration/resolution/aspect_ratio/
generate_audio 속성만 추출하고 나머지는 버린다.

**`supportsAudio`는 "오디오 입력을 받는다"는 뜻이 아니다** — Input 스키마에 `generate_audio`/
`generateAudio` 불리언 속성이 있을 때만 세팅되는 플래그로, "모델이 자체 오디오/SFX를 **생성**하는
토글이 있는가"만 뜻한다. 아바타/립싱크류가 오디오를 **입력**으로 받는지 여부와는 무관.

**검증 방법(이 세션에서 확립)**: ① 저장소 전체에서 모델 id로 grep해 실사용 프로덕션 코드를 찾는다
(있으면 최고 신뢰도), ② 없으면 Atlas `GET /api/v1/models` 응답의 모델별 `schema` URL(라이브
OpenAPI JSON)을 직접 fetch한다. `usedFor` 설명 문장만으론 결론 내지 않는다.

이 세션에서 조사한 이미지+오디오 결합(아바타/립싱크) 모델 6개 중, 저장소 내 유일한 실사용 body
증거는 `[[lampas-web-tools]]`의 `talking-photo.ts`(`kwaivgi/kling-v2.6-std/avatar`를
`{ image, audio }`만으로 호출, `prompt` 없음) — 나머지(`kling-v2.6-pro/avatar`,
`atlascloud/infinitetalk`, `bytedance/avatar-omni-human-v1.5`, `sync/lipsync-v3`,
`veed/lipsync`)는 카탈로그 설명 문장뿐이었으나, 어시스턴트가 라이브 Atlas OpenAPI 스키마를 직접
확인해 앞 4종(아바타 2종+infinitetalk+omni-human)이 이미지+오디오+선택적 텍스트 프롬프트를 함께
받는다고 확인하고 `[[lampas-web-scenario]]`의 컷별 영상 모델 드롭다운에 추가했다. 절차 일반화 →
[[parallel-survey-before-feature-gap-analysis]] "주의사항" 절.

## 영상 편집(Video Edit) 모델 레지스트리 — 3종 → 8종 확장 (2026-09-26 세션)

[[lampas-studio]] Node Studio("동영상 편집" 노드)가 받는 입력 영상 자체를 고쳐주는 편집 전용 모델
목록. 사용자가 모델 목록 스크린샷을 첨부하며 "쓸 수 있는 모델 모두 넣어줘"로 요청 → Atlas 카탈로그
전체에서 영상 입력을 받는 편집 모델을 조사해 기존 3종(xAI Imagine Edit·Gemini Omni Flash Edit·
Wan 2.7 Video Edit)에 5종(Gemini Omni 1.1 Flash Edit·HappyHorse 1.0 Video Edit·Kling O3 Pro/Std
Video Edit·FLUX 3 Edit Video)을 추가.

- **레지스트리 위치**: `lampas-api infrastructure/atlas-cloud/video-edit-models.ts`, `dalar-api`
  파사드에도 동일 파일 — 모델별 Atlas 입력 스키마를 실제 조회해 소스 영상 필드명(`video`/`video_url`)·
  길이 상한·비율 지원·기본 옵션을 한 곳에 정리. 요청 본문 생성·DTO 허용 목록·Transform 길이 캡·크롭
  판정이 모두 이 레지스트리에 위임되어 이후 모델 추가는 한 줄.
- **크롭 처리**: Wan 2.7만 모델이 비율을 직접 받고, 나머지 7종은 서버가 센터 크롭한 뒤 편집에 전달.
- **의도적으로 제외한 모델**: Wan 2.6 video-to-video(참조영상으로 새 영상을 생성하는 방식, 출력
  5·10초 고정·캐릭터 참조 문법 — "편집"이 아니라 "생성"), video-extend·video-upscaler·
  reference-to-video 계열(편집과 다른 작업, 필요하면 별도 노드로 분리하는 게 맞다고 판단).
- **스튜디오 반영**: `dalar-web-app`을 먼저 고치고 `pnpm sync:studio`로 `lampas-web-studio`에
  반영 — [[dalar]]의 "Node Studio SoT는 `dalar-web-app`" 관계가 2026-09-24에 이어 **세 번째
  실행 확인**.
- **가격 드리프트 미해소**: xAI Imagine Edit의 Atlas 정가가 현재 초당 $0.07인데 과금 오버라이드는
  여전히 $0.05(50크레딧)로 남아 있음 — `pnpm sync:atlas-pricing` 실행 시 재확인 필요, 이 세션에서는
  고치지 않음.
- 이 "동영상 편집" 노드는 Node Studio(`lampas-web-studio`/`dalar-web-app`) 소속이며 [[lampas-web-edit]]
  (edit.lampas.io, 자막·트랙 편집 앱)과는 별개 — 같은 세션에서 함께 다뤄져 혼동하기 쉽다. 상세 →
  [[dalar]] · 세션 → [[2026-09-26-edit-mp3사운드-원본교체-동영상편집모델확장-템플릿트랙편집]]

## 모션 컨트롤(이미지+레퍼런스 영상→동작 이식) 모델 2종 → 6종 + 비율·Actor 비종속화 (2026-09-26 세션)

[[lampas-studio]] Node Studio "모션 촬영" 노드가 Atlas에서 가능한 모델 전부로 확장됨.

| 모델 | 크레딧 | 비고 |
|---|---|---|
| Kling v2.6 Pro/Std | 112/70 cr/초 | 기존 |
| Kling v3.0 Pro/Std | 168/126 cr/초 | 신규 |
| Wan 2.2 Animate Move | 120 cr/초 | 이미지 인물에 영상 움직임 이식 |
| Wan 2.2 Animate Mix | 180 cr/초 | 영상 속 인물을 이미지 인물로 교체 |
| Seedance 2.0/2.0 Mini/2.0 Fast/2.5 reference-to-video | 초당 과금(2.0 Mini 56cr 등) | 후속 요청으로 통합, 아래 설명 |

- **Seedance엔 전용 motion-control 엔드포인트가 없다** — `reference-to-video`가
  `reference_images`+`reference_videos`(+`reference_audios`)를 함께 받아 "동작을 참고한 재생성"을
  하는 방식(2.0 계열 영상 3개·합계 15초, 2.5는 영상 10개·합계 30초). Kling처럼 프레임 단위로 동작을
  그대로 옮기는 게 아니라 프롬프트(`@Image1`이 `@Video1`의 동작을 수행)로 유도하는 재생성이라 **프레임
  정합·정체성 유지 보장이 없음** — Kling/Wan과 결과 특성이 다르다는 점을 사용자에게 명시.
- **비율 파라미터를 받는 모션 모델은 Seedance(`ratio`)뿐** — Kling·Wan은 레퍼런스 영상 비율을 그대로
  따른다. Seedance 선택 시에만 비율 셀렉트(원본·9:16·16:9·1:1·4:3·3:4·21:9) 노출.
- **Actor 비종속화**: 기존엔 액터 전용 API만 있어 레퍼런스 이미지·액터 없는 샷 연결 시 막혔는데,
  동영상 편집과 같은 내구 잡 방식의 `POST /v1/videos/motion`(lampas-api)·predictionId 방식(dalar-api)을
  신설해 Actor 없이도 모션 촬영 가능.
- **영상 생성 실패의 근본 원인**(Seedance만의 문제가 아니었음) — URL 문자열을 base64 전용 필드에
  그대로 실어 보내 69바이트 쓰레기 PNG로 디코딩되는 버그가 전 모델(Kling 포함)에 영향 →
  [[url-vs-base64-field-ambiguity]].
- `dalar-web-app`을 먼저 고치고 `pnpm sync:studio`로 반영 — [[dalar]] SoT 패턴 추가 실행 확인.
- 세션 → [[2026-09-26-studio개선-액터워크스페이스-모션모델확장-url버그-비율확장]]

## 이미지/영상 편집 노드 — 비율 선택 확장 (2026-09-26 세션, 위 모션 컨트롤과 같은 날 후속)

- **동영상 편집**: 편집 모델 중 비율 파라미터를 직접 받는 건 **Wan 2.7 Video Edit**뿐(세 번째
  모델로 추가) — 나머지(xAI Imagine Edit·Gemini Omni Flash Edit)는 서버가 `ffmpeg-static`으로
  소스를 목표 비율로 센터 크롭한 뒤 편집(`iw/ih` 비율 필터, 짝수 크기 보정, 720×1280 클립 기준
  1:1→720×720·16:9→720×404 검증 완료). 소스는 URL 다운로드(200MB 상한) 또는 data URL.
- **이미지 노드**(샷·레퍼런스 이미지·갤러리): 1:1/2:3/3:2 세 칸 토글 → 이미지 생성 API가 받는
  **10종 전부**(세로 9:16·2:3·3:4·4:5, 정사각 1:1, 가로 5:4·4:3·3:2·16:9·21:9)로 확장, 공용
  `AspectRatioPicker` 셀렉트로 통일. 영상 촬영 노드에 연결 시엔 영상 API 허용 비율
  (1:1·16:9·9:16·4:3·3:4·3:2·2:3)만 전달되고 4:5·5:4·21:9는 영상 노드가 자체 비율로 대체.
- 세션 → [[2026-09-26-studio개선-액터워크스페이스-모션모델확장-url버그-비율확장]]

## 텍스트 LLM 라우팅 — `[[lampas-web-trends]]` 제목 키워드 유추 (2026-09-19 세션)

이미지·영상·음악 외에 **순수 텍스트 생성(LLM 추론)도 Atlas Cloud를 경유**한다는 첫 확인 사례.
`lampas-trends-collector`/`lampas-api`가 기사 제목마다 핵심 키워드 3개를 유추할 때 Atlas Cloud
경유 `gemini-3.5-flash`에 40개씩(이후 20개로 축소) 배치로 질의 — 이미지/영상 모델과 같은
애그리게이터 계층을 텍스트 추론에도 그대로 쓴다는 근거. 배치가 너무 크면 타임아웃·응답 잘림으로
전량 실패하는 문제가 있어 배치 크기·타임아웃·응답 압축을 함께 조정 → [[llm-batch-inference-timeout-tuning]].
상세 → [[lampas-web-trends]] · [[2026-09-19-lampas-trends-고도화]].

## 텍스트 LLM 카탈로그 — `[[lampas-web-copy]]`/`[[lampas-web-pulse]]` 사용자 모델 선택 (2026-09-24~ 세션)

카피 생성·채점·페르소나 생성에 사용자가 직접 모델을 고르는 기능이 추가되며, 큐레이션된 옵션 목록이
**GPT-6 Astra, GPT-5.6 Sol, Claude Opus 5, Claude Sonnet 5, Gemini 3.5 Flash, Gemini 3.1 Pro,
Grok 4.5** — 여러 벤더를 한 목록에서 고를 수 있음이 드러남. 서버는 "목록 밖이라도 게이트웨이
카탈로그의 LLM이면 허용"이라 응답해, 이 텍스트 생성 경로도 특정 벤더 API를 직접 물지 않고
애그리게이터(가장 유력한 후보가 Atlas Cloud, 세션 소스로 확정되진 않음) 카탈로그를 통해 다중 벤더에
접근하는 구조임을 시사 — 위 [[lampas-web-trends]] 사례(`gemini-3.5-flash` 단일 모델 배치 호출)보다
더 폭넓은 벤더 혼합 카탈로그가 텍스트 생성에도 존재함을 처음 확인. 상세 → [[lampas-web-copy]]·
[[lampas-web-pulse]] · 세션 → [[2026-09-24-voice레퍼런스오디오-소프트삭제-카피페르소나모델선택]].

## 다른 제품에서의 언급

- **[[toktalk]] — 텍스트 모델 카탈로그로 실사용 확인** (2026-09-07~09 세션,
  [[2026-09-07-톡톡-2.0-재구축-사만다-도입]]) — "AtlasCloud WAN"이 목록에만 등장하던 이전 추정과
  달리, 이 세션에서 **사만다(Her) 페르소나의 텍스트 모델 선택(GPT Astra·Grok·Claude·Gemini)을
  AtlasCloud 카탈로그로 직접 통일**한 것이 실사용으로 확인됨 — 기존 AtlasCloud 키 재사용, 프로바이더별
  개별 키 불필요. 단 **실시간 음성 생성은 AtlasCloud가 비동기(생성 완료 대기) 방식이라 제외**하고
  xAI `grok-voice-latest`를 그대로 유지 — 실시간 스트리밍이 필요한 경로에는 AtlasCloud가 아직
  부적합하다는 두 번째 확인 사례(첫 사례는 위 "가격 카탈로그" 절과 무관, [[lampas-web-trends]] 텍스트
  경로는 배치 비동기라 문제 없었음과 대조). lampas-studio와 같은 Atlas Cloud 계정/연동인지는
  여전히 미확인 — 제품이 다르므로 별개 통합일 가능성이 더 높다.
- **[[openai]]** — OpenAI의 `gpt-image-2`가 Atlas Cloud를 통해 간접 노출되는 것으로 확인,
  Atlas Cloud가 다중 모델 애그리게이터 역할을 한다는 근거.

## 관련
- [[openai]] · [[gemini]] · [[grok]] · minimax(음악, [[lampas-web-music]] 경유) (Atlas Cloud가 라우팅하는 개별 모델 제공사)
- [[lampas-studio]] · [[toktalk]] · [[lampas-web-music]] · [[lampas-web-trends]] · [[lampas-web-scenario]] · [[lampas-web-tools]]
- 세션: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] · [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]] ·
  [[2026-09-22-music-lampas-io-minimax3.0-업그레이드-배포]] · [[2026-09-19-lampas-trends-고도화]] ·
  [[2026-09-13-시나리오-영상생성-오디오모델-길이슬라이더-카메라고정]] ·
  [[2026-09-07-톡톡-2.0-재구축-사만다-도입]] ·
  [[2026-09-26-edit-mp3사운드-원본교체-동영상편집모델확장-템플릿트랙편집]](영상 편집 모델 3→8종) ·
  [[2026-09-26-studio개선-액터워크스페이스-모션모델확장-url버그-비율확장]](모션 컨트롤 2→6종·비율 확장)
- 스킬: [[parallel-survey-before-feature-gap-analysis]] · [[url-vs-base64-field-ambiguity]]
