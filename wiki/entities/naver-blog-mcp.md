---
tags: [entity, tool, mcp, playwright, naver, automation]
created: 2026-07-13
updated: 2026-07-13
---
# naver-blog-mcp

네이버 블로그에 자동 발행하는 **MCP 서버**. `[[lampas-harness]]`가 `.cursor/mcp.json` 브리지로 Claude
세션에 노출해([[harness-mcp-bridge]]) 람파스가 직접 블로그 글을 올릴 수 있다. `mcp-servers/naver-blog-mcp`에 있음.

- 구현: **Playwright**로 헤드리스 브라우저를 몰아 네이버 스마트에디터 ONE에 글을 작성·발행.
- 저장 세션: `playwright-state/auth.json` (로그인 쿠키 영속).
- 편의 래퍼: `tools/post_to_naver_blog.py --title --content-file(.txt) --tags`
- 발행 절차·함정 총정리: [[naver-blog-mcp-posting]]

## 핵심 제약 — 본문은 순수 텍스트만 (plaintext-only)

본문을 Playwright `keyboard.type()`으로 **글자 그대로 타이핑**하므로 **HTML을 넘기면 태그가 원문 그대로
노출**된다. 스마트에디터 ONE은 WYSIWYG 전용이라 HTML 삽입 경로가 없다.
- 본문은 순수 텍스트(줄바꿈으로 문단 구분)로 작성.
- `"1. "` 형식 소제목은 번호목록 자동서식으로 불규칙 변형되므로 `"◆ "`, `"· "` 같은 기호를 쓴다.

## 서버 버그 3종 (2026-07-12 수정 — 재발 시 참고)
1. `tools.py`가 `blog_id=None` 자동탐색으로 **남의 블로그로 이동** → `config.NAVER_BLOG_ID` 명시.
2. 본문 셀렉터가 **제목 contenteditable을 클릭**해 본문 서두가 제목(100자 제한)에 섞임
   → `.se-component.se-text .se-text-paragraph` 우선 + macOS에서 커서이동인 `Control+A` 제거.
3. 발행 후 `wait_for_url` 글롭이 `PostView.naver?...` 리다이렉트와 안 맞아 **성공을 시간초과로 오보**
   → URL 폴링으로 교체.
- 글 삭제 도구는 **미구현(주석)** → 잘못 발행된 글은 수동 삭제.

## 세션 만료 — SRT 보안쿠키 ~24h

발행이 "글쓰기 페이지 로딩 실패 → `nidlogin.login` 리다이렉트"로 실패하면 세션 만료가 원인.
- `NID_AUT`/`NID_SES`(둘러보기용)는 오래 살지만, 글쓰기·에디터에 필요한 **`SRT5`/`SRT30` 보안 재인증
  쿠키는 약 24시간이면 만료**된다. 그래서 홈은 로그인 상태로 열려도 `/postwrite`만 로그인으로 튕긴다.
- **헤드리스 자동 재로그인 불가**: 네이버 **인라인 이미지 캡차**(영수증 사진 보고 숫자 답하기; iframe이
  아니라 `login.py`가 감지도 못 함)에 막힘. 코드로는 못 고치는, 사람이 풀어야 하는 봇 차단.
- 갱신법: `cd mcp-servers/naver-blog-mcp && uv run python tools/manual_login.py` → headed 창에서
  캡차 풀고 로그인 → `auth.json` 자동 재저장 → 헤드리스 발행이 ~24h 다시 동작. → [[naver-blog-mcp-posting]]

## 관련
- 호스트: [[lampas-harness]] (노출 경로: [[harness-mcp-bridge]])
- 스킬: [[naver-blog-mcp-posting]]
- 세션: [[2026-07-13-람파스-누적운영기억-이관]]
