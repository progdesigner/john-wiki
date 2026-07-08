---
tags: [session, lampas-studio, instagram, reference, image-generation, deploy]
created: 2026-07-09
updated: 2026-07-09
---
# 2026-07-08 — Lampas 스튜디오 레퍼런스·Instagram 통합 세션

`[[progdesigner]]`가 `[[lampas-studio]]`(AI 이미지 생성 스튜디오 제품, `apps/lampas-api` + `apps/lampas-web-sdk`)의
**레퍼런스 기능**을 하루 동안 반복적으로 확장한 세션. 핵심 축은 (1) Instagram 레퍼런스 이미지 통합,
(2) 레퍼런스 영역 공용화, (3) UI 모델 선택 기억이다.
`[[lampas]]`(하네스 에이전트)가 `[[lampas-harness]]` 웹 채팅으로 이 제품 코드베이스를 편집·배포했다.

기간: 2026-07-08 18:14 ~ 20:15+ (KST). 원본 말미(레퍼런스 시트 모델 선택 웹 배포 대기)에서 트랜스크립트가 끊김.
원본: [[raw/conversations/2026-07-08-lampas-스튜디오-레퍼런스-instagram.md]]

## 흐름 (시간순, 요청 단위)

1. **갤러리 편집 모델 기억** — `/gallery/view/:id` 프롬프트 수정 시 이미지 모델 선택을 localStorage(`lampas_gallery_edit_image_model`)에 저장, 새로고침·에셋 전환에도 유지. `transformUtils.js`에 read/store 헬퍼, `GalleryView.jsx`에서 복원·저장, 에셋 전환 시 모델 리셋 로직 제거. → `[[localstorage-ui-preference-persistence]]`
2. **Instagram 레퍼런스 계정 — 썸네일·정렬·더 불러오기** (3건 동시)
   - 썸네일 깨짐: IG CDN(`cdninstagram.com`)이 `Cross-Origin-Resource-Policy: same-origin`으로 `<img>` 핫링크 차단. → 서버 프록시 `GET /actors/reference-image-proxy?url=…`(`@Public`, 허용 호스트만, 이미지 content-type만, 6h 캐시) + SDK `buildStudioRefThumbnailSrc()`.
   - 더 불러오기: `nextCursor`(base64 불투명 커서). 공개 프로필은 죽은 `query_hash` GraphQL(400) 대신 `api/v1/feed/user/{id}/?max_id=…`로 전환, @instagram으로 1→2페이지·중복 0 검증.
   - → 상세: `[[instagram-reference-integration]]`
3. **오래된순 정렬 시도 → 최종 폐기** — 4번 반복하다 결국 제거. 클라 역순 → 서버 끝까지 걷기 → 입력줄로 셀렉트 이동 → Snowflake pk 시간점프 이진탐색까지 갔으나 신뢰성 확보 실패로 **전부 롤백**, 최신순+더 불러오기만 유지. → `[[instagram-reference-integration]]`의 "폐기된 접근" 참고.
4. **캐러셀 자식 이미지** — 게시물 선택 시 하단에 전체 이미지 표시. 세 소스 모두 응답에 이미 포함(`children{}` / `edge_sidecar_to_children` / `carousel_media[]`)이라 **추가 API 호출 없이** 구현. "N장" 배지 + 펼침 줄.
5. **분석 모델 기억** — Grok/Gemini 선택을 `lampas_studio_analyze_model`로 전역 저장. → `[[localstorage-ui-preference-persistence]]`
6. **더 불러오기 401** — `feed/user`가 IP 단위 일시 제한("Please wait a few minutes", 로그인 월 아님). dev API가 같은 IP라 함께 막힘. 보강: feed 페이지 인메모리 캐시(10분), 페이지당 12→33(엔드포인트 최대), 429/제한 401 감지 시 명확한 토스트+기존 결과 유지.
7. **레퍼런스 영역 공용화** — `StudioReferenceSection`은 이미 공용 molecule, `ReferenceToImage`도 이미 같은 훅(`useTextToImageStudio`) 사용. `embedded` prop(헤더·보더·패딩 생략) 추가 후 ReferenceToImage에 "3. 레퍼런스(선택)" 카드로 삽입, Shot을 4단계로. → 흐름: 시트로 신원 고정 + 레퍼런스로 연출·조명·구도.
8. **Instagram 계정 연결** — `GET/POST/DELETE /instagram/accounts[/connect|/:id]` 훅 함수 + account 탭 연결 UI(토큰 입력·발급 안내·해제 2단계 확인). 공식 API 사용 시 "✓ 공식 Instagram API로 실제 게시물" 표시.
9. **포스트 URL로 불러오기** — `https://www.instagram.com/p/{shortcode}`. 처음엔 공개 `media/{id}/info`(shortcode base64→pk)로 구현했으나 **로그인 월(HTML) 반환**으로 실패 → 토큰/서버 방식 조사 끝에 **facebookexternalhit UA HTML 파싱**으로 해결. → `[[instagram-public-media-fetch]]`
10. **레퍼런스 시트 모델 선택** (트랜스크립트 말미, 진행 중) — 시트 생성에 모델 드롭다운. `generateStudioReferenceSheetFromImage`가 `gemini-*`/미지정이면 Gemini 직접(멀티 그리드), 그 외 Atlas Cloud 경유(동일 `generateImage` 시그니처). 기본 Gemini라 기존 동작 유지. API 배포 완료, 웹 배포 대기 중 끊김.
11. **배포 관례 문서화** — "배포는 항상 `./scripts` 스크립트로"를 제품 CLAUDE.md에 기록(커밋 `06e5323`, 푸시 안 함). pnpm PATH 부재 시 corepack shim 우회도 함께 기록. → `[[deploy-sandbox-pnpm-shim]]`

## 핵심 결정사항

- **비공식 경로로 Instagram 접근** — 공식 Graph API(연결 계정 토큰 IGAA/IGQ)로는 **임의 게시물 조회 불가**(본인 미디어·Business Discovery만), oEmbed는 Meta "oEmbed Read" 앱 심사 필요 + 캐러셀 커버 1장만. 그래서 공개 프로필 feed 엔드포인트 + facebookexternalhit UA 파싱을 채택. 트레이드오프: IP·호출빈도 기반 일시 차단 위험(토스트로 안내, 기존 결과 유지). → `[[instagram-reference-integration]]`
- **오래된순은 포기** — Instagram이 오래된순 정렬을 API로 제공하지 않고, 시간점프 이진탐색까지 시도했으나 실용성·신뢰성이 부족. "안 되는 건 걷어내고 되는 것만 잘"이라는 사용자 판단으로 롤백.
- **추가 호출 없는 캐러셀** — 세 소스 응답이 이미 자식 이미지를 담고 있음을 확인해, 게시물 선택 시 재요청 없이 전체를 펼침.
- **공용 컴포넌트는 이미 90% 완성** — ReferenceToImage/TextToImage가 같은 훅·같은 molecule을 써서, `embedded` prop 하나 추가로 "시트→레퍼런스→Shot" 흐름이 완성. 큰 리팩터링 불필요.
- **UI 선택 기억은 localStorage 전역** — 액터·에셋과 무관하게 마지막 선택을 저장, 저장값이 옵션에 없으면 기본값 폴백, 임베드·프라이빗 환경에선 조용히 무시. → `[[localstorage-ui-preference-persistence]]`
- **배포는 `./scripts`로만** — 수동 빌드·S3·rsync·CloudFront를 개별 재현하지 않는다(제품 CLAUDE.md에 명문화).

## 배포·커밋

| 커밋 | 내용 |
|------|------|
| `26f8998` | Instagram 계정 연결/해제 |
| `a729948` | 포스트 URL 불러오기 (공개 info 엔드포인트 — 이후 obsolete) |
| `06e5323` | 제품 CLAUDE.md 배포 가이드 추가 (푸시 안 함) |
| `b470394` | 포스트 URL을 facebookexternalhit UA HTML 파싱으로 교체 |

배포: API `nest build` → 원격 PM2 재시작, 웹 `vite build`(프로덕션 env) → S3 업로드 → CloudFront 무효화(`I7X8MTU3EJODWK6VYZNLKEVZY1`). 앞선 세션의 파일 10개는 이미 커밋·푸시된 상태로 확인.

## 관찰된 하네스 함정 (→ `[[lampas-harness]]`와 연결)

- **"Stream closed" 재발** — 파일 수정 도구 권한 승인 채널이 세션 중 두 번 끊겨 편집 불가, 사용자 재전송으로 복구. `[[lampas-harness]]`의 미해결 이슈와 동일 증상.
- **인자 스캐너가 URL/슬래시 경로 차단** — bash 인자에 `https://…`나 `/gallery/...`, S3 경로가 있으면 차단됨 → **스크립트 파일로 작성 후 실행**하는 우회가 반복됨.
- **샌드박스가 pnpm/aws/ssh 차단** — 배포 시 언샌드박스 + corepack pnpm shim 필요. → `[[deploy-sandbox-pnpm-shim]]`

## 관련
- 엔티티: [[lampas-studio]] · [[progdesigner]] · [[lampas]] · [[lampas-harness]]
- 토픽: [[instagram-reference-integration]]
- 스킬: [[instagram-public-media-fetch]] · [[localstorage-ui-preference-persistence]] · [[deploy-sandbox-pnpm-shim]]
