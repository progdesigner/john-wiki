---
tags: [session, lampas-system, jev, typesafe, consulting, no-code-change]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-20 — Jev(TypeSafe) 활용처 추천 (lampas-system 전체)

`Tool: codex`, 작업 폴더 `lampas-system`. **코드 수정·설치 없이 추천만 정리한 자문 세션** — 파일명은
"Lampas Agent 개선"이지만 실제 내용은 `[[lampas-agent]]`(스포츠 클립 데몬)가 아니라 `[[lampas-studio]]`
(`lampas-system` 저장소) 전체 기능을 대상으로 한다.

## 요청
`[[progdesigner]]`가 [jev-search](https://github.com/superagents-lab/jev-search) 예제와
[TypeSafe 문서](https://docs.typesafe.ai/introduction)를 참고해 `lampas-system`에 Jev(TypeSafe)를
적용할 만한 기존 기능을 찾아 추천해달라고 요청(작업은 이후로 미룸).

## Jev/TypeSafe에 대한 어시스턴트 이해
`Choice`(후보 선택) · `Score`(기준별 평가) · `Noul`(예/아니오 확률)로 구성된 **선택·판정·점수화 전용
모델** — 생성형 모델과 역할을 분리해 적용해야 한다는 것이 핵심 전제. 텍스트 입력만 지원(이미지 분위기
평가 불가, Vision 설명을 먼저 만들어야 함). 언어 지원은 영어가 가장 정확하고 CJK는 자체 검증 필요.
가격: 입력 100만 토큰당 $0.042, 출력 무료(판정만 반환하므로).
→ `[[jev-typed-classification]]`에 이미 기록된 "typed 질문 기반 저비용 분류" 패턴과 같은 개념.

## 추천 5개 영역 (우선순위·별점)

| 순위 | 영역 | 코드 위치 | 추천도 |
|---|---|---|---|
| 1 | 레퍼런스 탐색 결과 재정렬 | `apps/lampas-api/src/modules/references/reference-explore.service.ts:217` | ★★★★☆ 우선 실험 |
| 2 | 채팅 플로우 progress/clarify/cancel 분류 + 촬영대상 선택 | `apps/lampas-api/src/modules/orchestration/orchestration.service.ts:487` (`analyzeFlowTurn`) | ★★★☆☆ 오류 확인 후 |
| 3 | 트렌드 기사·키워드 관련도 판정 | `apps/lampas-api/src/modules/trends/trends-search.lib.ts`, `trends.service.ts:160` | ★★★☆☆ 문제 있다면 |
| 4 | 광고 카피 후보 기준별 평가(Composite Scoring) | `apps/lampas-api/src/modules/product-insights/product-insights.service.ts:189` | ★★☆☆☆ 후순위 |
| 5 | 제품 리서치에 외부 검색 근거 추가(jev-search 패턴 응용) | `product-insights.service.ts:139` | ★★☆☆☆ 별도 기능 검토 |
| — | 이미지·영상·카피 **생성 자체** 대체 | — | ☆☆☆☆☆ 부적합(Jev는 생성 모델 아님) |

## 최종 결론
**전면 도입 보류.** "지금 기능에 큰 불만이 없다면 도입 보류"가 기본 방침이며, 유일하게 권하는 건
레퍼런스 검색 재정렬을 100~200건 규모로 기존 순서 대비 비교 실험(관련도·선택률·지연·비용)하는 것.
채팅 분류는 한국어 대화로 별도 검증 필요. 이 세션 자체는 코드를 건드리지 않았으므로, 실제 도입 여부는
후속 세션에서 확인해야 한다(이 위키 소스로는 후속 착수 여부 미확인).

## 기존 기록과의 관계
`[[jev-typed-classification]]` 페이지는 이미 4개의 **실제 구현된** Jev 사용처(sports-wiki 게이트,
Fixs 오류 triage, dalar-web-first 오류 판정, lampas-web-flow 노드 자동화)를 기록하고 있다. 이 세션은
그중 어느 것도 아니고, **아직 채택되지 않은 후보 5개**(레퍼런스 탐색·채팅 플로우·트렌드·광고 카피·
제품 리서치)를 추가로 제시한 자문 기록이다. 시점(2026-09-20)은 lampas-web-flow의 Jev 첫 사용(같은 날)과
근접하지만 이 세션에서 직접 언급되진 않는다 — 순서 관계 불명.

## 관련
- 토픽: `[[jev-typed-classification]]`
- 엔티티: `[[lampas-studio]]`, `[[lampas]]`(하네스 에이전트, 이 세션의 코딩 주체)
- 원본: `raw/conversations/2026-09-20-jev-활용처-추천-lampas-system.md`
