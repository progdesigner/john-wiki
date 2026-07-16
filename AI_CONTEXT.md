---
tags: [ai-context, summary]
created: 2026-07-12
updated: 2026-07-16
---
# AI_CONTEXT — 핵심 기억 요약

> 하네스가 매 대화 시작 시 이 파일을 시스템 프롬프트에 주입한다. 40줄 이내 유지 (규칙: CLAUDE.md).

## 사용자
- [[progdesigner]] (John, bacchus.dev@gmail.com) — CWC([[cwc-commerce]]) 소속 개발자·디자이너. **응답은 항상 한국어**(도구의 사람 읽는 필드까지). 기억은 비자명한 것만 저장, 임시 스크립트는 `tools/`·운영은 `scripts/`. 네이버 블로그 `study-ai-what`에 람파스 기억 시스템 공개 연재 → 블로그/SEO 태그 요청 반복([[naver-blog-tag-seo]]).

## 진행 중 프로젝트
- [[lampas-harness]] — Claude Agent SDK 웹 하네스. 맥미니 launchd 데몬(웹 8787/API 3787), 원격 접속은 Tailscale 사설 VPN 권장. 기본 모델 claude-opus-4-8. `.cursor/mcp.json` 외부 MCP를 Claude 세션에 노출([[harness-mcp-bridge]]) — 예 [[naver-blog-mcp]]. 로컬 LLM은 [[rapid-mlx]](launchd 상주). 프로젝트 스킬 시스템+대량 스킬 번들 보유(마케팅·Remotion영상·개발운영, 커밋 ef36c5f).
- [[john-wiki]] — 이 위키. 공통 장기 기억 저장소. 저장 진입점 3개: 수동 🧠 버튼·보관 🗄 자동(2026-07-13~)·야간 자동 ingest. 능동 조회(검색 tool)는 미완.
- [[toktalk]] — 에피소드 beat 플레이·배경 전환 시스템 개발 중.
- 코딩 외 [[harness-as-business-assistant]]: 이메일 대행·인보이스 대조·마케팅 컨설팅(사진 첨부 해석) 반복.
- [[lampas-studio]] — 스튜디오 레퍼런스·Instagram 통합. 로컬 저장소명 `lampas-system`. `pnpm install`이 저장소 전체에서 실패(누락된 `@iileex/shared`, origin/main도 동일 — 기존 이슈).
- `~/Works` 하위 git 저장소 12개 → [[works-project-portfolio]] (다수는 아직 내용 미조사).

## 확정된 결정
- 원격 접속: 공개 노출 회피(하네스가 맥 전체 제어) — Tailscale 사설 VPN 권장, 공유기 포트포워딩 비권장. (근거: 2026-07-06 세션; 당시 실제 설치는 미진행 — 설치 여부·구체 방식은 재확인 필요.)
- 장기 기억은 git markdown 위키(사람이 감사 가능) — SQLite 아님.
- 로컬 LLM은 Rapid-MLX 상주 ([[local-llm-rapidmlx-install]]).
- 사용자 대면 이름은 **"람파스"**로 표기 (내부 식별자 env `HARNESS_*`·저장소명 `lampas-harness`는 유지). → [[lampas]]
- 2026-07-12: 위키 회수(recall) 연결 — AI_CONTEXT.md 상시 주입 + index.md 능동 조회 + 스킬 카탈로그 노출 + 야간 자동 ingest.
- 2026-07-15: 하네스 Claude 실행은 **기본 Claude Code 구독(OAuth) 과금** (API 종량 아님). 대화별 "API 사용" 토글로 전환 가능, 구독 모드는 컨텍스트 잔여율 표시. → [[sdk-claude-code-vs-api-billing]]

## 업무 맥락
- CWC 엘레망 광화문 사무실: [[sylvan-korea]] 공간 공동 사용(전대) 동의 절차 진행 중 (2026-07 기준), 임대인 측 [[dongwon-building]].
