---
tags: [topic, lampas-studio, ai-video, clip-scoring, product-strategy]
created: 2026-09-26
updated: 2026-09-26
---
# AI Clip Intelligence — 긴 영상→숏폼 자동 제작 파이프라인 구조

`[[progdesigner]]`가 2026-09-18 세션에 붙여넣은 요구사항 문서(제3자 작성으로 보이는 제품 기획서)가
제안한 "긴 영상 → AI 숏폼(Reels/Shorts/TikTok) 자동 제작" 파이프라인 구조와, 이를 `[[lampas-studio]]`
(`lampas-system`)의 스포츠 클립 파이프라인에 실제로 적용한 결과를 정리한다. 세션 상세 →
[[2026-09-18-lampas-clip-intelligence-brand-kit-대량구현]].

## 제안된 파이프라인 (요구사항 문서 원안)

```
SOURCE VIDEO → Content Analysis(ASR/Scene/CV) → AI Clip Intelligence
  (Hook Detection·Highlight Detection·Semantic Analysis·Persona Matching·Clip Scoring)
  → TOP CLIPS → AI Editing Engine(9:16 Reframe·Tracking·Dynamic Caption·Hook·Brand Kit·BGM)
  → REELS/SHORTS → REVIEW/PUBLISH → PERFORMANCE DATA → Clip Intelligence Feedback(순환)
```

**4단계 개발 우선순위(원안)**: Phase 1 MVP(Upload→ASR→Scene→AI Clip Selection→9:16→Caption→Render)
→ Phase 2 Differentiation(Persona→Clip Scoring→Hook→Copy→Brand Kit) → Phase 3 B2B(Workspace→Team→
Brand Kit→Batch→API→Approval) → Phase 4 Growth Engine(SNS Publishing→Performance Tracking→A/B Test→
Performance-based Clip Ranking).

핵심 차별화 포인트로 제시된 것: 클립마다 **Hook/Virality/Information/Emotion/Standalone Completeness**
5축 점수를 매기는 "AI Clip Intelligence Engine", 같은 원본에서 **타겟 페르소나별로 다른 장면을 선택**
(Persona 기반 Reels), 단어별 강조 다이나믹 캡션, Brand Kit(로고·폰트·자막 스타일·인트로/아웃로·CTA·
워터마크), 성과 데이터를 다시 학습해 Clip Selection을 개선하는 피드백 루프.

## 착수 전 갭 분석 (2026-09-18, 4개 병렬 서베이)

구현 착수 전 `lampas-api`(clips/reels/copy/edit-sessions/packaging)·`lampas-agent`(인제스트)·
`lampas-web-edit`·AI 게이트웨이 4영역을 병렬 서베이해 실제로 이미 있는 것과 없는 것을 확정했다
(절차 → [[parallel-survey-before-feature-gap-analysis]]):

| 원안 기능 | 상태 (2026-09-18 착수 시점) |
|---|---|
| 업로드·YouTube 반입 | ✅ 이미 있음 (`ClipSource`, yt-dlp) |
| ASR(단어 타임스탬프) | ✅ 이미 있음 (`lampas-agent`, `xai/stt-v1`) — 단, 저장은 문장 단위로 뭉개짐 |
| 장면 분할 | ✅ 이미 있음 (`sceneDetect.ts`, 프레임 diff 기반, ffmpeg 없음) |
| 훅 점수 | ✅ 이미 있음 (`Clip.hookScore`, 단일 축) |
| 다축 클립 점수(5축) | ❌ 없음 → 이 세션에서 `clip-intelligence` 모듈로 신설 |
| 페르소나 기반 클립 **선택** | ❌ 없음(페르소나는 카피에만 쓰임) → 이 세션에서 부분 구현 후 릴스 UI에서 재단순화(§ 아래) |
| 9:16 Auto Reframe(피사체 추적) | ❌ 없음(수동 크롭/팬만) → 이 세션에서 모션 무게중심 근사로 구현 |
| 단어 단위 다이나믹 캡션 | ❌ 없음(문장 단위만) → 이 세션에서 karaoke 자막으로 구현 |
| 자막 핸드오프(Reels→Edit) | ❌ 알려진 결함(라벨은 넘어가되 자막 드롭) → 이 세션에서 해소 |
| Brand Kit | ❌ 전무(BrandProfile은 텍스트 톤/해시태그뿐) → 이 세션에서 `brand-kits` 모듈 신설 |
| 타입드 Hook 생성기(궁금증/사실/도발) | ❌ 없음(카피 "앵글"은 자유 텍스트) → 트림 결과에 스타일 훅 문구만 부착, 타입 enum화는 안 함 |
| 15/30/60/90초 자동 트림 | ❌ 없음 → 문장 경계 트림으로 구현 |
| SNS 게시·예약 | ✅ 이미 있음(Instagram만, `packaging` 모듈) |
| 성과 데이터 피드백 루프 | ❌ 전무(자사 게시물 인사이트 수집 자체가 없음) → **범위 밖으로 남김** |
| Batch Generation | ❌ 없음 → 릴스 "선별 결과 배치 편집 세션 생성"으로 부분 구현 |

## 실제 구현 결과 — 원안과의 차이

- **5축 점수는 살아남지 못했다.** `clip-intelligence` 모듈이 계산한 훅·확산성·정보성·감정·완결성은
  처음엔 에이전트 라벨링 시점(비전 모델)에도 함께 넣었으나, **다음 날(2026-09-19)** 배치 내 대비
  효과로 채점이 들쭉날쭉하다는 게 드러나 라벨링에서 전부 제거되고, 훅 점수만 텍스트 기반 상대 순위
  엔드포인트(`rank-hooks`)로 대체됐다. 뱅크 UI의 "4축 미니 바"는 라벨링이 아니라 reels의 AI 선별
  (`analyze`) 호출 결과로 채워지는 구조로 정착 — 상세 → [[lampas-agent]] "훅 점수" 절.
- **페르소나 기반 클립 선택도 오래 못 갔다.** 원안처럼 릴스에 페르소나 선택 + AI 선별 패널을 만들었지만,
  바로 다음 사용자 피드백("UI/UX가 너무 복잡해짐")으로 페르소나·카피 *생성* UI 전체가 릴스에서
  제거되고 Copy 앱의 결과를 칩으로 골라 쓰는 방식으로 후퇴했다. "같은 영상을 페르소나별로 다르게
  편집"이라는 원안의 핵심 차별화 지점은 Copy가 만든 페르소나를 편집 그룹 첫 비트에 붙이는 정도로만
  남았고, 클립 후보군 자체를 페르소나별로 분기하는 기능은 구현되지 않았다.
- **Auto Reframe은 얼굴 인식이 아니라 모션 무게중심 근사**로 구현됨 — 원안이 말한 "Ball/Player/Action
  Tracking" 수준의 정밀도는 아니며, 세션 종료 시점 "후속 후보"로 명시적으로 남음.
- **성과 피드백 루프(원안 Phase 4의 핵심)는 착수조차 안 됨** — Instagram Graph 클라이언트 자체가
  `/insights` 호출을 구현하지 않은 상태(서베이 §4 확인)라 전제 조건부터 없다.
- **B2B 요소(Workspace/Team/승인 워크플로)는 이 세션 범위에 아예 없었음** — 원안 Phase 3는 미착수.

## 관련
- 세션: [[2026-09-18-lampas-clip-intelligence-brand-kit-대량구현]]
- 엔티티: [[lampas-studio]] · [[lampas-agent]] · [[lampas-web-edit]] · [[lampas-web-reels]] ·
  [[lampas-web-copy]] · [[lampas-web-clips]]
- 스킬: [[parallel-survey-before-feature-gap-analysis]] · [[asr-long-audio-silent-truncation]]
