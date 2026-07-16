---
tags: [topic, works, portfolio, git, ops]
created: 2026-07-16
updated: 2026-07-16
---
# Works 프로젝트 포트폴리오

`[[progdesigner]]`의 작업 루트 `~/Works` 하위에 존재하는 git 저장소 전체 목록. 2026-07-15
"Works 전체 최신화" 요청에서 처음 한 번에 열거됨 → [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]].
이전까지 위키는 이 중 일부(`lampas-harness`, `lampas-studio`=`lampas-system`, `toktalk`=`talk-system`)만
알고 있었다.

## 저장소 12개 (2026-07-15 시점)

| 경로 | 위키 매핑 | 비고 |
|------|-----------|------|
| `lampas/lampas-harness` | [[lampas-harness]] | 이 세션 시점 이미 최신 |
| `lampas/lampas-system` | [[lampas-studio]] | 로컬 미커밋 18파일 vs 원격 9커밋 → 이 세션에서 rebase 정리 |
| `dbs/talk-system` | [[toktalk]] | 업데이트됨(432파일, api→talk-api 리네이밍 등 대규모 구조변경) |
| `cwc/elevino-system` | (미문서화) | 업데이트됨(609파일, +3.1만 라인) |
| `cwc/elevino-operator` | (미문서화) | 이미 최신 |
| `cwc/cwc-system` | (미문서화) | 이미 최신 |
| `goraesa/goraesa-system` | (미문서화) | 업데이트됨(360파일, +45만 라인 — 대형 변경) |
| `bokziri/bokziri-system` | (미문서화) | 업데이트됨(281파일) |
| `ai-labs/ai-labs-notes` | (미문서화) | 이미 최신 |
| `dark/dark-system` | (미문서화) | 이미 최신 |
| `arca8/arca8-system` | (미문서화) | 이미 최신 |
| `john-wiki` | [[john-wiki]] | 이 위키 자체. 이미 최신 |

## 미문서화 저장소 — 주의

`elevino-system`(`micro.elevino.io` 도메인이 [[toktalk]] 배포 대상과 겹쳐 계열 서비스로 추정),
`elevino-operator`, `cwc-system`, `goraesa-system`, `bokziri-system`, `ai-labs-notes`, `dark-system`,
`arca8-system`은 이 세션에서 **이름과 변경 규모(파일 수·라인 수)만** 확인됐고 내용(제품 성격·스택·목적)은
전혀 조사되지 않았다. 임의로 성격을 추정해 엔티티를 만들지 않았다 — 실제로 다뤄지는 세션이 생기면 그때
전용 엔티티 페이지를 만들 것.

## 관련
- 세션: [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]
- 스킬: [[multi-repo-safe-bulk-update]]
- 알려진 저장소 엔티티: [[lampas-harness]] · [[lampas-studio]] · [[toktalk]] · [[john-wiki]]
