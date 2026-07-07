---
tags: [entity, project, tool, claude-agent-sdk, typescript]
created: 2026-07-07
updated: 2026-07-07
---
# lampas-harness

Claude Agent SDK 기반 웹 하네스. `[[progdesigner]]`의 맥미니에서 데몬으로 돌며,
웹 채팅 UI로 에이전트(`[[lampas]]`)에게 작업을 시키고 큐로 백그라운드 태스크를 처리한다.

- 저장소: `github.com:lampas-dev/lampas-harness` (`main` 브랜치)
- 로컬 경로: `/Users/progdesigner/Works/lampas/lampas-harness`
- 언어/런타임: TypeScript, `@anthropic-ai/claude-agent-sdk@0.3.201`
- 실행: 맥미니, `0.0.0.0:8787` 바인딩, LAN `http://192.168.0.5:8787`
- 인증: `HARNESS_TOKEN` (비-localhost 접속 시 필수), `ANTHROPIC_API_KEY` 직접 키(구독 로그인 아님). OpenAI 키도 보유.
- 권한 모드: 기본 `bypassPermissions`(맥 전체 제어) — 그래서 공개 노출은 위험.

## 아키텍처 (관찰된 범위)

- `src/server.ts` — 웹 채팅 서버. `sessions: Map<chatId, ChatSession>` 인메모리 세션, 트랜스크립트 append(`<logDir>/chats/{chatId}.md`), 사이드카 세션 영속화(`{chatId}.session.json`), 히스토리 jsonl.
- `src/queue.ts` — 큐 스키마/enqueue/status. `daemon.ts`에서 분리됨.
- `src/runner.ts` — 큐 태스크 실행기.
- `harnessctl` — launchd 제어 스크립트 (install/start/stop/restart/status/logs).
- `launchd/io.lampas.harness.daemon.plist`, `...memory-lint.plist` — LaunchAgent 정의.
- 로그: `logs/daemon.out.log`, `logs/daemon.err.log`.

## 주요 기능 (2026-07-06~07 세션에서 구축)

- launchd 데몬화 + 제어 스크립트 → `[[macos-launchd-daemon]]`
- ad-hoc 프롬프트 태스크 큐 + 세션 resume → `[[harness-queue-vs-chat]]`
- 서버 크래시 후 대화 맥락 복구(사이드카) → `[[sdk-session-persistence]]`
- 모델 선택기(Claude+OpenAI, 기본 `claude-opus-4-8`) → `[[model-selection]]`
- 네트워크 재시도/멱등성(msgId dedupe) → `[[idempotent-message-retry]]`
- 메시지별 작업 폴더 샌드박싱 → `[[work-folder-sandboxing]]`
- thinking 스트리밍, 연결 상태 아이콘, 세션 중지, 대화 목록(햄버거)/보관, FIFO 대기 큐.

## 커밋 이력 (이 세션)

| 커밋 | 내용 |
|------|------|
| `3ecc049` | launchd 라벨 com.lampas → io.lampas 리네임, harnessctl 추가 |
| `2a3b6c1` | ad-hoc 태스크 큐 + 세션 resume + 큐 상태 패널 |
| `cc76704` | 채팅 UX 개선 (모델 선택기, 히스토리 영속화, 메시지 큐, 중지/thinking) |
| `3749e8a` | 네트워크 재시도/멱등성 + 큐 패널 내 중지 버튼 이동 |
| `5cb33ae` | 대화별 작업 폴더 선택 + 폴더 밖 접근 차단 |
| `155e641` | 대화 보관, 대기 큐 표시/취소, 대화별 폴더 잠금 |

## 관련
- 세션: [[2026-07-06-lampas-harness-구축]]
- `lampas-system` — 인접 저장소(이 세션과 별개, 미커밋 변경 2개 존재).
