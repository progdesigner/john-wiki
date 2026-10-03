---
tags: [entity, project, app, lampas-studio, admin, react, jwt, admin-guard, signup-abuse, credit, layout]
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

## 가입 도메인 필터 · 크레딧 회수 · 이상 가입 대시보드 (2026-09-26)
`lampas-api` auth에 도메인별 가입 상한 게이트가 추가되면서 web-admin에 대응하는 관리 UI가
신설됐다([[signup-domain-abuse-rate-limit-and-reclaim]] 절차):
- **`/signup-domains` 페이지**(신규): 필터 도메인 등록·상한 수정·소속 유저 조회·일괄/개별 크레딧
  회수·삭제. 대시보드 "필터에 추가" 버튼이 도메인을 프리필한 채 이 페이지로 이동시킨다.
- **대시보드 "이상 가입 감지" 섹션**(신규): 이메일 도메인별 24h/7d/30d/누적 가입 수 + 잔액 합.
  gmail 등 흔한 공급자는 제외하고 24h 3개·7d 5개·누적 10개 이상이면 "의심"으로 표시.
- **유저 상세 화면에 회수 버튼** 추가(개별 계정 크레딧 회수).

## 크레딧 지급/회수 버튼 통합 (2026-09-26, 커밋 `5ba07f9b`)
사용자 목록의 기존 "지급" 버튼을 **"크레딧"** 버튼 하나로 합쳐, 클릭 시 모달에서 **지급/회수** 탭을
고르는 방식으로 변경(위 도메인 필터 작업 배포 직후 별도 피드백으로 추가):
- 지급 탭은 기존과 동일(프리셋·양수/음수 입력·사유 메모).
- 회수 탭은 **잔액 전부 회수**(잔액 0) 또는 **가입 보너스만 회수**(미회수 보너스, 잔액 한도) 중 선택.
  유저 정보는 유지, 원장에 관리자 차감으로 기록, 잔액 0이면 버튼 비활성화.
- 처리 후 목록 잔액 즉시 갱신. 사용자 상세 페이지의 기존 충전·차감 폼 + 헤더 "보너스 회수/전액 회수"
  버튼은 그대로 유지돼 회수 진입점이 목록/상세 두 군데 공존한다.

## 전체 폭 레이아웃 (2026-09-26, 커밋 `5911f67d`)
상단 바·본문·리뷰 분쟁 페이지의 폭 제한을 없애 전체 화면을 쓰도록 변경 + 메뉴 글자 줄바꿈 방지("대시보
드"처럼 중간에 잘리던 문제 수정), 좁은 화면에서는 메뉴가 가로 스크롤되도록 함.

## 배포
- 배포 스크립트는 [[lampas-studio]] 공통 경로(`deploy-web.sh lampas-web-admin`) 재사용.
- 2026-09-13: CS 조회 화면 배포 완료(admin.lampas.io), lampas-api·lampas-web-cs와 함께 3종 순차 배포.
- 2026-09-26: 가입 도메인 필터·대시보드(1차)·전체 폭 레이아웃(2차)·크레딧 버튼 통합(3차) 순서로 같은
  날 3회 배포. 가입 도메인 필터 배포는 `lampas-api` 워킹트리에 다른 세션의 미커밋 packaging 변경이
  섞여 있어 1차 턴에서 보류됐다가, 2차 턴 시점엔 다른 세션이 이미 API를 0.1.154로 배포해 둔 상태를
  확인 후 web-admin 레이아웃만 배포했다.

## `/spot` — 맛집 지도 관리 모듈 (2026-09-26, [[lampas-web-spot]] 재구축의 일부)
`spot.lampas.io`를 "주소만으로 맛집 데이터를 생성하는 서비스"로 재편하면서 신설된 관리 화면.
주소 찾기 → 카카오 후보 선택 → 등록, "이름 | 주소" 여러 줄 일괄 등록, 상태별 목록, 재수집, 숨김·
삭제, 등급·소개·사진 순서 편집. 등록한 장소는 워커가 우선 처리해 보통 30초 안에 지도에 반영된다.
**신고로 숨겨진 장소의 복원은 이 화면(또는 spot 웹 자체 관리자 모드)에서만 가능**하도록 바뀌었다 —
이전엔 신고자 본인이 개인 화면에서 복원할 수 있었다. 상세 → [[lampas-web-spot]] ·
[[2026-09-26-spot-주소기반재구축-카카오맵전환-네이버보강-채팅검색]].

## 관련
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 이미 통합된 도메인(선례): [[lampas-web-music]](`admin-music.controller.ts`, `AdminGuard`)
- 이 세션에서 신규 통합된 도메인: [[lampas-web-cs]]
- 2026-09-26 추가 모듈: [[lampas-web-spot]](`/spot`, 별도 세션)
- 세션: [[2026-09-13-cs-기능수정-음악위젯제거-어드민조회신설]] ·
  [[2026-09-26-람파스-가입도메인필터-크레딧회수-대시보드-레이아웃]] ·
  [[2026-09-26-spot-주소기반재구축-카카오맵전환-네이버보강-채팅검색]]
- 스킬: [[admin-guard-precedent-reuse]] · [[signup-domain-abuse-rate-limit-and-reclaim]] ·
  [[credit-ledger-balance-pattern]]
