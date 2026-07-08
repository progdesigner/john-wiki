# Log

append-only. 형식: `## [YYYY-MM-DD] <ingest|query|lint> | <제목>`

## [2026-07-07] ingest | lampas-harness 데몬화·채팅 UX 구축 세션
- 소스: raw/conversations/2026-07-06-lampas-harness-구축.md (원본 b2f1cf49-...md 복사)
- 세션: [[2026-07-06-lampas-harness-구축]]
- 엔티티 신설: [[lampas-harness]], [[lampas]], [[progdesigner]]
- 토픽 신설: [[model-selection]], [[harness-queue-vs-chat]]
- 스킬 신설: [[macos-launchd-daemon]], [[sdk-session-persistence]], [[idempotent-message-retry]], [[work-folder-sandboxing]]
- index.md 갱신 (첫 카탈로그 등록)

## [2026-07-09] ingest | Lampas 스튜디오 레퍼런스·Instagram 통합 세션 (2026-07-08)
- 소스: raw/conversations/2026-07-08-lampas-스튜디오-레퍼런스-instagram.md (원본 e75201fd-...md 복사)
- 세션: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]]
- 엔티티 신설: [[lampas-studio]] (이미지 생성 제품 — 하네스와 별개). [[progdesigner]]·[[lampas-harness]] 크로스링크 갱신
- 토픽 신설: [[instagram-reference-integration]]
- 스킬 신설: [[instagram-public-media-fetch]], [[localstorage-ui-preference-persistence]], [[deploy-sandbox-pnpm-shim]]
- index.md 갱신 (세션1·엔티티1·토픽1·스킬3 추가)
- 특이사항: 원본 트랜스크립트가 레퍼런스 시트 모델 선택 웹 배포 대기 지점에서 끊김. 오래된순 정렬은 시도 후 폐기(토픽에 기록).

## [2026-07-09] ingest | 장기기억(john-wiki) provider 연동 설계 세션 (2026-07-08)
- 소스: raw/conversations/2026-07-08-장기기억-john-wiki-연동.md (원본 182cae7d-...md 복사)
- 세션: [[2026-07-08-장기기억-provider-연동-설계]]
- 엔티티 신설: [[john-wiki]] (이 위키 자체를 엔티티로 등록). [[lampas-harness]]·[[progdesigner]] 크로스링크·선호 갱신
- 토픽 신설: [[long-term-memory-architecture]] (저장≠조회, 3계층 전략, 조회 3방법, 안티할루시네이션)
- 스킬 신설: [[wiki-memory-provider-integration]] (경로 traversal 가드 버그·TS/Claude SDK 이식 주의 기록)
- index.md 갱신 (세션1·엔티티1·토픽1·스킬1 추가)
- 특이사항: **설계·제안 세션(미구현)** — 당시 어시스턴트에 파일 도구 없어 코드 스케치만. 제안된 provider 설계가 바로 이 위키가 실현하는 구조라는 메타 관계를 세션·토픽 페이지에 명시.
