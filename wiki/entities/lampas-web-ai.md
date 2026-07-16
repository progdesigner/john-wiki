---
tags: [entity, app, lampas-studio, react, chat, ai]
created: 2026-07-16
updated: 2026-07-16
---
# lampas-web-ai ("Lampas AI", 대화형 AI 스튜디오)

`[[lampas-studio]]` 모노레포(`apps/lampas-web-ai`)의 앱. 챗봇 대화만으로 액터/제품(Object) 생성과 스튜디오
촬영을 끝내는 React + Vite + TypeScript 앱. **2026-07-15부터 `[[progdesigner]]`가 주요 앱으로 지정** —
이전엔 CLAUDE.md에 "AI 웹 클라이언트" 한 줄로만 취급됐음.

- 배포: `https://ai.lampas.io` (S3 + CloudFront), 배포는 `./scripts/deploy-web.sh lampas-web-ai` →
  [[deploy-sandbox-pnpm-shim]].
- 규모는 작음(소스 16개 파일, 약 4,900줄)이지만 로직 밀도가 높고, 핵심의 절반 이상이 `src/chat/actorFlow.ts`
  한 파일(2,658줄)에 있음 — **단일 거대 파일이라 플로우 추가·수정 시 충돌 위험**. 기능이 계속 늘어나면
  플로우별 파일 분리 검토 필요(2026-07-15 세션 권고, 미해결).

## 구조

```
src/
├── chat/            # 앱의 두뇌
│   ├── actorFlow.ts   # 3개 유한 상태 머신 (2,658줄)
│   ├── store.tsx      # ChatContext — 세션·flow 상태 관리
│   └── types.ts
├── components/      # ChatView, MessageBubble, GalleryPanel, Sidebar, LoginScreen
├── contexts/        # AuthContext (Google OAuth)
└── lib/             # api.ts (lampas-api 클라이언트), auth.ts, image.ts
```

lampas-web-sdk와 달리 Atomic Design·variant 체계를 쓰지 않는 별도 구조.

## 핵심 동작 방식

- `actorFlow.ts`는 `type`+`step` 문자열로 단계를 전이하는 **상태 머신 3개**:
  - **액터 만들기**: 이름→성별→나이→인종→외모→확인→프로필 생성→레퍼런스 시트→등록. 이름 입력 시
    Grok(`analyze-creation-input`)이 여러 필드를 한 번에 추출해 이미 채워진 질문은 건너뜀.
  - **Object 만들기**: 제품 사진 업로드→특성 자동 분석(Gemini Vision, `/api/objects/analyze-reference`)→
    이름→확인→프로필. `ObjectFlowState.data`에 SDK와 동일한 6필드(category/material/primaryColor/style/
    background/description).
  - **촬영하기**: 대상 선택(액터/Object/둘 다)→스타일 또는 레퍼런스 검색→5그룹 프롬프트 분석(Object는
    단일 `customPrompt`)→촬영→자연어 재수정/재촬영/편집.
- 취소·긍정·부정 같은 짧은 응답은 정규식(`AFFIRM_PATTERN`/`NEGATIVE_PATTERN`)으로 즉시 처리해 LLM 왕복 절약.
- 상태 관리는 reducer 없이 Context+`useState`. 대화는 사용자별 localStorage(`lampas_ai_chats:{userId}`,
  최대 50개)에 저장, base64 이미지는 저장 전 제거.
- 인증: Google OAuth idToken → `POST /api/auth/google` → lampas-api 자체 JWT 발급.
- 이미지 생성: 액터 촬영만 잡 생성 후 2.5초 간격 폴링(최대 10분), 나머지는 동기 응답(최대 5분).
- 옵션 칩은 `kind: 'options'` 메시지로 append(`MessageBubble.tsx`). 프롬프트 직접수정은
  `kind: 'prompt-editor'` + `promptGroups` — 필드별 textarea + 적용 버튼, 여러 기능에서 재사용됨.
  `store.tsx`의 `lockStaleOptions`가 새 입력마다 미해결 옵션/에디터 메시지를 `optionsResolved: true`로 잠가
  항상 최신 칩만 유효하게 함.

## 2026-07-15 세션에서 배송된 기능 3종 → [[2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계]]

1. **Object 생성 프롬프트 항목별 수정** — SDK의 6필드 프롬프트 분할 구조를 그대로 이식. `object-confirm`/
   `object-profile-review` 단계에 "✏️ 프롬프트 수정" 칩, 이미 있던 `prompt-editor` 컴포넌트 재사용(신규
   컴포넌트·API 불필요). 커밋 `05cda59`.
2. **Object 촬영 샷 변경** — `shoot-review` 단계에 "📷 샷 변경" 칩, 기존 샷 추천 플로우로 재진입. 커밋
   `77e1b69`.
3. **채팅 되돌리기(undo) 2종** — ① "이전"/"뒤로"/"되돌리기"/"undo" 입력 시 직전 사용자 입력 시점으로 복귀,
   ② 사용자 메시지 옆 ✏️ 버튼으로 그 메시지 시점부터 재시작(원문 입력창 프리필). 사용자 입력마다 플로우
   상태 스냅샷을 메시지에 저장하는 방식(`store.tsx`). base64 이미지는 스냅샷에서 제거되므로 새로고침 후
   사진 단계 복귀 시 재업로드 필요. 배포 전 대화엔 스냅샷 없어 ✏️ 미표시. 커밋 `4e0fe77`.

## 관련
- [[lampas-studio]] (상위 제품) · [[deploy-sandbox-pnpm-shim]]
- 세션: [[2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계]] · [[2026-07-16-lampas-web-product-신규앱-구현]](슬림 베이스로 재사용)
- [[progdesigner]] · [[lampas]] on [[lampas-harness]]
