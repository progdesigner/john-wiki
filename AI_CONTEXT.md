---
tags: [ai-context, summary]
created: 2026-07-12
updated: 2026-07-13
---
# AI_CONTEXT — 핵심 기억 요약

> 하네스가 매 대화 시작 시 이 파일을 시스템 프롬프트에 주입한다. 40줄 이내 유지 (규칙: CLAUDE.md).

## 사용자
- [[progdesigner]] (John, bacchus.dev@gmail.com) — CWC([[cwc-commerce]]) 소속 개발자·디자이너. 한국어로 대화한다.

## 진행 중 프로젝트
- [[lampas-harness]] — Claude Agent SDK 웹 하네스. 맥미니 launchd 데몬(웹 8787/API 3787), 원격 접속은 Tailscale 사설 VPN 권장. 기본 모델 claude-opus-4-8.
- [[john-wiki]] — 이 위키. 공통 장기 기억 저장소 (ingest/lint/야간 자동 ingest 잡이 유지).
- [[toktalk]] — 에피소드 beat 플레이·배경 전환 시스템 개발 중.
- [[lampas-studio]] — 스튜디오 레퍼런스·Instagram 통합.

## 확정된 결정
- 원격 접속: 공개 노출 회피(하네스가 맥 전체 제어) — Tailscale 사설 VPN 권장, 공유기 포트포워딩 비권장. (근거: 2026-07-06 세션; 당시 실제 설치는 미진행 — 설치 여부·구체 방식은 재확인 필요.)
- 장기 기억은 git markdown 위키(사람이 감사 가능) — SQLite 아님.
- 로컬 LLM은 Rapid-MLX 상주 ([[local-llm-rapidmlx-install]]).
- 2026-07-12: 위키 회수(recall) 연결 — AI_CONTEXT.md 상시 주입 + index.md 능동 조회 + 스킬 카탈로그 노출 + 야간 자동 ingest.

## 업무 맥락
- CWC 엘레망 광화문 사무실: [[sylvan-korea]] 공간 공동 사용(전대) 동의 절차 진행 중 (2026-07 기준), 임대인 측 [[dongwon-building]].
