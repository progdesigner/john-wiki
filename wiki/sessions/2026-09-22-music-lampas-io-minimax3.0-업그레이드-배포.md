---
tags: [session, lampas-studio, lampas-web-music, minimax, atlas-cloud, deploy, model-upgrade]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-22 — music.lampas.io 모델 minimax 2.6→3.0 업그레이드·배포

`Tool: claude` 세션(2026-09-22 23:21 UTC 시작, 작업 디렉터리 `lampas-system`). "music.lampas.io 모델을
minimax 3.0 으로 바꿔죠" 요청으로 `[[lampas-web-music]]`의 생성 모델을 교체하고 운영까지 배포한 세션.
이 위키에 `[[lampas-web-music]]`이 처음 상세 노출된 소스이기도 하다(기존엔 `[[lampas-studio]]`의
2026-09-26 `AGENTS.md` 스냅샷 절에 Lampas 9개 앱 목록 중 이름만 등장).

## 배경

`[[lampas-studio]]`가 [[atlas-cloud]] 경유로 접근하는 minimax 음악 생성 모델이 2.6 → 3.0으로
버전업. 요청 필드(`prompt`·`lyrics`·`is_instrumental`·`format`·`sample_rate`·`bitrate`)가 2.6과
동일해 호출 코드 자체는 변경 없이 모델 식별자만 교체 가능함을 Atlas 스키마 확인으로 먼저 검증.

## 만든 것

- `apps/lampas-web-music/src/lib/api.ts`의 `MUSIC_MODEL`을 2.6 → `minimax/music-3.0`으로 교체.
- `model-pricing.overrides.ts`의 web-music 큐레이션 항목을 3.0으로 갱신 — 3.0은 이미 생성 카탈로그에
  곡당 150크레딧(PER_REQUEST)으로 등록돼 있어 과금 로직 변경 없음. 2.6 항목은 카탈로그에 유지.
- `model-pricing.spec.ts`를 3.0 기준으로 갱신 + 2.6 유지 케이스 추가, 9개 통과.
- 문서 갱신: 루트 CLAUDE.md 게이트웨이 표, `docs/credits.md`·`docs/other-apis.md` 예시.
- web-music 타입체크 통과.

## 참고 사항 (배포 전 기록)

- minimax 3.0은 최대 5분 곡, prompt 2,000자·lyrics 3,500자 제한 — **현재 UI에 길이 제한 로직이 없어
  그대로 방치**(다음에 사용자가 제한 초과 입력 시 서버 에러로 처음 드러날 가능성).
- `[[lampas-web-tools]]`(`tools.lampas.io`)의 `music-gen` 툴은 **여전히 2.6** — 이번 요청 범위가
  music.lampas.io 하나뿐이라 의도적으로 손대지 않음. 버전 불일치가 남은 상태.

## 운영 배포 (사용자 지시 "작업이 완료 되면 배포해")

1. 배포 전 lampas-api 스키마 드리프트 확인 절차(→ [[prod-ddl-before-deploy-with-drift-check]] 관례)
   실행 — 드리프트 없음. 새 DB 테이블이 없는 순수 모델 교체라 이 세션 자체가 그 스킬의 전형적
   적용 사례는 아니지만, 배포 전 습관적으로 드리프트를 먼저 확인하는 절차가 DDL 유무와 무관하게
   지켜지고 있음을 보여준다.
2. Prisma 클라이언트를 lampas 스키마로 재생성.
3. `./scripts/deploy-api.sh lampas-api`(API, 오버라이드 포함)와
   `./scripts/deploy-web.sh lampas-web-music`(웹, S3+CloudFront) 병렬 배포.
4. PM2에서 `lampas-api` 0.1.137 정상 기동, 웹은 S3 업로드+CloudFront 무효화까지 완료.

## 검증 (운영)

- 운영 API 모델 목록에 `minimax/music-3.0`이 곡당 150크레딧(PER_REQUEST)으로 노출 확인.
- `music.lampas.io` 실제 배포 번들에 `minimax/music-3.0`만 포함된 것 확인.
- **스모크 테스트는 건너뜀** — `.env.smoke`가 없어 두 배포 스크립트 모두 배포 후 스모크 단계 스킵.

## 알아둘 점

- **이번 API 배포에 다른 미커밋 변경(dalar 스튜디오·flow-works·pulse 등)도 함께 실려 올라감** —
  music 모델 교체와 무관한 변경들이 같은 빌드에 섞여 배포됨. 이 변경분은 세션 종료 시점까지
  **커밋되지 않음** — 다음 세션에서 커밋 상태·의도 재확인 필요.
- `tools.lampas.io`의 음악 툴은 배포 후에도 여전히 2.6.

## 관련
- 엔티티: [[lampas-web-music]](신설) · [[lampas-studio]] · [[atlas-cloud]] · [[lampas-web-tools]]
- 스킬: [[prod-ddl-before-deploy-with-drift-check]]
- 원본: `raw/conversations/2026-09-22-music-lampas-io-minimax3.0-업그레이드-배포.md`
