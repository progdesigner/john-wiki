---
tags: [entity, project, app, music-generation, lampas-studio, minimax, atlas-cloud]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-music (`music.lampas.io`)

`[[lampas-studio]]` 저장소(`lampas-system`)의 앱. [[atlas-cloud]] 경유로 minimax 음악 생성 모델을
호출하는 음악 생성 웹. `[[lampas-studio]]`의 2026-09-26 `AGENTS.md` 스냅샷에 Lampas 9개 앱 목록 중
하나로 이름만 등장했었고, 이 위키에 상세가 처음 드러난 것은 2026-09-22 모델 업그레이드 세션
([[2026-09-22-music-lampas-io-minimax3.0-업그레이드-배포]])이다.

## 모델

- **생성 모델**: [[atlas-cloud]] 경유 minimax 음악 모델. **2026-09-22: `minimax/music-2.6` →
  `minimax/music-3.0` 업그레이드·운영 배포 완료.** 요청 필드(`prompt`·`lyrics`·`is_instrumental`·
  `format`·`sample_rate`·`bitrate`)가 두 버전 간 동일해 호출 코드 변경 없이 모델 식별자만 교체.
  `apps/lampas-web-music/src/lib/api.ts`의 `MUSIC_MODEL` 상수로 지정.
- **과금**: 곡당 150크레딧(PER_REQUEST) — 3.0도 생성 카탈로그에 동일 단가로 이미 등록돼 있어
  버전 교체가 과금 로직에 영향 없음. 2.6 카탈로그 항목은 삭제하지 않고 유지.
- **3.0 제약**: 최대 5분 곡, prompt 2,000자·lyrics 3,500자. **UI에 길이 제한 로직 없음** —
  다음에 이 한도를 넘는 입력이 서버 에러로 처음 드러날 수 있음(미해결 관찰).
- **버전 불일치**: 자매 앱 `[[lampas-web-tools]]`(`tools.lampas.io`)의 `music-gen` 툴은 2026-09-22
  배포 이후에도 **여전히 minimax 2.6** — 요청 범위가 music.lampas.io로 한정돼 의도적으로 손대지 않음.

## 배포

- 스크립트: `./scripts/deploy-api.sh lampas-api`(오버라이드 포함) + `./scripts/deploy-web.sh
  lampas-web-music`(S3+CloudFront), `[[lampas-studio]]`의 공통 배포 경로 재사용.
- 2026-09-22 배포에 lampas-api 쪽으로 다른 미커밋 변경(dalar 스튜디오·flow-works·pulse 등)이
  같은 빌드에 함께 실림 — music 교체와 무관한 변경들이 섞여 나감(세션 종료 시점 미커밋 상태로 남음).
- `.env.smoke` 부재로 배포 후 스모크 테스트는 건너뜀 — 검증은 운영 API 모델 목록 확인·배포 번들
  직접 확인으로 대체.

## 소비자 — CS 채팅 배경음악 어시스턴트 (신설 후 2026-09-13 제거됨)
[[lampas-web-cs]]에 한때 `cs-music-assistant.service.ts`가 있어 채팅 메시지의 인텐트를 파싱해
`MusicService.publish()`를 `source:'CS_CHAT'`으로 호출(`source:'MUSIC_WEB'`과 구분 저장)하던
소비 경로가 있었다. 2026-09-13 세션에서 이 어시스턴트·프론트 위젯(`MusicPlayer.tsx`)이 사용자
요청으로 전면 제거됐다 — `music` 모듈 자체(`GET /music/tracks` 공개 API, `MusicService.publish`)는
CS와 결합이 약해 영향 없이 계속 운영됨. 관리자 화면 패턴(`admin-music.controller.ts`의
`AdminGuard`)은 오히려 이 세션에서 CS 관리자 화면의 선례로 재사용됨 →
[[admin-guard-precedent-reuse]] · [[2026-09-13-cs-기능수정-음악위젯제거-어드민조회신설]].

## 관련
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 자매 앱: [[lampas-web-tools]] (`music-gen` 툴, 여전히 2.6 — 버전 불일치)
- 자매 앱(소비자, 2026-09-13 소비 경로 제거됨): [[lampas-web-cs]]
- 외부 의존: [[atlas-cloud]] (minimax 음악 모델 라우팅)
- 세션: [[2026-09-22-music-lampas-io-minimax3.0-업그레이드-배포]] ·
  [[2026-09-13-cs-기능수정-음악위젯제거-어드민조회신설]]
