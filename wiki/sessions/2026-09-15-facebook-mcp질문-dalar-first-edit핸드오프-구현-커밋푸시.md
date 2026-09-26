---
tags: [session, dalar-web-first, lampas-web-edit, lampas-api, edit-sessions, handoff, facebook, mcp, git-commit]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-15 — Facebook MCP 질문 + dalar-web-first→lampas-web-edit 컷 핸드오프 구현 + 선택적 커밋·푸시

`Tool: claude`, 작업 폴더 `lampas-system`. 세션 시작 2026-09-15T02:27:22Z. **2026-09-26 뒤늦게
ingest**(아카이브 파일 제목은 "AI Dalar 로 패키징 하는 기능 만들기"이나 실제 1턴은 무관한 Facebook
MCP 질문). `[[dalar-web-first]]`("First") 관련 세션 중 [[2026-09-13-dalar-web-first-최초구축-오만크레딧결제요청]](09-13~14)과
[[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]](09-20~) **사이**에 위치하는, 지금까지
위키에 없던 중간 시점 이벤트다.

## 1턴 — Facebook 앱 생성 MCP 질문 (무관한 선행 질문)
"facebook app 을 만들 수 있는 mcp 가 있나?"에 대해 웹 검색 후 답변: **Meta 앱 자체를 생성해주는
MCP는 없다**(Meta가 앱 생성 자체를 Graph API로 공개하지 않음). MCP가 할 수 있는 건 이미 만든
앱/토큰으로 Graph API를 호출하는 것뿐 — 공식 Meta Ads MCP(`mcp.facebook.com/ads`, 광고 전용
29툴, Business OAuth)와 커뮤니티 Graph API MCP(jordanburke/meta-graph-api-mcp-server 등, 페이지
게시·인사이트·미디어 업로드, 단 셋업 첫 단계가 여전히 "대시보드에서 앱 만들기")를 대안으로 제시.
대시보드 클릭을 Playwright로 자동화하는 것은 2FA·캡차·ToS 위험으로 **권장하지 않음**이라 명시.
이 저장소 맥락(packaging 모듈의 인스타 게시, lampas-api의 Instagram Business Discovery)과
연결지었으나 실제 MCP 연결 진행은 없이 다음 요청으로 넘어감 — 콘텐츠 가치는 낮으나 Meta/Facebook
MCP 생태계에 대한 정리된 답변으로 기록.

## 2턴 — dalar-web-first 관리자 → lampas-web-edit 컷 핸드오프 구현
요청: "dalar web first admin 에서 콘티마다 각각 생성된 영상을 lampas-web-edit 으로 보낼 수 있게
해줘." 착수 전 dalar-web-first admin 코드와 **기존 edit-sessions 핸드오프 패턴(scenario/reels)**을
먼저 확인 — 즉 이 시점에 이미 `[[lampas-web-scenario]]`·`[[lampas-web-reels]]`가 각각
`lampas-web-edit`으로 세션을 넘기는 핸드오프 경로가 존재했고, 이번 작업은 그 패턴을 **First(3번째
소스 앱)로 확장**하는 것이었다.

### 동작 방식
- **컷별 버튼**: 관리자 주문 화면 컷 편집기(`AdminSceneEditor.tsx`)에 컷마다 "이 컷 편집기로
  보내기" 버튼, 상단 바에 "영상 있는 컷 전부 편집기로" 일괄 버튼.
- **세션 생성**: 컷의 영상 URL을 url 클립으로 담아 편집 세션을 만들고 `lampas-web-edit`의
  `/editor?session=<id>`를 새 탭으로 연다.
- **계정 문제**: 편집 세션은 Lampas 사용자 소유라 admin JWT로는 만들 수 없음. 해결책 —
  **"Lampas 계정 연결"** 버튼을 상단 바에 두고, 기존 Google 팝업 브리지(`googlePopup.ts`)를 재사용해
  lampas 네임스페이스(`edit.lampas.io` 로그인과 동일 계정)로 토큰을 받아 **sessionStorage에만**
  보관, 딥링크에 **`access_token` 쿼리 파라미터로 직접** 실어 보낸다. 연결 안 된 상태로 보내기를
  누르면 연결 팝업이 먼저 뜬다.
  - **주의**: 이 방식은 [[cross-subdomain-session-handoff]] 스킬이 나중(09-20)에 정식화한 "토큰을
    URL에 넣지 않고 일회용 교환 코드로 대체" 원칙과 다르다 — 이 세션은 5일 앞선 시점이라 **토큰을
    URL에 직접 실어 보내는 더 단순하고 약한 방식**을 썼다. 두 세션이 같은 문제(서로 다른 로그인
    저장소 간 세션 이어받기)를 다른 방식으로 풀었다는 점에서 대비된다 — 이후 First의 이 경로가
    교환 코드 방식으로 개정됐는지는 이 소스로 확인 불가.
- **세션 재사용**: 같은 주문·컷은 편집 세션 1개를 재사용(reuse 키 `<orderId>:scene:<n>`, 전체는
  `<orderId>:all`) — 다시 눌러도 이어서 편집.
  - **주의**: 이 재사용 키가 사실상 [[cross-subdomain-session-handoff]] 스킬 절차와 무관한 **별도
    멱등 패턴**(같은 리소스에 대한 핸드오프 반복 클릭을 새 세션 난립 대신 기존 세션으로 수렴시키는
    설계)이라 향후 다른 핸드오프 지점(Copy·Package 등)에도 재사용할 만한 절차로 눈여겨볼 만하다.
- **팝업 차단 대비**: 새 탭이 막히면 카드에 "편집기 열기" 링크가 대신 노출.

### 변경 파일
- `apps/dalar-web-first`: `src/lib/editHandoff.ts`, `src/lib/googlePopup.ts`(+스펙),
  `src/components/molecules/EditorHandoff.tsx`(훅+상단 바), `AdminSceneEditor.tsx` 버튼, `first.css`,
  env 두 벌에 `VITE_EDIT_APP_URL` 추가, **vitest 도입**(`pnpm test`, 이 앱에 테스트 프레임워크가
  없었다는 뜻), README.
- `apps/lampas-api`: **edit-sessions·media-exports 앱 화이트리스트에 `first` 추가**(First가
  3번째 화이트리스트 소스 앱이 됨) + LOG.md.
- `apps/lampas-web-edit`: `EditSessionApp` 타입과 앱 라벨에 First 추가.
- 루트 `CLAUDE.md`의 edit-sessions 행 갱신.

### 검증
First vitest 13개, edit vitest 22개, API jest(edit-sessions·media-exports) 41개 통과. First
`tsc`+`vite build`, edit `tsc` 통과. **배포는 하지 않음** — 배포 순서만 안내(lampas-api 화이트리스트
먼저 → lampas-web-edit → dalar-web-first), API 배포 전 스키마 드리프트 확인 절차([[prod-ddl-before-deploy-with-drift-check]])가
여전히 유효하다고 명시.

## 3턴 — "배포 말고 커밋·푸시만" — 선택적 헝크 커밋
요청: "작업한거 배포 하지 말고 커밋 후 푸시만 해줘." 작업 트리엔 **이전 세션의 미커밋 변경**(First
관리자 화면 재구성, CS 모듈 정리 등)이 다수 남아 있어, 이번 핸드오프 작업 범위만 골라야 했다.

- **선택적 헝크 커밋**: 5개 공유 파일(`AdminSceneEditor.tsx`, `first.css`, `package.json`,
  `LOG.md`, `CLAUDE.md`)과 lockfile은 HEAD 기준으로 **이번 변경 헝크만** 스테이징 — 나머지 이전
  미커밋 변경은 작업 트리에 그대로 남김.
  - `package.json`의 버전 bump(0.1.21→0.1.30)는 이전 작업 소속이라 **이번 커밋에서 제외**(배포
    스크립트가 재bump하므로 문제 없다고 판단).
- **커밋 전 검증**: 스테이징된 트리만 별도로 꺼내 First `tsc`+vitest 13개, edit `tsc` 통과 확인 —
  이전 미커밋 작업 없이도 이 커밋 단독으로 빌드됨을 입증.
- **결과**: `main`에 커밋 `dbacd8c8`로 push 완료. 배포는 하지 않음.
- 이 절차는 [[selective-hunk-commit-shared-file]] 스킬(원 출처 09-19/09-24 세션)의 핵심 절차와
  거의 동일하다 — **이 세션이 4~9일 앞선 더 이른 실사례**다. 스킬 페이지에 이 세션을 이른 출처로
  추가.

## 관련
- 엔티티: [[dalar-web-first]](이 세션으로 09-13/14와 09-20 사이 공백 메워짐) ·
  [[lampas-web-edit]](edit-sessions 화이트리스트 세 번째 앱으로 First 추가) · [[lampas-api]]
- 선행: [[2026-09-13-dalar-web-first-최초구축-오만크레딧결제요청]]
- 후속: [[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]]
- 스킬: [[selective-hunk-commit-shared-file]](더 이른 출처로 추가) ·
  [[cross-subdomain-session-handoff]](대비되는 더 단순·약한 이전 방식으로 교차기록) ·
  [[prod-ddl-before-deploy-with-drift-check]](배포 순서 언급)
