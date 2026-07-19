---
tags: [entity, project, repo, monorepo]
created: 2026-07-16
updated: 2026-07-19
---
# dark-system

`~/Works/dark/dark-system`에 클론된 pnpm 모노레포(`apps/`, `scripts/`, `pnpm-workspace.yaml`).
원격 `git@github.com:progdesigner/dark-system.git` — [[progdesigner]] 개인 GitHub 계정 소유
(CWC 계열 org 아님). 2026-07-15 06:38 UTC에 `~/Works/dark` 폴더 생성과 함께 최초 클론됨
→ [[2026-07-15-dark-system-저장소-클론]].

클론 직후에는 구조(pnpm 모노레포)만 확인되고 내용은 미조사 상태로 [[works-project-portfolio]]에
"미문서화"로 남아 있었으나, 같은 날(2026-07-15 23:04 UTC, 클론 약 16시간 뒤) 국장 매수시간 관련
질문을 계기로 앱 하나의 내용이 처음 조사되었다.

## 확인된 앱
같은 날 저녁 21:52~22:38 UTC [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] 세션(아래
장전매수 조사 세션보다 시간상 **먼저** 있었음)에서 최소 4개 앱이 한 번에 확인됨:
- [[dark-upbit-api]] (`apps/dark-upbit-api`) — Upbit(업비트, 코인) API 연동 자동매매 서비스.
- [[dark-upbit-web]] — 위 앱의 대시보드 웹.
- [[dark-toss-api]] (`apps/dark-toss-api`) — 토스증권 API 연동 자동매매(트레이딩 봇) 서비스.
  이후(같은 날 23:04) [[2026-07-15-dark-toss-api-장전매수-코드조사]] 세션에서 매매 게이트·장시간
  판정 로직이 더 깊이 조사됨.
- [[dark-toss-web]] — 위 앱의 대시보드 웹.

Upbit·Toss 두 자산군 앱이 스타일(초단타/단타/스윙/장기투자) 개념·전략 스캔(LLM 자동 전환)·`.panel-wrap`
CSS 구조를 공유하는 자매 앱 쌍으로 확인됨.

모노레포에 이 4개 외 다른 앱이 더 있는지는 아직 확인되지 않았다 — `apps/` 전체 목록은 미조사.

## 기타 변경
- 2026-07-17 04:36 UTC [[2026-07-17-works-저장소-일괄최신화-pull]] (Works 일괄 pull 세션): 로컬 미커밋
  변경 1개 + `main` 원격 4커밋 뒤처짐 상태로 확인됨 → 충돌 위험으로 pull 보류, 처리 방식(커밋 후 pull /
  stash 후 pull / 그대로 두기)을 사용자에게 물었으나 **응답 없이 소스 종료 — 미해결**. 이 로컬 변경 1개가
  아래(같은 날 늦게 보고된) `decide-signal.ts` 관련 작업이거나, 다음날 07-18 push 세션에서 커밋된 변경과
  동일物일 가능성이 있으나 확정할 근거는 없다.
- 2026-07-17: `decide-signal.ts`(업비트·토스 공통 신호 판정 로직) 신설 + 설정/서비스 확대 + 웹 UI
  개편 + 신규 엔드포인트를 어시스턴트가 보고 — 커밋 해시·검증 없는 자기 보고뿐이라 미검증 표시.
  상세: [[dark-toss-api]]·[[dark-upbit-api]]의 동일 섹션, [[2026-07-17-dark-system-신호리스크-설계-코드업데이트]].
- 2026-07-18: `tools` 모델 필터 테스트 추가(커밋·push). 원격에 새 커밋이 있어 push가 한 번 거부돼
  `git pull --rebase` 후 재push로 해소 — [[2026-07-18-works-전체저장]] (Works 전체 저장 세션의 일부,
  변경 내용 자체는 소스에 한 줄 요약만 있어 세부 미상).

## 관련
- [[dark-toss-api]] · [[dark-toss-web]] · [[dark-upbit-api]] · [[dark-upbit-web]] · [[progdesigner]] · [[works-project-portfolio]]
- [[2026-07-15-dark-system-저장소-클론]] · [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] · [[2026-07-15-dark-toss-api-장전매수-코드조사]] · [[2026-07-17-works-저장소-일괄최신화-pull]] · [[2026-07-18-works-전체저장]]
- [[2026-07-17-dark-system-신호리스크-설계-코드업데이트]]
