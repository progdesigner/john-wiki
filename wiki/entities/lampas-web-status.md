---
tags: [entity, project, app, status-page, monitoring, lampas-studio]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-status (Lampas 상태 페이지)

`apps/lampas-web-status`(포트 8461) — `[[lampas-studio]]` 저장소(`lampas-system`) 내 앱. status.claude.com을
본떠 만든 공개 시스템 상태 페이지, 도메인 `status.lampas.io`. 2026-09-25 세션에서 처음부터 구현되고
같은 세션에서 운영 배포까지 완료됨 → [[2026-09-25-status-서비스-구축-배포]].

## 구조

- **백엔드**: `apps/lampas-api/src/modules/status`. 컴포넌트 레지스트리(`lib/status-components.ts`)에
  등록된 **40개 컴포넌트**를 60초마다 병렬 프로브 — Lampas API 헬스·AI 게이트웨이·MySQL, 배포된 웹
  도메인 전부(lampas.io/dalar.ai/toktalk.ai/iileex.com), 로컬 에이전트 `/healthz`, 트렌드 S3 스냅샷
  신선도, [[atlas-cloud]]. 결과는 원시 기록 테이블(7일 보존) + 한국시간 일 집계 테이블(90일 바) 2단
  저장.
- **판정 규칙**(순수 함수로 분리): 2xx/3xx 정상 · 헬스 JSON `status:'ok'` 또는 `ok:true` · S3 스냅샷
  15분 경과 시 저하/30분이면 장애 · 응답 3초 초과 시 저하. Atlas Cloud는 루트 경로가 404라
  `/v1/models`로 도달성 판정하는 예외 처리 보유.
- **자동 인시던트**: 3회 연속 실패 시 오픈(core 컴포넌트면 심각도 상향), 3회 연속 정상이면 자동 해결.
  약 3분 간격(60초 프로브 기준).
- **공개 API**: `GET /v1/status`(전체+그룹+90일 일별+1/7/30/90일 업타임+인시던트, 10초 캐시),
  `GET /v1/status/components/:id?hours=`(시간별 가용성·p50/p95·최근 프로브), `GET /v1/status/incidents`.
  관리용 `/v1/admin/status/*`(인시던트 CRUD·즉시 프로브) — `lampas-web-admin`의 `/status` 페이지가 소비.
- **프론트**: status.claude.com 형태 UI — 전체 상태 배너, 그룹별 90일 업타임 바(툴팁), 행 클릭 시
  드로어(응답 시간 그래프·시간대별 가용성·이상 프로브), 분석 패널(느린 곳·장애 일수·평균 복구 시간),
  진행 중·지난 14일 인시던트 목록.
- **컴포넌트 관측 대상에서 제외된 것**: `photobooth.lampas.io`는 DNS가 없어 항상 장애로 잡혀 레지스트리
  에서 뺐음(`deploy.json`에는 여전히 항목이 남아 있음 — 실서빙 계획이 생기면 재확인 필요).

## 배포·검증 (2026-09-25)

- 로컬: API jest 45건·web-status vitest 17건 통과, web-status/web-admin 빌드 통과, 로컬 데몬 40개
  컴포넌트 전부 정상 프로브 스크린샷 확인.
- 운영: 신규 DB 테이블 4개 DDL 선적용(`tools/apply-status-page-ddl.sh`) → `deploy-api.sh lampas-api`
  (PM2 v0.1.144) → `distibute-web.sh lampas-web-status`(CloudFront 신규 생성+Route53 최초 연결) →
  `deploy-web.sh lampas-web-admin` → `deploy-web.sh lampas-web-status`. 커밋 `e1b0419d`.
  운영 API 첫 프로브에서 40개 전부 정상, `status.lampas.io`·`admin.lampas.io/status` 모두 HTTP 200
  확인. 절차 전체 → [[prod-ddl-before-deploy-with-drift-check]].
- 배포 중 드리프트 검사로 무관해 보이던 [[lampas-agent]] "경기(game)" 기능의 `sports_wiki_games`
  테이블이 운영에 없다는 사실을 함께 발견·DDL 적용 — 두 기능이 같은 배포 창에서 우연히 얽힌 사례.

## 신규 서비스 등록 방법
새 컴포넌트를 관측 대상에 추가하려면 `apps/lampas-api/src/modules/status/lib/status-components.ts`에
한 줄 추가하면 됨. 빠뜨리면 `deploy.json`과 비교하는 spec이 실패로 잡아줌(레지스트리-배포 목록 불일치
가드).

## 관련
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 자매 앱: [[lampas-agent]] (같은 배포 창에서 스키마가 얽힘) · `lampas-web-admin`(인시던트 관리 UI 소비처)
- 세션: [[2026-09-25-status-서비스-구축-배포]]
- 스킬: [[prod-ddl-before-deploy-with-drift-check]] · [[new-subdomain-cloudfront-wildcard-deploy]]
- 외부 의존: [[atlas-cloud]] (프로브 대상)
