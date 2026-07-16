---
tags: [session, lampas-harness, quick-html, work-folder-sandboxing, model-selection, ui-ux]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-16 — quick.html에 작업 폴더 선택기 + Auto 모델 구현 (02:16~02:20)

`[[progdesigner]]`가 "quick.html 에도 어느 폴더로 할건지와 Auto 를 선택할 수 있게 해줘"라고 요청한
1왕복 quick 세션. 소스: `raw/conversations/2026-07-16-quick-html-폴더선택기-auto모델-구현.md`
(원본 경로 `logs/chats/archive/605d1768-15e5-4e60-9a21-74ba9d9f2993.md`).

이 세션은 [[2026-07-16-quick-작업폴더선택기-커밋푸시]](02:34~)가 "신규 확인, 구체 구현 코드는 미확인"
이라 flag했던 **quick.html 작업 폴더 선택기의 실제 구현 세션**이다(18분 먼저 실행됨). 커밋 `c100edd`에
포함된 변경 중 하나가 바로 이 세션의 산출물.

## 진행

`index.html`(메인 채팅)에 이미 있던 폴더 선택·Auto 모델 로직을 조사한 뒤, `apps/web/public/quick.html`
(퀵 채팅 오버레이)에 동일하게 이식:

- **폴더 선택기** — 헤더에 폴더 드롭다운 추가. `/api/workdirs`로 목록을 채우고, 선택 폴더를 메시지의
  `cwd`로 전송해 서버가 도구 실행을 그 폴더 안으로 제한(`[[work-folder-sandboxing]]`과 동일 메커니즘).
  선택값은 `quick_workdir` 키로 `localStorage`에 저장(`[[localstorage-ui-preference-persistence]]` 패턴).
- **폴더 잠금** — 첫 턴이 끝나면(서버 `done` 이벤트·히스토리의 `workDir`) 대화가 폴더에 묶여 선택기가
  비활성화되고, "새 대화"(⌘N) 시작 시 다시 풀린다. index.html과 동일한 잠금 규칙.
- **Auto 모델** — 모델 목록 맨 위에 "Auto — 난이도 자동 선택" 옵션 추가(`[[model-selection]]`의
  easy/medium/hard/extreme 4단계 서버 판정 로직을 그대로 재사용). 선택 시 어떤 모델이 배정됐는지
  `⚙ auto → claude-…` 한 줄을 채팅에 표시.

수정 파일은 `apps/web/public/quick.html` 하나. 스크립트 구문 검사 + 서빙 확인.

### 배포 요령 (스킬화)
`dist/quick.html`은 vite가 `public/`에서 **그대로 복사만 하는 정적 파일**이라, 전체 `vite build` 없이
`cp apps/web/public/quick.html apps/web/dist/quick.html`만으로 반영 확인이 가능했다(퀵 창을 새로 열면
바로 보임). → [[self-hosted-agent-server-ops]] 함정4(빌드 누락)의 반대 사례로 편입.

## 결정사항

- quick.html도 index.html과 동일한 폴더 선택/잠금 + Auto 모델 UX를 갖는다.
- 이 세션 시점엔 **커밋 미완료** — 사용자 확인 후 커밋하기로 함(실제 커밋은 18분 뒤
  [[2026-07-16-quick-작업폴더선택기-커밋푸시]] 세션에서 `c100edd`로 완료).

## 관련
- 엔티티: [[lampas-harness]] (Auto 모델·quick.html 절 갱신)
- 후속 세션(커밋·push 확인): [[2026-07-16-quick-작업폴더선택기-커밋푸시]]
- 선행 구현(index.html 원본): [[2026-07-06-lampas-harness-구축]](폴더 선택) ·
  [[2026-07-15-auto모델-난이도판정-확인ux-개선]](Auto extreme 티어)
- 관련 스킬: [[work-folder-sandboxing]] · [[localstorage-ui-preference-persistence]] ·
  [[self-hosted-agent-server-ops]]
- 관련 주제: [[model-selection]]
