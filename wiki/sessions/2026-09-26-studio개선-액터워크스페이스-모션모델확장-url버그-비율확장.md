---
tags: [session, lampas-studio, dalar, atlas-cloud, sync-studio, motion-video, aspect-ratio, bugfix]
created: 2026-10-03
updated: 2026-10-03
---
# 2026-09-26 Studio 개선 — 워크스페이스 Actor 선택·모션 모델 확장·이미지 URL 버그·비율 확장

`Tool: claude`, `lampas-system` 작업 폴더, 09:46Z 시작. 하루 동안 이미지 8장을 첨부한 사용자
피드백이 9라운드 이어진 장시간 세션 — 각 요청마다 조사→구현→테스트→**네 앱 배포**(`lampas-web-studio`·
`dalar-web-app`·`lampas-api`·`dalar-api`)→커밋을 반복했다. 전 구간에서 [[dalar]]의 "`dalar-web-app`이
Node Studio SoT, `pnpm sync:studio`로 `lampas-web-studio`에 반영" 패턴이 **일관되게 재확인**됨
(2026-09-24·09-26 앞선 두 세션에 이어 네 번째~아홉 번째 반복 실행).

## 1. 워크스페이스 간 Actor 선택 불가 수정 (커밋 `a6cf3b16`)
Node Studio Work 화면은 상단 `ScopeSwitcher`를 숨기는 구조(backAction 진입)라 화면 안에서
워크스페이스를 바꿀 길이 없어, Actor/Object/Space 선택 다이얼로그가 **전역 스코프 목록만** 불러와
다른 워크스페이스 소속 Actor를 고를 수 없었다. 선택 다이얼로그 헤더에 "범위"(Draft + 내 워크스페이스)
셀렉트를 추가 — 다이얼로그 안에서만 바뀌고 Work의 저장 스코프는 건드리지 않는다. 다른 워크스페이스에서
고른 엔티티는 노드 데이터 스냅샷(이름·이미지·레퍼런스 시트)으로 복원해 목록에 없어도 노드에 계속
표시된다. 순수 로직 `node/libs/entityPickerScope.js` 분리, vitest 10개. studio 256개·dalar 488개
통과.

## 2. 모션 촬영 모델 2종 → 6종 확장 (커밋 `e0544eb7`·`1c9c9e66`)
"할 수 있는 모델 다 넣어줘" 요청에 Atlas 카탈로그에서 "이미지+레퍼런스 영상→움직임 이식"이 가능한
모델 전부를 찾아 추가:

| 모델 | 크레딧 | 비고 |
|---|---|---|
| Kling v2.6 Pro/Std | 112/70 cr/초 | 기존 |
| Kling v3.0 Pro/Std | 168/126 cr/초 | 신규 |
| Wan 2.2 Animate Move | 120 cr/초 | 이미지 인물에 영상 움직임 이식 |
| Wan 2.2 Animate Mix | 180 cr/초 | 영상 속 인물을 이미지 인물로 교체 |

Wan 2.2는 요청 본문이 `image`·`video`·`mode`만(프롬프트·사운드 옵션 없음) — 모델별 본문 생성을
`motion-control-body.ts` 순수 모듈로 분리해 lampas-api·dalar-api가 공유. `lampas-api`는 다른
세션의 미커밋 packaging 변경을 피해 **HEAD 기준 임시 git worktree**에서 배포 후 제거.

## 3. Seedance reference-to-video를 모션 촬영에 통합 (후속 요청)
사용자가 "See Dance는 모션 컨트롤러가 없어?"로 질문 → Seedance엔 Kling류 전용 motion-control
엔드포인트가 없고, `reference-to-video` 변형(`reference_images`+`reference_videos`)이 **"동작을
참고한 재생성"**(프레임 단위 그대로 옮기는 게 아님, 정체성 유지 보장 없음)으로 비슷한 효과를 낸다고
설명 → 사용자가 "그거 포함해서 할 수 있게 해줘"로 확정, Seedance 2.0/2.0 Mini/2.0 Fast/2.5를 모션
촬영 노드에 추가(이미지→`reference_images[0]`, 영상→`reference_videos[0]`).

## 4. 영상 생성 실패 진단 — imageData/imageUrl 필드 혼동 버그 (근본 원인 2건)
사용자가 "영상 생성이 안 되는데 원인 찾아줘" 요청. 운영 DB의 실패 잡을 직접 조회해 발견:
- **직접 원인**: 스튜디오 엔진의 이미지 해석 함수가 `previewSrc`가 https URL이면 변환 없이 **URL
  문자열을 그대로** 반환하는데, 모션 촬영 실행부가 이 값을 base64 전용 필드 `imageData`에 그대로
  담아 보냈다. 서버는 `imageData`를 무조건 base64 디코딩해 S3에 올리므로, ~92자 URL 문자열이
  69바이트 쓰레기 PNG로 둔갑 — ByteDance TOS가 "이미지 형식 미지원"으로 거부. Seedance만의 문제가
  아니라 **모션 촬영 전체의 버그**(Kling도 동일하게 깨진 이미지를 받는 중이었고, 이 경로로 성공한
  잡은 과거 4월 1건뿐).
- **부가 원인**: 레퍼런스 영상 1.5초가 Seedance(2~15/30초)·Wan 2.2(2~30초) 최소 길이 미달(Kling은
  하한 없음).
- 절차·일반화 → [[url-vs-base64-field-ambiguity]]

## 5. 갤러리 버튼 통합으로 수정 (커밋 `01d8e394`·`6c0796d0`)
별도 "URL 만들기" 버튼을 새로 만들지 않고, 이미 URL을 만드는 역할을 하던 **갤러리 저장 버튼에 통합**:
샷 노드 버튼은 이미지에 https URL이 없으면 항상 보이고 생기면 사라진다(갤러리 미등록 시 "갤러리
저장", 갤러리 id는 있는데 URL만 없으면 "이미지 링크 만들기"). 엔진 액션 `ensureShotImageUrl`(기존
URL→액터 에셋 URL 재조회→갤러리 에셋 URL 재조회→갤러리 업로드 순으로 확보)을 버튼과 모션 촬영이
공유. 모션 촬영은 이제 URL을 `imageUrl`로 먼저 보내고 data URL은 최후 폴백, 서버도 `imageData`가
http(s)로 시작하면 URL로 처리하도록 방어. 레퍼런스 영상 길이를 브라우저에서 사전 검사해 조건 밖이면
호출 전에 막는다. 순수 로직 `node/libs/shotImageUrl.js` 분리. studio 266개·dalar-web-app 498개
통과.

## 6. 모션 컨트롤 비율 선택 (커밋 `df0d1e6a`·`2fa7f98d`)
비율 파라미터를 받는 모션 모델은 Seedance(`ratio`)뿐(Kling·Wan은 레퍼런스 영상 비율을 그대로
따름) — Seedance 선택 시에만 비율 셀렉트(원본·9:16·16:9·1:1·4:3·3:4·21:9) 노출. `aspectRatio`로
API에 전달, 옵션 밖 값은 adaptive로 정규화.

## 7. 동영상 편집 노드 비율 선택 (커밋 `eede790c`·`11c242a7`)
편집 모델 중 비율을 직접 받는 건 Wan 2.7 Video Edit뿐 — 이를 세 번째 모델로 추가하고, 나머지
(xAI Imagine Edit·Gemini Omni Flash Edit)는 **서버가 `ffmpeg-static`으로 센터 크롭** 후 편집(
`iw/ih` 비율 필터, 짝수 크기 보정, 720×1280 클립 검증 완료). 소스는 URL 다운로드(200MB 상한) 또는
data URL. → [[atlas-cloud]] 영상 편집 레지스트리 절 참고.

## 8. 레퍼런스 라이브러리 업로드가 저장 안 되는 버그 (커밋 `675f7e04`·`1c677f1a`)
업로드한 레퍼런스가 "모아둔 레퍼런스"에 안 쌓이던 원인: 라이브러리 항목 id가 `u:https://…` 형태인데
이를 경로 파라미터(`PUT /v1/references/library/:id`)로 보내면 **프록시가 `%2F`를 풀고 `//`를 `/`로
합쳐** 서버 라우트와 불일치, 저장 요청이 전부 404 — 삭제(`DELETE /:id`)도 동일 문제. 메모리엔 잠깐
보이다 새로고침하면 사라졌고 운영 VIDEO 라이브러리 행은 0건. id를 **본문으로 받는**
`POST /v1/references/library/upsert`·`/remove` 신설(기존 PUT/DELETE는 유지)로 해결. 절차·일반화 →
[[url-shaped-id-as-rest-path-param]]. `app.dalar.ai`의 `dalar-api`엔 이 라우트 자체가 없어 여전히
메모리에만 남는 상태로 남김(요청 없었음).

## 9. "샷이 갤러리에 없다" — 스코프 필터링 설명 (코드 변경 없음, 조사만)
샷 #561은 서버에 정상 저장돼 있었으나, 갤러리가 **"지금 선택된 스코프의 액터" 것만** 모으는 구조라
소속 액터(워크스페이스 "Gabby")가 현재 갤러리 스코프(Draft 또는 다른 워크스페이스)와 다르면 안
보인다. 1번 기능(워크스페이스 간 Actor 선택 허용)이 새로 만들어낸 부작용 — Work의 스코프와 연결
액터의 스코프가 달라지는 경우가 처음 생겼다. 개선안("내 전체" 보기+워크스페이스 배지, 액터 목록
20개 페이지 제한 문제)은 제시만 하고 미구현. 절차·일반화 → [[scope-filtered-list-hides-cross-scope-items]]

## 10. 모션 촬영을 Actor 없이 동작하도록 확장 (커밋 `3130445a`·`0fc64b3f`)
기존엔 액터 전용 API(`/actors/:key/generate-motion-video`)만 있어 레퍼런스 이미지·검색 이미지·
액터 없는 샷 연결 시 "Actor가 필요합니다"로 막혔다. 동영상 편집과 같은 내구 잡 방식의 액터 비종속
`POST /v1/videos/motion`(lampas-api)·predictionId 방식(dalar-api)을 신설 — 완료 시 `actorKey`가
잡히면 액터 에셋으로, 아니면 스튜디오 갤러리(액터 비종속)로 자동 저장되고 어느 쪽이든 갤러리
"전체"에서 보인다. 입력 판별(URL/data URL 파싱) 순수 함수 `videos/lib/media-input.ts` 분리.
기존 액터 전용 API는 SDK 호환을 위해 유지.

## 11. 이미지 노드 비율 선택 1:1/2:3/3:2 세 칸 → 10종 확장 (커밋 `747efe9f`·`d2c4552c`)
샷·레퍼런스 이미지·갤러리 노드의 비율 토글을 공용 `AspectRatioPicker` 셀렉트로 교체, 이미지 생성
API가 받는 10종(세로 9:16·2:3·3:4·4:5, 정사각 1:1, 가로 5:4·4:3·3:2·16:9·21:9) 전부 노출. 노드
폭이 비율에 비례해 달라짐. 갤러리 에셋이 16:9 등 넓은 비율로 저장돼 있으면 이제 그대로 유지(전엔
2:3로 뭉개짐). 영상 촬영 노드에 샷을 연결할 때는 영상 API 허용 비율(1:1·16:9·9:16·4:3·3:4·3:2·2:3)
로만 전달되고 4:5·5:4·21:9는 영상 노드가 자체 비율로 대체. 서버 변경 없어 웹 두 개만 배포. studio
274개·dalar-web-app 505개 통과.

## 관련
- 저장소/제품: [[lampas-studio]] · [[dalar]](SoT sync 4~9번째 실행 확인) · [[atlas-cloud]](모션·편집
  모델 레지스트리)
- 스킬: [[url-vs-base64-field-ambiguity]] · [[url-shaped-id-as-rest-path-param]] ·
  [[scope-filtered-list-hides-cross-scope-items]] · [[selective-hunk-commit-shared-file]](공유
  워킹트리 상태 확인 습관과 동일 계열)
- 커밋: `a6cf3b16`(워크스페이스 Actor 선택) · `e0544eb7`/`1c9c9e66`(모션 모델 6종) ·
  (미표기 커밋, Seedance reference-to-video 통합) · `01d8e394`/`6c0796d0`(갤러리 버튼 통합·URL
  버그 수정) · `df0d1e6a`/`2fa7f98d`(모션 비율) · `eede790c`/`11c242a7`(편집 비율·서버 크롭) ·
  `675f7e04`/`1c677f1a`(레퍼런스 라이브러리 저장 버그) · `3130445a`/`0fc64b3f`(Actor 비종속 모션) ·
  `747efe9f`/`d2c4552c`(이미지 비율 10종)
