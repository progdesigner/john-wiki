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

## [2026-07-12] ingest | desktop 퀵 채팅 설치 스크립트 세션 (2026-07-11)
- 소스: raw/conversations/2026-07-11-desktop-퀵채팅-설치-스크립트.md (원본 e5a3f591-...md 복사)
- 세션: [[2026-07-11-desktop-퀵채팅-설치-스크립트]] (5개 스레드: 개발상태 점검·git commit 2회·desktop 설치 기능·restart 스크립트 차이)
- 핵심: desktop 퀵 채팅(Electron) launchd 상주 기능 신설(Option+Space 소환). 로컬 모델(qwen)이 만든 install-desktop.sh가 실행 불가(root /Library/LaunchAgents sudo 없이 쓰기·Electron을 `node main.js`로 실행·launchctl bootout 도메인 누락/순서 뒤바뀜) → 사용자가 "고급 모델로 다시 실행"으로 에스컬레이션 → 상위 모델이 기존 launchd 패턴대로 재작성·설치 검증(exit 0, 200 응답).
- 스킬 갱신: [[macos-launchd-daemon]] — GUI/Electron 앱 launchd 상주 함정 섹션 추가(Electron 바이너리·LimitLoadToSessionType Aqua·KeepAlive SuccessfulExit:false·kickstart 명시·상태출력 오표시), 사용자 vs root LaunchAgents·bootout 도메인 필수 함정 보강.
- 엔티티 갱신: [[lampas-harness]] — 2026-07-11 추가 기능(desktop 클라이언트·git 도구·restart 스크립트 2종·Google 401)·커밋 이력(7694af0→6f3704f, 6f3704f→9132930) 추가.
- 토픽 갱신: [[model-selection]] — 로컬→고급 모델 수동 에스컬레이션 패턴·불투명 라우팅 UX 관찰 추가.
- index.md 갱신 (세션1 추가).
- 특이사항: 스킬은 신설이 아닌 기존 [[macos-launchd-daemon]] 정제(GUI 앱 케이스 반복 사용으로 확장). Google Models 401 오류 미해결로 이월.

## [2026-07-12] ingest | "하네스가 뭐야" 질문 세션 (2026-07-12 quick)
- 소스: raw/conversations/2026-07-12-하네스-뜻-질문.md (원본 quick-e5374693-...md 복사)
- 세션: [[2026-07-12-하네스-뜻-질문]] (1왕복 초단타 quick 채팅)
- 핵심: "하네스가 뭐야"에 일반 사전식 답변 4종(DevOps Harness/승마 착구/항공 안전장비/전기 배선)만 나오고 사용자 자신의 [[lampas-harness]] 맥락은 전혀 인식 못 함. → memory/컨텍스트 미주입 정황.
- 토픽 갱신: [[long-term-memory-architecture]] — "구현 진행 상태" 절에 방법 B(AI_CONTEXT.md 주입)가 quick-chat 경로엔 아직 안 닿음(2026-07-12 관찰) 데이터포인트 추가, 절 제목 2026-07-11→2026-07-11~12로.
- index.md 갱신 (세션1 추가).
- 특이사항: 콘텐츠 가치 낮은 quick 1왕복. 신규 엔티티/스킬 없음, AI_CONTEXT.md 변경 없음(핵심 사실 불변). 값은 "회수 배선이 quick 채팅 경로까지 아직 안 닿았다"는 단일 관찰. 파일명 quick- 접두사는 [[2026-07-11-desktop-퀵채팅-설치-스크립트]]의 데스크톱 퀵 클라이언트 경로로 추정.

## [2026-07-13] lint | 위키 전체 건강 점검
- **구조 검증**: index.md ↔ 실제 파일 목록 일치 확인(세션11·엔티티9·스킬13). 링크 그래프 분석 결과 **고아 페이지 0·중복 basename 0**. `[[raw/...]]`는 실존 원본을 가리키는 경로형 링크(정상), `[[위키링크]]`는 john-wiki.md의 문법 예시 텍스트(정상) — 둘 다 깨진 링크 아님.
- **모순 수정 1 — AI_CONTEXT.md 낡음**: [[john-wiki]] 페이지가 `AI_CONTEXT.md`를 "제안됨/아직 없음(제안 상태)"으로 기술했으나 실제로는 2026-07-12 생성됨(커밋 0c05aae, 상시 주입). 해당 두 문장을 "실현됨(방법 B 구현물)"으로 갱신.
- **모순 수정 2 — AI_CONTEXT.md 근거 초과**: 확정된 결정 항목이 "원격 접속=Tailscale **Funnel**, 고정 IP·포트포워딩·**Cloudflare Tunnel 사용 안 함**"으로 단정했으나, 위키/raw의 유일 근거([[2026-07-06-lampas-harness-구축]])는 "Tailscale **또는** Cloudflare Tunnel, Tailscale 권장, **미설치**, 포트포워딩 비권장"뿐. "Funnel"·"고정 IP"·Cloudflare 하드 배제는 무근거 → "공개 노출 회피·Tailscale 사설 VPN 권장·미설치(재확인 필요)"로 완화. [[lampas-harness]] 엔티티에도 원격 접속 절 추가(근거 세션 명시).
- **크로스레퍼런스 보강**: [[lampas-harness]] 실행 정보에 API 포트 `3787` 추가(근거: [[2026-07-11-desktop-퀵채팅-설치-스크립트]]) — 기존엔 웹 8787만 기재.
- **신규 topic**: [[use-area-personas]] 생성 — "사용 영역 페르소나"가 10개 파일에서 언급되나 전용 페이지 부재였음. 6영역 프리셋·presets.json 저장·"일반" 정책 비어있음→채움 변천(2026-07-08 vs 07-09 모순 명시)을 통합. 인바운드 링크 4개 보강([[lampas-harness]]·[[system-prompt-mimicry-misconception]]·두 페르소나 세션), index.md Topics에 등록.
- **AI_CONTEXT.md 재증류**: updated 2026-07-13, 26줄(<40 규칙 준수). 위 두 모순 반영 외 핵심 사실은 불변이라 구조 유지.
- 결과: 깨진 링크 0·고아 0. 신규 엔티티/세션/스킬 없음.

## [2026-07-13] ingest | 람파스 누적 운영 기억 이관 (source: memory-export-2026-07-13.md)
- 소스: raw/conversations/2026-07-13-람파스-누적운영기억-이관.md (원본 memory-export-2026-07-13.md 복사) — lampas-harness auto-memory 13개 정제분
- 세션: [[2026-07-13-람파스-누적운영기억-이관]] (13개 기억 → 위키 매핑표 포함)
- 엔티티 신설: [[rapid-mlx]] (로컬 LLM 서버·launchd io.lampas.rapidmlx KeepAlive), [[naver-blog-mcp]] (Playwright 발행 MCP·순수 텍스트만·SRT ~24h)
- 토픽 신설: [[harness-mcp-bridge]] (하네스가 .cursor/mcp.json 외부 MCP를 Claude 세션에 노출 — 오해 정정 + cwd/PATH/재시작 이식 함정)
- 스킬 신설: [[env-empty-var-shadows-dotenv]] (빈 env가 .env 값 가림 진단), [[self-hosted-agent-server-ops]] (stray-port·restart-kills-own-turn·pkill-prod 3함정 통합), [[naver-blog-mcp-posting]] (본문 텍스트·SRT 갱신·서버 버그 3종)
- 스킬 정제: [[detach-long-job-nohup]] (run_in_background도 세션 teardown에 죽음·"No completion record" 오인), [[local-llm-rapidmlx-install]] (launchd 관리 후 인자 변경은 plist 재배포·말미 stray </content> 태그 제거), [[macos-launchd-daemon]] (bootout 직후 bootstrap 오류 5 → 대기 후 재시도)
- 엔티티 갱신: [[lampas-harness]] (Google 401 근본원인 규명=env-empty-shadow ⚠️→✅, MCP 브리지·naver-blog·rapid-mlx·스크립트 컨벤션·서버 함정 절 추가), [[progdesigner]] (확정 선호: 한국어·비자명만 저장·tools/scripts 분리·람파스 이름), [[lampas]] (이름 사용 규칙 절)
- 토픽 갱신: [[model-selection]] (프로바이더 모델 미표시 = env-empty-shadow 진단 절)
- AI_CONTEXT.md 갱신: 한국어·저장정책·스크립트 컨벤션(사용자 줄), 람파스 이름 결정, 하네스 MCP 브리지·rapid-mlx 링크. 27줄(<40 준수).
- index.md 갱신 (세션1·엔티티2·토픽1·스킬3 추가).
- 특이사항: 소스가 대화 트랜스크립트가 아닌 auto-memory 내보내기(정제된 기억 목록) — "비자명한 것만 저장" 정책의 산물. Google Models 401은 미해결 이월→개연적 해소로 상태 전환(401 인증증상과 빈-목록 증상 동일성은 미단정). 실제 코드 수정 여부는 소스 기술만으로 판단, 위키엔 소스가 말한 대로 기록.

## [2026-07-13] ingest | 대화 보관 시 자동 장기기억 저장 (source: 674dc1b9-9c5b-4ef8-9d0a-4f5f689828be.md)
- 소스: raw/conversations/2026-07-13-보관시-자동-기억저장.md (원본 674dc1b9-...md 복사)
- 세션: [[2026-07-13-보관시-자동-기억저장]] (짧은 구현 세션, 2026-07-13 14:28~14:33, 트랜스크립트는 어시스턴트 진행 요약만)
- 내용: `[[lampas-harness]]` 보관(🗄) 동작에 `memory-ingest` 자동 큐잉 연결. `src/server.ts` `POST /api/sessions/archive`가 파일을 `chats/archive/`로 이동하며 이동 후 경로로 ingest 큐잉, `rememberedAt`(아카이브 동반 이동) vs 트랜스크립트 변경시각 멱등 게이트로 중복 방지. 프런트(툴팁·안내문구·dist 빌드), 응답 `remembered` 플래그.
- 엔티티 갱신: [[lampas-harness]](2026-07-13 추가 기능 절 신설 — 보관→자동 ingest·멱등 게이트·이동후 경로 함정), [[john-wiki]](저장 진입점 3개로 확장 기록)
- 토픽 갱신: [[long-term-memory-architecture]](저장 측 자동화 절 신설 — 진입점 3개, 병목은 여전히 방법 C 조회 tool)
- AI_CONTEXT.md 갱신: [[john-wiki]] 줄에 저장 진입점 3개(보관 자동 포함) 반영. 여전히 <40줄.
- index.md 갱신 (세션1 추가)
- 특이사항: 신규 엔티티/토픽/스킬 없음(기존 페이지에 반영). 스킬 미추출 — 절차가 특정 기능 구현이라 세션·엔티티에 함정(이동후 경로·rememberedAt 게이트) 기록으로 충분. 배포는 서버 재시작 필요하나 restart-kills-own-turn 때문에 어시스턴트가 직접 미수행 → 실제 활성화 여부 미확정.

## [2026-07-14] ingest | 모델별 시간당 코딩 비용 (source: 932ed96a-6283-4f90-9c5e-6541a102458d.md)
- 소스: raw/conversations/2026-07-13-모델별-시간당-코딩비용.md (원본 932ed96a-...md 복사 — 원본은 logs/chats/archive/로 이동돼 있어 archive에서 복사)
- 세션: [[2026-07-13-모델별-시간당-코딩비용]] (1왕복 quick 질의응답, 2026-07-13 14:29)
- 내용: "클로드로 1시간 코딩 시 모델별 비용?" → API 종량제 추정. Opus 4.8 $10~25, Sonnet 5 $5~15(프로모션 $3~10), Haiku $2~5, Fable 5 $20~50. 핵심 산정 논리: Claude Code는 프롬프트 캐싱을 강하게 써서 비용 대부분이 캐시 읽기(입력가×0.1)·출력에서 발생. 종량제 vs Pro/Max 구독 트레이드오프, `usage` 필드 집계로 실측 가능 제안.
- 토픽 신설: [[claude-model-pricing]] (모델 API 단가표·시간당 비용 추정·캐싱 중심 산정 로직·종량제 vs 구독 — reference성, 2026-07-13 시점 단가 명시)
- 토픽 갱신: [[model-selection]] (권장 절에 [[claude-model-pricing]] 링크·Opus 4.8 시간당 ~$10~25 추가)
- index.md 갱신 (세션1·토픽1 추가)
- 특이사항: 신규 엔티티/스킬 없음. AI_CONTEXT.md 변경 없음(일회성 정보성 질의, 핵심 사실 불변). 단가·프로모션 종료일은 어시스턴트 답변 시점 값이라 낡을 수 있음 → 세션·토픽에 "2026-07-13 기준" 명시. 실측 집계는 제안만 하고 미실행.

## [2026-07-14] ingest | 블로그 AI기억글 해시태그 추천 (source: 8f549187-c6a3-4e80-984c-c308c581aee9.md)
- 소스: raw/conversations/2026-07-13-블로그-ai기억글-해시태그.md (원본 archive/8f549187-...md 복사). 한 트랜스크립트 두 주제.
- Part1(기억 이관 트리거)는 이미 [[2026-07-13-람파스-누적운영기억-이관]]에 정리됨 — 여기선 하네스 측 실행 방식(작업폴더 밖·위키 못 읽음→memory-ingest 잡, queue/*.processing→done/failed 폴링)만 세션에 보강.
- Part2(고유): 네이버 블로그 SEO 태그 30개 추천. 대상 글이 곧 이 위키/[[lampas]] 장기기억 아키텍처의 **공개 연재글**(블로그 `study-ai-what`, 글 224345496699).
- 세션 신설: [[2026-07-13-블로그-ai기억글-해시태그]]
- 스킬 신설: [[naver-blog-tag-seo]] (본문 근거 필수·대표+핵심+롱테일 배분·무관 인기태그 저품질 위험·URL만이면 본문 요청)
- 엔티티 갱신: [[progdesigner]](공개 채널 절 신설 — 네이버 블로그 study-ai-what 연재), [[naver-blog-mcp]](발행 전용·글 읽기 기능 없음 재확인, 대상 블로그 config.NAVER_BLOG_ID=study-ai-what)
- 토픽 갱신: [[long-term-memory-architecture]](블로그의 recall 3층 구현 주장 vs 2026-07-11~12 "방법 C 미완" 관찰 **모순 병기** — 로컬 LLM search/read/list_memory 도구·corpus 요약 스트리밍 API·5:30 크론·주간 lint는 사용자 공개 서술, 미검증)
- AI_CONTEXT.md 갱신: [[progdesigner]] 줄에 블로그 연재 맥락 추가(태그 요청 반복). <40줄 유지.
- index.md 갱신 (세션1·스킬1 추가). 참고: claude-model-pricing 세션/토픽은 병행 ingest가 이미 반영.
- 특이사항: 블로그 서술을 근거 있는 사실이 아니라 **사용자 저작 주장**으로 취급 — recall 구현 여부는 채팅 실측 전까지 미확정으로 명시.

## [2026-07-14] ingest | 은행 환불 거래처 매칭 (source: quick-5bc3f296-7b29-4d41-ba98-77eabe76180a.md)
- 소스: raw/conversations/2026-07-14-은행환불-거래처매칭.md (원본 archive/quick-5bc3f296-...md 복사)
- quick 채팅. 코딩 아님 — [[lampas]]를 업무 비서로 사용. 은행 거래내역·프랑스 와인 프로포마 인보이스 이미지 해석 + 거래처 영어 회신 초안.
- 핵심: "지급 후 환불 요청" 사실 확정 → PRINC CR/T/W FR ADV = 프랑스 거래처 선지급 환입(90,377.76)으로 해석. 단 인보이스 합계 83,429.16(Mestrezat 30,255.96 + Mähler-Besse 53,173.20)과 6,948.60 차액 미해소 → 확신도 하향, 통화 단위·거래처명 되묻기로 마감.
- 세션 신설: [[2026-07-14-은행환불-거래처매칭]]
- 엔티티 신설: [[cwc-lab-singapore]] (CWC LAB PTE. LTD 싱가포르, 와인 수입 매수 주체)
- 엔티티 갱신: [[cwc-commerce]] (계열 구조 LAB/COMMERCE 병기·보르도 와인 수입 사업 추가)
- 토픽 신설: [[harness-as-business-assistant]] (전대 메일 세션과 함께 코딩 외 사용 패턴 묶음)
- 스킬 신설: [[bank-refund-invoice-reconciliation]]
- index.md 갱신 (세션1·엔티티1·토픽1·스킬1). AI_CONTEXT.md 변경 없음(업무 맥락은 엔티티/토픽으로 충분, 40줄 유지). 
- 특이사항: 은행 코드·계좌번호 해석은 전부 추정(비표준 표기)·미검증. SRKK/Scott 별건은 맥락 미상으로 세션에 추적 표시만.

## [2026-07-15] ingest | 올리브유 마케팅 포지셔닝 상담 (source: 89b46b29-6343-43f1-ae48-69ef2f274272.md)
- 소스: raw/conversations/2026-07-15-올리브유-마케팅-포지셔닝.md (원본 89b46b29-...md 복사)
- 세션: [[2026-07-15-올리브유-마케팅-포지셔닝]] (3왕복 quick, 2026-07-15 01:14~01:16). 코딩 아님 — [[lampas]]를 마케팅 컨설턴트로 사용.
- 내용: (스레드1) 사용 가능 스킬 목록 문의 → 카테고리별 정리, 마케팅·Remotion 번들이 커밋 `ef36c5f`(프로젝트 스킬 시스템)에서 추가됐다고 명시. (스레드2~3) 올리브유 병 사진 첨부 → 프리미엄/기프트 포지셔닝 판독(어두운 유리·엠보싱·Cosecha propia·저온압착·DO·마요르카), 스페셜티/B2B/D2C 채널 권고, 포지셔닝·ICP 선행 강조 후 product-marketing 스킬 착수 제안. 트랜스크립트는 입력 요청 직전 종료(스킬 미실행).
- 엔티티 갱신: [[lampas-harness]](2026-07-15 절 신설 — 프로젝트 스킬 시스템+마케팅/Remotion/개발운영 스킬 번들 목록, 커밋 ef36c5f), [[progdesigner]](세션 크로스링크)
- 토픽 갱신: [[harness-as-business-assistant]](마케팅 컨설팅 사례 추가·상담/기획 특징·altitude 우선 방법론)
- AI_CONTEXT.md 갱신: 하네스 스킬 번들 보유, 코딩 외 업무 비서 사용 패턴 한 줄 추가. 28줄(<40 준수).
- index.md 갱신 (세션1 추가).
- 특이사항: 신규 엔티티/토픽/스킬 없음. 마케팅 방법론이 얇고 세션이 스킬 실행 전 끊겨 별도 스킬 미추출 — 방법론은 토픽에 기록으로 충분. 스킬 카탈로그는 어시스턴트 답변 시점(2026-07-15) 목록이라 이후 변동 가능.

## [2026-07-15] ingest | 과금 모드 토글·구독 컨텍스트 표시 (source: 4d8a9cd6-2a96-4692-93b5-67936c472cf9.md)
- 소스: raw/conversations/2026-07-15-과금모드-토글-컨텍스트표시.md (원본 4d8a9cd6-...md 복사)
- 세션: [[2026-07-15-과금모드-토글-컨텍스트표시]] (약 6왕복 코딩, 2026-07-15 01:17~01:40). [[lampas-harness]] 웹 채팅에 Claude 과금 모드(API vs 구독 OAuth) 토글 + 구독 모드 컨텍스트 잔여 표시 구현.
- 핵심 사실: 하네스 Claude 실행은 **기본 Claude Code 구독(OAuth, CLAUDE_CODE_OAUTH_TOKEN) 과금** — query()가 apiKey 미전달, SDK Options에 apiKey 없음(env가 통째 교체). init 메시지 apiKeySource='oauth'로 판별. stream.getContextUsage()/usage_EXPERIMENTAL로 컨텍스트·5h/7d 한도 조회.
- 구현: TurnOpts.apiBilling + claudeAuthEnv() 헬퍼(env에서 원치 않는 자격증명 제거), apiKeySource/getContextUsage 캡처→UsageTotals.billingMode/contextWindow, /api/models에 anthropicApiKeyConfigured, 프론트 "API 사용" 체크박스(Claude 모델 한정)·구독 모드 컨텍스트 잔여% 표시.
- 엔티티 갱신: [[lampas-harness]] (2026-07-15 과금모드 토글 절 신설, 인증 줄 정정 — 기존 "ANTHROPIC_API_KEY 직접 키(구독 아님)"와 **모순** 명시)
- 토픽 갱신: [[claude-model-pricing]](하네스 기본=구독 과금 명시), [[model-selection]](과금 모드 토글 절)
- 스킬 신설: [[sdk-claude-code-vs-api-billing]]
- AI_CONTEXT.md 갱신: 확정된 결정에 하네스 기본 구독 과금·토글 한 줄 추가 (30줄, <40 준수)
- index.md 갱신 (세션1·스킬1)
- 특이사항: .env의 ANTHROPIC_API_KEY 주석 상태가 세션 내에서 진술 엇갈림(01:17 주석됨 ↔ 01:40 해제됨) → 재확인 필요로 세션/엔티티에 기록. "Stream closed" 쓰기 도구 끊김 재발(새로고침으로 복구). 서버 재시작은 사용자 몫으로 남김.
