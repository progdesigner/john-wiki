---
tags: [entity, ai-provider, elevenlabs, tts, external]
created: 2026-08-31
updated: 2026-08-31
---
# ElevenLabs

음성 합성(TTS)·음성 관련 외부 AI 프로바이더. [[toktalk]]의 음성 스택 구성 요소이자
[[lampas-harness]]의 `tts-stream`(봇 메시지 실시간 읽어주기) 시도의 기반. (2026-08-31 lint 신설)

## 어디에 쓰이는가

- **[[toktalk]] 스택** — AI 음성 계층에 OpenAI·Grok·DeepSeek·[[gemini]] 등과 함께 포함(음성/영상은
  별도로 Agora).
- **[[lampas-harness]] 음성 입력(STT) 자격** — `.env`에 `ELEVENLABS_API_KEY` 존재. 기존 코드의
  `mintVoiceSecret`은 **STT(음성 입력)용**이었다(현행 STT 실경로는 [[openai]] Realtime `gpt-4o-transcribe`).
- **`tts-stream` — 봇 메시지 실시간 읽어주기(구현 착수, 완료 미확인)** — 봇 메시지를 선택하면 ElevenLabs로
  실시간 스트리밍 TTS. 참고 예시(`dbs/talk-system`=[[toktalk]])가 작업 폴더 밖이라 읽지 못해, 표준
  ElevenLabs 스트리밍 API로 독자 구현 착수. 기본 모델 **`eleven_flash_v2_5`**(저지연 다국어), 보이스·모델은
  `ELEVENLABS_VOICE_ID` 등 env로 오버라이드 가능. "server.ts 상수 추가" 직후 소스가 끊겨 **완료 여부
  미확인** → 세션 [[2026-07-16-tts-stream-elevenlabs-구현착수]].

## ⚠ Web Speech API와 혼동 주의

하네스의 실제로 배포된 TTS 3중 트리거(롱프레스 자동재생·봇 말풍선 클릭 읽어주기)는 모두 브라우저 내장
**Web Speech API**(`speak()`/`stopSpeech()`, ko-KR, 서버비용 0) 기반으로 **확정**됐으며 ElevenLabs
`tts-stream`과는 **무관**하다 → [[2026-07-16-봇메시지-클릭읽어주기-구현]] · [[2026-07-16-롱프레스설정-자동음성재생-구현]].
ElevenLabs 방식을 무료·경량 대안으로 대체했는지, 병행 중인지는 미확정.

## 자격증명
- `ELEVENLABS_API_KEY`가 하네스 `.env`에 존재. 2026-07-17 평문 노출 사고에 포함 →
  [[secrets-plaintext-exposure-pattern]] · 세션 [[2026-07-17-env읽기-pwa세이프에어리어-여백수정]].

## 관련
- [[openai]] · [[gemini]] · [[higgsfield]] (외부 AI 도구군)
- [[toktalk]] · [[lampas-harness]]
