---
name: work-folder-sandboxing
description: Claude Agent SDK 작업을 특정 작업 폴더 안으로 제한(폴더 밖 파일 접근 차단)
created: 2026-07-07
tags: [claude-agent-sdk, security, sandbox, permission]
---
# 작업 폴더 샌드박싱 (에이전트를 폴더 밖으로 못 나가게)

## 언제 쓰는가
`bypassPermissions`로 도는 에이전트를 특정 메시지/태스크 동안 지정 폴더 안으로 제한할 때.
전체 머신 제어 하네스에서 "이 작업은 이 프로젝트 폴더만 건드려" 보장이 필요할 때.

## 절차 (단계별)
1. **권한 모드 전환** — 폴더가 선택되면 그 턴은 `bypassPermissions` 대신 **`default` 권한 모드**로 실행.
   → 모든 도구 호출이 서버의 권한 가드(canUseTool 콜백)를 거치게 됨.
2. **경로 가드** — 도구 호출의 대상 경로가 선택 폴더 밖이면 그 호출 자체를 거부(blockedPath).
   - 파일 도구(Read/Write/Edit)는 SDK의 경로 감지로 확실히 차단.
   - bash는 명령 문자열에서 절대경로·`~` 경로를 추출해 검사(시스템 경로 `/usr/bin`,`/tmp` 등은 실행 위해 허용).
3. **cwd 검증** — 요청의 `cwd`가 존재하지 않거나 홈 밖(`/etc` 등)이면 요청 자체를 400으로 거부.
4. **UI** — 입력창 위 폴더 선택기(`/api/workdirs`가 `~/Works` 하위 + "전체(제한 없음)" 제공), `localStorage` 저장. 첫 턴 이후 대화가 폴더에 묶이면 선택기 잠금. 큐 패널에 `@폴더명` 태그 표시.
   - **퀵 채팅 오버레이로 확장(2026-07-16)**: 원래 index.html(메인 채팅)에만 있던 이 UI를
     `apps/web/public/quick.html`에도 동일하게 이식(`quick_workdir` localStorage 키). 서버 쪽 가드
     로직은 공유, 클라이언트 UI만 복제. → [[2026-07-16-quick-html-폴더선택기-auto모델-구현]]

## 검증 (실서버 테스트)
- 폴더 안 파일 읽기 → 정상. 폴더 밖 파일(`~/.zshrc`) Read → 차단.
- bash로 폴더 밖 쓰기(`~/Desktop/leak.txt`) → 차단, 파일 실제 미생성.
- 홈 밖 cwd(`/etc`) → 400.

## 주의사항 / 함정
- **완전한 OS 샌드박스가 아니다.** bash는 문자열 경로 추출 기반이라 우회 여지 존재. 파일 도구는 확실히 막히지만 bash는 명백한 이탈만 차단.
- "전체(제한 없음)" 선택 시 기존처럼 `bypassPermissions`로 무제한 동작.
- **파일 도구(Read 등) 차단은 하네스 가드보다 아래 단계, SDK 레벨**로 보인다(2026-07-16 재확인).
  형제 프로젝트(`dbs/talk-system`)를 "읽기 전용으로만" 보려는 시도조차 막혔고, 어시스턴트는
  `dangerouslyDisableSandbox` 상당의 우회도 듣지 않는다고 진단했다 — 즉 canUseTool 콜백을
  통과하더라도 Claude Agent SDK 자체가 cwd 밖 파일 도구 호출을 허용하지 않을 가능성.
  **폴더 밖 레퍼런스 코드를 보여주고 싶다면, 참고 대상을 작업 폴더 자체로 잡거나("전체(제한
  없음)") 내용을 프롬프트에 직접 붙여넣는 우회가 필요**하다. → [[2026-07-16-tts-stream-elevenlabs-구현착수]]

## 추가 실사례 (2026-07-18)
quick 채팅이 `lampas/lampas-harness` 폴더에 샌드박싱된 상태에서 "Works의 모든 git 저장소 저장해줘"
요청 → `~/Works` 상위 접근이 막혀 진행 불가, 어시스턴트가 폴더 확장 재요청 또는 현재 저장소만 저장
중 선택을 묻고 대화가 끊김. 3분 뒤 별도의(제한 없는) 세션에서 같은 요청이 재시도되어 12개 저장소를
순회·완료됨 → [[2026-07-18-works-전체저장]]. quick 채팅 창구가 기본적으로 특정 작업 폴더에 묶인다는
것을 보여주는 사례. → [[2026-07-17-람파스-차별화전략-용어-works저장-quick]]

## 출처: [[2026-07-06-lampas-harness-구축]] ([[lampas-harness]]) · 보강: [[2026-07-16-tts-stream-elevenlabs-구현착수]] · [[2026-07-17-람파스-차별화전략-용어-works저장-quick]]
