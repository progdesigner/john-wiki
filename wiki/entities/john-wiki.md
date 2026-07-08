---
tags: [entity, project, tool, knowledge-base, long-term-memory]
created: 2026-07-09
updated: 2026-07-09
---
# john-wiki

`[[progdesigner]]`의 **공통 장기기억 저장소이자 개인 위키**. karpathy/llm-wiki 패턴을 따른
markdown 3계층 지식베이스(`raw/` 원본, `wiki/` LLM 작성 페이지, `index.md`/`log.md`).
LLM이 작성·유지하고 사람은 소스를 공급·질문한다.

- 로컬 경로: `/Users/progdesigner/john-wiki`
- 구조: `raw/conversations/`(원본, 읽기전용) · `wiki/{sessions,entities,topics,skills}/` · `index.md` · `log.md`
- 규칙: 모든 페이지 markdown + YAML frontmatter(`tags`/`created`/`updated`), 페이지 참조는 `[[위키링크]]`, 파일명 kebab-case, 세션·대화는 `YYYY-MM-DD-` 접두사. (상세는 저장소 CLAUDE.md)

## 역할 (2026-07-08 설계 세션에서 명문화)

`[[long-term-memory-architecture]]`에서 john-wiki는 저장 계층의 핵심이다:

- **"기억에 저장" 아이콘** = 안 변하는 선호·규칙 (짧게)
- **john-wiki** = 실제 장기 작업 이력·프로젝트 기록 (김)
- **`AI_CONTEXT.md`**(제안됨) = 매 대화 자동 주입되는 요약
- **`[[lampas-harness]]` memory provider** = LLM이 john-wiki를 검색/읽기로 조회하는 연결부

## 현황

- 2026-07-06·07-08 세션이 ingest되어 `[[lampas-harness]]`·`[[lampas-studio]]` 관련 페이지가 축적됨.
- 2026-07-08 세션에서 "이 위키를 harness에 장기기억 provider로 붙이자"는 설계가 도출됨 → 이 위키의 존재 이유가 그 설계의 실현. → [[2026-07-08-장기기억-provider-연동-설계]]
- **주의**: 2026-07-08 세션이 제안한 `AI_CONTEXT.md`는 아직 이 저장소에 없다(제안 상태). 이 위키는 대신 `CLAUDE.md`+`index.md`가 그 역할을 겸한다.

## 관련
- 소유자·사서 소스: [[progdesigner]]
- 연동 대상 하네스: [[lampas-harness]]
- 토픽: [[long-term-memory-architecture]]
- 스킬: [[wiki-memory-provider-integration]]
