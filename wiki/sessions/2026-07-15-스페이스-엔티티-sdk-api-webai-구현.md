---
tags: [session, lampas-studio, space, feature-build, deploy, git]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-15~16 — [[lampas-studio]] "Space(공간)" 엔티티 전체 구현 (SDK→API→web-ai) + 배포·푸시

`[[progdesigner]]`가 "스페이스를 만들고 그 스페이스를 포함해 촬영하는 기능"의 완성 여부를 물으며 시작해,
조사 결과 **Space라는 엔티티 자체가 코드베이스에 없다는 사실이 드러나자 그 자리에서 신규 기능으로
설계·구현**까지 진행한 세션. 2026-07-15 23:11~2026-07-16 01:14(UTC). 중간에 배포+푸시 작업도 별도로 끼어
있다. 소스: [[raw/conversations/2026-07-15-스페이스-엔티티-sdk-api-webai-구현]].

## 1. 조사 — "스페이스/차량 선택 영역"은 존재하지 않았다

사용자가 반복해서 "스페이스 만들기·차량 선택·촬영 선택 영역을 마무리해달라"고 요청했으나, 실제 코드
전수 조사(main·dev·omnara 브랜치, 토스 포토스튜디오까지) 결과:

- 촬영 대상 선택(`actorFlow.ts:1252~`)은 `actor / object / both` 3분기로 **이미 완성**.
- 촬영 샷(연출) 선택(`actorFlow.ts:1404~2317`)도 추천 3개+17종 프리셋+커스텀+레퍼런스까지 **완성**.
- "차량"은 독립 영역이 아니라 **Object 카테고리 `vehicle`** 값 하나일 뿐(`objects.service.ts:1052`).
- **"Space(공간)"는 독립 엔티티로 아예 존재하지 않음** — 가장 가까운 건 `environment`(자유 텍스트)와
  `sceneReferenceImageData`(1회성 연출 참조 이미지, 저장 안 됨) 두 임시 형태뿐.

어시스턴트가 두 차례 구체 화면 경로를 되물었으나 사용자가 응답하지 않아, **"강행해서 헛작업하지 않는다"**는
판단으로 진행을 멈추고 확정 질문만 반복 → 이후 사용자가 "스페이스 만들기부터 촬영까지 정리해봐"로 방향을
명확히 하면서 신규 기능 설계로 전환됨.

## 2. 배포 + 푸시 (조사 중간에 낀 별도 지시)

"배포 후 커밋 푸시해줘" 지시에 대해 배포 대상을 사용자에게 확인하려 했으나 무응답 → 최근 미푸시 커밋
4개가 모두 `lampas-web-ai` 관련이라는 근거로 **그 프로젝트를 기본값으로 판단**하고 진행(자율 판단 사례).

- `./scripts/deploy-web.sh lampas-web-ai` 실행 → S3(`s3://lampas.io/ai/production/`) 업로드 +
  CloudFront(`E2CVB6NRDH40V1`) 무효화 성공. → [[deploy-sandbox-pnpm-shim]]
- 작업 트리는 clean(새 커밋 없음), main이 origin보다 4커밋 앞선 상태에서 push 시도 → **원격에 로컬에 없는
  커밋 3개**(Instagram Meta 앱 연결 기능 등)가 있어 거부됨 → `git pull --no-rebase`로 **충돌 없이 자동
  병합**(`54fecd5`) 후 push 성공.
- **주의 사실 발견**: 병합으로 들어온 원격 커밋에 `apps/lampas-api/env/.env.development` 파일이 저장소에
  커밋된 채로 존재 — 이 세션이 만든 것은 아니고 이미 origin main에 있던 것이지만, 개발용 시크릿 노출
  가능성이 있어 사용자에게 확인을 권고함(미해결 플래그).

## 3. Space 엔티티 설계 — Actor·Object 파이프라인 복제

기존 `StudioObject` 계층을 그대로 복제/확장하면 된다는 결론으로 계층별 작업 지도를 만듦:

| 계층 | 작업 |
|------|------|
| DB | `Space` 모델 신설(`schema.prisma`), 기존 enum(`ActorStatus`/`ScopeArea`) 재사용 |
| API 모듈 | `modules/spaces/`를 `modules/objects/` 복제로 신설 |
| web-ai 생성 플로우 | `SpaceFlowState`(사진→이름→확인→저장) |
| web-sdk UI | `organisms/space/` + `molecules/space/` + 라우트 |
| 촬영 합성 | **핵심** — 이미지 슬롯 연결 (아래 4번) |

## 4. 촬영 합성 — 두 경로 상태가 달랐다

- **Actor 포함 촬영(Atlas Cloud 경유)**: 참조 이미지에 이미 라벨드 슬롯(`appearance/costume/background/
  objective/object`)이 있고, `background` 슬롯 프롬프트 가이던스도 이미 정의돼 있어 **거의 무개조로 연결
  가능** — `studioReferenceImages`에 `{slot:'background', imageData: spaceImage}` 한 항목만 추가.
- **Object 단독 촬영(Gemini 직접 경로)**: 2슬롯(제품+씬레퍼런스)이 하드코딩돼 있었으나 Gemini가 최대
  8장까지 받으므로 **3번째 슬롯으로 Space 이미지 추가** + 프롬프트에 서수 자동계산 지시 라인 삽입.

모델 배정(기존 패턴 그대로 따름):
| 단계 | 모델 |
|------|------|
| Space 레퍼런스 → 특성 분석 | Grok(1차) → Gemini(폴백) |
| Space 프로필 이미지 생성 | Gemini `generateImage` |
| Space 연출 프롬프트 작성 | Grok |
| Actor+Space 합성 촬영 | Atlas Cloud(`background` 슬롯) |
| Object+Space 합성 촬영 | Gemini `generateImage`(3번째 슬롯) |

CLAUDE.md엔 "스튜디오 합성=Atlas"로 요약돼 있지만, **Object 촬영은 실제로 Gemini 직접 경로**라는 점을
확인 — [[lampas-studio]] 엔티티 페이지의 기존 서술과 교차 확인 필요.

## 5. 실제 구현 (3턴에 걸쳐 완료)

**web-sdk (1턴, 신규 파일 7개 + 기존 3개 수정)**
- `molecules/space/creation/libs/constants.js`(공간 카테고리 11종), `organisms/space/{SpaceSelection,
  SpaceCreation,SpaceView}.jsx`, 페이지 래퍼 3개, 라우트(`templates/sdk`+`templates/playground` 양쪽),
  내비게이션(`AppMainNav.jsx`에 Spaces 탭, Objects-Gallery 사이).
- 이 시점 API가 없어 화면은 빈 상태/저장 실패 — 검증은 esbuild 문법 검증으로 대체(로컬 `node_modules` 부분
  설치 상태, `@iileex/shared` 누락 기존 이슈 때문 — [[lampas-studio]] 참조).

**lampas-api (같은 턴, 신규 모듈)**
- `prisma/schema.prisma`에 `Space` 모델(테이블 `spaces`) 추가, `modules/spaces/` 신규(생성·목록·조회·수정·
  삭제·복원·Draft↔Workspace 이전·공개 이미지 스트림 — Objects와 동일 스코프/권한).
- `/tmp` 격리 환경에서 의존성 설치 + `prisma generate` + `tsc --noEmit` 통과.

**촬영 합성 연결 + web-ai 채팅 플로우 (같은 턴)**
- Object 촬영(`generate-studio-image`)에 `spaceKey` 파라미터 추가. Actor/Actor+Object 촬영은 기존
  `background` 슬롯 그대로 재사용.
- 채팅 촬영 플로우: 피사체 확정 직후 저장된 Space가 있으면 카드 그리드로 배경 선택 제안("배경 없이
  진행" 칩 포함), 없으면 기존 플로우 무변화. 촬영 요약에 배경 공간 표시, 갤러리 재촬영 복원 시 공간
  이미지 재확보, localStorage 저장 시 대용량 base64 제거.
- web-ai `tsc -b` 통과.

## 6. 남은 것 / 알려둔 제약

- **채팅에서 Space를 "만드는" 플로우는 없음** — 생성은 SDK 웹(`/spaces/create`)에서만, 채팅은 선택·촬영만.
- SDK의 ActorStudio/ObjectStudio(비채팅 스튜디오 UI)에는 아직 Space 선택 드롭다운 없음 — 채팅 촬영에서만
  동작.
- 반영하려면 **lampas-api / lampas-web-sdk / lampas-web-ai 세 곳 모두 배포 필요** — 이 세션에서 배포는
  아직 안 함(마지막 메시지가 "커밋·푸시하고 배포까지 진행할까요?"로 끝남, 사용자 응답 이전에 세션 종료).

## 관련
- [[lampas-studio]] (Space 기능 섹션 신설) · [[lampas]] on [[lampas-harness]]
- [[deploy-sandbox-pnpm-shim]] · [[clone-sibling-entity-pipeline]](신규 스킬)
- [[progdesigner]]
