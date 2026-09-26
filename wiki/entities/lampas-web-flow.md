---
tags: [entity, app, lampas-studio, sports-clip-pipeline, orchestration, tailscale, s3, jev]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-flow ("Flow", flow.lampas.io)

`[[lampas-studio]]` 저장소(`lampas-system`)의 스포츠 클립 파이프라인을 하나로 묶는 **노드 그래프
오케스트레이션 허브**. `[[lampas-agent]]`(맥미니, 유튜브 영상 수집)·Clips(클립 폴더링, `lampas-agent`
탭)·`[[lampas-web-copy]]`(페르소나·카피)·`[[lampas-web-reels]]`(편집 그룹)·Edit(템플릿 편집·내보내기,
`[[lampas-studio]]` "Edit 템플릿 에디터" 절)·Package(갤러리→패키지 전송)를 **"Work"** 단위로 연결해
진행 상황과 각 작업 공간을 한 화면에서 관리한다. 2026-09-20 세션에서 요청·구현·배포까지 완료 →
[[2026-09-20-lampas-flow-만들기]].

## 핵심 개념 — Work
- Work 하나 = 6단계(또는 키워드 기반 실험형, 아래 참고) 노드 그래프. 노드 추가·분기·드래그 가능.
- **완료 판정은 Flow가 소유하지 않는다** — 각 서비스가 실제로 만든 결과물(클립·카피·편집 그룹·편집
  세션·내보낸 영상)의 존재 여부로 판정. 서비스에서 작업 후 Flow로 돌아오면 상태를 다시 조회해 갱신.
- 결과물이 삭제되는 등 연결이 끊기면 "확인 필요" 상태로 표시 — 단, 이 표시가 **오래된 비동기 알림
  때문에 잘못 뜨는 버그**가 있었고 수정됨 → [[execution-run-scoped-status-vs-stale-notification]].
- 저장 시 다른 탭의 변경을 덮어쓰지 않도록 **충돌 검사**가 있다.
- 편집본이 마지막 내보내기보다 새로 바뀌면 완료 표시를 해제하고 재내보내기를 안내.

## 기본 6단계 Work (2026-09-20 초기 구현)
1. **영상 수집** — `[[lampas-agent]]`(맥미니)를 Tailscale로 호출, 유튜브 URL 전달.
2. **Clips** — 특정 소스의 특정 클립만 폴더링(`lampas-agent`의 Clips 탭 화면).
3. **Copy** — 클립의 추천 페르소나로 카피 생성(`[[lampas-web-copy]]`, 엔진은 `[[lampas-agent]]` Pulse).
4. **Reels** — 클립·카피를 선택해 편집 그룹 생성(`[[lampas-web-reels]]`).
5. **Edit** — 클립·카피 바탕으로 계정별 저장 템플릿 적용·내보내기.
6. **Package** — 갤러리의 영상을 선택해 패키지로 전송.

## 트렌드 분석 Work (2026-09-21 신설 — 두 번째 Work 유형)
원본 영상을 다운로드하지 않고, **키워드로 수집한 제목·캡션·댓글**만 근거로 페르소나·카피를 실험하는
Work. 1) Pulse 키워드 수집(최근 7일) → 2) 페르소나 4개 생성+AI 평가 → 3) 페르소나별 카피 10개씩
생성·독립 채점 → 4) 평점·수정 의견 저장 → "평가 반영·다음 실험 생성"으로 이전 평가를 다음 생성에
반영. 실험 회차별 결과·평가가 남아 Flow·Pulse·Copy 세 화면에서 비교 가능. 상세 →
[[2026-09-20-lampas-flow-만들기]] "트렌드 분석 Work" 절.

## Jev 노드 자동화 (2026-09-20)
각 노드에 `[[jev-typed-classification]]`(선택·평가 모델)을 연결해 대표 작업을 자동 수행, 실제 카피
생성·영상 편집 실행은 각 서비스 기존 기능이 담당. 기본 자동 제작 결과물(사용자 확정): **추천 클립
3개 → 한국어 카피 → 세로 9:16 릴스 1개 → 패키지 저장**. 전체 실행/노드별 실행 지원, 중단 시 저장된
결과 재사용해 이어서 실행. 노드별 프롬프트는 화면에서 확인·수정·저장 가능(기본값 늦게 로드되며
사용자 입력을 덮어쓰던 버그 수정됨). Jev 실 호출은 `TYPESAFE_API_KEY` 설정 후 검증 완료(페르소나
선택 약 0.5초) — 키 값은 위키에 기록하지 않음.

## Harness 개선요청 브리지 (2026-09-21)
오류 발생 시 옆에 뜨는 **"Harness에 개선 요청"** 버튼 — 실행·노드·오류 정보를 담은 진단 요청문을
만들어 수정·복사 가능, `[[lampas-harness]]`를 열어 붙여넣는 것은 사용자가 직접 수행(Flow가 자동
전송하지 않음). 관련해 자동 실행이 Flow의 **30초 요청 제한**에 걸리던 버그를 즉시-응답+별도 폴링
구조로 수정 → [[accept-then-poll-for-slow-ai-jobs]].

## 계정 이동 (SSO)
Flow에서 다른 작업 공간(Clips/Copy/Reels/Edit/Package)으로 이동하거나 돌아올 때, 짧게 만료되는
일회용 교환 코드로 현재 로그인 계정을 이어받는다(대상에 다른 계정이 로그인돼 있어도 전환). 맥미니
`[[lampas-agent]]`는 로컬 처리 세션과 운영 업로드 세션을 함께 연결. 상세 절차 →
[[cross-subdomain-session-handoff]].

## 접속·인프라
- 배포: S3 + CloudFront (`./scripts/deploy-web.sh`), API는 기존 `lampas-api`에 `flow_works` 테이블
  추가.
- 맥미니 Agent 접속: `https://lampas-system.tail0e32ab.ts.net/` (Tailscale HTTPS).
- 로컬: `http://127.0.0.1:8461` (개발/최초 검증 시 사용된 주소).

## 관련
- 엔티티: [[lampas-agent]](영상 수집·Clips·Pulse) · [[lampas-web-copy]](카피) ·
  [[lampas-web-reels]](편집 그룹) · [[lampas-studio]](Edit 템플릿) · [[lampas-harness]](오류 개선 루프)
- 토픽: [[jev-typed-classification]]
- 스킬: [[tailscale-funnel-large-payload-bypass]] · [[cross-subdomain-session-handoff]] ·
  [[execution-run-scoped-status-vs-stale-notification]] · [[accept-then-poll-for-slow-ai-jobs]]
- 세션: [[2026-09-20-lampas-flow-만들기]]
