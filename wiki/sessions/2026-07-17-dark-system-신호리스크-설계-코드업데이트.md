---
tags: [session, dark-system, trading, upbit, toss-securities]
created: 2026-07-19
updated: 2026-07-19
---
# 2026-07-17 dark-system 신호 판정·리스크 설계 코드 업데이트 (12:30 UTC, 1왕복)

"최신 코드를 업데이트해 줘"라는 짧은 지시 1왕복. [[dark-system]] 모노레포([[dark-upbit-api]]·
[[dark-toss-api]])에 대한 코드 변경 요약을 어시스턴트가 보고했다.

## 보고된 변경 사항
- **`decide-signal.ts` 신설** — 업비트·토스 **양쪽** API에 신호 판정 로직 추가.
- `trading.config.ts`·`trading.service.ts` 대폭 확대 — LLM 카탈로그 연동, 신호 처리 강화.
- 웹 UI 전면 개편 — 차트, 포트폴리오 표시, 거래 기록 등 추가.
- 새 API 엔드포인트: `/trading/status`, `/trading/signal` (거래 상태·신호 조회).
- 설계 문서 신설: `2026-07-17-per-style-signal-risk-design.md` — 스타일별(초단타/단타/스윙/장기투자)
  신호 리스크 설계안.

## 신뢰도 관찰 (미검증)
이 세션은 기존 dark-system 관련 세션들([[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]],
[[2026-07-15-dark-toss-api-장전매수-코드조사]])과 달리 **커밋 해시·파일 line 번호·검증 절차가 전혀
없다** — 어시스턴트의 자기 보고 요약뿐이고, 사용자가 결과를 확인하거나 되묻는 후속 턴도 없이
세션이 끝났다. "현재 상태를 확인할까요?"라는 어시스턴트 질문에 대한 답도 소스에 없음.

특히 `decide-signal.ts`(신호 판정)와 "스타일별 신호 리스크 설계"는 [[trading-strategy-mean-reversion-bollinger]]
토픽이 **"2026-07-17 기준 dark-toss-api에 실제 구현된 적 없음"**이라고 명시한 바로 그 날짜에 나온
보고라, 그 미구현 상태를 메운 작업일 가능성이 있다. 다만 두 소스가 서로 다른 개념(볼린저/평균회귀
전략 vs 범용 "신호" 판정+리스크 설계)을 가리킬 수도 있어 **동일 작업이라고 단정하지 않는다** — 코드를
직접 읽어 확인하는 세션이 있어야 연결을 확정할 수 있다.

## 관련
- 엔티티(갱신): [[dark-toss-api]] · [[dark-upbit-api]] · [[dark-system]]
- 토픽(갱신): [[trading-strategy-mean-reversion-bollinger]]
- [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] · [[2026-07-15-dark-toss-api-장전매수-코드조사]]
- [[2026-07-16-볼린저밴드-평균회귀-트레이딩전략-질문]]
