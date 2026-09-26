---
tags: [entity, app, lampas-studio, video-generation, storyboard, atlas-cloud]
created: 2026-09-13
updated: 2026-09-26
---
# lampas-web-scenario

`[[lampas-studio]]` 저장소(`lampas-system`)의 앱. 컷(샷) 단위로 이미지를 만들고, 그 이미지를 영상으로
바꿔 시나리오/스토리보드를 완성하는 웹. [[2026-09-13-시나리오-영상생성-오디오모델-길이슬라이더-카메라고정]]
세션에서 이 위키에 처음 상세 노출됨 — `[[lampas-studio]]`가 그동안 기록해온 Lampas 9앱/스포츠 클립
파이프라인 앱 목록 어디에도 이 앱 이름이 없어, 그 목록들이 전체 앱 집합이 아니라는 정황이 또 하나
늘었다(기존에도 Copy/Reels/Edit 등 "AGENTS.md 목록에 없는 앱"이 반복 확인됨).

## 핵심 데이터 흐름 (2026-09-13 세션 기준)

- **UI**: `StoryboardGrid.tsx` — 컷 카드마다 이미지 프롬프트·영상 모션 프롬프트 편집, 대사 줄별
  오디오 생성(`SceneLinesEditor`), 영상 모델 선택 드롭다운, "영상 만들기"/"영상 재생성" 버튼.
- **핸들러**: `ScenarioPage.tsx`의 `handleGenerateSceneVideo(index)` — 모델(`shot.videoModel ||
  DEFAULT_VIDEO_MODEL`)·프롬프트(`sceneVideoPrompt`)·길이를 계산해 `startSceneVideo`를 호출.
- **백엔드**: **시나리오 전용 영상 생성 엔드포인트는 없음** — `POST /v1/ai/model/generateVideo`(
  `AiService.generateVideo`, `atlasPost('/api/v1/model/generateVideo', ...)`)라는 제네릭 [[atlas-cloud]]
  게이트웨이 패스스루 하나만 탄다. 스토리보드 전체를 한 영상으로 잇는 "reference-to-video" 경로
  (`generateStoryboardVideo`)도 존재하지만 2026-09-13 시점 UI 호출부가 없어 사실상 미사용/레거시로
  추정.
- **대사 오디오**: `LineAudio`(`sessions-api.ts`) — 대사 줄마다 `{ audioUrl(S3), voiceId, text,
  durationSec }`, 컷의 `lineAudios` 배열에 줄 인덱스로 정렬 저장. `/v1/voice/tts` 호출로 생성되며
  이미 S3 URL이라 영상 생성 모델에 직접 넘길 수 있음.

## 영상 모델·길이 (2026-09-13 이전 상태 → 이 세션에서 확장)

- 기본 모델: `bytedance/seedance-2.5/image-to-video`. 스토리보드 전체용은
  `bytedance/seedance-2.5/reference-to-video`.
- 모델 목록은 `fetchVideoModels()`가 공개 `/v1/ai/models` 카탈로그에서 `category==='VIDEO'`이고
  id가 `image-to-video|i2v`에 매치하는 것만 필터링(`spicy|lora|extend|infinite` 제외) — 이 정규식
  때문에 아바타/립싱크 계열 모델은 애초에 후보에 안 뜨는 구조였음(2026-09-13에 필터 확장).
- 길이 하드코딩이 **세 곳에 서로 다른 값**으로 흩어져 있었음: 클라이언트 `sceneVideoDuration()`
  1~10초, `clampVideoDuration()` 카탈로그 없으면 10초 상한, 서버 `scenario-normalize.ts`
  `clampDuration()` 1~15초. 2026-09-13 세션에서 클라이언트 쪽을 5~30초로 통일.

## 2026-09-13 세션 — 3가지 기능 추가

`[[2026-09-13-시나리오-영상생성-오디오모델-길이슬라이더-카메라고정]]` 참고. 구현 전 서브에이전트
3개로 순차 조사([[parallel-survey-before-feature-gap-analysis]] 변형 적용) 후 한 번에 구현·배포
전 검증(131개 테스트, tsc 클린)까지 완료:

1. **오디오 구동 아바타 모델 4종 추가** — `kwaivgi/kling-v2.6-pro/avatar`,
   `kwaivgi/kling-v2.6-std/avatar`, `atlascloud/infinitetalk`, `bytedance/avatar-omni-human-v1.5`를
   컷별 영상 모델 드롭다운에 추가. `fetchVideoModels()`의 `image-to-video|i2v` 필터를 확장해 이
   모델들이 나오게 함. 선택 시 `buildSceneVideoBody`가 새 분기(`/avatar|infinitetalk|omni-human/i`
   매치)로 컷의 `lineAudios` 중 대표 오디오 URL(`primaryLineAudioUrl`, 신규 헬퍼)을 `audio`/
   `audio_url` 필드로 전송 — 오디오가 없으면 "영상 만들기" 버튼 비활성+안내. 실제 요청 body 스키마는
   카탈로그(`model-pricing.generated.ts`)에 없어 라이브 Atlas OpenAPI 스키마로 별도 확인(아래
   [[atlas-cloud]] 참고).
2. **길이 슬라이더 5~30초 + 자동 추정** — 기존 1~10초 하드 상한 제거, `estimateSceneVideoDuration()`
   신설(대사/모션 텍스트 길이 → 한글 초당 ~4.5자 추정, 기존 line-audio 추정 공식 재사용)이 슬라이더
   기본값을 채우고 수동 조정 시 `videoDurationSeconds` 컷별 override로 영속. 오디오 구동 모델
   선택 시엔 길이가 오디오 길이로 정해지므로 슬라이더 숨김.
3. **카메라 고정 프롬프트 자동 삽입** — `CAMERA_LOCK_INSTRUCTION` 상수를 컷별(`sceneVideoPrompt`)·
   전체 스토리보드(`buildStoryboardVideoPrompt`) 양쪽 프롬프트에 자동 추가, 고정 카메라·일관된 구도
   지시.

**변경 파일**: `storyboard-video.ts`(모델 분기·`CAMERA_LOCK_INSTRUCTION`·길이 상수), `scene-lines.ts`
(`primaryLineAudioUrl`), `scenario-api.ts`(모델 필터·`SceneVideoRequest.audioUrl`), `sessions-api.ts`
(`videoDurationSeconds` 필드), `StoryboardGrid.tsx`(슬라이더 UI·오디오 구동 힌트),
`ScenarioPage.tsx`(`handleVideoDurationChange`, `handleGenerateSceneVideo` 배선).

## 보이스/TTS 기능 전면 제거 (2026-09-14 이후, [[dalar-web-first]] 세션 중 발견·연쇄 제거)

위 "대사 오디오"(`LineAudio`, `/v1/voice/tts`) 기능은
[[2026-09-14-돌잔치-ai-기능-보완-장면구조화-tts빌드제거-어드민개편]] 세션 후반에 **완전히
제거됐다** — `[[dalar-web-first]]`가 자체 TTS 나레이션 파이프라인을 만들다가, 정작 이
lampas-web-scenario 세션 7 영상이 실제로는 (보이스가 캐릭터에 연결돼 있었음에도) TTS를 거의
쓰지 않고 **Seedance 자체 오디오**(`generate_audio: true`, 대사는 영상 프롬프트에 텍스트로만
전달)로 만들어졌다는 사실이 드러났고, 사용자가 "필요 없으니 제거"를 지시하면서 이 앱에서도
포크 에이전트로 동시에 정리됐다.

- **삭제**: `src/lib/voice-api.ts`, `src/components/VoicePicker.tsx`.
- **정리**: `CharacterPanel.tsx`(보이스 드롭다운/"새 보이스" 제거), `SceneLinesEditor.tsx`
  (화자/텍스트/감정 편집만 남김), `StoryboardGrid.tsx`(`lineAudios`/오디오 구동 분기 제거),
  `ScenarioPage.tsx`(음성 생성 관련 상태·버튼 전부 제거, 영상 프롬프트는 항상
  `sceneSpeechPrompt` 텍스트 포함), `ComposePage.tsx`("텍스트+이미지"로 개명, TTS 믹싱 제거),
  `lib/scene-lines.ts`/`lib/storyboard-video.ts`/`lib/compose-plan.ts`/`lib/compose-render.ts`
  등에서 `lineAudioStale`/`AUDIO_DRIVEN_VIDEO_MODELS`/`DUCK_GAIN` 등 관련 코드 전부 삭제.
- **유지된 것**: `estimateLineSeconds`(자막 큐 타이밍용으로 계속 사용), 대사 텍스트·`emotion`
  (여전히 Seedance 프롬프트로 들어감), 컷 자체 오디오 믹싱(게인 1, TTS 오버레이만 제거).
- 위 "1. 오디오 구동 아바타 모델 4종"(`kling avatar`/`infinitetalk`/`omni-human`, 대표 오디오를
  `audio_url`로 전송하던 기능)도 이번에 **함께 제거**됨 — 09-13 세션 기록과 모순되는 게 아니라,
  09-14 이후 세션에서 명시적으로 걷어낸 것.
- 검증: `tsc --noEmit` 클린, vitest 14파일/119개 통과, vite build 성공, 잔여 참조 grep 0건.
  기존 저장된 세션의 `lineAudios`/`voiceId`는 로드 시 그냥 무시됨(마이그레이션 없음).

## 관련
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 외부 의존: [[atlas-cloud]] (제네릭 영상 생성 게이트웨이, 카탈로그가 request body 스키마를 기록하지
  않는다는 함정을 이 세션이 처음 문서화)
- 세션: [[2026-09-13-시나리오-영상생성-오디오모델-길이슬라이더-카메라고정]](보이스/오디오 구동
  기능 도입) · [[2026-09-14-돌잔치-ai-기능-보완-장면구조화-tts빌드제거-어드민개편]](같은 기능
  전면 제거, [[dalar-web-first]] 작업 중 파생)
- 스킬: [[parallel-survey-before-feature-gap-analysis]]
