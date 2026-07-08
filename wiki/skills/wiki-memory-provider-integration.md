---
name: wiki-memory-provider-integration
description: markdown 위키/폴더를 LLM 어시스턴트의 검색 가능한 장기기억(memory provider)으로 붙이는 절차
created: 2026-07-09
tags: [long-term-memory, memory-provider, rag, tool-calling, context-injection]
---
# 위키를 LLM 장기기억 provider로 연동

## 언제 쓰는가
어시스턴트가 "내가 예전에 뭐 했지?" 류 질문에 **과거 작업 이력을 기억 못 할 때**.
markdown 위키/폴더(예: [[john-wiki]])는 있는데 모델이 그걸 읽을 도구가 없는 상황.
목표: 저장(위키)과 조회(모델 컨텍스트)를 연결한다. → 배경: [[long-term-memory-architecture]]

## 절차 (단계별)

1. **역할 분리부터 확정** — 짧은 선호·규칙은 인메모리/"기억에 저장", 긴 이력은 위키. 이걸 안 나누면 컨텍스트가 폭발한다.
2. **MemoryProvider 인터페이스 정의** — `status()` / `read_context()` / `search(query, top_k)` / `read(path)` / `list_pages()`. 위키 백엔드를 이 인터페이스 뒤에 숨겨 나중에 교체 가능하게.
3. **위키 구현** — 루트 아래 `.md/.markdown/.txt`를 rglob(`.git` 제외), 쿼리 토큰 카운트로 스코어링, 상위 top_k 반환. 발췌는 첫 매치 주변 ±수백자로 잘라 컨텍스트 절약.
4. **`AI_CONTEXT.md` 자동 주입** — 위키 루트에 AI용 요약 페이지를 두고, 대화 시작 시 system/developer 메시지에 `read_context()` 결과를 붙인다. (선호·구조·프로젝트·답변규칙)
5. **검색 tool 노출** — `..._status` / `search_...` / `read_...` / `list_..._pages`를 function/tool schema로 등록하고 dispatcher에 연결. 모델이 능동 조회하게.
6. **안티할루시네이션 지침** — "과거 작업은 tool로 조회, 없으면 접근 불가라 명시, 이력을 지어내지 말 것"을 system prompt에 명문화.
7. **검증** — 고유 문장을 위키에 심고 새 대화에서 되묻기(주입 확인) → 목록/검색/읽기 3개 tool 각각 호출 확인.

## 주의사항 / 함정

- **경로 탈출(path traversal) 가드 버그** — 2026-07-08 제안 코드의 `str(target).startswith(str(root))`는 취약하다. `/root`와 형제인 `/root-evil` 같은 경로가 prefix 검사를 통과한다. **반드시 경계에 구분자를 포함**해 검사하거나(`target == root or target.is_relative_to(root)`), `Path.is_relative_to()`(py3.9+)를 쓸 것.
- **언어/SDK 이식** — 제안 스케치는 Python + OpenAI-style function calling. 실제 [[lampas-harness]]는 **TypeScript + Claude Agent SDK**다. 인터페이스는 재사용하되 tool 정의·주입 방식은 대상 SDK에 맞게 재작성. (Claude tool은 `input_schema`, OpenAI는 `parameters`)
- **컨텍스트 예산** — `read_context()`·발췌 길이에 상한(`max_context_chars`, 발췌 max_chars)을 둬서 주입이 토큰을 잠식하지 않게.
- **"저장됐는데 왜 기억 못 하지"는 조회 문제** — 저장 UI가 있어도 재주입/검색이 끊겨 있으면 무의미. 먼저 조회 경로가 연결됐는지 확인. → [[long-term-memory-architecture]]
- **naive 토큰 카운트 스코어링의 한계** — 짧은 흔한 토큰이 스코어를 지배. 임베딩/BM25로 올릴 여지 있음(현재는 단순 count).

## 상태
설계·제안 단계(2026-07-08). 실제 [[lampas-harness]]에는 아직 미구현. 이 위키 자체가 대안 구현
(사서 LLM이 CLAUDE.md 워크플로로 ingest/query하며 `index.md`를 검색 진입점으로 사용).

## 출처: [[2026-07-08-장기기억-provider-연동-설계]] ([[john-wiki]] · [[lampas-harness]])
