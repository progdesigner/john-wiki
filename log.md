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

## [2026-07-10] ingest | 전대 동의 메일 작성 (CWC 엘레망 광화문)
- 소스: raw/conversations/2026-07-10-전대-동의-메일-작성.md (원본 5304240d-...md 복사)
- 세션: [[2026-07-10-전대-동의-메일-작성]] (기술 작업 아닌 비즈니스 이메일 대행, 1왕복)
- 내용: 임대인(동원빌딩 관리실)이 "전대차계약서 → 검토 → 진행"으로 서류 요구를 이어가는 흐름에서, 서류 협의보다 **임대인의 원칙적 전대 동의 여부를 먼저 확정**해달라는 정중한 회신 메일 초안 작성.
- 엔티티 신설: [[cwc-commerce]] (씨더블유씨커머스 유한회사·대표 이용욱/John Lee·엘레망 광화문 임차·위키 배경 회사 "CWC"), [[sylvan-korea]] (계열사·공유오피스 2026-09 종료·일부 공간 공동 사용 검토), [[dongwon-building]] (동원리소스 서울지점·임대인/관리)
- 스킬 신설: [[business-email-consent-first]] (서류 요구 반복 시 원칙적 동의 여부를 먼저 확정 요청하는 회신 메일 화법)
- 엔티티 갱신: [[progdesigner]] (John Lee=CWC 대표와 동일 인물 가능성 명시·단정 보류, 회사·세션 크로스링크)
- index.md 갱신 (세션1·엔티티3·스킬1 추가)
- 특이사항: 소스에 실제 연락처(휴대폰·주소·이메일)·거래 상대 이메일 포함 — 개인/거래정보. 정체성(progdesigner=John Lee)은 근거상 개연적이나 트랜스크립트만으로 단정하지 않고 "추정"으로 기록.

## [2026-07-11] lint | 저장소 이동 (john-wiki → Works/john-wiki)
- `/Users/progdesigner/john-wiki` → `/Users/progdesigner/Works/john-wiki`로 폴더 이동.
- `wiki/entities/john-wiki.md`의 로컬 경로(베이스 폴더) 기록을 새 위치로 갱신.

## [2026-07-12] ingest | 기억 요약 요청·wiki 경로/WIKI_DIR 확인
- 소스: raw/conversations/2026-07-11-기억-요약-wiki-경로-확인.md (원본 88de2ab0-...md 복사)
- 세션: [[2026-07-11-기억-요약-wiki-경로-확인]] (관찰·확인 세션, 2026-07-11 16:27~16:32, 4왕복)
- 내용: 사용자가 "기억 저장 내용 요약" 요청 → 어시스턴트는 장기기억 조회 tool 부재로 답 못 함. 이어 wiki 위치·`WIKI_DIR` 값 확인 시도 → `src/config.ts`의 `wikiDir: process.env.WIKI_DIR ?? path.join(os.homedir(), "Works", "llm-wiki")` 인용, 환경변수 직접 확인 불가(이 세션 tool: 파일/git/웹만, bash 없음).
- 핵심 관찰: (1) 2026-07-08 provider 설계 후에도 어시스턴트 쪽 memory 조회 tool 여전히 미노출, (2) **코드엔 `wikiDir`/`WIKI_DIR` 설정이 이미 존재** → 배선 착수됨(진전), (3) **⚠️ 경로 불일치**: 기본값 `~/Works/llm-wiki`인데 실제 저장소는 `~/Works/john-wiki`.
- 엔티티 갱신: [[john-wiki]](wikiDir 설정 존재+경로 불일치 명시), [[lampas-harness]](2026-07-11 진행 관찰 절 추가)
- 토픽 갱신: [[long-term-memory-architecture]](구현 진행 상태 절 신설 — 방법 C 미완, "config 경로만 넣으면 끝" 아님)
- 스킬 갱신: [[wiki-memory-provider-integration]](함정 추가 — config 경로만 넣는 착각 + 경로명 불일치 확인)
- index.md 갱신 (세션1 추가)
- 특이사항: 신규 엔티티/스킬 없음(기존 페이지에 후속 상태 반영). 2026-07-08 "완전 미구현" 대비 코드 진전을 모순 아닌 갱신으로 기록.

## [2026-07-12] ingest | 기억 저장 내용 조회 가능 여부 질문 (2026-07-11)
- 소스: raw/conversations/2026-07-11-기억-저장내용-조회가능여부.md (원본 1a683446-...md 복사)
- 세션: [[2026-07-11-기억-저장내용-조회가능여부]]
- 토픽 갱신: [[long-term-memory-architecture]](구현 진행 상태 절 — 같은 날 두 채팅 재확인 + 파일 tool 위키 미스코프 관찰 추가)
- index.md 갱신 (세션1 추가)
- 특이사항: 신규 엔티티/토픽/스킬 없음. memory provider 미연결의 세 번째 독립 재확인 데이터포인트 — 기존 [[2026-07-11-기억-요약-wiki-경로-확인]]과 near-duplicate지만 별도 채팅이라 세션 페이지 신설, 새 디테일(`search_files`/`list_directory` tool·작업폴더 스코프)만 반영.
