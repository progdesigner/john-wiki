---
tags: [session, lampas-studio, lampas-web-fit, codex, fitness, deploy]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-fit 구축·배포 (2026-09-25~26, codex 도구)

`Tool: codex`(PTY 웹 터미널, → [[lampas-harness]] 신규 서브시스템) 세션. 작업 폴더
`/Users/progdesigner/Works/lampas/lampas-system`. [[progdesigner]]가 유튜브 쇼츠
(흥둥이 "1분 플랭크 챌린지")를 예시로 들며 **음악 박자에 맞춰 운동하고 다음 동작을
미리 준비할 수 있게 카운팅해주는 서비스**를 요청 → 신규 앱 `lampas-web-fit` 구현 →
사용자 요청으로 `fit.lampas.io`에 즉시 배포까지 한 세션에서 완료.

## 요청 배경
- 참고 영상 세부 동작·음원은 확인 불가(링크만 제공) → 원본 재현 대신 **직접 재생되는
  비트 음악 + 운동 가이드**로 자체 설계.

## 구현 내용 (`lampas-web-fit`)
- **오디오 동기화**: 운동 타이머와 음악이 **같은 오디오 시계**를 공유. 일시정지 시 둘 다
  정지, 탭 이탈 시 자동 정지.
- **동작 예고·카운트**: 다음 동작을 **8박 전** 미리보기로 안내, 전환 직전 **마지막 4박**은
  음성으로 카운트.
- **설정**: 루틴 3종, BPM·라운드 수·음악 변경 가능.
- **기타 기능**: 일시정지·재개·초기화, 전체화면, 운동 기록 저장, 모바일 화면 지원.
- **음악**: 라이선스 문제로 **내장 오리지널 비트** 사용(참고 영상 음원 미사용).
- **검증**: 빌드 통과 + 타이밍 테스트 6개 통과 + 실제 세션 1회를 끝까지 재생해 동작 전환·
  완료 기록 저장까지 확인. 로컬 실행 포트 **8462**, 스크립트 `pnpm dev:lampas:web:fit`.

## 배포 (`fit.lampas.io`)
- 기존 Lampas 배포 스크립트(`./scripts/deploy-web.sh`) 경로로 진행.
- `fit.lampas.io` 전용 **CloudFront 신규 생성** + 기존 Lampas 와일드카드 인증서로 HTTPS 적용.
- 앱 빌드·S3 업로드·캐시 무효화 완료 → CloudFront 전역 배포 전파 대기 → DNS 연결 마무리.
- 검증 시 하네스 작업 환경 자체의 **DNS 캐시가 예전(조회 실패) 결과를 들고 있어**, 공개 DNS가
  반환한 주소로 별도 재검증 — 작업 환경 캐시를 곧이곧대로 믿지 않고 외부 소스로 재확인한 사례.
- 최종 확인: HTTPS, 시작·일시정지·초기화, 모바일 화면 정상 동작.

## 이 세션이 노출한 더 큰 사실 — `lampas-system` 저장소 구조 스냅샷 (AGENTS.md, 2026-09-26)
세션 최초 시스템 프롬프트로 주입된 저장소 `AGENTS.md` 전문이, 이 위키가 마지막으로 기록한
2026-07-15/18 구조([[lampas-studio]])와 크게 달라진 최신 상태를 드러냄. 상세·모순 기록은
[[lampas-studio]]·[[toktalk]] 엔티티 페이지에 이관:
- 앱 목록이 Lampas 9개 + **Dalar(신규 발견) 6개** + Talk 9개(**구 dbs/talk-system 통합 정황**,
  [[toktalk]]과 모순) + Iileex 1개로 확장.
- `lampas-web-fit`이 이번 세션에서 신규 추가되어 Lampas 목록에 편입(포트 **8462**).
- DB 문서화 값이 **MySQL**로 명시(2026-07-15 확인된 "PostgreSQL"과 모순, → [[lampas-studio]]에 병기).

## 관련
- 제품: [[lampas-studio]] (구조 스냅샷·모순 상세) · [[toktalk]] (Talk 통합 정황)
- 신규: [[dalar]] (스텁, 2026-09-26 최초 노출)
- 인프라: [[lampas-harness]] (codex PTY 도구 경로로 실질 배포 작업 수행)
