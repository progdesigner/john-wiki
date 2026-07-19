---
tags: [entity, project, tool, knowledge-base, long-term-memory]
created: 2026-07-09
updated: 2026-07-19
---
# john-wiki

`[[progdesigner]]`의 **공통 장기기억 저장소이자 개인 위키**. karpathy/llm-wiki 패턴을 따른
markdown 3계층 지식베이스(`raw/` 원본, `wiki/` LLM 작성 페이지, `index.md`/`log.md`).
LLM이 작성·유지하고 사람은 소스를 공급·질문한다.

- 로컬 경로: `/Users/progdesigner/Works/john-wiki`
- 구조: `raw/conversations/`(원본, 읽기전용) · `wiki/{sessions,entities,topics,skills}/` · `index.md` · `log.md`
- 규칙: 모든 페이지 markdown + YAML frontmatter(`tags`/`created`/`updated`), 페이지 참조는 `[[위키링크]]`, 파일명 kebab-case, 세션·대화는 `YYYY-MM-DD-` 접두사. (상세는 저장소 CLAUDE.md)

## 역할 (2026-07-08 설계 세션에서 명문화)

`[[long-term-memory-architecture]]`에서 john-wiki는 저장 계층의 핵심이다:

- **"기억에 저장" 아이콘** = 안 변하는 선호·규칙 (짧게)
- **john-wiki** = 실제 장기 작업 이력·프로젝트 기록 (김)
- **`AI_CONTEXT.md`** = 매 대화 자동 주입되는 요약 (2026-07-12 실제 생성됨, 아래 현황 참고)
- **`[[lampas-harness]]` memory provider** = LLM이 john-wiki를 검색/읽기로 조회하는 연결부

## 현황

- 2026-07-06·07-08 세션이 ingest되어 `[[lampas-harness]]`·`[[lampas-studio]]` 관련 페이지가 축적됨.
- 2026-07-08 세션에서 "이 위키를 harness에 장기기억 provider로 붙이자"는 설계가 도출됨 → 이 위키의 존재 이유가 그 설계의 실현. → [[2026-07-08-장기기억-provider-연동-설계]]
- **`AI_CONTEXT.md` 실현(2026-07-12)**: 2026-07-08 세션이 제안했던 `AI_CONTEXT.md`가 실제로 저장소 루트에 생성됨(커밋 `0c05aae`). 하네스가 매 대화 시작 시 이 파일을 시스템 프롬프트에 통째로 주입한다(40줄 이내 유지 규칙). 즉 방법 B(자동 주입)의 위키 쪽 구현물이며, `CLAUDE.md`에 그 규칙이 명문화됨. → [[long-term-memory-architecture]]
- **연동 진행(2026-07-11 관찰)**: `[[lampas-harness]]` `src/config.ts`에 `wikiDir`(+`WIKI_DIR` env 오버라이드) 설정이 들어옴 → 배선이 코드 레벨에선 착수됨. 단 어시스턴트에 조회 tool은 아직 미노출. → [[2026-07-11-기억-요약-wiki-경로-확인]]
- **⚠️ 경로 불일치**: `config.ts`의 `wikiDir` 기본값은 `~/Works/**llm-wiki**`(karpathy 원 패턴명)인데, 실제 저장소는 `~/Works/**john-wiki**`다. `WIKI_DIR` env로 새 경로를 지정하지 않으면 하네스는 없는 경로를 가리킨다.
- **저장(ingest) 자동화(2026-07-13)**: `[[lampas-harness]]`의 대화 보관(🗄)이 `memory-ingest` 잡을 자동 큐잉하도록 개선됨 → 이제 위키로 저장하는 진입점이 셋(수동 🧠 버튼·보관 🗄 자동·야간 자동 ingest). 모두 `rememberedAt`/source 키로 중복 방지. 위키 이력 축적(저장 계층)은 자동화 진전, 능동 조회(방법 C)는 미완 유지. → [[2026-07-13-보관시-자동-기억저장]]
- **Works 일괄 pull 대상에서 항상 예외(2026-07-15, 07-17 재확인)**: 이 저장소는 `~/Works` 일괄 최신화
  요청 시 매번 로컬 미커밋 변경(위키 자체의 진행 중 편집)이 있는 채로 발견되지만, 원격이 앞서 있던 적은
  없어 pull 자체가 불필요했다 → [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] ·
  [[2026-07-17-works-저장소-일괄최신화-pull]]. 반대 방향(commit+push)은 [[2026-07-18-works-전체저장]] 참고.

## 관련
- 소유자·사서 소스: [[progdesigner]]
- 연동 대상 하네스: [[lampas-harness]]
- 토픽: [[long-term-memory-architecture]]
- 스킬: [[wiki-memory-provider-integration]]
