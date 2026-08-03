---
tags: [topic, dark-system, trading, unverified]
created: 2026-08-03
updated: 2026-08-03
---
# dark-system `decide-signal.ts` 신호 판정·스타일별 리스크 설계 (2026-07-17, 미검증)

[[2026-07-17-dark-system-신호리스크-설계-코드업데이트]] 세션에서 어시스턴트가 [[dark-upbit-api]]·
[[dark-toss-api]] 양쪽에 걸쳐 보고한 변경 묶음. **커밋 해시·line 번호·코드 확인 없이 어시스턴트
자기 보고만으로 기록**되어 세 엔티티 페이지([[dark-system]]·[[dark-upbit-api]]·[[dark-toss-api]])에
거의 동일한 내용과 동일한 "미검증" 경고가 각각 반복 기재돼 있었다 — 이 페이지로 통합해 하나의
출처만 갱신하면 되도록 정리(2026-08-03 lint).

## 보고된 변경 내용
- 신규 `decide-signal.ts` — 업비트·토스 공통 신호 판정 로직.
- `trading.config.ts`·`trading.service.ts` 확대 — LLM 카탈로그 연동, 신호 처리 강화.
- 웹 UI 전면 개편(차트·포트폴리오·거래기록).
- 신규 엔드포인트 `/trading/status`·`/trading/signal`.
- 설계 문서 `2026-07-17-per-style-signal-risk-design.md`(스타일별 신호 리스크 설계안).

## 미검증 상태
이 항목 전체가 커밋 해시·line 번호·코드 확인 없는 어시스턴트 자기 보고뿐이다. 다음에 dark-system
코드를 열어볼 때 `decide-signal.ts` 실존 여부·내용을 실제로 확인할 것 — 확인되면 이 페이지 상태를
"검증됨"으로 갱신하고, 세 엔티티 페이지의 링크는 그대로 두면 된다(내용 수정은 이 페이지 한 곳만).

## [[trading-strategy-mean-reversion-bollinger]]와의 관계
같은 날짜 기준 [[trading-strategy-mean-reversion-bollinger]]는 "미구현·상담 단계"라 적어뒀다.
이 `decide-signal.ts` 보고가 그 볼린저/평균회귀 전략의 실제 구현인지, 아니면 무관한 범용 "신호"
판정 시스템인지는 **소스에 근거가 없어 단정하지 않는다**. 다음 코드 확인 시 이 연결도 함께 확정하거나
기각할 것.

## 관련
- 세션: [[2026-07-17-dark-system-신호리스크-설계-코드업데이트]]
- 엔티티: [[dark-system]] · [[dark-upbit-api]] · [[dark-toss-api]]
- 토픽: [[trading-strategy-mean-reversion-bollinger]]
