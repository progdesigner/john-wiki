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
- [[2026-07-12-하네스-뜻-질문]] — "하네스가 뭐야" quick 채팅 1왕복. 일반 사전식 답변(lampas 맥락 인식 못함) → quick-chat 경로에 AI_CONTEXT.md/장기기억 미주입 정황
- [[2026-07-13-람파스-누적운영기억-이관]] — 람파스 auto-memory 13개(운영 함정·피드백)를 위키로 이관. Google 401 근본원인(env-empty-shadow) 규명, MCP 브리지·naver-blog-mcp·rapid-mlx launchd 정리
- [[2026-07-13-보관시-자동-기억저장]] — 대화 보관(🗄) 시 memory-ingest 자동 큐잉 구현. rememberedAt 멱등 게이트·이동후 경로 함정. 위키 저장 진입점 3개로 확장
- [[2026-07-13-모델별-시간당-코딩비용]] — Claude Code 1시간 코딩 시 모델별 API 종량제 비용 추정 (캐싱이 비용 좌우, 구독 vs 종량제) 1왕복
- [[2026-07-13-블로그-ai기억글-해시태그]] — 네이버 블로그 SEO 태그 30개 추천. 대상 글이 곧 이 위키 아키텍처 공개 연재(블로그 study-ai-what). 블로그의 recall 3층 주장 vs 기존 "방법 C 미완" 관찰 모순 기록
- [[2026-07-14-은행환불-거래처매칭]] — 은행 거래내역·프랑스 와인 인보이스 이미지 해석, 환불액 vs 인보이스 합계 대조(6,948.60 차액 미해소), 거래처 영어 회신 초안. 하네스를 업무 비서로 사용
- [[2026-07-15-올리브유-마케팅-포지셔닝]] — 사용 가능 스킬 목록(마케팅·Remotion 번들, 커밋 ef36c5f) + 올리브유 사진 프리미엄 포지셔닝 상담(ICP 선행 → product-marketing 착수 제안)
- [[2026-07-15-과금모드-토글-컨텍스트표시]] — 하네스에 Claude 과금 모드 토글(API vs 구독 OAuth) + 구독 모드 컨텍스트 잔여 표시 구현. 기본 과금이 구독(OAuth)임을 코드로 확인(기존 기록과 모순)
- [[2026-07-15-아우보카사-공동구매-단가설계]] — 인플루언서 공동구매 단가표 사진 1장 해석(1왕복 quick). 할인 티어 설계 의도·인플루언서 수익 10%+·시뮬레이션(실질 병당 판매가) 풀이
- [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] — Works 12개 저장소 일괄 최신화 + lampas-system(=lampas-studio) 로컬 미커밋 18파일을 원격 9커밋(리팩터) 위로 충돌 없이 rebase·push
- [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]] — cwc-system에 elevino DB를 그대로 재사용하는 멤버십 검색+크레딧 지급/차감 관리자 기능 설계 완료, 구현 착수 직전 하네스 권한 채널 장애로 중단(실제 구현 미완)
- [[2026-07-15-데스크톱-file메뉴-new-window]] — lampas-harness desktop(Electron) File 메뉴에 New Window(Cmd+Shift+N) 추가 + 쌓여있던 변경사항 3커밋으로 push

## Entities

- [[lampas-harness]] — Claude Agent SDK 기반 웹 하네스 (맥미니 데몬, 큐, 채팅 UI)
- [[lampas-studio]] — Lampas AI 이미지 생성 스튜디오 제품 (lampas-api + lampas-web-sdk, sdk.lampas.io)
- [[toktalk]] — TokTalk AI 캐릭터/보이스 챗 제품 (talk-* 7앱 모노레포, toktalk.ai)
- [[lampas]] — 하네스 에이전트의 이름(람파스/Lampas)
- [[john-wiki]] — progdesigner의 공통 장기기억 저장소·개인 위키 (이 저장소)
- [[progdesigner]] — 개발자·소스 공급자, 맥미니 운용
- [[cwc-commerce]] — 씨더블유씨커머스 유한회사, 위키/제품군 배경 회사 "CWC" (대표 이용욱/John Lee, 엘레망 광화문 임차)
- [[sylvan-korea]] — CWC 계열사, 공유오피스 계약 2026-09 종료 → 엘레망 광화문 일부 공간 공동 사용 검토
- [[cwc-lab-singapore]] — CWC LAB PTE. LTD, 싱가포르 계열법인. 프랑스 보르도 와인 수입 매수 주체(EUR)
- [[dongwon-building]] — 동원빌딩/동원리소스 서울지점, 엘레망 광화문 사무실 임대인·관리 주체
- [[rapid-mlx]] — Apple Silicon 로컬 LLM 서버(vllm-mlx), 하네스 "Local" 프로바이더. launchd `io.lampas.rapidmlx` KeepAlive 상주
- [[naver-blog-mcp]] — 네이버 블로그 자동 발행 MCP(Playwright). 본문 순수 텍스트만·SRT 쿠키 ~24h 만료
- [[cwc-system]] — CWC ERP 분석/관리 pnpm 모노레포 (cwc-api NestJS+Prisma, cwc-web-admin React+Vite)
- [[elevino-system]] — CWC 계열 서비스 저장소 (elevino-api NestJS+TypeORM, elevino-admin Next.js). 원장 방식 크레딧 시스템 보유

## Topics

- [[model-selection]] — 하네스 요청별 모델 선택 (기본 claude-opus-4-8, Claude+OpenAI)
- [[claude-model-pricing]] — Claude 모델 API 단가·Claude Code 시간당 코딩 비용 추정 (캐싱 중심 산정, 종량제 vs 구독)
- [[harness-queue-vs-chat]] — 채팅(즉시 실행) vs 큐(백그라운드 적재) 두 경로 구분
- [[instagram-reference-integration]] — Instagram 레퍼런스 이미지 통합 (소스 3종·프록시·캐러셀·폐기된 오래된순)
- [[long-term-memory-architecture]] — LLM 장기기억: 저장≠조회, 3계층 저장 전략, 조회 연결 3방법
- [[episode-beat-play-system]] — TokTalk 에피소드 beat 플레이·배경 전환(선전환·sticky·직접연결) 로직
- [[system-prompt-mimicry-misconception]] — 공개 시스템 프롬프트 복제로 모델 흉내내기 통념의 한계 + 출처미상 지침 붙여넣기 보안 주의
- [[harness-background-process-lifecycle]] — 에이전트가 턴 안에서 띄운 배경작업은 턴/세션 종료로 죽는다 (스케줄러·다운로드 관통 한계)
- [[local-llm-on-apple-silicon]] — Apple Silicon 로컬 LLM: 모델크기·양자화·백엔드(Metal/MLX/CPU), Rapid-MLX vs llama.cpp vs Ollama
- [[use-area-personas]] — 하네스 사용 영역별 전문가 페르소나(6영역) + "일반" 정책 비어있음→채움 변천
- [[harness-mcp-bridge]] — 하네스가 `.cursor/mcp.json` 외부 MCP를 Claude 세션에 노출 (mcpBridge, cwd 우회·PATH·재시작 반영)
- [[harness-as-business-assistant]] — 하네스를 코딩 외 업무 비서로 사용 (이미지 첨부 해석·인보이스 대조·대외 이메일 초안)
- [[works-project-portfolio]] — `~/Works` 하위 git 저장소 12개 목록(2026-07-15 시점) — 다수 미문서화

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
- [[env-empty-var-shadows-dotenv]] — 새 API 키가 기능/목록에 안 뜰 때: 셸의 빈 env가 `.env` 값을 가리는지 진단·수정
- [[self-hosted-agent-server-ops]] — 에이전트 턴을 호스팅하는 서버 재시작·종료·디버그 안전 규칙(stray-port·restart-kills-own-turn·pkill-prod)
- [[naver-blog-mcp-posting]] — naver-blog-mcp 발행: 순수 텍스트 본문·SRT 세션 갱신(headed 캡차)·서버 버그 대응
- [[naver-blog-tag-seo]] — 네이버 블로그 SEO 태그 30개 생성(본문 근거 필수·대표+롱테일 배분·무관 인기태그 저품질 위험)
- [[bank-refund-invoice-reconciliation]] — 애매한 은행 환입/입금을 인보이스와 대조·검증하고 거래처 회신 잡기(코드 단정 금지·차액 명시·통화 확인)
- [[sdk-claude-code-vs-api-billing]] — Claude Agent SDK 실행이 구독(OAuth) 과금인지 API 종량인지 판별하고 options.env로 전환·한도 조회
- [[multi-repo-safe-bulk-update]] — 다중 git 저장소 일괄 업데이트 시 미커밋 변경 저장소만 골라 보류
- [[rebase-local-feature-onto-refactored-remote]] — 원격이 구조를 리팩터한 코드 위에 로컬 미커밋 기능을 rebase로 이식
- [[credit-ledger-balance-pattern]] — 원장(ledger) 방식 크레딧 잔액 시스템 이식 시 확인할 것들(네이밍 역전·동시성·검색 대상 불일치)
