---
tags: [entity, project, app, lampas-studio, admin, react, jwt, admin-guard]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-admin (`admin.lampas.io`)

[[lampas-studio]] 저장소(`lampas-system`)의 관리자 콘솔. 2026-07-15 앱 구성 조사에서 Lampas 6앱 중
하나로 이름만 등장했었고, 이 위키에 내부 패턴이 처음 상세히 드러난 것은
[[2026-09-13-cs-기능수정-음악위젯제거-어드민조회신설]] 세션(CS 조회 화면 신설 작업 중 기존 구조를
조사)이다.

## 인증·라우팅 패턴 (2026-09-13 조사 기준)
- **인증**: `lib/auth.ts`의 `getAdminToken()` — 로그인은 `POST /admin/auth/login`, 백엔드
  `AdminGuard`가 발급 JWT의 `role==='admin'`을 검증. **이 로그인 JWT가 web-admin 전역 인증 수단** —
  개별 도메인 모듈이 별도 토큰 가드를 쓰면 web-admin이 재사용할 수 없다(→ 아래 CS 사례,
  [[admin-guard-precedent-reuse]]).
- **API 클라이언트**: `lib/api.ts`의 `request<T>()` 헬퍼 — 호스트 `VITE_API_URL` + `/v1` prefix,
  `Authorization: Bearer <adminJWT>` 자동 첨부, 401 시 로그인 리다이렉트. 도메인별 함수(예:
  `listMusicTracks`/`deleteMusicTrack`)를 이 파일에 export하는 것이 관례.
- **라우팅**: `App.tsx`에 `<Route path="/music" element={<MusicTracksPage/>}>` 식으로
  `AdminLayout`(`ProtectedRoute`로 감싸짐) 하위에 추가.
- **페이지 템플릿**: `MusicTracksPage.tsx`(단순 목록+삭제) · `DisputesPage.tsx`(상세 카드+액션
  버튼) — 이 둘이 신규 도메인 조회 화면의 표준 템플릿(`useState`+`useCallback reload`+로딩/에러
  처리 패턴 공통).

## 신규 도메인을 web-admin에 연결할 때의 함정 — CS 사례 (2026-09-13)
[[lampas-web-cs]]의 관리자 API(`/cs/admin/sessions*`)는 이미 존재했지만, web-admin의 `AdminGuard`가
아니라 **CS 전용 `AdminTokenGuard`**(`CS_ADMIN_TOKEN` 단순 Bearer 대조 — web-admin 로그인 JWT를
받아들이지 않음)로 보호되고 있어 그대로 재사용할 수 없었다. 반면 `music` 모듈은 이미
`admin-music.controller.ts`가 `AdminGuard`를 쓰는 정확히 같은 패턴으로 통합돼 있었다 — 이 세션은
그 **music 모듈의 선례를 그대로 따라 신규 `admin/cs/*` 컨트롤러를 `AdminGuard` 기반으로 구현**하는
쪽을 택했다(레거시 `CS_ADMIN_TOKEN` 경로를 폐기했는지는 소스에 명시 안 됨 — 확인 필요). 결과물:
- `CsSessionsPage.tsx`(세션 목록) · `CsSessionDetailPage.tsx`(대화 상세+상담원 답장) 신설,
  `MusicTracksPage.tsx`/`DisputesPage.tsx` 템플릿 재사용.
- 상단 네비게이션에 "CS 문의" 메뉴 추가(`/cs`, `/cs/:id`).
- 절차 일반화 → [[admin-guard-precedent-reuse]] 스킬.

## 배포
- 배포 스크립트는 [[lampas-studio]] 공통 경로(`deploy-web.sh lampas-web-admin`) 재사용.
- 2026-09-13: CS 조회 화면 배포 완료(admin.lampas.io), lampas-api·lampas-web-cs와 함께 3종 순차 배포.

## 관련
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 이미 통합된 도메인(선례): [[lampas-web-music]](`admin-music.controller.ts`, `AdminGuard`)
- 이 세션에서 신규 통합된 도메인: [[lampas-web-cs]]
- 세션: [[2026-09-13-cs-기능수정-음악위젯제거-어드민조회신설]]
