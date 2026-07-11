# Index

전체 페이지 카탈로그. ingest/lint 때마다 갱신할 것.

## Sessions

- [[2026-07-06-lampas-harness-구축]] — lampas-harness 데몬화·채팅 UX 하루 집중 구축 세션
- [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] — Lampas 스튜디오 레퍼런스·Instagram 통합·배포 세션
- [[2026-07-08-장기기억-provider-연동-설계]] — john-wiki를 harness 장기기억 provider로 붙이는 설계·제안 세션
- [[2026-07-08-toktalk-에피소드-배경전환-플레이]] — TokTalk 에피소드 beat 플레이 배경 전환(선전환·sticky)·AI 로딩 버그 세션
- [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]] — 예약 메시지 스케줄러·로컬 LLM(qwen3.5-35b) 설치·사용 영역 페르소나·일본어 드리프트 세션
- [[2026-07-09-일반-사용영역-페르소나-설정]] — "일반" 사용 영역 페르소나 설정(페이블5 지침 루머 계기), 기존 "페르소나 없음" 정의와 모순
- [[2026-07-10-전대-동의-메일-작성]] — CWC 엘레망 광화문 사무실 일부 공간 계열사 전대(轉貸) 동의 요청 회신 메일 작성 (비즈니스 이메일 대행)
- [[2026-07-11-기억-요약-wiki-경로-확인]] — 기억 요약 요청·wiki 경로/WIKI_DIR 확인. memory provider 미연결 재확인 + config.ts `wikiDir` 경로 불일치(llm-wiki vs john-wiki) 발견
- [[2026-07-11-기억-저장내용-조회가능여부]] — "기억에 저장된 걸 찾을 수 있어?" 1왕복. memory provider 미연결 세 번째 재확인, 노출 tool은 위키 아닌 작업폴더(`search_files`/`list_directory`) 스코프
- [[2026-07-11-desktop-퀵채팅-설치-스크립트]] — desktop 퀵 채팅(Electron) launchd 설치 스크립트, 로컬 모델 결과물 실행불가 → 고급 모델 재작성, git 도구·재시작 스크립트

## Entities

- [[lampas-harness]] — Claude Agent SDK 기반 웹 하네스 (맥미니 데몬, 큐, 채팅 UI)
- [[lampas-studio]] — Lampas AI 이미지 생성 스튜디오 제품 (lampas-api + lampas-web-sdk, sdk.lampas.io)
- [[toktalk]] — TokTalk AI 캐릭터/보이스 챗 제품 (talk-* 7앱 모노레포, toktalk.ai)
- [[lampas]] — 하네스 에이전트의 이름(람파스/Lampas)
- [[john-wiki]] — progdesigner의 공통 장기기억 저장소·개인 위키 (이 저장소)
- [[progdesigner]] — 개발자·소스 공급자, 맥미니 운용
- [[cwc-commerce]] — 씨더블유씨커머스 유한회사, 위키/제품군 배경 회사 "CWC" (대표 이용욱/John Lee, 엘레망 광화문 임차)
- [[sylvan-korea]] — CWC 계열사, 공유오피스 계약 2026-09 종료 → 엘레망 광화문 일부 공간 공동 사용 검토
- [[dongwon-building]] — 동원빌딩/동원리소스 서울지점, 엘레망 광화문 사무실 임대인·관리 주체

## Topics

- [[model-selection]] — 하네스 요청별 모델 선택 (기본 claude-opus-4-8, Claude+OpenAI)
- [[harness-queue-vs-chat]] — 채팅(즉시 실행) vs 큐(백그라운드 적재) 두 경로 구분
- [[instagram-reference-integration]] — Instagram 레퍼런스 이미지 통합 (소스 3종·프록시·캐러셀·폐기된 오래된순)
- [[long-term-memory-architecture]] — LLM 장기기억: 저장≠조회, 3계층 저장 전략, 조회 연결 3방법
- [[episode-beat-play-system]] — TokTalk 에피소드 beat 플레이·배경 전환(선전환·sticky·직접연결) 로직
- [[system-prompt-mimicry-misconception]] — 공개 시스템 프롬프트 복제로 모델 흉내내기 통념의 한계 + 출처미상 지침 붙여넣기 보안 주의
- [[harness-background-process-lifecycle]] — 에이전트가 턴 안에서 띄운 배경작업은 턴/세션 종료로 죽는다 (스케줄러·다운로드 관통 한계)
- [[local-llm-on-apple-silicon]] — Apple Silicon 로컬 LLM: 모델크기·양자화·백엔드(Metal/MLX/CPU), Rapid-MLX vs llama.cpp vs Ollama

## Skills

- [[macos-launchd-daemon]] — macOS launchd 데몬화 + 제어 스크립트 (라벨 일치 함정)
- [[sdk-session-persistence]] — SDK 세션 sessionId 사이드카 영속화로 크래시 후 resume 복구
- [[idempotent-message-retry]] — msgId 멱등성 + 자동 재전송으로 network error 소실 방지
- [[work-folder-sandboxing]] — 에이전트를 작업 폴더 안으로 제한 (default 모드 + 경로 가드)
- [[instagram-public-media-fetch]] — 토큰·심사 없이 facebookexternalhit UA로 공개 게시물 이미지 조회
- [[localstorage-ui-preference-persistence]] — 마지막 선택 UI 값을 localStorage에 검증·폴백하며 기억
- [[deploy-sandbox-pnpm-shim]] — 샌드박스 배포 시 corepack pnpm shim으로 ./scripts 스크립트 실행
- [[wiki-memory-provider-integration]] — markdown 위키를 LLM 검색형 장기기억 provider로 연동 (경로 가드 함정 포함)
- [[loading-state-key-mismatch]] — 요청은 되는데 UI 무반응 = 로딩 상태 키 불일치 진단·수정
- [[session-independent-scheduler]] — 능동 예약 메시지는 서버 내장 파일 영속 스케줄러로 (에이전트 타이머 금지, 도착시각 기준 정확도)
- [[detach-long-job-nohup]] — 긴 배경 작업을 `nohup … & disown`으로 세션과 분리해 턴 넘어가도 생존
- [[local-llm-rapidmlx-install]] — Rapid-MLX로 Apple Silicon에 로컬 LLM 설치·launchd 상주·하네스 모델 선택기 통합
- [[business-email-consent-first]] — 상대가 서류 요구만 반복할 때 원칙적 동의 여부를 먼저 확정 요청하는 정중한 회신 메일 작성
