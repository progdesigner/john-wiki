---
tags: [topic, avatar, realtime-voice, research, open-source]
created: 2026-09-26
updated: 2026-09-26
---
# 실시간 사진 아바타 + 음성 대화 AI 지형도 (2026-09 조사)

"사진 한 장을 보여주면 실시간으로 말하는 AI"를 만들려는 `[[toktalk]]` `virtual.toktalk.ai` 요구사항으로
조사된 오픈소스·API 후보 비교. 두 축으로 나뉜다: **① 사진을 말하게 하는 아바타 엔진**과
**② 카메라를 보며 음성 대화하는 멀티모달 AI**. 실제 서비스에는 대부분 두 축을 조합해야 한다.

> 조사 시점 2026-09-25, 검색엔진 차단으로 후반 조사가 제한됨 — "전 세계 최신"을 보장하지 않음.

## 오픈소스 (GPU 자가호스팅)

| 프로젝트 | 축 | 공개 시점 | 라이선스 | 비고 |
|---|---|---|---|---|
| [OpenAvatarChat](https://github.com/HumanAIGC-Engineering/OpenAvatarChat) | 통합 프레임워크(STT·대화·TTS·아바타) | v0.6.0, 2026.04 | Apache-2.0 | 프론트·백엔드 분리, 말끊기, FlashHead 통합 지원 |
| [SoulX-FlashHead](https://github.com/Soul-AILab/SoulX-FlashHead) | ① 사진+음성→실시간 talking head | 2026.02.12 코드·가중치, 2026.03 스트리밍 데모 | Apache-2.0 | Lite: RTX 4090 최대 96FPS 또는 3스트림 동시 25FPS+. Pro: RTX 4090 ~10.8FPS, 실시간 25FPS+엔 RTX 5090×2 필요 |
| [MiniCPM-o 4.5](https://huggingface.co/openbmb/MiniCPM-o-4_5) | ② 카메라+듣기+말하기 동시 | 2026.02 웹데모 | — | 공식 음성 대화가 **영어·중국어 중심** — 한국어 서비스 부적합 |
| [Qwen3-Omni](https://github.com/QwenLM/Qwen3-Omni) | ② 사진·영상·음성 이해+음성 응답 | 2025.09.22 | — | **한국어 음성 입출력 공식 지원** — 한국어 서비스의 유력 후보 |
| [Qwen3-TTS](https://github.com/QwenLM/Qwen3-TTS) | 한국어 음성 생성·목소리 복제 | 2026.01.22 | — | 스트리밍은 vLLM-Omni 권장(공식 기본 패키지는 데모·배치 중심). 홍보 수치 97ms는 서비스 전체 지연이 아님 |
| [LiveTalking](https://github.com/lipku/LiveTalking) | ① 대안 | — | — | WebRTC·중단 처리·다중 세션 지원 |
| [MuseTalk 1.5](https://github.com/TMElyralab/MuseTalk) | ① 대안 | — | — | 입 모양 동기화 특화 |

**GPU 자가호스팅 추천 조합**: `OpenAvatarChat(프레임워크) + Qwen3-Omni(한국어 대화) + SoulX-FlashHead Lite(아바타)`.
단, Qwen3-Omni 연결·음성 청크 전달·영상 동기화는 전부 통합 작업이 필요하며, OpenAvatarChat의 기존 Qwen-Omni
지원이 Qwen3-Omni 완전 호환을 보장하지 않는다.

## API (자가호스팅 없이)

| 서비스 | 특징 |
|---|---|
| **`[[tavus]]` CVI** | 사진→아바타(Phoenix-4/4.5) + 실시간 음성대화(CVI) + 카메라 이해(Raven), 한국어 공식 지원 — **두 축을 한 서비스로 해결** |
| **HeyGen LiveAvatar** | 사진 아바타 + 립싱크. FULL(자체 대화 AI 포함)/LITE(외부 대화 AI 응답 음성만 입력) 두 모드 |
| **OpenAI Realtime + 별도 아바타 API** | 대화 로직(캐릭터·기억·도구)을 세밀히 제어하고 싶을 때, 얼굴 영상은 별도 조달 |

`virtual.toktalk.ai`는 **Tavus API 경로**를 채택 — 초기 출시엔 GPU 인프라·통합 리스크가 없는 API가 유리하다는 판단.
→ 실제 구축 결과와 제약사항은 `[[tavus]]` · `[[2026-09-26-virtual-toktalk-tavus-아바타-구축]]`

## 검증 기준 (실제 구현 시 체크리스트)

FPS·공식 발표 수치가 높아도 서비스 체감 지연과는 다르다. 실제로 측정해야 하는 항목:
- 한국어 인식 정확도·발음 자연스러움
- 사용자 발화 후 **첫 음성**·**첫 영상**까지 지연
- 입 모양 동기화(lip-sync) 품질
- 사용자가 끼어들 때(바지인) 대기 중인 음성·아바타 영상까지 함께 취소되는지

## 관련

- `[[tavus]]` — 채택된 API, 실제 제약(결제 게이트·통화시간 상한) 확인 완료
- `[[toktalk]]` — 적용 제품(`virtual.toktalk.ai`)
- 세션: `[[2026-09-26-virtual-toktalk-tavus-아바타-구축]]`
