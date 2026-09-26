---
tags: [entity, project, app, lampas-studio, cs, chat, customer-support]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-cs (`cs.lampas.io`)

[[lampas-studio]] 저장소(`lampas-system`)의 앱. 사용자가 상담원과 대화하는 CS(고객상담) 채팅 위젯.
`lampas-web-fit` 세션의 `AGENTS.md` 스냅샷(Lampas 9앱 목록)에 이름만 등장했었고, 이 위키에 상세가
처음 드러난 것은 [[2026-09-13-cs-기능수정-음악위젯제거-어드민조회신설]] 세션이다.

## 구조 (2026-09-13 조사 기준)

- **백엔드**(`apps/lampas-api/src/modules/cs/`) — `cs.controller.ts`(`createMessage` 등 진입점),
  `cs.service.ts`(세션·메시지 상태 변경), `cs-store.service.ts`(저장), `cs.types.ts`.
- **저장**: DB 없이 `data/cs-store.json`(경로는 `CS_DATA_DIR` env)에 `{sessions, messages}` 전체를
  저장, 300ms debounce flush(임시파일→rename). `CsSession`(id, code, name, email, topic, service,
  status)과 `CsMessage`(id, sessionId, role: USER/AGENT/SYSTEM, content, imageUrls[], createdAt)
  두 배열뿐인 단순 구조.
- **프론트**(`apps/lampas-web-cs/src/`) — `App.tsx`, `ChatRoom.tsx`(세션 폴링).
- **관리자 API**: `cs.controller.ts` 하단에 `/cs/admin/sessions`(목록)·`/cs/admin/sessions/:id`(상세)·
  `/cs/admin/sessions/:id/messages`(상담원 답장)가 있었지만, [[lampas-web-admin]]이 쓰는 로그인 JWT
  가드를 받아들이지 않는 CS 전용 `AdminTokenGuard`(`CS_ADMIN_TOKEN` 단순 Bearer 대조)로만 보호돼
  재사용이 불가했음 — 2026-09-13 세션에서 `AdminGuard` 패턴의 신규 `admin/cs/*` 경로로 별도 해결
  (상세 → [[lampas-web-admin]] · [[admin-guard-precedent-reuse]]).

## 배경음악 어시스턴트 — 신설됐다가 제거됨 (2026-09-13)

한때 채팅에 **배경음악 어시스턴트**가 있었다 — `cs-music-assistant.service.ts`가 메시지를
키워드 프리필터→Grok 인텐트 파싱(`SET_GENRE`/`GENERATE`/`NONE`)으로 분석해, 장르 설정이나
[[lampas-web-music]]의 `MusicService.publish()`(`source:'CS_CHAT'`)를 통한 곡 자동생성을 트리거했다
(세션당 상한 3곡). 프론트에는 `components/MusicPlayer.tsx`(재생/일시정지/다음곡/장르 배지 위젯,
`GET /music/tracks` 60초 폴링)가 붙어 있었다. **2026-09-13 세션에서 이 어시스턴트·위젯을 전면
제거** — 사용자가 스크린샷으로 해당 위젯 영역을 가리키며 제거를 요청. `music` 모듈 자체와 수동 재생
위젯은 CS와 결합이 약해 분리 제거가 쉬웠고, [[lampas-web-music]]은 이 제거와 무관하게 계속 운영됨.
절차 → [[full-stack-feature-removal-audit]].

## 배포
- 배포 스크립트는 [[lampas-studio]] 공통 경로(`deploy-web.sh lampas-web-cs`) 재사용.
- 2026-09-13: 음악 위젯 제거분 `lampas-api`→`lampas-web-cs`(cs.lampas.io) 순으로 배포 완료.

## 관련
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 자매 앱: [[lampas-web-admin]](관리자 조회 화면) · [[lampas-web-music]](음악 모듈, 제거된 CS 소비 경로)
- 세션: [[2026-09-13-cs-기능수정-음악위젯제거-어드민조회신설]]
