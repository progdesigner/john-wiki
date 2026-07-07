# Index

전체 페이지 카탈로그. ingest/lint 때마다 갱신할 것.

## Sessions

- [[2026-07-06-lampas-harness-구축]] — lampas-harness 데몬화·채팅 UX 하루 집중 구축 세션

## Entities

- [[lampas-harness]] — Claude Agent SDK 기반 웹 하네스 (맥미니 데몬, 큐, 채팅 UI)
- [[lampas]] — 하네스 에이전트의 이름(람파스/Lampas)
- [[progdesigner]] — 개발자·소스 공급자, 맥미니 운용

## Topics

- [[model-selection]] — 하네스 요청별 모델 선택 (기본 claude-opus-4-8, Claude+OpenAI)
- [[harness-queue-vs-chat]] — 채팅(즉시 실행) vs 큐(백그라운드 적재) 두 경로 구분

## Skills

- [[macos-launchd-daemon]] — macOS launchd 데몬화 + 제어 스크립트 (라벨 일치 함정)
- [[sdk-session-persistence]] — SDK 세션 sessionId 사이드카 영속화로 크래시 후 resume 복구
- [[idempotent-message-retry]] — msgId 멱등성 + 자동 재전송으로 network error 소실 방지
- [[work-folder-sandboxing]] — 에이전트를 작업 폴더 안으로 제한 (default 모드 + 경로 가드)
