---
name: nestjs-admin-guard-requires-jwtmodule
description: NestJS 모듈에 AdminGuard를 쓰는 컨트롤러를 새로 추가할 때 JwtModule 등록을 빠뜨리면 타입체크·유닛테스트는 통과하지만 운영 기동 자체가 실패한다 — 배포 전 로컬 실기동으로 확인
created: 2026-09-26
tags: [nestjs, dependency-injection, admin-guard, jwt, deploy-safety, lampas-studio]
---
# AdminGuard는 JwtModule 등록이 전제조건이다

## 언제 쓰는가
기존 도메인 모듈(예: `AiModule`)에 관리자용 컨트롤러를 새로 추가하면서 `AdminGuard`(로그인 JWT
검증 가드, → [[admin-guard-precedent-reuse]])를 그대로 가져다 쓸 때. `AdminGuard`는 내부적으로
`JwtService`에 의존하는데, 이 서비스는 해당 모듈이 `JwtModule`을 **직접 import**해야만 Nest DI
컨테이너에 주입된다 — 다른 모듈이 이미 `JwtModule`을 쓰고 있어도 전역(global) 등록이 아니면
새 모듈에는 보이지 않는다.

## 절차 (단계별)
1. 새 관리자 컨트롤러에 `AdminGuard`를 붙이기 전에, 그 가드가 생성자에서 요구하는 서비스
   (`JwtService` 등)가 무엇인지 확인한다.
2. 컨트롤러가 속한 모듈(`@Module` 데코레이터)의 `imports` 배열에 `JwtModule`이 있는지 확인한다 —
   같은 레포의 다른 모듈(`AdminModule` 등)에 이미 있다고 안심하지 않는다, 모듈별로 독립이다.
3. 없으면 추가하고, **배포 전에 로컬에서 실제로 서버를 기동시켜** 부팅 로그에 DI 오류가 없는지
   확인한다 — 유닛 테스트와 `tsc --noEmit`은 이 오류를 잡지 못한다(런타임 DI 그래프 조립 시점에만
   드러남).
4. 로컬 기동이 정상이면(수 초~40초 내 "Nest application successfully started" 류 로그 확인) 배포를
   진행한다.

## 주의사항 / 함정
- **타입체크·유닛테스트 통과 = 안전하다는 보장이 아니다.** DI 와이어링 오류는 Nest가 실제로
  모듈 그래프를 조립하는 부팅 시점에만 터진다. 이 종류의 버그를 잡는 유일한 방법은 로컬에서 실제
  프로세스를 띄워보는 것이다.
- 운영에서 이 오류가 나면 증상이 간접적이다 — 프로세스가 기동 실패→PM2가 재시작 반복→nginx가
  업스트림 없음으로 404를 반환. 에러 로그만 보면 "API가 느리다/안 뜬다" 정도로 보일 수 있어
  `pm2 logs`로 부팅 단계 에러 메시지를 직접 봐야 원인(DI 실패)이 드러난다.
- 장애 지속 시간을 줄이는 핵심은 **배포 전** 40초 내외의 로컬 기동 확인이다 — 사후 복구(원인
  특정→수정→재배포)보다 훨씬 싸다.

## 출처: [[2026-09-26-studio5도메인분리-models카탈로그-packaging플랫폼-유튜브]]
(`models.lampas.io` 일일 카탈로그 기능 추가 중 `AiModule`에 관리자 컨트롤러를 신설하며 `AdminGuard`
를 붙였으나 `JwtModule` 미등록으로 첫 배포(0.1.153) 직후 운영 API가 기동 실패, PM2 재시작 반복·
nginx 404로 약 5분 장애. `JwtModule` 등록 + 로컬 실기동 확인 후 재배포(0.1.154)로 복구)
