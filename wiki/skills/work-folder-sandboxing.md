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

## 검증 (실서버 테스트)
- 폴더 안 파일 읽기 → 정상. 폴더 밖 파일(`~/.zshrc`) Read → 차단.
- bash로 폴더 밖 쓰기(`~/Desktop/leak.txt`) → 차단, 파일 실제 미생성.
- 홈 밖 cwd(`/etc`) → 400.

## 주의사항 / 함정
- **완전한 OS 샌드박스가 아니다.** bash는 문자열 경로 추출 기반이라 우회 여지 존재. 파일 도구는 확실히 막히지만 bash는 명백한 이탈만 차단.
- "전체(제한 없음)" 선택 시 기존처럼 `bypassPermissions`로 무제한 동작.

## 출처: [[2026-07-06-lampas-harness-구축]] ([[lampas-harness]])
