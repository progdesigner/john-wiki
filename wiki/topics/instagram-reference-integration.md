---
tags: [topic, instagram, api, reference, scraping, pagination]
created: 2026-07-09
updated: 2026-07-09
---
# Instagram 레퍼런스 통합

`[[lampas-studio]]`가 Instagram에서 레퍼런스 이미지를 가져오는 방법의 총정리.
공식 API의 한계 때문에 **비공식 경로 + 크롤러 트릭**을 조합했고, 여러 접근을 시도하다 폐기한 이력이 있다.
출처: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]].

## 소스 3종 + 포스트 URL

| 소스 | 방법 | 페이지네이션 | 캐러셀 자식 |
|------|------|------------|-----------|
| 연결 계정(Business Discovery) | Graph API, 연결 계정 토큰 | `media.after(cursor)` | `children{media_type,media_url,thumbnail_url}` |
| 공개 프로필 | 비로그인 `api/v1/feed/user/{id}/?max_id=…` | `{lastPk}_{igUserId}` 커서 | `edge_sidecar_to_children`(첫 페이지) / `carousel_media[]`(feed) |
| Google 검색 폴백 | 검색 결과 | 없음(`nextCursor: null`) | — |
| 포스트 URL(`/p/`,`/reel/`,`/tv/`) | facebookexternalhit UA HTML 파싱 | — | `image_versions2.candidates` + `og:image` |

- 커서는 소스 정보를 base64로 인코딩한 **불투명 문자열** — 다음 페이지에서 폴백 체인 없이 같은 소스만 이어감.
- 공개 프로필: 기존 `query_hash` GraphQL은 **이미 죽음(400 확인)**. 실계정으로 테스트해 비로그인 동작하는 `feed/user` 방식으로 전환, @instagram 1→2페이지·중복 0건 검증.

## 썸네일 핫링크 차단 (해결됨)

Instagram CDN(`cdninstagram.com`)은 `Cross-Origin-Resource-Policy: same-origin` 헤더로 브라우저 `<img>` 직접 로드를 차단한다. 선택 미리보기는 서버가 data URL로 변환해 됐지만 그리드 썸네일은 직접 로드라 깨졌다.
- **해결**: 서버 프록시 `GET /actors/reference-image-proxy?url=…`. `<img>`는 Authorization 헤더를 못 보내므로 `@Public()`, 오픈 프록시 악용 방지로 instagram·fbcdn·gstatic 등 **허용 호스트만**·**이미지 content-type만** 통과, 6시간 캐시. SDK `buildStudioRefThumbnailSrc()`가 IG CDN 호스트일 때만 프록시 URL로 치환.

## 캐러셀 (추가 호출 없이)

게시물 하나가 여러 이미지를 가질 때, 선택하면 하단에 전체를 펼친다. 세 소스 응답이 **이미** 자식 이미지를 담고 있어 재요청 불필요. 각 항목에 `children: [{url, thumbnail}]`을 담아 내리고, SDK는 2장 이상이면 "N장" 배지 + 펼침 줄(순번·하이라이트·프록시 썸네일)로 표시.

## 포스트 URL — facebookexternalhit UA 발견 (핵심)

공개 `media/{id}/info`(shortcode base64→media pk)로 처음 구현했으나 이제 **로그인 월(HTML)** 을 반환해 실패.
토큰/서버 방식을 조사한 결과:
- 연결 계정 토큰(IGAA/IGQ) → Graph API로 **임의 게시물 조회 불가**(본인 미디어·Business Discovery만).
- oEmbed(앱 토큰 `META_APP_ID|SECRET`) → Meta **"oEmbed Read" 앱 심사 승인 필요** + 캐러셀은 커버 1장만.
- **채택**: `facebookexternalhit` 크롤러 UA로 게시물 페이지 요청 → 로그인 없이 SEO HTML, 임베드 `image_versions2.candidates`(캐러셀 전체) + `og:image`(커버). 토큰·심사 불필요. `DahiuV0D936` → 4장 캐러셀 전체·HTTP 200 검증.
- 절차 스킬화 → `[[instagram-public-media-fetch]]`

## 폐기된 접근 — 오래된순 정렬

Instagram API는 오래된순 정렬을 제공하지 않는다. 4단계로 시도했으나 최종 **전부 롤백**:
1. **클라이언트 역순** — 불려온 범위 내 역순. 사용자 거부(진짜 오래된 데이터를 다시 불러와야 함).
2. **서버 끝까지 걷기** — 최신순 페이지네이션을 상한(10회/400개, 공개 프로필 250ms 지연)까지 따라가 뒤집기. `truncated:true` 표시. 대형 계정은 최근 것만 나오는 한계.
3. **셀렉트를 입력줄로 이동** — 불러오기 전에 정렬 선택해 1회만 호출(결과 있는 상태에서 바꾸면 재조회 불가피).
4. **Snowflake pk 시간점프 이진탐색** — IG 미디어 pk에 게시 시각이 인코딩됨(`igMaxIdForTime`으로 임의 시각 max_id 합성). 2010~현재를 반씩 좁혀 "이 시점 이전 ≤33개" 구간=가장 오래된 게시물, 최대 ~18회 요청(`getPublicProfileOldestMedia`). 오래된순 더 불러오기는 앵커 pk·시각으로 창을 앞으로 넓힘(`getPublicProfileNewerMedia`).

→ 신뢰성 확보 실패(+검증이 IP 제한에 반복 차단)로 **오래된순 기능 전체 제거**, 최신순+더 불러오기만 남김. 관련 코드(`order` 파라미터, 이진탐색, 걷기 수집, asc 커서 모드) 모두 삭제.

## 함정 — feed 엔드포인트 IP 일시 제한

`feed/user`(더 불러오기·공개 프로필)는 IP·호출빈도 기반으로 일시 차단된다.
- 401 본문 `"Please wait a few minutes before you try again"` = **로그인 월이 아니라 몇 분짜리 쿨다운**. dev API가 같은 IP라 함께 막힘. 쿠키 부트스트랩·`i.instagram.com`·앱 UA 우회 모두 **같은 제한 버킷**이라 무효.
- **보강**: feed 페이지 인메모리 캐시(10분) — 재클릭·타 사용자 동일 계정 조회가 IG를 재타격하지 않음. 페이지당 12→33개(엔드포인트 최대)로 요청 수 1/3. 429/제한 401 감지 시 "몇 분 후 다시" 토스트 + 기존 결과 유지.

## 계정 연결

`GET/POST/DELETE /instagram/accounts[/connect|/:id]`로 연결 계정 토큰을 관리. 연결 시 Business Discovery로 **공식 API 실제 게시물**을 불러온다("✓ 공식 Instagram API로…" 표시). 단 연결 토큰(IGAA/IGQ)으로도 **임의 게시물(포스트 URL) 조회는 불가**.

## 관련
- 제품: [[lampas-studio]]
- 스킬: [[instagram-public-media-fetch]]
- 세션: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]]
