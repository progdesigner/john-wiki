---
tags: [session, lampas-harness, handoff, codex, claude-agent-sdk]
created: 2026-10-03
updated: 2026-10-03
---

# 2026-10-03 — lampas-harness Codex↔Claude 작업 넘기기(handoff) 기능 구현

`Tool: claude` 세션, `[[lampas-harness]]` 저장소(`/Users/progdesigner/Works/lampas/lampas-harness`),
10:34:25Z 시작. 소스: `raw/conversations/2026-10-03-lampas-harness-codex-claude-핸드오프-구현.md`
(원본 `982b8bdc-e120-46f7-806e-ffd91037795d.md`).

## 요청
"기존 대화에서 작업하던 내용을 codex > claude 또는 claude > codex 로 작업을 이어갈 수 있게 보내는
기능을 만들어줘" — 같은 하네스 안에서 돌아가는 두 CLI 코딩 도구(Codex, Claude Code) 사이에 진행
중이던 작업을 넘기는 기능 요청.

## 구현
- 람파스 터미널 세션 구조(세션 생성·재개·트랜스크립트 추출)를 먼저 파악한 뒤 바로 구현.
- **세션 헤더에 버튼 추가**: Codex 세션엔 "Claude로 이어가기", Claude 세션엔 "Codex로 이어가기".
- **동작**: 누르면 원본 대화 기록(사용자·도우미 메시지만, 도구 호출 결과는 제외)을
  `<작업 폴더>/.lampas-attachments/handoff-<uuid>.md`로 저장 → 반대 도구의 새 세션을 **같은 작업
  폴더·제목·권한 모드·브라우징 유형**으로 시작 → 화면이 새 세션으로 전환.
- 새 세션 첫 프롬프트(CLI 위치 인자로 전달: `codex "<prompt>"`, `claude --session-id … "<prompt>"`)는
  "기록 파일을 읽고 작업 폴더의 현재 상태를 확인한 뒤, 된 일과 남은 일을 정리하고 이어서 진행하라"는
  지시.
- 원본 세션은 종료하지 않고 그대로 유지(필요 없으면 사용자가 직접 보관).
- 테스트 90개 전체 통과. **실제 CLI 구동 검증은 못 함**(이 셸 PATH에 `codex`/`claude` 바이너리 없음).
  빌드(`npm run build:server && npm run build:web`)와 서버 재시작은 수행하지 않고 사용자에게 위임
  (자기 턴에서 재시작하면 응답 채널이 끊길 수 있다는 기존 운영 원칙 → [[self-hosted-agent-server-ops]]
  함정 2).

## 알려진 한계 (어시스턴트가 명시)
- 도구 호출 결과는 넘어가지 않는다 — 기록은 대화 텍스트뿐이라 새 세션이 `git status` 등으로 현재
  상태를 직접 재확인하도록 프롬프트에 포함시켰다.
- 모델 설정은 넘어가지 않는다 — 도구가 달라서 새 세션은 기본 모델로 시작.
- **트랜스크립트가 바인딩되지 않은 Codex 세션은 넘길 수 없다** — 대화가 아직 없는 세션과 동일하게
  오류만 뜨고 새 세션은 생성되지 않는다.
- 넘김 파일은 자동으로 지워지지 않는다 — 넘길 때마다 `.lampas-attachments`에 하나씩 누적.
- 테스트는 가짜 CLI로 인자 전달 형태만 확인(실제 바이너리 구동 미검증).

## 변경 파일
- `src/terminalSessions.ts` — `handoffTerminal()` 추가, `startTerminal`에 첫 프롬프트 인자, 세션에
  `handoffFrom` 기록.
- `src/server.ts` — `POST /api/terminals/:id/handoff`, `capabilities.handoff`.
- `apps/web/public/index.html`, `terminal.js` — 버튼과 핸들러.
- `tests/terminal-handoff.test.ts` — 신규 테스트.

## 부수 사항
- 테스트 중 기존 실패 여부를 비교하려고 `git stash`/`pop`을 한 번 사용 — 변경은 모두 복원됐으나,
  같은 작업 트리를 편집 중인 다른 세션이 있었다면 그 순간 파일이 잠깐 되돌아갔을 수 있다는 점을
  스스로 경고.
- 작업 트리에 미커밋 상태로 남아 있던 `src/branding.ts` 등 branding 변경도 함께 빌드에 포함됨을 안내.

## 후속
- 사용자가 "재시작해줘" 요청으로 세션 종료 — 실제 빌드·재시작·실기기 검증 결과는 이 소스만으로는
  미확인 (다음 세션에서 반영 여부 확인 필요).

## 크로스레퍼런스
- 엔티티: [[lampas-harness]] (신규 절 "Codex↔Claude 작업 넘기기" 추가)
- 스킬(신규): [[cli-tool-handoff-via-transcript-file]]
- 관련이나 다른 패턴: [[cross-subdomain-session-handoff]](웹 서비스 간 **로그인 세션** 이어받기,
  이번 기능은 **대화 맥락**을 파일로 넘기는 것이라 별개 패턴)
- 운영 원칙: [[self-hosted-agent-server-ops]] (자기 턴에서 재시작 안 함)
