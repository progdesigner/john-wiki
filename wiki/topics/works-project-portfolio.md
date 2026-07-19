---
tags: [topic, works, portfolio, git, ops]
created: 2026-07-16
updated: 2026-07-19
---
> **2026-07-19 추가 갱신 (2)**: [[2026-07-17-works-저장소-일괄최신화-pull]] 세션(2026-07-17 04:36 UTC,
> 아래 07-18 세션보다 하루 앞섬) — "모든 프로젝트를 최신화 해줘"(pull 방향, [[multi-repo-safe-bulk-update]]
> 두 번째 재사용). `bokziri-system`·`cwc-system`·`elevino-system`(dev)·`lampas-system`(=[[lampas-studio]])
> 4개 pull 완료. **처음으로 브랜치 정보 확인**: `elevino-system`·`arca8-system`·`talk-system`(=[[toktalk]])·
> `goraesa-system` 4개가 `dev` 브랜치. `dark-system`은 로컬 변경 1개+원격 4커밋 뒤처짐으로 보류(사용자
> 응답 없이 소스 종료) — 이 로컬 변경이 다음날 07-18 세션의 push 대상과 동일物인지는 미확정.
>
> **2026-07-19 추가 갱신**: [[2026-07-18-works-전체저장]] 세션(2026-07-18) — "Works 전체 저장해줘"
> (pull이 아니라 commit+push 방향, 대칭 스킬 [[multi-repo-bulk-commit-push]] 신설). 12개 중
> [[john-wiki]]·[[dark-system]]·[[lampas-harness]]·[[lampas-studio]] 4개에 미커밋 변경이 있어
> 커밋·push. `dark-system`은 push가 한 번 거부돼(원격에 새 커밋 존재) `pull --rebase` 후 재push로
> 해소 — 구조적 충돌은 아니었던 것으로 보임. **이 세션 3분 전, 같은 요청이 다른(작업 폴더 샌드박싱된
> quick) 채팅에서 먼저 시도됐다가 `~/Works` 접근 차단으로 막힌 바 있음** →
> [[2026-07-17-람파스-차별화전략-용어-works저장-quick]] · [[work-folder-sandboxing]].
>
> 2026-07-16 갱신: [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]] 세션에서 `cwc-system`·`elevino-system`
> 두 저장소의 내용이 처음 조사됨 → [[cwc-system]] · [[elevino-system]] 엔티티 생성. 나머지(elevino-operator,
> goraesa-system, bokziri-system, ai-labs-notes, arca8-system)는 여전히 미문서화.
> 같은 날 [[2026-07-15-dark-system-저장소-클론]] 세션에서 `dark/dark-system`도 Works 일괄 최신화(06:48)
> 10분 전(06:38)에 막 폴더 생성·클론됐음이 확인됨 — `cwc-system`(06:42 클론)과 같은 패턴.
> **2026-07-16 추가 갱신**: 같은 날 저녁(21:52~22:38 UTC) [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]]
> 세션에서 `dark-system` 안에 최소 4개 앱(`dark-upbit-api`/`dark-upbit-web`/`dark-toss-api`/
> `dark-toss-web`)이 있음이 처음 확인됨 → [[dark-upbit-api]]·[[dark-upbit-web]]·[[dark-toss-web]]
> 엔티티 신설. 26분 뒤(23:04 UTC) [[2026-07-15-dark-toss-api-장전매수-코드조사]] 세션에서
> `apps/dark-toss-api`가 더 깊이 조사됨 → [[dark-toss-api]] 엔티티 생성. `dark-system`은 이제
> "미문서화" 목록에서 제외.

# Works 프로젝트 포트폴리오

`[[progdesigner]]`의 작업 루트 `~/Works` 하위에 존재하는 git 저장소 전체 목록. 2026-07-15
"Works 전체 최신화" 요청에서 처음 한 번에 열거됨 → [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]].
이전까지 위키는 이 중 일부(`lampas-harness`, `lampas-studio`=`lampas-system`, `toktalk`=`talk-system`)만
알고 있었다.

## 저장소 12개 (2026-07-15 시점)

| 경로 | 위키 매핑 | 비고 |
|------|-----------|------|
| `lampas/lampas-harness` | [[lampas-harness]] | 이 세션 시점 이미 최신 |
| `lampas/lampas-system` | [[lampas-studio]] | 07-15: 로컬 미커밋 18파일 vs 원격 9커밋 → rebase 정리. 07-17: 깨끗하게 1커밋 pull(배포 스크립트 추가) |
| `dbs/talk-system` | [[toktalk]] | 07-15: 업데이트됨(432파일, api→talk-api 리네이밍 등 대규모 구조변경). 07-17: 이미 최신, **`dev` 브랜치** 확인 |
| `cwc/elevino-system` | [[elevino-system]] | 07-15: 업데이트됨(609파일, +3.1만 라인), 내용 조사됨. 07-17: **`dev` 브랜치**에서 1커밋 pull(`_v2/api` 설정 정리) |
| `cwc/elevino-operator` | (미문서화) | 이미 최신 (07-15·07-17 모두) |
| `cwc/cwc-system` | [[cwc-system]] | 07-15: 이미 최신(06:42 클론 직후). 07-17: `main`에서 1커밋 pull(주간 매출 리포트 도구 추가) |
| `goraesa/goraesa-system` | (미문서화) | 07-15: 업데이트됨(360파일, +45만 라인 — 대형 변경). 07-17: 이미 최신, **`dev` 브랜치** 확인 |
| `bokziri/bokziri-system` | (미문서화) | 07-15: 업데이트됨(281파일). 07-17: `main`에서 3커밋 pull(35파일, +562/−520) |
| `ai-labs/ai-labs-notes` | (미문서화) | 이미 최신 (07-15·07-17 모두) |
| `dark/dark-system` | [[dark-system]] | 07-15: 이미 최신(클론 직후). 07-17: **보류** — 로컬 변경 1개 + `main` 원격 4커밋 뒤처짐, 사용자 미응답으로 미해결 종료 |
| `arca8/arca8-system` | (미문서화) | 07-15: 이미 최신. 07-17: 이미 최신, **`dev` 브랜치** 확인 |
| `john-wiki` | [[john-wiki]] | 07-15: 이미 최신. 07-17: 로컬 변경 4개, 원격 뒤처짐 없어 pull 불필요 |

## 미문서화 저장소 — 주의

`elevino-operator`, `goraesa-system`, `bokziri-system`, `ai-labs-notes`, `arca8-system`은
2026-07-15 최신화 세션에서 **이름과 변경 규모(파일 수·라인 수)만** 확인됐고 내용(제품 성격·스택·목적)은
전혀 조사되지 않았다. 임의로 성격을 추정해 엔티티를 만들지 않았다 — 실제로 다뤄지는 세션이 생기면 그때
전용 엔티티 페이지를 만들 것. (`elevino-system`·`cwc-system`·`dark-system`은 같은 날 후속 세션에서
조사되어 엔티티가 생겼다 — 위 표 참고. `dark-system`도 모노레포 내 앱 전체가 확인된 건 아니고
`apps/dark-toss-api` 하나만 조사됨 → [[dark-system]] 참고.)

## 관련
- 세션: [[2026-07-15-dark-system-저장소-클론]], [[2026-07-15-cwc-system-저장소-클론]], [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]], [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]], [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]], [[2026-07-15-dark-toss-api-장전매수-코드조사]], [[2026-07-17-works-저장소-일괄최신화-pull]], [[2026-07-18-works-전체저장]]
- 스킬: [[multi-repo-safe-bulk-update]], [[multi-repo-bulk-commit-push]], [[credit-ledger-balance-pattern]], [[config-flag-gate-audit]], [[trading-feature-flag-auto-exclude-manual-allow]], [[deploy-sandbox-pnpm-shim]]
- 알려진 저장소 엔티티: [[lampas-harness]] · [[lampas-studio]] · [[toktalk]] · [[john-wiki]] · [[cwc-system]] · [[elevino-system]] · [[dark-system]] · [[dark-upbit-api]] · [[dark-upbit-web]] · [[dark-toss-api]] · [[dark-toss-web]]
