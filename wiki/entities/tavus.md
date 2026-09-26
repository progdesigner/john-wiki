---
tags: [entity, ai-provider, avatar, video-call, external, tavus]
created: 2026-09-26
updated: 2026-09-26
---
# Tavus

사진 한 장으로 실시간 대화형 영상 아바타를 만드는 API 서비스. `[[toktalk]]`의 신규 서브앱(`virtual.toktalk.ai`)이
채택한 아바타·대화 엔진. (2026-09-26 세션에서 신설)

## 핵심 컴포넌트

- **CVI (Conversational Video Interface)** — 대화·음성·얼굴 영상을 하나로 연결하는 실시간 영상통화 레이어. 한국어(`ko`) 공식 지원.
- **Phoenix-4 / Phoenix-4.5 (Image-to-Face)** — 사진 한 장 → 아바타 생성. 생성 직후 상태는 `finetune_status: training`이며,
  이 동안 미리보기 영상에 워터마크가 박힌다(사이트 설정으로 제거 불가, [공식 안내](https://docs.tavus.io/sections/faces/phoenix-45-image-requirements) 기준 2~3시간 후 고화질 전환 시 사라짐).
- **Raven** — 사용자 카메라 영상에서 사물·상황을 파악해 대화에 반영(카메라 이해).
- **Sparrow 2** — 자연스러운 말 끊기(대화 중 인터럽션) 처리.
- **메모리** — 같은 사용자 + **같은 대화 에이전트(persona)** 조합에서만 이어짐. 통화 후 자동 축적 + 직접 저장/수정/삭제 API 제공.
  대화 분위기별로 에이전트를 나누면 메모리가 끊기므로, 캐릭터 하나에 에이전트 하나를 유지하고 분위기는 파라미터로 분기해야 한다.
- **웹 검색** — 대화 중 최신 정보 조회 기능, 온/오프 가능.
- 화면 공유·텍스트 대화·자막 설정도 CVI에 포함.

## 확인된 제약 (2026-09-26 시점, `[[progdesigner]]` 계정)

- **사진 아바타 생성(Image-to-Face)이 `402 Payment required`로 거절됨** — Phoenix-4/4.5 공통. 계정에 커스텀 아바타 슬롯(유료
  구독) 활성화가 필요. 기본 제공 아바타로는 결제 없이 통화 가능.
- **통화 최대 길이(Maximum conversation duration)가 요금제로 상한 고정** — 서버가 1,800초(30분)를 요청해도 요금제 상한을
  초과하면 **자동으로 낮춰서 적용**되며 별도 오류 없이 조용히 잘린다. 종료 사유는 `max_call_duration`. 이 계정은 실측 900초(15분)
  상한 확인됨. → [공식 문서](https://docs.tavus.io/sections/conversational-video-interface/conversation/customizations/call-duration-and-timeout)
- 위 두 제약은 **API 키 재발급과 무관** — 요금제·크레딧 활성화 문제.
- ⚠ **호출 횟수 제한("오늘의 이용 한도")은 Tavus가 아니라 `[[toktalk]]` 자체 서버가 건 제한**이었음(혼동 주의) →
  `[[self-imposed-vs-provider-limit-diagnosis]]`

## 라이선스 비교 대상

경쟁 오픈소스 스택(`[[realtime-photo-avatar-voice-ai-landscape]]`)의 SoulX-FlashHead·OpenAvatarChat은 코드·가중치 모두
Apache-2.0으로 자가호스팅 가능하지만, GPU 인프라·통합 작업이 커서 Tavus API 경로가 초기 출시엔 채택됐다.

## HeyGen LiveAvatar (비교 검토, 미채택)

- **FULL 모드**: 음성 인식·대화·합성까지 자체 제공.
- **LITE 모드**: 자체 구성한 대화 AI의 응답 음성만 넣으면 실시간 아바타 영상 생성 — 캐릭터 성격·기억을 직접 관리하고 싶을 때 적합.
- 사진 아바타는 유료 구독의 커스텀 아바타 슬롯 필요(Tavus와 동일한 제약 유형).
- 공식 문서: [사진 아바타](https://docs.liveavatar.com/docs/core-concepts/avatars) · [FULL/LITE 비교](https://docs.liveavatar.com/)

## 관련

- `[[toktalk]]` — Tavus를 적용한 신규 서브앱 `virtual.toktalk.ai`
- `[[realtime-photo-avatar-voice-ai-landscape]]` — 채택 전 비교한 오픈소스·API 대안 전체
- 세션: `[[2026-09-26-virtual-toktalk-tavus-아바타-구축]]`
- 외부 AI 도구군: `[[openai]]` · `[[gemini]]` · `[[grok]]` · `[[elevenlabs]]` · `[[higgsfield]]`
