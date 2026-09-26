---
tags: [session, lampas-studio, lampas-api, lampas-agent, lampas-web-edit, lampas-web-reels, lampas-web-copy, lampas-web-clips, clip-intelligence, brand-kit, ai-video]
created: 2026-09-26
updated: 2026-09-26
---
# 2026-09-18 — Lampas 숏폼 자동 제작 파이프라인 대량 고도화 (AI Clip Intelligence + Brand Kit)

`Tool: claude` 세션. 작업 디렉터리 `~/Works/lampas/lampas-system`([[lampas-studio]]). 시작 시각
`2026-09-18T11:14:17Z`. 사용자가 "긴 영상 → AI 숏폼 자동 제작" 요구사항 문서(MVP 기능표·AI Clip
Intelligence Engine·Persona 기반 Reels·Auto Editing·Hook Generator·Brand Kit·SNS Publishing·성과
피드백 학습·4단계 개발 우선순위)를 붙여넣고 "lampas-system에 없는 기능을 만들거나 업그레이드"를
요청한 것으로 시작. 한 세션 안에서 최초 구현부터 여러 차례의 후속 수정·버그 수정·기능 단순화·배포까지
길게 이어졌다(정확한 종료 시각은 소스에 없음, "이어서 작업해줘"가 반복되는 것으로 보아 여러 턴에
걸쳐 진행). 원본 → `raw/conversations/2026-09-18-lampas-clip-intelligence-brand-kit-대량구현.md`.

## 0. 갭 분석 — 4개 병렬 서베이 에이전트

커밋 전에 먼저 기존 코드가 요구사항 대비 무엇을 갖췄는지 정확히 파악하기 위해 4개 서베이 에이전트를
병렬로 띄웠다(절차 자체를 스킬로 추출 → [[parallel-survey-before-feature-gap-analysis]]):

1. **clips 모듈 + agent Clips** — `lampas-api`의 `ClipSource`/`Clip`/`ClipFolder`/`ClipCategory` Prisma
   모델, `lampas-agent`의 인제스트 파이프라인(`download→probe→split→extract→transcribe→label→wiki→upload`),
   `lampas-web-clips` 뱅크 UI 라이브러리 구조.
2. **lampas-web-edit 에디터** — `ReelProject`/`ReelClip` 데이터 모델, mediabunny/WebCodecs 렌더러,
   9:16 캔버스는 이미 배선돼 있으나 **피사체 추적·자동 리프레임은 전무**, 자막은 문장 단위뿐(단어
   강조 없음), BGM은 사실상 사용 불가 상태, 브랜드 요소 전무 확인.
3. **AI 게이트웨이 + ASR + 스펙/DDL 컨벤션** — `AiService.chatCompletions` 시그니처, ASR이 `lampas-api`가
   아니라 `lampas-agent` 브라우저 SPA에서 실행됨(`xai/stt-v1` 1차, `bytedance/seed-asr-2.0` 폴백,
   단어 타임스탬프 응답), 수동 DDL 컨벤션(`prisma/manual/`, `db push` 금지) 재확인.
4. **reels/copy/edit-sessions/packaging 모듈** — `ReelPlan`(A/B 세트 생성 파이프라인), `CopyService`
   (페르소나·카피·채점), `EditSession`/`MediaExport` 계약, Instagram 게시·예약 스케줄러, **Brand Kit은
   전무**(`BrandProfile`은 텍스트 톤/해시태그뿐, 로고·폰트·워터마크 없음) 확인.

**갭 분석 결론** — 이미 있는 것: 업로드·YouTube 반입, 단어 타임스탬프 ASR, 장면 분할, 훅 점수, 클립
뱅크, 릴스 세트 추론, 페르소나 카피·교차 채점, 9:16 캔버스·수동 크롭, 문장 자막, 인스타 게시·예약.
**없는 것**: 다축 클립 점수(훅/확산성/정보성/감정/완결성), 페르소나 기반 클립 *선택*, 타입드 훅
생성기, 15/30/60/90초 자동 트림, 단어 단위 다이나믹 캡션, 에디터로의 자막 핸드오프, 브랜드 킷,
성과 피드백 루프. 이 구조 분석 자체를 별도 토픽으로 정리 → [[lampas-clip-intelligence]].

## 1. 1차 구현 — AI Clip Intelligence + Brand Kit (API·에이전트·에디터·릴스)

- **`lampas-api` 신규 모듈 `clip-intelligence`** (`/v1/clip-intelligence`). `Clip.axisScores`(신규
  Prisma 필드) + `clip_intel_runs` 테이블 신설(수동 DDL `2026-09-18-clip-intelligence-brand-kits.sql`,
  로컬·운영 모두 적용). 훅·확산성·정보성·감정·완결성 5축 채점, 페르소나가 있으면 적합도를 섞어 순위
  결정, 시간 안 겹치게 상위 N개 선택(다양성 픽커), 15/30/60/90초 목표 길이에 맞춰 **문장 경계에서
  트림**, 궁금증·사실·도발 등 스타일별 훅 문구 부착. 채점 모델은 `CLIP_INTEL_SCORE_MODEL` env로 교체
  가능. 테스트 작성 중 다양성 픽커가 겹치는 이웃 클립을 정상적으로 스킵하는데 테스트 기댓값이 틀렸던
  것으로 판명(서비스 버그 아님) — 기댓값 3건 수정.
  - `prisma format`이 스키마 파일 전체를 재인덴트해 diff가 부풀어 오르는 문제 발생 → 스테이지된
    버전을 복원하고 자신의 편집만 재적용해 114줄짜리 깨끗한 diff로 정리.
- **`brand-kits` 모듈** (`/v1/brand-kits`). 로고(S3)·폰트·자막 스타일·팔레트·CTA·워터마크를 계정
  프리셋으로 저장, 편집 세션에 스냅샷으로 전달.
- **편집 세션 계약 확장**: 클립별 전사 큐(단어 타임스탬프 포함) + `captionHighlight`·`brandKit` 필드
  추가. 릴스 세트 생성에도 페르소나를 넘겨 그 목소리로 카피 생성.
- **에이전트 라벨링 업그레이드**: 인제스트 라벨이 4축(확산성·정보성·감정·완결성으로 추정, hookScore와
  별개)을 함께 내고, 전사 문장에 단어 타임스탬프를 보존하도록 수정.
  > **후속 반전(2026-09-19, 하루 뒤)**: [[lampas-agent]]의 "훅 점수 — 절대 점수→상대 순위 전환" 세션이
  > 배치 내 대비 효과로 채점이 불안정하다는 것을 발견해 **에이전트 1.0.12에서 비전 라벨 프롬프트의
  > 훅·4축 점수를 통째로 제거**하고, API 0.1.108의 신규 `rank-hooks`(텍스트만, 이미지 0장) 엔드포인트로
  > 대체했다. 즉 이 세션에서 갓 추가한 "에이전트 라벨링 4축"은 다음 날 되돌려졌고, 뱅크의 4축 미니 바는
  > 그 이후 라벨링이 아니라 reels의 AI 선별(`analyze`)이 채우는 구조로 바뀌었다 — 상세는
  > [[lampas-agent]] 참고.
- **통합 에디터(`lampas-web-edit`)**: 단어 강조(karaoke) 자막을 미리보기·최종 렌더 공용 함수로 그림.
  세션에서 넘어온 자막·브랜드 킷(자막 스타일·워터마크·CTA·로고 레이어)을 시드 직후 자동 적용 — 기존에
  기록돼 있던 "reels 핸드오프 자막 누락" 한계(에디터 서베이가 발견한 갭)가 이걸로 해소됨. 홈에 브랜드
  킷 관리 패널 추가.
- **릴스 앱(`lampas-web-reels`)**: 페르소나 선택(AI 후보 또는 직접 입력) + AI 선별 패널 신설. 선별
  카드에서 훅 클립으로 지정하거나, 트림·자막·훅 문구·브랜드 킷을 실어 에디터로 바로 넘기는 버튼.

**검증**: lampas-api jest 103 suites/914 tests, lampas-agent vitest 342, lampas-web-edit vitest 461,
lampas-web-reels vitest 103 — 전부 통과 + 타입체크·빌드 통과. 커밋은 하지 않음(작업 트리에 다른
세션의 미커밋 변경이 많아 그대로 둠). 설계 문서
`docs/superpowers/specs/2026-09-18-clip-intelligence-brand-kit-design.md` 신설.

## 2. 2차 구현 — Auto Reframe(모션 추적 팬) + 뱅크 다축 표시 + 릴스 배치 생성

"이어서 작업해줘" 요청으로 제품 문서 🔴 MVP 항목 중 남겨뒀던 것을 이어 구현. 전부 프론트만 변경(API·
DB 변경 없음):

- **9:16 자동 리프레임**(`lampas-web-edit`) — 인스펙터 "화면 맞춤" 탭에 "피사체 따라 자동 팬" 버튼.
  클립 구간을 4fps로 샘플링해 프레임 간 움직임의 무게중심을 구하고, 세로 캔버스 가시 비율 기준으로
  그 중심이 화면 가운데 오도록 **팬 키프레임**을 생성. 얼굴 검출이 아니라 **모션 무게중심 근사**라
  스포츠·인물 이동 컷에 적합, 정적 컷은 중앙 고정으로 남음. 미리보기·최종 렌더가 같은 보간 함수 공유.
- **클립 뱅크 다축 점수 표시·정렬**(`lampas-web-clips`) — 카드에 훅·확산·정보·감정·완결 미니 바,
  상단 "축 정렬" 셀렉트로 복합 점수/축별 재정렬(가중치는 API와 동일).
- **선별 결과 배치 생성**(`lampas-web-reels`) — AI 선별 패널에 "편집 세션 N개 일괄 생성" 버튼. 선별
  전체를 각각 트림·전사 자막·훅 문구·브랜드 킷 포함 편집 세션으로 만들고 링크 목록 표시.

**후속 후보로 남긴 것**: 얼굴·객체 검출 기반 리프레임 정밀화, 게시 성과 수집(Instagram insights)+선별
순위 피드백 루프, 배치 생성→예약 게시(packaging) 연결. 검증 후 **전체 배포**(운영 DB DDL 선적용 →
API → 에이전트 → 웹 3종 순서, `[[prod-ddl-before-deploy-with-drift-check]]`와 동일 원칙) — 운영에서
`GET /v1/clip-intelligence/models`·`/v1/brand-kits`(401 가드) 응답 확인. 운영 DB에 이 저장소 스키마에
없는 `scopeArea`/`workspaceId` 컬럼(다른 작업본이 추가)이 남아있던 것을 발견 — 기본값이 있어 이번
배포엔 무해했지만, 앞으로도 `db push`가 아니라 추가 DDL만 수동 적용해야 한다고 재확인.

## 3. 후속 수정 — 키프레임 수동 조정 + 스케일 유지 분석 + Contain 모드 팬 버그

세 번의 개별 사용자 요청, 매번 배포까지 완료:

1. **팬 키프레임 수동 조정**(`lampas-web-edit`) — 인스펙터에서 좌↔우 슬라이더로 키프레임 추가·이동·
   삭제, "+ 현재 위치" 버튼, 미리보기 드래그 시 전체 해제 대신 **현재 시각의 키프레임만 갱신**.
2. **자동 분석 시 맞춤(fit)·스케일 유지** — 사용자가 "스케일링은 그대로 둔 채 분석하게" 요청. 기존엔
   분석이 Cover로 강제 전환했으나, 이후 현재 Cover/Contain·스케일을 유지한 채 그 상태에서 보이는
   창 기준으로만 키프레임 생성. 전부 보이는 상태(팬 여지 없음)면 안내만.
3. **Contain 모드에서 좌우 이동 불가 버그** — 원인: Contain에서 가로 영상은 캔버스 폭에 딱 맞아 팬
   범위가 0이었고, 스케일로 넘친 부분엔 팬이 전혀 안 걸리는 구조였음. 릴스 기본 레이아웃(Contain +
   1:1 확대)이 정확히 이 상태라 실사용에서 막혀 있었다. `clipDrawRect`를 고쳐 스케일 전 넘침이 없는
   축에서는 **스케일 후 넘친 만큼**에 팬을 적용하도록 수정(팬 0.5=가운데는 두 방식 모두 동일이라 기존
   프로젝트 렌더 결과는 불변). 미리보기 `<video>`의 CSS `objectPosition`으로는 Contain 넘침에 팬을
   걸 수 없어, 렌더와 동일한 dest-rect 직접 배치로 전환 — 미리보기·렌더가 완전히 같은 공식을 쓰게 됨.

## 4. 버그 수정 — 에디터 자막 생성이 긴 구간에서 중간에 끊김

"자막 생성 시 모든 내용이 전사되지 않는다" 제보. 원인: 에디터가 클립 트림 구간 전체를 48kHz 스테레오
WAV **한 파일**로 통째 ASR에 보냈는데(에이전트 인제스트는 16kHz 모노로 줄여 600초 단위 분할 전송),
구간이 길면 모델이 앞부분만 전사하고 뒤를 버렸다. 게다가 단어 타임스탬프가 클립 기준 큐로 옮길 때
누락되고 있었다. 수정: 트림 구간을 **300초 청크**로 나눠 청크마다 오디오 추출→16kHz 모노 리샘플→WAV
업로드→전사(빈 결과/실패 시 소음 보정 폴백 모델)→청크 시작 시각 보정 후 이어붙임, 단어 타임스탬프
보존. 리샘플로 업로드 크기 약 1/6 감소. 일반화 가능한 패턴 → [[asr-long-audio-silent-truncation]].

## 5. 릴스 UI 단순화 — 페르소나·카피 기획은 Copy 앱으로 이관

"reels가 너무 UI/UX 복잡해짐" 피드백. 릴스에서 세트 5개 랜덤 생성·교차 심사·AI 그룹핑·훅 그룹·폴더
브라우저·세트 카드·**페르소나/훅 문구 생성 UI 전체 제거**, 대신 Copy 앱의 결과를 재사용하는 단일
화면 6단계 플로우로 재편(**이 세션이 `apps/lampas-web-reels/src/lib/editGroup.ts`의 최초 구현으로
보인다** — [[lampas-web-reels]]가 현재 문서화한 "편집 그룹 생성" 로직의 출처):

1. 소스 선택 → 2. 클립 선택(체크박스 그리드, 훅 기준 슬라이더, "AI 선별로 체크" 상위 12개 일괄 체크,
드로어 미리보기) → 3. 페르소나 선택(Copy가 만든 후보를 칩으로 보여주고 고르기만, 만들기는 Copy 링크로)
→ 4. 카피 선택(Copy에서 채점 완료·페르소나 일치 run을 칩으로, 순위 카피를 라디오로) → 5. 편집 그룹
생성(카피의 클립이 훅 비트, 나머지는 선택 순서, `ReelPlan`으로 저장) → 6. 편집 그룹 목록(삭제/에디터로
열기, 전사 자막+기본 브랜드 킷 동반 전달). 서버 세트 API 자체는 남겨둠. vitest 80건(세트 전용 lib 5개
삭제)·타입체크·빌드 통과, 배포 완료.

이어서 사용자가 **"copy 에서 페르소나·카피 만들기" 링크에 `?clip=<id>` 딥링크**를 요청 → Copy 앱이
`?clip=` 진입 시 그 클립을 1단계에서 미리 선택(페르소나 후보 자동 로드까지), `?run=`이 함께 있으면
run 복원이 우선. Reels의 두 링크(③④)가 첫 선택 클립(또는 카피 선택 중이면 그 카피의 클립)을 실어
Copy를 연다. 두 앱 모두 배포·검증 완료.

> **Pulse와의 관계**: 이 세션 시점(2026-09-18) `lampas-web-copy`는 여전히 자체 `suggestPersonas`/
> `POST /copy/personas`로 페르소나를 만들고 있었다(위 서베이 §4 확인). 페르소나 생성이 Copy에서
> `[[lampas-web-pulse]]`로 완전히 이관되는 것은 **다음 날(2026-09-19)** 별도 병행 세션에서다 —
> 이 세션 후반부(§6)에서 "다른 세션이 같은 작업 트리에서 copy fields → pulse personas 이동을 실시간
> 리팩터링 중"이라고 직접 관찰한 것이 바로 그 병행 세션이다. → [[lampas-web-copy]]·[[lampas-web-pulse]].

## 6. Clips 폴더/제목 인라인 이름변경 (2회 반복 수정)

사용자 스크린샷 피드백에 따라 `lampas-web-clips`의 `FolderSidebar`에 이름 변경 UI를 두 번 고쳐 배포:
① 처음엔 활성 폴더 옆에 "이름" 버튼 추가(기존 더블클릭 방식이 발견하기 어려웠다는 이유) → ② 사용자가
"제목 부분을 선택해서 수정하는 걸로 다시" 요청해 "이름" 버튼 제거, 대신 **선택된 폴더의 상단 제목을
클릭하면 바로 인라인 입력**으로 전환(Enter/포커스 이탈 저장, Esc 취소, "전체 클립"/"폴더 없음"은
편집 대상 제외). 둘 다 기존 `PATCH /clips/folders/:id`만 사용, API 변경 없음.

## 7. Copy 추가 지시사항·톤 — 서버 프리셋 + 분야·카테고리별 마지막 선택 기억

"다른 브라우저로 가면 추가 지시사항이 사라진다" + "분야/카테고리별로 마지막 선택을 기억해달라"는
요청으로 신규 **독립 모듈 `copy-hints`**(`/v1/copy-hints`, 기존 `copy` 모듈과 무관해 위 §5 말미의
동시 리팩터링과 충돌 없음)를 신설:

- 테이블 `copy_hint_presets`·`copy_hint_selections`(범위 키 = 클립의 분야+카테고리, 예 `sports:12`/
  `general:none`). `GET /`(프리셋+범위별 마지막 선택) · `POST /`(중복 병합, 대소문자·공백 무시) ·
  `PUT /selection {scopeKey, text}`(비우면 해제, 사용 횟수 증가) · `DELETE /:id`.
- `lampas-web-copy`: 추가 지시 입력이 "저장된 지시 선택" 셀렉트 + 직접 입력 textarea로 바뀌고 "이
  지시 저장"/"저장 삭제" 버튼 추가. 카피를 만들면 그 클립의 분야+카테고리에 자동 기억.
- **배포는 즉시 보류됨** — 같은 작업 트리에서 §5 말미에 언급한 다른 세션이 copy 웹의 `copy-api.ts`에서
  `fetchCopyFields` 등을 이미 제거했는데 `StudioPage.tsx`가 아직 그걸 import해서 컴파일이 깨져 있었고,
  API를 먼저 배포하면 운영이 의존하는 `/v1/copy/fields`가 사라져 함께 깨질 상황이었기 때문. 그 세션이
  끝나 컴파일이 복구된 뒤(§7-2) 함께 배포됨.
- **§7-2 (톤도 동일 구조로 통일)**: 사용자가 "톤도 추가지시처럼 저장되게, 선택 삭제도 되게" 요청.
  프리셋에 `kind`(`hint`|`tone`) 필드 추가, 선택 유니크를 `(userId, kind, scopeKey)`로, 웹은 공용
  `PresetSelect` 컴포넌트로 통합. 이번엔 다른 세션의 리팩터링이 완료돼 있어 API→copy 웹 순서로 정상
  배포(운영 DB `kind` 컬럼 추가 시 FK 인덱스 순서 문제는 새 인덱스 추가 후 구 인덱스 삭제로 해결).
  같은 배포에 §5 말미의 pulse-personas 이관분도 함께 실려나감(운영 `/v1/pulse/fields` 정상 응답 확인).

## 관련
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 신규/갱신 엔티티: [[lampas-web-edit]] · [[lampas-web-reels]] · [[lampas-web-copy]] · [[lampas-web-clips]] ·
  [[lampas-agent]]
- 토픽: [[lampas-clip-intelligence]]
- 스킬: [[parallel-survey-before-feature-gap-analysis]] · [[asr-long-audio-silent-truncation]] ·
  [[prod-ddl-before-deploy-with-drift-check]](동일 배포 원칙 재사용)
- 후속/병행 세션: [[2026-09-19-lampas-edit-자막-템플릿-대량기능개발]] ·
  [[2026-09-19-pulse-페르소나-단일출처-계정이관-신뢰도개선]] ·
  [[2026-09-19-lampas-agent-clips재생목록-pulse로그인수집-훅점수상대순위]](4축→상대순위 전환의 출처)
