# john-wiki — 스키마

이 저장소는 CWC의 **공통 장기 기억 저장소**다. 패턴: [karpathy/llm-wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).
LLM(너)이 이 위키를 작성·유지하고, 사람은 소스를 공급하고 질문한다.

## 구조 (3계층)

```
raw/                # 원본 소스 — 읽기 전용. 절대 수정·삭제 금지
  conversations/    # 대화 트랜스크립트 (YYYY-MM-DD-제목.md)
wiki/               # LLM이 작성·유지하는 페이지
  sessions/         # 대화 세션 요약 (YYYY-MM-DD-제목.md)
  entities/         # 인물·프로젝트·도구·조직 페이지
  topics/           # 개념·주제·관심사 페이지
  skills/           # 재사용 가능한 해결 절차 (SKILL.md 형식)
index.md            # 전체 페이지 카탈로그 (카테고리별, 링크 + 한 줄 요약)
log.md              # append-only 시간순 기록
```

## 페이지 규칙

- 모든 페이지는 markdown. 상단에 YAML frontmatter: `tags`, `created`, `updated`.
- 페이지 간 참조는 `[[위키링크]]` 사용. 링크 대상이 아직 없어도 괜찮다 (나중에 쓸 것 표시).
- 새 정보가 기존 내용과 모순되면 덮어쓰지 말고 명시한다: 어떤 소스가 언제 무엇을 말했는지.
- 파일명은 kebab-case, 세션·대화는 `YYYY-MM-DD-` 접두사.

## 워크플로

### Ingest (새 소스 통합)
1. 소스를 읽는다.
2. 원본을 `raw/`의 알맞은 위치에 보관한다 (이미 있으면 생략).
3. `wiki/sessions/`(대화) 또는 적절한 위치에 요약 페이지를 만든다.
4. 언급된 인물·프로젝트·개념의 entity/topic 페이지를 생성·갱신한다. 하나의 소스가 여러 페이지를 건드리는 것이 정상이다.
5. `index.md`를 갱신한다.
6. `log.md`에 `## [YYYY-MM-DD] ingest | 제목` 항목을 추가한다.

### Query (질문 답변)
1. `index.md`를 먼저 읽어 관련 페이지를 찾고, 그 페이지들만 읽는다.
2. 답변에는 근거 페이지를 인용한다.
3. 가치 있는 답변(비교, 분석, 새 연결)은 `wiki/topics/`에 새 페이지로 저장한다 — 탐구도 복리로 쌓인다.
4. `log.md`에 `## [YYYY-MM-DD] query | 질문 요약` 항목을 추가한다.

### Skill 추출 (Hermes Agent 패턴)
ingest 중 소스에서 "어려운 문제를 해결한 재사용 가능한 절차"를 발견하면
`wiki/skills/`에 스킬 페이지를 만든다. 형식:

```markdown
---
name: 스킬-이름 (kebab-case)
description: 언제 이 스킬을 쓰는지 한 문장
created: YYYY-MM-DD
tags: [...]
---
# 스킬 이름

## 언제 쓰는가
## 절차 (단계별)
## 주의사항 / 함정
## 출처: [[관련 세션/소스]]
```

같은 문제를 다시 만나면 이 스킬을 먼저 찾아 따른다. 스킬이 틀렸거나
불완전했다면 고쳐서 다듬는다 (스킬은 반복 사용으로 정제된다).

### Lint (건강 점검)
1. 모순, 낡은 주장, 고아 페이지, 누락된 크로스링크, 인덱스 불일치를 점검·수정한다.
2. `log.md`에 `## [YYYY-MM-DD] lint | 요약` 항목을 추가한다.

## log.md 형식

항목은 반드시 `## [YYYY-MM-DD] <종류> | <제목>`으로 시작한다.
(`grep "^## \[" log.md | tail -5`로 최근 활동을 파싱할 수 있어야 한다.)
