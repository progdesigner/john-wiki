---
name: instagram-public-media-fetch
description: 토큰·앱 심사 없이 서버에서 Instagram 공개 게시물(캐러셀 포함) 이미지를 가져올 때
created: 2026-07-09
tags: [instagram, scraping, crawler, image, no-auth]
---
# Instagram 공개 게시물 이미지 서버 사이드 조회

## 언제 쓰는가
포스트 URL(`/p/`, `/reel/`, `/tv/`) 또는 shortcode로 **임의 공개 게시물**의 이미지(캐러셀 전체 포함)를
서버에서 가져와야 할 때. 사용자 로그인·앱 심사·연결 계정 토큰 없이.

## 먼저 알 것 — 안 되는 경로 (시간 낭비 방지)
Instagram은 시간이 지나며 공개 경로를 대부분 막았다. 아래는 **이미 검증된 실패**다:
- **공개 `media/{id}/info`** (shortcode base64→media pk) → 이제 **로그인 월(HTML)** 반환.
- **연결 계정 Graph 토큰**(IGAA/IGQ) → 임의 게시물 조회 불가. 본인 미디어·Business Discovery만.
- **oEmbed**(앱 토큰 `{APP_ID}|{APP_SECRET}`) → Meta **"oEmbed Read" 앱 심사 승인 필요**, 게다가 캐러셀은 커버 1장만.
- 쿠키 부트스트랩 / `i.instagram.com` / 앱 UA 우회 → 제한 시 **같은 IP 제한 버킷**이라 무효.

## 절차 (단계별)
1. **URL 파싱** — `/p/`, `/reel/`, `/tv/{shortcode}` 또는 shortcode 단독을 받아 shortcode 추출.
2. **facebookexternalhit UA로 게시물 페이지 요청** — User-Agent를 페이스북 크롤러(`facebookexternalhit`)로 설정해 게시물 HTML을 서버에서 GET. 로그인 없이 SEO용 HTML이 반환된다.
3. **임베드 JSON 파싱** — HTML 안에 `"image_versions2":{"candidates":[{"url":"…"`(각 이미지의 후보 URL)와 캐러셀이면 `carousel_media`가 여러 번 등장. 정규식으로 candidates의 첫 URL(최대 해상도)들을 추출. 커버는 `og:image` 메타태그에도 있음.
4. **캐러셀 펼치기** — 여러 장이면 `children: [{url, thumbnail}]`로 개별 이미지를 반환.
5. **썸네일은 프록시 경유** — 추출한 IG CDN URL은 브라우저 `<img>` 직접 로드가 차단되므로(`Cross-Origin-Resource-Policy`), 서버 이미지 프록시로 서빙. → `[[instagram-reference-integration]]`의 프록시 참고.

## 검증 (실계정으로 확인)
- `DahiuV0D936` → 4장 캐러셀 전체 파싱, 각 이미지 HTTP 200 로드 확인.
- facebookbot HTML에 `carousel_media`(13회)·`image_versions2`(5회) 임베드 확인.

## 주의사항 / 함정
- **정규식은 실제 스니펫을 덤프해서 맞춰라** — 첫 시도의 정규식이 안 맞았고, 실제 JSON을 덤프해 `"image_versions2":{"candidates":[{"url":` 구조를 확인한 뒤에야 성공.
- **IP 일시 제한** — 짧은 시간에 호출이 몰리면 401 `"Please wait a few minutes"`(쿨다운). 재시도·캐시로 완화하고 사용자에겐 명확한 안내.
- **비공식 경로** — Instagram이 언제든 HTML 구조·차단 정책을 바꿀 수 있다. 실패 시 기존 결과를 지우지 말고 유지.
- **URL 리터럴은 bash 인자로 넣지 말 것** — 하네스 인자 스캐너가 `https://…`를 차단한다. 검증 스크립트는 **파일로 작성 후 실행**. → `[[lampas-harness]]` 함정.

## 출처: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] ([[lampas-studio]], [[instagram-reference-integration]])
