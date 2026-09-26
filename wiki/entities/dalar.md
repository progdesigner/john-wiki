---
tags: [entity, project, product, monorepo, stub, instagram]
created: 2026-09-26
updated: 2026-09-26
---
# Dalar

이 위키에 **2026-09-26 처음 노출**된 제품/서비스 라인. `lampas-web-fit` 구축 세션
([[2026-09-25-lampas-web-fit-구축-배포]], `Tool: codex`)에서 `[[lampas-studio]]` 저장소
(`lampas-system`) 루트 `AGENTS.md`를 통해서만 간접 확인됐고, 이 세션에서 Dalar 자체를 다루거나
조사한 적은 없다 — 아래는 문서 인용뿐이며 검증되지 않음.

**2026-09-26 ingest 시 시점 정정(스텁 부분 해소)**: 위 "2026-09-26 처음 노출"은 이 위키의 관찰
시점일 뿐이다. 실제로는 그보다 6일 앞선 [[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]]
세션(`Tool: codex`, `lampas-system`)에서 Dalar 소속 실사용 소비자 제품 **`[[dalar-web-first]]`**
("First" — AI 돌잔치 인터뷰 영상 서비스, `first.dalar.ai`/`admin.first.dalar.ai`)가 대규모로
개발·배포됐음이 뒤늦게 ingest됨. 이는 `AGENTS.md` 인용만으로 존재를 추정했던 이전 기록과 달리
**실제 소스코드 경로(`apps/dalar-web-first/public/samples/...`)·운영 도메인·배포 커밋까지 확인된
첫 사례**다. 상세는 [[dalar-web-first]] 참고.

- **앱 목록 갱신**: 기존 6앱(`dalar-web-root/www/cs/app/admin`, `dalar-api`) 목록에 **7번째 앱
  `dalar-web-first`**가 추가된다.
- **2026-09-26 ingest, 스냅샷 시점 재확정**: 동일한 Dalar 6앱 목록(포트까지 일치)이
  [[2026-09-13-dalar-web-first-최초구축-오만크레딧결제요청]] 세션의 `AGENTS.md`에 이미
  존재함 — 기존에 "2026-09-25 `lampas-web-fit` 세션에서 첫 관찰"로 기록했던 스냅샷보다 12일
  앞선 시점이다. `dalar-web-first`는 이 09-13 시점에도 여전히 미등재였으므로, "실사용 앱이
  있는데 문서 목록에 없다"는 괴리는 최소 09-13부터 09-25까지 계속 유지된 것으로 정정한다.
- **`AGENTS.md` 모순**: `[[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]]` 세션
  시스템 프롬프트에 포함된 `AGENTS.md`(2026-09-20 시점)에는 **Dalar 앱이 하나도 열거되지 않는다**
  (Lampas 9앱+Talk 9앱+Iileex만 기재). 반면 하루 뒤인
  [[2026-09-21-lampas-agent-fixs-신설]] 세션의 `AGENTS.md`는 Dalar 6앱을 포함한다 — 같은 저장소의
  문서가 짧은 기간에도 버전이 다르거나 세션마다 다른 캐시를 인용받고 있다는 뜻. `dalar-web-first`는
  **두 버전 모두에 등재되지 않았다**(실사용 도메인·배포 이력이 있는데도 문서 목록 누락) — 문서가
  실제 앱 구성을 못 따라간다는 기존 관찰([[lampas-studio]] "구조 괴리" 절)의 세 번째 사례.

## 관찰된 정보 (AGENTS.md 인용, 미검증)
- `lampas-system` 모노레포 안에 Lampas·Talk와 나란한 세 번째 앱 그룹으로 존재.
- 앱 6개: `dalar-web-root`(8350), `dalar-web-www`(8351), `dalar-web-cs`(8352),
  `dalar-web-app`(8355), `dalar-web-admin`(8359), `dalar-api`(3356).
- **`apps/dalar-web-app`이 Node Studio(스튜디오 UI)의 SoT(Source of Truth)** — `[[lampas-studio]]`의
  `lampas-web-studio`는 `pnpm sync:studio`(`scripts/sync-studio-from-dalar.mjs`)로 여기서 동기화받는
  구조라고 `AGENTS.md`가 명시. **2026-09-24 갱신**: [[lampas-studio]]의 Seedance 2.0 Mini 추가 세션
  ([[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]])이 `dalar-web-app`을 먼저 수정하고
  `pnpm sync:studio`를 **실제로 실행**해 `lampas-web-studio`에 반영·드리프트 0을 확인했다 — 이 SoT
  관계가 문서 인용이 아니라 실행으로 검증된 첫 사례. 다만 그 세션도 Dalar 자체(`dalar-web-app` 내부
  구조, 다른 5앱)를 조사하진 않았으므로, `lampas-web-sdk` 자체 구현 서술과의 배치 여부·Dalar 소속
  자체는 여전히 다음 조사 필요.
- 스펙 문서 경로: `docs/superpowers/specs/2026-08-02-studio-sync-dalar-to-lampas-design.md`(내용 미확인).
- `pnpm sync:studio:check`로 스튜디오 SoT 드리프트를 검사하는 명령이 별도 존재.

## 채팅 의도 분류 Jev 도입 — 구현 완료, 배포 여부 미확인 (2026-09-20 세션)

`[[lampas-studio]]`(`lampas-system`) AI 호출 패턴 전체 감사 세션(`Tool: claude`, 2a32a383, → 아래
세션 링크)에서 사용자가 "#6 채팅 의도 분류·플로우 턴 판별"을 지정해 구현:

- `dalar-api POST /v1/orchestration/classify-intent` — 의도 `choice`(10종)+취소 `noul`을
  `[[jev-typed-classification]]`(TypeSafe) 한 호출로 판정. `analyze-flow-turn`이 확신 취소(≥0.75)·
  shoot 이외 플로우 진행(≥0.85)을 Grok 없이 확정.
- `dalar-web-app director/mergeIntent.ts` — 기존 `classifyIntent.ts`(16종 한국어 정규식)와 confidence
  게이팅(≥0.6 채택, 취소 ≥0.8, 사진 첨부는 규칙)으로 병합. 기존 평가 세트 28건 그대로 통과, 병합
  로직 테스트 10건 추가.
- 벤치 스크립트 `apps/dalar-api/scripts/bench-intent-jev.ts` — 같은 평가 세트로 Jev의 한국어 정확도를
  직접 측정하기 위한 도구(실행 여부 미확인).
- lampas-api 1003·dalar-api 19 스위트·dalar-web-app 469 테스트까지 전부 통과했으나, **세션 종료
  시점엔 `TYPESAFE_API_KEY`가 어느 env에도 없어 배포가 보류됨**. 이후 실제 운영 배포·벤치 실행 여부를
  언급하는 후속 소스가 이 위키에 없어 **미확인 상태로 남긴다** — 같은 세션에서 함께 구현된
  `lampas-api` sports-wiki 게이트(#7)는 5일 뒤 소스로 실제 배포가 간접 확인됐지만, 이 기능은 그런
  확인이 없다.

## 열린 질문
- Dalar가 `[[lampas-studio]]`와 같은 회사/제품군인지, 별도 브랜드인지 이름만으로는 판단 불가.
- ~~`lampas-web-studio`가 Dalar에서 단방향 동기화만 받는지, 자체 변경분도 있는지 미확인.~~ →
  2026-09-24 세션에서 최소 1건은 단방향(dalar-web-app→sync:studio→lampas-web-studio) 동기화로
  확인됨. 다른 기능도 전부 단방향인지는 여전히 미확인.

## `ai.dalar` Instagram 계정 — 콘텐츠 자동 게시용 (2026-09-26, 추정)

[[2026-09-26-ai-dalar-인스타그램-토큰발급-메타앱생성]] 세션에서 Instagram 계정 **`ai.dalar`**
(IG user ID `17841446605741878`)용 전용 Meta 앱 "dalar ai publisher"(앱 ID `1845197946676275`,
Instagram 앱 ID `4664045167163943`)를 만들어 `instagram_business_content_publish` 등 5개 권한과
장기 액세스 토큰을 확보하려는 작업이 진행됐다. 목표 연결 지점이 `[[lampas-web-package]]`의
`POST /v1/packaging/channels/instagram/connect`인 점으로 미루어 `ai.dalar`는 Dalar 콘텐츠를
자동 게시하는 전용 계정으로 **추정**된다(세션 내 직접 확인 없음). **세션 종료 시점까지 토큰 발급
미완료** — Meta 앱 시크릿 확보가 막혀 있었다(자세한 장애물은 세션·스킬 링크 참고).

## 관련
- 세션: [[2026-09-25-lampas-web-fit-구축-배포]] · [[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]] ·
  [[2026-09-20-lampas-first-장면가격체계-샘플영상-초대코드]] ·
  [[2026-09-20-jev-typesafe-어댑터-dalar의도분류-sportswiki게이트-구현]] ·
  [[2026-09-26-ai-dalar-인스타그램-토큰발급-메타앱생성]]
- 저장소: [[lampas-studio]] (같은 모노레포 `lampas-system` 안에 공존)
- 앱: [[dalar-web-first]]("First" AI 돌잔치 영상 서비스, `first.dalar.ai`)
- 토픽: [[jev-typed-classification]] · [[lampas-system-ai-call-architecture-audit]]
- 채널 연동: [[lampas-web-package]] (Instagram 채널 연결 API 소비처로 추정)
