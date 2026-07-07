---
name: sdk-session-persistence
description: Claude Agent SDK 서버가 죽어도 대화 맥락(resume)이 이어지도록 sessionId를 디스크에 영속화
created: 2026-07-07
tags: [claude-agent-sdk, persistence, session, resume]
---
# Claude Agent SDK 세션 영속화 (크래시 후 resume 복구)

## 언제 쓰는가
Agent SDK로 만든 채팅 서버가 재시작/크래시 후에도 **에이전트의 대화 기억**을 이어가야 할 때.
트랜스크립트(글자)는 남는데 "이어하기"가 끊기는 증상.

## 문제의 본질
서버가 `chatId → sdkSessionId` 매핑을 **인메모리 Map에만** 들고 있으면
(`const sessions = new Map<string, ChatSession>()`), 서버가 죽는 순간 통째로 사라진다.
재시작 후 클라이언트가 같은 `chatId`를 보내도 서버엔 `sdkSessionId`가 없어 `resume: undefined`
→ 새 SDK 세션 시작 → 에이전트가 이전 대화를 기억 못 함.
(트랜스크립트 `.md`는 매 턴 `appendFileSync`로 디스크에 남으므로 글자 자체는 안 사라진다 — 별개 문제.)

## 절차 (단계별)
1. **사이드카 저장** — SDK가 `init` 메시지로 세션 id를 줄 때 `logs/chats/{chatId}.session.json`에 기록.
   - **resume 시 SDK가 새 id를 발급**하므로, id가 바뀔 때마다 최신값으로 덮어쓸 것.
2. **lazy 복구** — 새 요청 처리 시 인메모리 Map에 세션이 없으면(첫 대화 또는 서버 재시작) 사이드카 파일에서 읽어옴.
3. **resume 주입** — 세션 생성 시 그 값을 `query()`의 `resume` 옵션에 넣는다.
   - SDK 자체 세션 기록은 `~/.claude/projects/...`에 남아 있어, id만 복구하면 resume이 실제 동작한다.

## 검증 (이 방식으로 확인함)
1. "비밀 단어는 바나나" 전달 → 사이드카에 sdkSessionId 기록.
2. 서버 kill(인메모리 Map 소멸 = 크래시 재현) → 재시작.
3. 같은 chatId로 "비밀 단어 뭐였지?" → **"바나나"** 응답. ✅

## 주의사항 / 함정
- 브라우저 화면 히스토리 소실은 또 다른 층위 — jsonl 블록 단위 저장 + 세션 자동 복구로 별도 처리.
- 트랜스크립트/세션id/브라우저히스토리는 서로 다른 3개 층위다. "히스토리가 사라진다"는 증상은 어느 층인지 먼저 구분할 것. → [[harness-queue-vs-chat]]

## 출처: [[2026-07-06-lampas-harness-구축]] ([[lampas-harness]])
