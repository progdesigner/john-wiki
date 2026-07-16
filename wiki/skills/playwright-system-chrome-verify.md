---
name: playwright-system-chrome-verify
description: Playwright 브라우저 바이너리 미설치 환경에서 macOS 시스템 Chrome으로 UI 변경을 실브라우저 종단 검증
created: 2026-07-16
tags: [playwright, verify, testing, chrome, headless]
---
# Playwright + 시스템 Chrome 폴백 검증

## 언제 쓰는가
프론트엔드 UI 변경(드래그앤드롭, 폼 제출 등)을 코드만 읽고 "됐다"고 끝내지 않고, 실제 브라우저 동작으로
검증하고 싶은데 `npx playwright install`로 받는 브라우저 바이너리가 샌드박스/환경에 없어 바로 실행이 안 되는 경우.

## 절차 (단계별)
1. 저장소에 `playwright`(또는 `@playwright/test`)가 `node_modules`에 있는지 확인.
2. 검증 스크립트를 **저장소 내부**에 작성해서 실행 — `/tmp` 등 저장소 밖에 두면 node가 `node_modules`를 못 찾아
   `playwright` require가 실패한다.
3. `playwright.chromium.launch()`가 브라우저 바이너리 없음 에러를 내면, macOS에 이미 설치된 시스템 Chrome을
   `channel: 'chrome'` 옵션으로 지정해 그걸 구동:
   ```js
   const browser = await chromium.launch({ channel: 'chrome' });
   ```
4. 검증할 화면(로컬 서버 URL)으로 이동해, 실제 사용자 동작을 이벤트로 재현한다 — 예: 이미지 드롭은
   `DataTransfer` 객체를 만들어 `dragenter`/`drop` 이벤트를 dispatch.
5. 검증 항목을 좁게 특정한다: 필요한 UI 요소가 렌더링됐는지, 상태 변화(미리보기 생성·오버레이 해제)가
   일어났는지, 네트워크 요청 바디에 기대한 필드가 실렸는지(요청 가로채기), 콘솔에 JS 에러가 없는지.
6. 임시 스크립트는 검증 후 정리한다(저장소에 테스트 흔적을 남기지 않음).

## 주의사항 / 함정
- 브라우저 바이너리를 새로 다운로드하려면 대개 네트워크/디스크가 필요하고 샌드박스에서 막힐 수 있음 —
  시스템 Chrome 폴백이 더 빠르고 확실한 경우가 많다.
- `channel: 'chrome'`은 macOS/Windows에 설치된 실제 Chrome 앱을 쓰는 것이라 CI처럼 Chrome이 없는 환경에는
  못 쓴다. 로컬 개발 머신(맥미니) 한정 기법.
- 검증은 "코드가 문법적으로 맞다"가 아니라 "사용자 동작 → 눈에 보이는 결과"까지 이어져야 의미가 있다 —
  `verify` 스킬의 취지와 동일.

## 출처: [[2026-07-14-quick-html-이미지-첨부-구현]]
`quick.html`에 이미지 드래그 첨부 기능을 이식한 뒤, `localhost:8787`에서 PNG 드롭 → 썸네일 생성 →
전송 바디에 `images` 포함 → 사용자 말풍선 렌더링까지 시스템 Chrome으로 종단 검증했다.
