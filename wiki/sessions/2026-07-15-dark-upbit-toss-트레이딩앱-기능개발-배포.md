---
tags: [session, dark-system, trading, upbit, toss-securities, deploy]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-15 dark-upbit/dark-toss 트레이딩앱 기능개발·배포 (21:52~22:38 UTC)

[[dark-system]] 모노레포 안 [[dark-upbit-api]]/[[dark-upbit-web]]/[[dark-toss-api]]/[[dark-toss-web]]
4개 앱을 넘나든 장시간 연속 기능개발+배포 세션. 7개 커밋(main `4f306fa..9033b73`)이 나왔고, 이 세션
자체가 이 4개 앱을 위키에 처음 노출시킨 사건이다 — 이전엔 [[dark-toss-api]] 하나만(더 나중에 있었던
[[2026-07-15-dark-toss-api-장전매수-코드조사]] 세션, 23:04~에서) 조사된 상태였다.

## 진행 순서·결정

1. **최신 코드 확인** — 이미 원격 main과 동일(`4f306fa`), pull할 것 없음.
2. **Upbit 초단타(scalping) 비활성화** — `trading.config.ts`에 `DISABLED_STYLES=['scalping']` 추가.
   화면 목록·수동 변경 API·전략 스캔(LLM 프롬프트+검증)·재시작 복원 4경로 모두 차단. [[dark-toss-api]]
   (Toss)는 건드리지 않음.
3. **모바일 표 가로 스크롤** — Upbit·Toss 웹 둘 다 `.panel-wrap`에 `overflow-x:auto` + 720px 이하
   `min-width:640px`. `index.css`만 수정으로 모든 표(보유 포지션/AI 판단 로그/체결 내역/파라미터
   변경 이력)에 일괄 적용.
4. **1차 배포+커밋** — [[deploy-sandbox-pnpm-shim]] 스킬로 배포. dark-upbit-api(pm2 재시작+검증),
   dark-upbit-web·dark-toss-web(S3+CloudFront). dark-toss-api는 변경 없어 스킵. gitignore된
   `env/.env.production`이 로컬에 없어 배포가 한 번 중단됐다가, API는 실서버 `.env`로, 웹은 배포된
   번들의 `VITE_API_URL`로 복원해 진행 — 이 복구 절차를 스킬에 반영(아래 갱신 항목 참고). 커밋
   `d8d602d`(초단타 비활성화), `ca57b7f`(모바일 가로 스크롤).
5. **확대 방지** — 두 웹앱 `index.html` viewport에 `maximum-scale=1.0, user-scalable=no`. 배포+커밋
   `f11c686`.
6. **Upbit 초단타 잔존 노출 버그 + LLM 차단 강화** — 원인은 upbit 웹 드롭다운이 서버 API를 안 쓰고
   옵션을 하드코딩하고 있었던 것. 드롭다운을 서버 `/trading/styles` 기준으로 고침. 추가로 초단타
   프리셋 엔진을 `ai`→`signal`(볼린저 지표)로 바꿔, 나중에 수동 재활성화돼도 후보별 판단
   LLM(`llm.decide`)·AI 후보 추천이 구조적으로 호출 안 되게 만듦.
7. **"자동전환은 막되 수동은 허용"으로 요구 정교화** — `DISABLED_STYLES`(전면 차단) →
   `AUTO_SWITCH_EXCLUDED_STYLES`(자동 전환만 제외)로 개념 교체. 수동 선택 복원(`setStyle` 거부 로직
   제거, 목록·드롭다운 복원, 재시작 유지) + 수동 선택 보호(AUTO 중 다음 스캔이 수동 선택을 되돌리던
   기존 버그를 스타일이 수동전용이면 제안 로그만 남기도록 수정). LLM 미사용은 6번의 엔진 교체로 계속
   유지됨. → 패턴 일반화: [[trading-feature-flag-auto-exclude-manual-allow]].
8. **모델 select 날짜 스냅샷 필터** — 요청이 처음엔 모호해("모델 선택에 날짜가 붙은 긴 모델명" — 하네스
   자체 `/model` 목록인지 트레이딩앱 LLM 모델 드롭다운인지 불명확) 되물어 확인 후 진행. 두 API의
   `llm-catalog.ts`(파일 복제, 공유 패키지 아님)에 `isDatedSnapshotModel` 필터 추가 — 상세 규칙은
   [[dark-upbit-api]] 참고. 15개 대표 케이스로 검증(로컬 스크립트 `tools/dated-model-filter.test.mjs`,
   커밋 제외).
9. **Toss 국장/미장 자동 장이동 확장** — 기존 개장 추적(국장/미장 자동 전환)에 "양쪽 마감 시 자동판단
   일시정지(`autoPaused`) + 재개장 시 재개" 추가, 사용자가 AUTO를 직접 끄면 일시정지 해제(재개장해도
   재시작 안 함). Toss 웹에 "AUTO ⏸ 장마감 · 개장 시 재개" 배지 추가. 상세: [[dark-toss-api]].
10. **2차 배포+커밋** — 4개 대상(upbit api/web, toss api/web) 전부 배포. 검증 중 모델 필터가
    `gemini-2.5-computer-use-preview-10-2025`(월-연도 패턴)를 놓친 걸 발견해 정규식 보강 후 두 API
    재배포. 커밋 3개: `7c1445a`(초단타 수동전용), `557abe7`(날짜 모델 필터), `81a1799`(토스 장마감
    일시정지/재개).
11. **초기화 시 LLM 비용도 초기화** — `POST /trading/reset`이 `llmUsage`도 초기화하도록 변경. Upbit은
    "실비용이라 유지" 정책을 뒤집어 비용도 리셋. Toss는 KR/US 지역 슬롯 간 비용 동기화 구조라 전 지역
    슬롯에서 제거해야 부활을 막을 수 있음(활성 슬롯만 지우면 장 전환 때 되살아남). 대기 중이던
    미집계 사용량도 `drainUsage` 후 폐기.
12. **3차 배포+커밋** — dark-upbit-api·dark-toss-api 배포(재시작 시 upbit은 `autoStart`로 AUTO
    자동 재개, toss는 꺼진 채). 커밋 `9033b73`.

## 신규 확인 사실
- [[dark-system]]에 최소 4개 앱: [[dark-upbit-api]]·[[dark-upbit-web]]·[[dark-toss-api]]·
  [[dark-toss-web]]. Upbit·Toss가 스타일 개념·전략 스캔·CSS 구조를 공유하는 자매 앱 쌍.
- API 배포는 pm2 재시작을 동반 — 배포 전 AUTO가 돌고 있었다면 배포 직후엔 꺼진다(재개는 `autoStart`
  설정 또는 수동 재시작에 달림).

## 관련
- 엔티티(신설): [[dark-upbit-api]] · [[dark-upbit-web]] · [[dark-toss-web]]
- 엔티티(갱신): [[dark-toss-api]] · [[dark-system]]
- 스킬(신설): [[trading-feature-flag-auto-exclude-manual-allow]]
- 스킬(갱신): [[deploy-sandbox-pnpm-shim]] (dark-system 일반화 확인 + env 복구 함정), [[config-flag-gate-audit]] (실제 사례 역참조)
- 다음 세션: [[2026-07-15-dark-toss-api-장전매수-코드조사]] (같은 날 저녁 23:04~, 이 세션 종료 26분 뒤)
