---
name: naver-blog-mcp-posting
description: naver-blog-mcp로 네이버 블로그에 글을 발행할 때 — 순수 텍스트 본문·세션(SRT) 만료 갱신·서버 버그 대응
created: 2026-07-13
tags: [naver, blog, mcp, playwright, publishing, session, lampas-harness]
---
# 네이버 블로그 MCP 발행 절차

## 언제 쓰는가
`[[naver-blog-mcp]]`(Playwright 기반 MCP)로 네이버 블로그에 자동 발행할 때. 하네스가 이 MCP를
Claude 세션에 노출하는 경로: [[harness-mcp-bridge]].

## 절차 (단계별)
1. **본문은 순수 텍스트로 준비** — 서버가 `keyboard.type()`으로 글자 그대로 타이핑하므로 **HTML 금지**
   (태그가 그대로 노출됨). 문단은 줄바꿈으로 구분. 소제목은 `"1. "`(번호목록 자동서식으로 깨짐) 대신
   `"◆ "`, `"· "` 같은 기호를 쓴다.
2. **발행 실행**:
   ```bash
   tools/post_to_naver_blog.py --title "제목" --content-file 본문.txt --tags "태그1,태그2"
   ```
3. **발행 확인** — 성공 시 `PostView.naver?...`로 리다이렉트(URL 폴링으로 판정). 글 삭제 도구는
   미구현이므로 잘못 올리면 **수동 삭제**해야 한다.

## 세션(SRT) 만료 대응 — 발행이 로그인으로 튕길 때
"글쓰기 페이지 로딩 실패 → `nidlogin.login` 리다이렉트"면 **글쓰기용 보안쿠키 `SRT5`/`SRT30`이 만료**된 것
(~24h). 홈은 열려도 `/postwrite`만 튕긴다. 헤드리스 자동 재로그인은 **인라인 이미지 캡차**에 막혀 불가능.
- 갱신(사람이 캡차를 직접 풂):
  ```bash
  cd mcp-servers/naver-blog-mcp && uv run python tools/manual_login.py
  ```
  headed 창에서 캡차 풀고 로그인 → `playwright-state/auth.json` 자동 재저장 → 헤드리스 발행 ~24h 재개.

## 주의사항 / 함정 (서버 버그 3종, 2026-07-12 수정 — 재발 대비)
- `blog_id` 자동탐색으로 **남의 블로그로 이동** → `config.NAVER_BLOG_ID` 명시.
- 본문 셀렉터가 **제목 칸을 클릭**해 본문이 제목(100자)에 섞임 → `.se-component.se-text .se-text-paragraph`
  우선 + macOS 커서이동인 `Control+A` 제거.
- 발행 성공을 `wait_for_url` 글롭 불일치로 **시간초과 오보** → URL 폴링으로 교체.
- MCP 코드/설정을 고치면 실행 중 데몬은 옛 코드를 물고 있으니 재시작 후 반영 → [[macos-launchd-daemon]].

## 출처: [[2026-07-13-람파스-누적운영기억-이관]] ([[naver-blog-mcp]])
