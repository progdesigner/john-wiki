---
tags: [topic, claude-agent-sdk, model, config]
created: 2026-07-07
updated: 2026-07-07
---
# 하네스 모델 선택

`[[lampas-harness]]`에서 요청마다 실행 모델을 고르는 방식과 그 배경.

## 배경 — 모델 미고정 문제
코드·잡·`.env`·launchd·`.claude/settings.json` 어디에도 `model`이 지정돼 있지 않았다.
`query()` 옵션에 `systemPrompt: {preset: "claude_code"}`, `settingSources: ["project"]`만 있고 `model`은 없음.
→ SDK가 Claude Code CLI의 **기본 모델 별칭("default")**을 따르므로, CLI 버전/계정에 따라
조용히 다른 모델로 실행돼 비용·성능이 달라질 수 있다(재현성 위험). 코드만 봐서는 ID 확정 불가.

## 해결 — UI 모델 선택기
채팅 입력창 바로 위 드롭다운. 선택값을 `localStorage`에 저장하고 매 요청에 전송.
- 기본값: **`claude-opus-4-8`**
- Claude 전 모델 + OpenAI 라이브 목록(`/api/models`가 계정 접근 가능 모델을 라이브로 반환).

서버가 내려준 Claude 목록(2026-07-07 시점):
```
claude-fable-5, claude-opus-4-8(기본), claude-opus-4-7, claude-opus-4-6,
claude-sonnet-5, claude-sonnet-4-6, claude-haiku-4-5-20251001,
claude-opus-4-1-20250805, claude-opus-4-5-20251101, claude-sonnet-4-5-20250929
```
드롭다운은 페이지 로드 시 `/api/models`를 1회만 받으므로, 목록 갱신엔 브라우저 새로고침 필요.

## 권장
- 최고 성능: `claude-opus-4-8` / 비용 절감: `claude-sonnet-5`.
- 코드 고정이 필요하면 `.env`의 `HARNESS_MODEL`을 chat/runner 양쪽에서 읽게 배선.

## 관련
- [[lampas-harness]] / [[lampas]] / [[2026-07-06-lampas-harness-구축]]
