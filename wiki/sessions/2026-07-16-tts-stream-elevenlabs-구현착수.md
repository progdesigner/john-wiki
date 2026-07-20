---
tags: [session, lampas-harness, elevenlabs, tts, sandbox]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-16 — tts-stream(ElevenLabs) 구현 착수

`[[lampas-harness]]` 채팅에서 AI가 전달한 대화(봇 메시지)를 선택하면 ElevenLabs로 실시간
읽어주는 `tts-stream` 기능을 만들어 달라는 요청. 사용자가 참고 예시로 지정한
`dbs/talk-system`(=`[[toktalk]]`)의 기존 구현은 **작업 폴더 밖이라 끝내 못 봤고**, 어시스턴트가
표준 ElevenLabs 스트리밍 API로 독자 구현을 막 시작한 지점에서 소스가 끝난다 — **완료 여부 미확인**.

## 요청
- 봇 메시지를 선택하면 ElevenLabs TTS로 실시간(스트리밍) 읽어주는 `tts-stream` 기능.
- 참고: `dbs/talk-system`(=`[[toktalk]]`)에 이미 구축된 예시가 있다고 사용자가 명시.

## 조사 결과 (구현 전)
- `.env`에 `ELEVENLABS_API_KEY` 이미 존재. 기존 코드의 `mintVoiceSecret`은 **STT(음성 입력)**용
  ([[2026-07-15-gpt-realtime-음성입력-길게누르기]]의 OpenAI Realtime 토큰 발급과 별개) — TTS(출력)는
  아직 없음.
- 서버(`src/server.ts`)는 커스텀 `http` 서버 + `/api/*` 라우팅으로 **SSE·바이너리 스트리밍 패턴을
  이미 사용 중** → TTS 스트리밍 프록시를 붙이기 적합한 구조로 판단.
- 프론트(`apps/web/index.html`)는 단일 파일, 봇 메시지는 `add("bot", …)` 버블로 렌더 → 여기에
  재생 버튼을 얹는 방향으로 계획.

## 막힌 지점 — 참고 예시(`dbs/talk-system`) 접근 불가
- `dbs/talk-system`은 현재 작업 폴더(`lampas-harness`) 밖이라 [[work-folder-sandboxing]] 가드가
  차단. **읽기 전용 시도(`dangerouslyDisableSandbox` 상당의 우회)도 실패** — 하네스 자체 권한
  가드가 아니라 **Claude Agent SDK 레벨의 폴더 제한**이라고 어시스턴트가 명시적으로 진단.
  → 기존 스킬 [[work-folder-sandboxing]]의 "완전한 OS 샌드박스가 아니다" 서술에, **파일 도구
  차단은 SDK 레벨까지 내려가 우회 불가**라는 디테일을 보강할 근거.
- 결과적으로 예시의 구체 설정(보이스 ID·모델·스트리밍 방식)을 못 보고 진행.

## 결정 — 사용자 무응답으로 합리적 기본값 채택
어시스턴트가 진행 방식을 확정하려 사용자에게 물었으나(02:44:14) 답이 없어(02:44:58), 아래
가정으로 바로 구현 착수:
1. 예시를 못 보므로 **ElevenLabs 공식 스트리밍 API**로 표준 구현.
2. 보이스·모델은 `.env`로 오버라이드 가능하게 — 기본 모델 **`eleven_flash_v2_5`**(저지연 다국어),
   기본 보이스는 다국어 보이스(env `ELEVENLABS_VOICE_ID`로 교체 가능).
3. 나중에 실제 예시 설정을 확인하면 env 값만 바꾸면 되도록 설계.

## 진행 상황 — 소스가 여기서 끝남
"1) server.ts — ElevenLabs 상수 추가"를 시작한다고 밝힌 직후 소스 트랜스크립트가 종료된다.
실제 구현(엔드포인트, 프론트 재생 버튼, 커밋)이 이 세션 안에서 완료됐는지 **확인 불가** —
[[lampas-harness]]에 커밋 이력이나 후속 세션이 나타나면 상태를 갱신할 것.

## 관찰 — 동일 사용자 메시지 반복(6회)
소스 원문에 동일한 요청 텍스트가 02:42:10~02:47:55 사이 총 6회 그대로 등장(뒤 5회는
02:47:12~02:47:55, 약 40초 안에 몰림). 어시스턴트는 이 구간 전체에 답변을 한 번만 남겨
([[idempotent-message-retry]]의 msgId 멱등성이 실행 중복은 막은 것으로 보임) — 다만 원문
트랜스크립트 자체에 중복 항목이 그대로 쌓이는 점은 [[2026-07-16-srkk-인보이스-james확인-0243]]
등에서 이미 기록된 "아카이브/로그 중복 노출" 계열 이상 정황과 유사해 병기해 둔다.

## 관련
- [[lampas-harness]] · [[toktalk]] (`dbs/talk-system`) · [[work-folder-sandboxing]] ·
  [[idempotent-message-retry]] · [[chat-archive-duplication-anomaly]]
- 소스: `raw/conversations/2026-07-16-tts-stream-elevenlabs-구현착수.md`
