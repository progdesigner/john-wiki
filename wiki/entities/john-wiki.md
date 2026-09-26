---
tags: [entity, project, tool, knowledge-base, long-term-memory]
created: 2026-07-09
updated: 2026-09-26
---
# john-wiki

`[[progdesigner]]`의 **공통 장기기억 저장소이자 개인 위키**. karpathy/llm-wiki 패턴을 따른
markdown 3계층 지식베이스(`raw/` 원본, `wiki/` LLM 작성 페이지, `index.md`/`log.md`).
LLM이 작성·유지하고 사람은 소스를 공급·질문한다.

- 로컬 경로: `/Users/progdesigner/Works/john-wiki`
- 구조: `raw/conversations/`(원본, 읽기전용) · `wiki/{sessions,entities,topics,skills}/` · `index.md` · `log.md`
- 규칙: 모든 페이지 markdown + YAML frontmatter(`tags`/`created`/`updated`), 페이지 참조는 이중 대괄호로 감싸는 위키링크 문법(여닫는 대괄호를 두 겹씩), 파일명 kebab-case, 세션·대화는 `YYYY-MM-DD-` 접두사. (상세는 저장소 CLAUDE.md)

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
- **저장(ingest) 자동화(2026-07-13)**: `[[lampas-harness]]`의 대화 보관(🗄)이 `memory-ingest` 잡을 자동 큐잉하도록 개선됨 → 당시 위키로 저장하는 진입점이 셋(수동 🧠 버튼·보관 🗄 자동·야간 자동 ingest). 모두 `rememberedAt`/source 키로 중복 방지. 위키 이력 축적(저장 계층)은 자동화 진전, 능동 조회(방법 C)는 미완 유지. → [[2026-07-13-보관시-자동-기억저장]]
  - **[정정, 2026-07-16]** 상단 "기억에 저장" 🧠 버튼이 "기억에 보관"으로 개명되며 보관(🗄) API로 통합됨 — 위 "셋"은 UI상 사실상 둘(수동 보관·야간 자동 ingest)로 수렴. → [[2026-07-16-기억버튼-보관통합-NaN토큰버그수정]] · [[long-term-memory-architecture]]
- **Works 일괄 pull 대상에서 항상 예외(2026-07-15, 07-17 재확인)**: 이 저장소는 `~/Works` 일괄 최신화
  요청 시 매번 로컬 미커밋 변경(위키 자체의 진행 중 편집)이 있는 채로 발견되지만, 원격이 앞서 있던 적은
  없어 pull 자체가 불필요했다 → [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] ·
  [[2026-07-17-works-저장소-일괄최신화-pull]]. 반대 방향(commit+push)은 [[2026-07-18-works-전체저장]] 참고.

## 사람용 브라우징 UI 연결 — `lampas-harness apps/wiki` (2026-09-26)

기존 `[[wiki-memory-provider-integration]]`(방법 C, LLM이 tool로 능동 검색)와는 **별개의 새 소비
경로**가 처음 생겼다: `[[lampas-harness]]`에 이 저장소를 **사람이 위키피디아처럼 브라우징**하는
읽기 전용 웹 UI(`apps/wiki`, 진입점 `wiki.html`)가 추가되고, 하네스 설정(⚙) 아래 **"위키" 버튼**으로
노출됨. 하네스 기존 인증 + `WIKI_DIR` 설정(2026-07-11에 이미 코드 레벨로 존재했던 그 경로,
[[2026-07-11-기억-요약-wiki-경로-확인]])을 그대로 재사용해 이 저장소의 markdown 원본을 서버가 직접
읽는다. 본문 검색·문서 목차·분류·위키링크·역링크(backlink) 렌더링까지 구현, 하네스 테스트 79개
(검색·위키링크·역링크·접근 제한 포함)와 Playwright 데스크톱·모바일 실브라우저 검증 통과. →
[[2026-09-26-threads기능제거-llm위키탐색기-apps-wiki-이전]]

- **경위**: 원래 `[[lampas-agent]]`(스포츠 클립 데몬)에 이 위키 기반 Threads(SNS 글 자동생성) 기능을
  요청했다가, 어시스턴트가 처음엔 "위키"를 sports-wiki로 오인 → 사용자 정정으로 이 저장소임을 확정 →
  결국 Threads 기능 자체는 취소되고, "이 위키를 보는 기능"만 분리되어 `lampas-harness apps/wiki`로
  이전됨.
- **소비 경로 분류 갱신**: 기존 방법 A(요약 붙여넣기)/B(`AI_CONTEXT.md` 자동주입)/C(LLM 검색 tool,
  미완)에 이어 **방법 D: 사람용 읽기 전용 웹 UI**가 실제 구현·검증까지 완료된 첫 사례. → 상세 비교는
  [[long-term-memory-architecture]] 참고.
- **미확인**: 세션 종료 시점에 하네스 서버 재시작(반영)이 실행 중인 터미널 세션 보호를 위해 대기
  중이었음 — 실제 프로덕션에 이 기능이 반영됐는지는 다음 확인 필요.

## 관련
- 소유자·사서 소스: [[progdesigner]]
- 연동 대상 하네스: [[lampas-harness]] (`apps/wiki` 사람용 브라우저 + memory provider 제안)
- 토픽: [[long-term-memory-architecture]]
- 스킬: [[wiki-memory-provider-integration]]
- 세션: [[2026-09-26-threads기능제거-llm위키탐색기-apps-wiki-이전]]
