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

## [2026-07-09] ingest | TokTalk 에피소드 배경 전환·플레이 세션 (2026-07-08)
- 소스: raw/conversations/2026-07-08-toktalk-에피소드-배경전환-플레이.md (원본 03c73e80-...md 복사)
- 세션: [[2026-07-08-toktalk-에피소드-배경전환-플레이]]
- 엔티티 신설: [[toktalk]] (AI 캐릭터/보이스 챗 제품 — talk-* 7앱 모노레포, toktalk.ai. lampas-studio와 별개 코드베이스, 같은 하네스로 개발). [[progdesigner]] 프로젝트·세션 크로스링크 갱신
- 토픽 신설: [[episode-beat-play-system]] (beat 플레이·배경 선전환·선택지 sticky·beat 직접연결·로직 이중화 함정)
- 스킬 신설: [[loading-state-key-mismatch]] (요청은 되는데 UI 무반응 → busy 키 접미사 불일치 진단)
- index.md 갱신 (세션1·엔티티1·토픽1·스킬1 추가)
- 특이사항: 커밋 `0664ea9`에 실제 API 키 `.env`·Firebase 서비스 계정 JSON이 포함되어 origin/main에 푸시됨(어시스턴트 고지 후 사용자 지시로 진행) — 노출 상태를 세션·엔티티에 기록.

## [2026-07-09] ingest | 스케줄러·로컬 LLM 설치·사용 영역 페르소나 세션 (2026-07-08~09)
- 소스: raw/conversations/2026-07-08-스케줄러-로컬llm-사용영역페르소나.md (원본 affc3b6b-...md 복사)
- 세션: [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]] (4개 스레드: 예약 스케줄러·로컬 LLM 설치·사용 영역 페르소나·일본어 드리프트)
- 토픽 신설: [[harness-background-process-lifecycle]] (턴/세션 종료로 배경작업 사망 — 스케줄러·다운로드 관통 한계), [[local-llm-on-apple-silicon]] (모델크기·양자화·백엔드, Rapid-MLX/llama.cpp/Ollama)
- 스킬 신설: [[session-independent-scheduler]] (서버 내장 파일 영속 스케줄러·도착시각 기준 정확도), [[detach-long-job-nohup]] (nohup & disown 세션 분리), [[local-llm-rapidmlx-install]] (Rapid-MLX 설치·launchd 상주·모델 선택기 통합)
- 엔티티 갱신: [[lampas-harness]] (scheduler.ts·로컬 LLM 프로바이더·사용 영역 페르소나 추가·재확인된 함정), [[model-selection]] 관련 링크
- index.md 갱신 (세션1·토픽2·스킬3 추가)
- 특이사항: **이전 ingest가 중단됨** — 페이지·raw·엔티티는 생성됐으나 세션 페이지 말미 tool 태그 잔재(`</content></invoke>`) 제거, index.md 미반영분(세션1·토픽2·스킬3) 보강, 누락된 log 항목 추가로 이번에 완결. DeepSeek-V4-Flash(284B/~95GB)는 64GB로 실행 불가 반려. 로컬 qwen 모델 전환 후 웹검색/MCP 질문에 환각("MC P"=Multi-Controller Protocol) 관찰. 일본어 드리프트는 코드 버그 아닌 모델 행동 문제로 규명.

## [2026-07-09] ingest | "일반" 사용 영역 페르소나 설정 (페이블5 지침 루머)
- 소스: raw/conversations/2026-07-09-일반-사용영역-페르소나-설정.md (원본 73bab575-...md 복사)
- 세션: [[2026-07-09-일반-사용영역-페르소나-설정]] (짧은 세션, 2026-07-09 11:27~11:32)
- 토픽 신설: [[system-prompt-mimicry-misconception]] (공개 시스템 프롬프트 복제로 모델 흉내 통념의 한계 + 출처미상 지침 붙여넣기 보안 주의)
- 엔티티 갱신: [[lampas-harness]](사용 영역 "일반" 페르소나 정책 변경 기록), [[progdesigner]](세션 크로스링크)
- index.md 갱신 (세션1·토픽1 추가)
- 특이사항: **기존과 모순 명시** — 2026-07-08 세션은 "일반=페르소나 없음"으로 정의했으나 이 세션에서 사용자 지시로 "일반"에 페르소나를 채움(비어있음→채움). 계기는 "페이블5 숨은 지시문" 프로모션 루머(단축링크·클릭베이트)이나 취지만 참고하고 원문은 복제하지 않음. 트랜스크립트는 데몬 재시작 확인 직전 끊김(반영 미확정). 스킬 추출 없음(절차가 얇고 기존 페르소나 UI 재사용).
