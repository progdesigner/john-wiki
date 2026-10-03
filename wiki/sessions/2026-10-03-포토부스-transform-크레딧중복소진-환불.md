---
tags: [session, lampas-studio, lampas-api, credit, bugfix, refund, photobooth, unresolved]
created: 2026-10-03
updated: 2026-10-03
---
# 2026-10-03 — 포토부스 Transform 크레딧 중복 소진 원인 수정 + 과거 환불

`Tool: claude`, 작업 디렉터리 `lampas-system`([[lampas-studio]]). 11:19:32Z 시작, 이미지 첨부 1장
포함 짧은 세션. 원본: [[raw/conversations/2026-10-03-포토부스-transform-크레딧중복소진-환불]].

## 요청
[[progdesigner]]가 Transform 실행 시 크레딧이 추가로 더 빠진다고 제보 — "AI 사진 생성" 차감이 이미
그 소진을 대신하는 의도인데 중복으로 빠지고 있으니 정리해달라는 요청.

## 원인
포토부스 앱(`lampas-app-photobooth`, 구 `lampas-app-toss` → [[lampas-studio]] 2026-07-18 리네이밍
기록 참고)이 생성 1회당 **앱 쪽에서 "AI 사진 생성" 50크레딧을 직접 차감**한 뒤, 같은 요청이 호출하는
서버의 `POST /transforms/:key/run`이 **모델 단가(nano-banana 80, gpt-image-2 10)를 한 번 더 차감** —
같은 생성 1건에 두 개의 독립된 차감 경로가 겹쳐 있었다.

## 수정 (로컬, 세션 종료 시점 미배포·미커밋)
- 포토부스 앱 세션 토큰으로 들어온 `POST /transforms/:key/run` 요청은 서버의 모델 단가 차감을 건너뛴다.
  이미지뿐 아니라 영상·이미지→영상 Transform도 동일 적용.
- web-studio 등 일반 로그인 사용자의 Transform 실행은 기존대로 모델 단가 과금 유지(건너뛰기는
  포토부스 앱 세션에만 한정).
- 판정 로직을 `apps/lampas-api/src/modules/credits/lib/app-credit-session.ts`로 분리. 테스트·타입
  체크 통과, **실제 앱 요청으로 차감이 생략되는지는 미검증**.
- 루트 `CLAUDE.md` 크레딧 산정 절에 규칙 한 줄 추가.
- 이 "앱이 선차감 → 서버는 그 세션이면 건너뛴다" 판정 패턴은 재사용 가치가 있어 스킬로 추출 →
  [[app-session-flat-fee-vs-server-metered-double-charge]].

## 환불 (운영 DB에는 즉시 반영 완료)
- **대상**: 토스 포토부스 사용자 60명, 255건, 총 **13,610크레딧** (2026-07-23 ~ 세션 당일 12:51 건까지).
- **매칭 기준**: 같은 사용자의 `AI 사진 생성` 차감 뒤 30분 이내에 따라온 `Transform 실행` 차감을 1:1로
  짝지음.
- **기록**: 사용자별 조정(ADJUSTMENT) 원장. 사유 `포토부스 Transform 중복 소진 환불 N건 [tx:…]`,
  처리자 `system:refund`.
- **재실행 안전(idempotent)**: 적용 후 재실행해 추가 환불 대상 0건 확인 — [[signup-domain-abuse-rate-limit-and-reclaim]]
  스킬이 썼던 것과 같은 "재실행 시 스킵" 검증 패턴.
- **제외 1건**: 사용자 10008의 8월 3일 80크레딧 차감은 짝이 되는 앱 차감이 없어 환불 대상에서 제외.
- 환불 스크립트: `apps/lampas-api/scripts/refund-photobooth-transform-double-charge.js`.

## 미해결 — 다음 단계
세션 종료 시점까지 **수정 코드는 배포되지 않아, 배포 전까지는 포토부스 생성마다 중복 차감이 계속된다.**
어시스턴트가 제안한 다음 단계: `./scripts/deploy-api.sh lampas-api`로 배포·커밋 후, 환불 스크립트를
한 번 더 돌려 배포 시점까지 새로 쌓인 중복분을 추가 환불해야 한다. 사용자 확인 대기 상태로 세션 종료
— **이 세션 소스만으로는 배포 여부 확인 불가**, 다음 lampas-system 세션에서 배포·추가 환불 완료 여부
재확인 필요.

## 관련
- 엔티티: [[lampas-studio]](갱신 — "포토부스 Transform 크레딧 중복 소진" 절 추가)
- 스킬: [[app-session-flat-fee-vs-server-metered-double-charge]](신설) ·
  [[credit-ledger-balance-pattern]](원장 조정 방식 선례) ·
  [[signup-domain-abuse-rate-limit-and-reclaim]](재실행 idempotent 검증 패턴 선례)
- 비교: [[2026-09-25-엘레비노-예약취소-크레딧환불-배포장애]](다른 저장소의 크레딧 환불 사례,
  `CREDIT_REFUND`/`ADJUSTMENT` 모두 원장 트랜잭션 타입 추가 방식) ·
  [[2026-09-26-람파스-가입도메인필터-크레딧회수-대시보드-레이아웃]](같은 저장소의 이전 크레딧 원장
  조정 사례, 보너스만/전액 모드)
