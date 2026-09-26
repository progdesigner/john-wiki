---
tags: [session, toktalk, tavus, avatar, realtime-voice, codex]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-26 — virtual.toktalk.ai: Tavus 기반 사진 아바타 실시간 영상통화 구축

Tool: codex, 작업 디렉터리: `/Users/progdesigner/Works/lampas/lampas-system` (`[[lampas-studio]]` 저장소). 하루 만에 리서치 → 구축 → 배포 → 3차례 버그 수정까지 이어진 장기 세션(2026-09-25~26).

## 요청 배경

`virtual.toktalk.ai`에 "사진을 보여주면 실시간으로 말하는" AI 캐릭터 영상통화를 만들고 싶다는 요청. `[[toktalk]]` 저장소의 신규 서브앱으로, 기존 `talk-web-app`(캐릭터 텍스트/보이스 챗)과는 별개의 "라이브 영상통화" 제품.

## 1단계 — 오픈소스 리서치 (GPU 자가호스팅 경로)

두 기능을 분리해 조사: **사진을 말하게 하는 아바타 엔진**과 **카메라를 보며 대화하는 멀티모달 AI**.

| 후보 | 역할 | 판단 |
|---|---|---|
| [OpenAvatarChat](https://github.com/HumanAIGC-Engineering/OpenAvatarChat) | STT·대화·TTS·아바타 연결 프레임워크 (v0.6.0, Apache-2.0) | 서비스 개발 기반 추천 |
| [SoulX-FlashHead](https://github.com/Soul-AILab/SoulX-FlashHead) | 사진+음성 → 실시간 talking head (Lite/Pro, Apache-2.0) | 사진 아바타 엔진 추천. Lite: RTX 4090 최대 96 FPS. Pro: RTX 4090 ~10.8 FPS, 실시간엔 5090×2 필요 |
| [MiniCPM-o 4.5](https://huggingface.co/openbmb/MiniCPM-o-4_5) | 카메라 보며 동시 듣기·말하기 | 음성 공식 지원이 영어·중국어 중심 — 한국어 서비스엔 부적합 |
| [Qwen3-Omni](https://github.com/QwenLM/Qwen3-Omni) | 사진·영상·음성 이해 + 음성 응답 (2025-09-22 공개) | 한국어 음성 입출력 공식 지원 — 유력 후보 |
| [Qwen3-TTS](https://github.com/QwenLM/Qwen3-TTS) | 한국어 음성 생성·목소리 복제 (2026-01-22) | 스트리밍은 vLLM-Omni 안내, 97ms는 데모 수치일 뿐 서비스 전체 지연 아님 |
| [LiveTalking](https://github.com/lipku/LiveTalking) / [MuseTalk 1.5](https://github.com/TMElyralab/MuseTalk) | 기존 대안 (WebRTC·다중세션 / 입모양 동기화 특화) | 참고용 |

추천 조합: `OpenAvatarChat + Qwen3-Omni(한국어) + SoulX-FlashHead Lite`. 다만 통합 작업량이 크고 (Qwen3-Omni 연결·음성 청크·영상 동기화 미검증), GPU 자가호스팅이 필요해 초기 출시엔 비현실적으로 판단.

## 2단계 — API 경로로 선회, Tavus 채택

"API로 안 되나?"라는 후속 질문에 Tavus·HeyGen LiveAvatar·OpenAI Realtime을 비교:

| 방식 | 가능한 기능 |
|---|---|
| **Tavus CVI** | 사진→아바타 + 실시간 음성대화 + 카메라 이해, 전부 한 서비스 |
| HeyGen LiveAvatar | 사진 아바타 + 립싱크. LITE 모드로 외부 대화 AI 연결 가능 |
| OpenAI Realtime + 별도 아바타 API | 대화 로직 세밀 제어, 조합 작업 필요 |

**Tavus를 채택** — Phoenix-4/4.5 Image-to-Face(사진→아바타), CVI(대화·음성·영상 통합), Raven(카메라 인식), 한국어(`ko`) 공식 지원이 결정적이었다. → `[[tavus]]`

## 3단계 — 구축·배포 (당일)

- 신규 서브앱으로 구성(기존 `talk-web-app`/`talk-api`와 분리), 기존 TokTalk 로그인 재사용.
- 첨부 인물 사진으로 기본 캐릭터 **"하루"** 등록 → **Tavus 사진 아바타 생성이 `402 Payment required`로 거절** (계정에 사진 아바타 크레딧/결제 미활성화). 기본 제공 아바타로는 한국어 통화 정상 동작 확인.
- 브라우저 종단 검증: 영상·음성 수신, 카메라 on/off, 사진 공유(선택 이미지를 영상 트랙으로 전송해 AI가 보고 답함), 통화 종료 시 미디어 해제 — 모두 확인.
- **배포**: `virtual.toktalk.ai`.

## 4단계 — 버그: 새 아바타 학습 완료 후에도 대화 버튼이 안 열림

원인: Tavus가 새로 생성한 아바타 상태를 `training`으로 반환하는데, 프론트가 `started` 상태만 감시하고 있어 학습 완료를 감지 못함. 학습 진행률 표시 + 완료 시 대화 버튼 자동 활성화로 수정·배포·검증.

## 5단계 — 캐릭터 교체: "한소연"으로 전면 전환

- 두 번째 첨부 사진을 imagegen 스킬로 편집(정면 구도·배경 제거, 원본 얼굴·의상 유지) → [편집 프롬프트·결과 경로](/Users/progdesigner/Works/lampas/lampas-system/docs/talk/virtual-han-soyeon-image.md)
- 기존 캐릭터(하루 등) 삭제 기능 추가.
- 시작 화면을 "한소연" 단일 선택으로 축소, 대화 화면은 영상이 화면을 꽉 채우도록(상단 메뉴 숨김, 컨트롤은 하단에 작게) 재구성.
- 로그인 타이밍 race condition(첫 화면 로딩 중 빠르게 로그인하면 상태가 꼬임) 발견·수정.
- 모바일 전체화면 포함 재검증 후 배포.

## 6단계 — 운영 중 3가지 "왜 이래?" 질문과 원인 진단 (모두 Tavus 문제 아님)

| 증상 | 실제 원인 | 조치 |
|---|---|---|
| "오늘의 이용 한도 도달" (유료 결제했는데도) | **서비스 자체**에 설정한 계정당 3회/사이트 전체 30회 하루 제한(`VIRTUAL_USER_DAILY_CALL_LIMIT`/`VIRTUAL_DAILY_CALL_LIMIT`). Tavus 결제와 무관, 연결 실패도 차감 포함, 자정(KST) 초기화 | 100회/1,000회로 상향·재배포 |
| 미리보기에 워터마크 표시 | Tavus 쪽 진짜 제약 — `finetune_status: training`(고화질 학습 미완료) 아바타에 실제로 박히는 표시, 사이트 설정으로 끌 수 없음. [공식 안내](https://docs.tavus.io/sections/faces/phoenix-45-image-requirements) 기준 완료까지 2~3시간 | 안내만, 학습 완료 대기 |
| 최대 30분 설정했는데 15분에 끊김 | 서버는 Tavus에 1,800초를 요청했지만, **Tavus 요금제의 실제 상한(Maximum conversation duration)이 초과 요청을 자동으로 낮춤** — 종료 사유 `max_call_duration`, 실측 정확히 900초 | 요청값을 15분(900초)으로 맞춰 재배포(요금제 상한에 맞춤) |

이 구분(자체 서버 env 제한 vs Tavus 실제 제약)이 이 세션의 핵심 반복 패턴 → `[[self-imposed-vs-provider-limit-diagnosis]]`

## 7단계 — Tavus 기능 최대 활용 (메모리·웹검색 등)

사용자 요청: "메모리 저장 기능 활용, tavus.io 기능 최대한 이용." 조사 결과 Tavus 메모리는 **같은 사용자 + 같은 대화 에이전트(persona)**에서만 이어짐 — 기존엔 대화 분위기별로 에이전트가 분리돼 있어 메모리가 끊겼음. **한소연 하나의 에이전트로 통합**하고 분위기는 통화별 파라미터로만 분기하도록 변경.

추가·확인된 Tavus 기능:
- **메모리**: 통화 후 자동 축적 + 직접 저장/수정/삭제 (실제로 "민트차를 좋아한다" 기억 후 답변에 반영 확인, 두 번째 통화·분위기 전환 후에도 이어짐 확인)
- **웹 검색**: 대화 중 최신 정보 조회
- **화면 공유·텍스트 대화·자막 설정**
- **Sparrow 2**(자연스러운 말 끊기) · **Raven 1**(사진/화면 인식)
- 메모리·웹검색 각각 끄는 설정, 관련 테스트 27개 통과 후 배포
- 문서·PPT·외부 서비스 연동은 아직 미적용 → [전체 적용 범위](/Users/progdesigner/Works/lampas/lampas-system/docs/talk/virtual-tavus-features.md)

## 관련

- 엔티티: `[[tavus]]`(신규) · `[[toktalk]]`(갱신 — `talk-web-virtual`/`talk-api-virtual` 추측이 이 세션으로 확정)
- 토픽: `[[realtime-photo-avatar-voice-ai-landscape]]`(오픈소스·API 조사 결과, 신규)
- 스킬: `[[self-imposed-vs-provider-limit-diagnosis]]`(신규)
- 이전 세션과의 관계: `[[2026-09-25-lampas-web-fit-구축-배포]]`의 `AGENTS.md` 스냅샷이 처음 언급했던 `talk-web-virtual`(포트 8247)·`talk-api-virtual`(포트 3247) 추측이 이 세션에서 실제 구축·배포로 확인됨.
