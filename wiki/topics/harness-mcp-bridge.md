---
tags: [topic, mcp, claude-agent-sdk, lampas-harness, architecture]
created: 2026-07-13
updated: 2026-07-13
---
# 하네스 MCP 브리지 — Claude 세션에 외부 MCP 노출

`[[lampas-harness]]`의 Claude 세션(웹 채팅 턴)에서도 프로젝트의 `.cursor/mcp.json`에 정의된 **외부 MCP
서버**(naver-blog, threads 등)를 쓸 수 있다. 도구는 다음 턴부터 `mcp__<서버>__<도구>` 이름으로 뜬다.

## 오해 정정

예전엔 이 MCP 브리지가 로컬 모델(OpenAI 호환)용으로만 쓰인다고 보아 **"MCP는 Cursor/claude.ai에서만
된다"고 오해**했다. 실제로는 하네스가 Claude Agent SDK 세션에도 직접 붙일 수 있다.

## 배선

- `src/mcpBridge.ts`의 `externalMcpServers()`가 `.cursor/mcp.json` 설정을 Claude Agent SDK의
  `mcpServers` 규격으로 변환한다.
- `src/server.ts`의 `query({ options.mcpServers })`에 내장 `imagegen`·`scheduler`와 함께 **스프레드**한다.

## 이식 함정 (SDK stdio 설정)

- **`cwd` 필드 없음** — SDK stdio 설정에는 작업 폴더 지정 필드가 없다. 작업 폴더가 필요한 서버는
  `/bin/sh -c 'cd … && exec …'`로 감싼다.
- **launchd 최소 PATH** — 데몬이 최소 PATH로 뜨므로 보강 PATH를 `env`로 넘긴다.
- **코드 변경 후 반영** — 서버 코드/설정을 바꾸면 실행 중 프로세스는 옛 코드를 메모리에 들고 있으므로,
  데몬 재시작(`launchctl kickstart -k …`) 후 새 프로세스에 반영된다. → [[macos-launchd-daemon]]

## 실사용 예
- `[[naver-blog-mcp]]` — 네이버 블로그 자동 발행 MCP(Playwright). 발행 절차: [[naver-blog-mcp-posting]]

## 관련
- [[lampas-harness]] / [[naver-blog-mcp]]
- 서버 라이프사이클(재시작이 왜 필요/위험한지): [[self-hosted-agent-server-ops]] · [[macos-launchd-daemon]]
- 세션: [[2026-07-13-람파스-누적운영기억-이관]]
