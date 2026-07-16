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

## [2026-07-16] ingest | 아우보카사 인플루언서 공동구매 단가표 해석 (source: quick-39170295-487b-43dc-a4f1-6a713aa92dd1.md)
- 소스: raw/conversations/2026-07-15-아우보카사-공동구매-단가설계.md (원본 archive/quick-39170295-...md 복사)
- 세션: [[2026-07-15-아우보카사-공동구매-단가설계]] (1왕복 quick, 2026-07-15 11:01). 코딩 아님 — [[lampas]]를 가격/마케팅 컨설턴트로 사용.
- 내용: "아우보카사"(정가 59,000원) 인플루언서 공동구매 단가 초안 표 사진 1장을 첨부, "이해하기 쉽게 정리해줘" 요청. 어시스턴트가 표(1/2/3병 구성별 할인율·병당가·결제액)와 설계 의도(기존 최저가 39,000원 대비 우위·인플루언서 수익 10%+)를 해독, 60병 판매 가정 시뮬레이션(총매출 1,864,400원·인플 몫 11%·실질 병당 판매가 약 27,655원)으로 마진 확인. 인플루언서 지급 방식은 미결로 남김.
- 토픽 갱신: [[harness-as-business-assistant]] (사례 추가 — 표 해석·시뮬레이션 계산, 표 자체는 사용자 초안)
- index.md 갱신 (세션1 추가)
- 특이사항: 신규 엔티티/스킬 없음. "아우보카사" 제품 정체(카테고리·[[cwc-commerce]] 연결 여부)는 소스에 미상이라 임의 연결하지 않음. 방법론이 1턴 해석으로 얇아 스킬 미추출(2026-07-15 올리브유 세션과 동일 판단 기준). AI_CONTEXT.md는 기존 "코딩 외 업무 비서" 일반 패턴 줄이 이미 이 사례를 포괄해 변경 없음.

## [2026-07-16] ingest | Works 전체 프로젝트 일괄 최신화·lampas-system 리베이스 (source: 2d5c9979-48a0-4df2-b244-9c646c8fbe7e.md)
- 소스: raw/conversations/2026-07-15-works-프로젝트-최신화-lampas-system-리베이스.md (원본 archive/2d5c9979-...md 복사)
- 세션: [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] (2026-07-15 06:48~07:04). "Works 전체 최신화" 요청 → 12개 저장소 점검·pull → `lampas-system`(=[[lampas-studio]]) 한 곳만 로컬 미커밋 18파일 vs 원격 9커밋 충돌 위험으로 보류 → 사용자 지시로 rebase 정리 후 push.
- 토픽 신설: [[works-project-portfolio]] (`~/Works` 하위 git 저장소 12개 최초 전수 열거 — 다수는 이름·변경 규모만 확인, 내용 미조사)
- 스킬 신설: [[multi-repo-safe-bulk-update]] (미커밋 변경 저장소 선별 보류), [[rebase-local-feature-onto-refactored-remote]] (원격 구조 리팩터 위에 로컬 기능 rebase 이식)
- 엔티티 갱신: [[lampas-studio]] (로컬 저장소명 `lampas-system` 확인, DB=PostgreSQL, 2026-07-15 rebase로 추가된 Instagram `order` 재도입·RegeneratePrompt 제거 커밋, pnpm install 저장소 전체 실패 기존 이슈, git user 설정 신설), [[lampas-harness]] (기존 "lampas-system 인접 저장소" 메모를 lampas-studio와 동일 저장소로 정정·해소 기록)
- 토픽 갱신: [[instagram-reference-integration]] (**모순 병기** — 2026-07-08 폐기했던 오래된순 정렬이 2026-07-15 `order=newest|oldest`로 재도입됨, 범위가 연결계정 한정인지 불명확)
- AI_CONTEXT.md 갱신: [[lampas-studio]] 줄에 로컬 저장소명·pnpm 이슈 추가, Works 12개 저장소 포트폴리오 한 줄 신설. 30줄(<40 준수).
- index.md 갱신 (세션1·토픽1·스킬2 추가)
- 특이사항: 미문서화 저장소 7개(elevino-system/-operator, cwc-system, goraesa-system, bokziri-system, ai-labs-notes, dark-system, arca8-system)는 이름·파일 변경 규모만 확인됐고 성격은 조사되지 않아 임의로 엔티티를 만들지 않음(추측 금지). pnpm install 실패는 이 세션이 만든 문제가 아니라 origin/main에도 있던 기존 이슈로 명시.

## [2026-07-16] ingest | cwc-system 멤버십 크레딧 지급/차감 이식 설계 (source: 4a072a02-1fe6-491d-9d69-52c8b2a69771.md)
- 원본 보관: `raw/conversations/2026-07-15-cwc-system-멤버십-크레딧-이식-설계.md`
- 세션 신설: [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]] — elevino DB를 그대로 재사용해 cwc-system에
  멤버십 검색+크레딧 지급/차감 관리자 기능을 이식하는 설계(DB/API/UI 3단 계획) 완료. 구현 착수 직전
  하네스 권한 채널 장애(`Tool permission request failed: Stream closed`)로 중단 — 실제 구현·커밋·푸시는
  이루어지지 않음.
- 엔티티 신설: [[cwc-system]] (CWC ERP 분석/관리 모노레포, cwc-api NestJS+Prisma6 + cwc-web-admin
  React19+Vite6), [[elevino-system]] (CWC 계열 서비스, elevino-api NestJS+TypeORM + elevino-admin
  Next.js14, 원장 방식 크레딧 시스템 보유). [[works-project-portfolio]]에서 "미문서화"였던 두 저장소가
  최초로 내용 조사됨.
- 스킬 신설: [[credit-ledger-balance-pattern]] — 잔액 컬럼 없이 원장(ledger) 방식으로 크레딧을 관리하는
  시스템을 이식할 때 확인할 것들(네이밍 역전 함정, 동시성 미보호, 검색 대상 실제 테이블 불일치 등).
- 토픽 갱신: [[works-project-portfolio]] (cwc-system·elevino-system을 미문서화 목록에서 제외, 엔티티 링크 추가)
- AI_CONTEXT.md 갱신: 진행 중 프로젝트에 cwc-system 멤버십 크레딧 이식(설계 완료·구현 미완, 재개 필요) 2줄 추가. 33줄(<40 준수).
- index.md 갱신 (세션1·엔티티2·스킬1 추가)
- 특이사항: 이 세션은 실제 구현이 완료되지 않은 채 끝남 — 다음 세션에서 하네스 권한 상태를 먼저 확인하고
  이어서 구현해야 함. 부수적으로 cwc-system 프로덕션 env 파일에 DB 비밀번호·JWT 시크릿이 평문 커밋되어
  있는 보안 이슈가 발견됨(미조치, cwc-system 엔티티 페이지에 기록).

## [2026-07-16] ingest | 데스크톱 File 메뉴 New Window + 3커밋 push (source: 4d3cb780-11be-4c55-8f53-94ea35644849.md)
- 원본 세션(대화 발생 2026-07-15 06:40~06:45 UTC)을 raw/conversations/2026-07-15-데스크톱-file메뉴-new-window.md로 보관.
- wiki/sessions/2026-07-15-데스크톱-file메뉴-new-window.md 신규 작성.
- wiki/entities/lampas-harness.md 갱신: "추가 기능 (2026-07-15 세션 — desktop File 메뉴 New Window)" 섹션 신설
  (apps/desktop/main.js Cmd+Shift+N New Window), 커밋 3건(bc7d150·5c737bd·085faa2) 기록, 관련 세션 링크 추가.
- index.md 갱신 (세션 1건 추가). 엔티티/토픽/스킬 신규 없음 — 기존 lampas-harness·desktop 범위 내 소규모 기능 추가로 판단.
- AI_CONTEXT.md 갱신 없음 — 소규모 기능 세션이라 40줄 예산 내 유지할 만큼 장기 핵심 사실 아님.
- 특이사항: 커밋 5c737bd(API 과금 토글)는 [[2026-07-15-과금모드-토글-컨텍스트표시]] 세션에서 구현만 되고
  이번 세션에서야 커밋됨 — 구현/커밋 세션 분리 사례로 기록.

## [2026-07-16] ingest | cwc-system 저장소 클론 (source: 3f5b6c94-de35-45f6-a762-615a63559bd0.md)
- 원본 보관: `raw/conversations/2026-07-15-cwc-system-저장소-클론.md`
- 세션 신설: [[2026-07-15-cwc-system-저장소-클론]] — 2026-07-15 06:42 UTC, `git@github.com:cwc-commerce/cwc-system.git`을
  `~/Works/cwc/cwc-system`에 최초 클론(1왕복, 짧은 세션).
- 시간순 규명: 이 클론(06:42)이 같은 날 06:48 [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]](이 세션에서
  cwc-system이 "이미 최신"으로 집계된 이유가 바로 직전 클론)과 06:51 [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]](cwc-system
  내용 첫 조사)의 선행 이벤트임을 확인 — 세 세션의 인과관계를 처음으로 연결.
- 엔티티 갱신: [[cwc-system]] (도입부에 최초 클론 시각·순서 반영, 관련 세션 링크 추가)
- 토픽 갱신: [[works-project-portfolio]] (표의 cwc/cwc-system 행에 "이미 최신"의 근거인 직전 클론 사실 추가, 관련 세션 링크 추가)
- index.md 갱신 (세션1 추가). 신규 엔티티/토픽/스킬 없음 — 단순 git clone 요청이라 재사용 절차로 추출할 내용 없음.
- AI_CONTEXT.md 갱신 없음 — cwc-system 프로젝트 상태는 기존 줄이 이미 포괄, 이 세션은 그 선행 이벤트를 보강할 뿐 새 핵심 사실 아님.
- 특이사항: 매우 짧은(1왕복) 세션이지만 같은 날 후속 두 세션의 순서를 재구성하는 데 필요해 ingest함.

## [2026-07-16] ingest | dark 폴더 생성·dark-system 저장소 클론 (source: quick-18c9f545-162d-4ff1-ae9b-8d6655b53e62.md)
- 원본 보관: `raw/conversations/2026-07-15-dark-system-저장소-클론.md`
- 세션 신설: [[2026-07-15-dark-system-저장소-클론]] — 2026-07-15 06:38 UTC, `~/Works/dark` 폴더 생성 후
  `git@github.com:progdesigner/dark-system.git`을 `~/Works/dark/dark-system`에 최초 클론(2왕복, 짧은 세션quick).
  원격 소유자는 `progdesigner` 개인 계정(cwc-commerce 등 org 아님).
- 시간순 규명: 이 세션(06:38)이 [[2026-07-15-cwc-system-저장소-클론]](06:42)보다 4분 앞서고, 같은 날 06:48
  [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]]에서 `dark/dark-system`이 "이미 최신"으로
  집계된 이유가 바로 이 세션임을 확인 — cwc-system과 동일한 "일괄 최신화 직전 신규 클론" 패턴.
- 토픽 갱신: [[works-project-portfolio]] (표의 dark/dark-system 행에 "이미 최신"의 근거인 직전 클론 사실
  추가, 상단 갱신 노트·관련 세션 링크 추가)
- index.md 갱신 (세션1 추가). 신규 엔티티/토픽/스킬 없음 — dark-system 내용(제품 성격·스택)은 여전히
  미조사라 포트폴리오 자체 규칙(실제로 다뤄지는 세션이 생기기 전엔 엔티티 미생성)에 따라 엔티티 미생성.
  단순 mkdir+git clone이라 재사용 절차로 추출할 내용도 없음.
- AI_CONTEXT.md 갱신 없음 — 매우 짧은 선행 이벤트 세션으로, 기존 works-project-portfolio 한 줄이 이미 포괄.
- 특이사항: 매우 짧은(2왕복) 세션이지만 같은 날 후속 세션의 "이미 최신" 집계 근거를 재구성하는 데 필요해
  ingest함 — cwc-system-저장소-클론 세션과 동일한 판단 기준 적용.

## [2026-07-16] ingest | SRKK Azure 펀딩 영업메일 번역 요청 (source: quick-4773210a-3901-4ede-8bca-9dea6cdc78a6.md)
- 원본 보관: `raw/conversations/2026-07-15-srkk-azure-펀딩메일-번역.md`
- 세션 신설: [[2026-07-15-srkk-azure-펀딩메일-번역]] — 2026-07-15 05:31 UTC, 1왕복 quick 채팅.
  SRKK Group(말레이시아/싱가포르 MS 파트너 추정)의 Azure DB 마이그레이션 펀딩(최대 SGD 35,000)
  영업메일을 사용자가 영→한 번역 요청, 어시스턴트가 전문 번역 + "벤더 영업/리드 제너레이션 메일"로
  성격 규정.
- 토픽 갱신: [[harness-as-business-assistant]] (관찰된 사례 목록에 추가 — 대행 작성이 아닌 순수
  번역 사례로, 기존 사례들보다 개입이 가장 얇음을 명시)
- index.md 갱신 (세션1 추가). 신규 엔티티 생성 안 함 — SRKK Group은 1회성 영업메일 발신자일 뿐
  기존/후속 거래 맥락이 소스에 없어 엔티티화 보류. 신규 스킬 없음 — 순수 번역 1턴 작업.
- AI_CONTEXT.md 갱신 없음 — 일회성 스팸성 영업메일 번역으로 핵심 기억에 반영할 만한 지속적 사실 없음.
- 특이사항: 매우 짧은(1왕복) 세션. 발신자-수신자 관계·후속 조치(통화 예약 여부) 모두 소스에 근거 없어
  임의 연결하지 않음.

## [2026-07-16] ingest | 주식투자 10만원 가능 여부 질문 (source: quick-2d40188f-44a7-4062-b97f-44d62e96503e.md)
- 원본 보관: `raw/conversations/2026-07-15-주식투자-10만원-질문.md`
- 세션 신설: [[2026-07-15-주식투자-10만원-질문]] — 2026-07-15 05:05~05:06 UTC, 1왕복 quick 채팅.
  "10만원으로 주식 거래가 가능한가?" 질문에 국내(1주 단위·수수료율)·미국(소수점 매매) 주식 매매
  가능 여부와 소액 투자 현실적 제약을 답변. CWC 업무 맥락 없는 순수 개인 재무 상담.
- index.md 갱신 (세션1 추가). 신규 엔티티/토픽/스킬 없음 — 일반 금융 상식 답변으로 재사용 절차나
  고유 사실 없음(수수료율·소수점 매매 지원 여부는 시점에 따라 바뀔 수 있는 일반 정보).
- AI_CONTEXT.md 갱신 없음 — 일회성 정보성 질의, 지속적 핵심 사실 없음.
- 특이사항: 매우 짧은(1왕복) 세션. [[progdesigner]]가 실제 투자를 시작했는지는 이 세션만으로 확인 불가.

## [2026-07-16] ingest | 삼성전자 주식분할 질문 + 배너 HTML 전환 인사이트 (source: quick-161a37b9-afc2-473d-932e-feec2f8947b9.md)
- 원본 보관: `raw/conversations/2026-07-15-삼성전자-주식분할-배너-html-인사이트.md`
- 세션 신설: [[2026-07-15-삼성전자-주식분할-배너-html-인사이트]] — 서로 무관한 두 왕복이 한 quick 파일에
  묶임. (1) 02:40 UTC: "삼성전자 주식 분할된 적 없나?" → 2018년 5월 50:1 액면분할(액면가 5,000원→100원,
  250만원대 고가주 진입장벽 해소 목적, 분할 후 5만원대) 이력 답변, 출처 4건. (2) 03:22 UTC: 사진 1장
  첨부 "정리해봐" → 배너 디자인 이미지를 이미지 조각 조합 없이 AI가 통째로 HTML(Response UI용)로
  변환하는 워크플로우 인사이트 + 셀프서비스 툴 제품 아이디어 정리.
- 토픽 신설: [[ai-design-to-html-banner-conversion]] — 배너 디자인→HTML 직접 변환 인사이트·제품
  아이디어. "복지리 앱(토스)" 배너가 이 방식으로 실제 제작된 사례로 언급(원문 표기 그대로 보존, 정체 미상).
- 토픽 갱신: [[harness-as-business-assistant]] (관찰된 사례에 추가 — 사업 상담보다 제품 기획 아이디어
  정리에 가까운 케이스)
- index.md 갱신 (세션1·토픽1 추가). 신규 엔티티 없음 — "복지리 앱(토스)"·"Response UI" 모두 소스만으로는
  정체·성격 확인 불가라 엔티티화 보류.
- AI_CONTEXT.md 갱신 없음 — 배너 HTML 전환은 아직 착수 결정 안 된 아이디어 단계, 주식분할 질문은 일회성
  정보성 질의로 지속 핵심 사실 아님.
- 특이사항: 첨부 사진의 원본 내용(대화/노트 캡처로 추정)은 소스 텍스트에 남아있지 않아 어시스턴트 요약만
  근거로 기록. 같은 날 [[2026-07-15-주식투자-10만원-질문]]과 함께 [[progdesigner]]의 주식 관련 quick
  질의 2건째이나 두 세션 간 직접적 후속 관계는 소스에 근거 없음.

## [2026-07-16] ingest | 사용량 한도(5시간/주간) 게이지 표시 가능 여부 확인 (source: f63be918-0585-46f1-a363-7dda10de75ca.md)
- 원본 보관: `raw/conversations/2026-07-15-사용량한도-rate-limit-sdk-확인.md`
- 세션 신설: [[2026-07-15-사용량한도-rate-limit-sdk-확인]] — [[2026-07-15-과금모드-토글-컨텍스트표시]]
  (01:17~01:40) 세션의 같은 날 후속 질문(02:53~02:56). 사용자가 컨텍스트 잔여율 옆에 5시간/주간
  사용량 한도도 보여줄 수 있는지 질문 → 어시스턴트가 조사 완료 전 일반론으로 "불가능"이라 먼저
  답했다가, 조사 완료 후 "정정하겠습니다 — 가능합니다"로 자체 정정. SDK(`usage_EXPERIMENTAL_MAY_
  CHANGE_DO_NOT_RELY_ON_THIS_API_YET()`, `rate_limit_event`)엔 5시간/7일(opus/sonnet 세분·
  model_scoped·extra_usage 포함) 한도 조회 훅이 이미 있으나 `src/server.ts`는 둘 다 호출하지 않음
  확인(grep 0건) — 기술적으로 가능, 미구현. 소스 트랜스크립트가 어시스턴트의 구현 제안 질문에서
  끝나 사용자 승인 여부 불명.
- 스킬 갱신: [[sdk-claude-code-vs-api-billing]] — rate_limits 필드 세분화(five_hour/seven_day/
  seven_day_opus/seven_day_sonnet/model_scoped/extra_usage), `SDKRateLimitEvent` 실시간 이벤트
  추가, "하네스는 이 API들을 아직 미사용" 상태 기록, "일반론으로 단정 금지" 함정 추가.
- 엔티티 갱신: [[lampas-harness]] — 과금 모드 토글 섹션에 후속 항목 추가, 관련 세션 목록 갱신.
- index.md 갱신 (세션1 추가, 스킬 한 줄 요약 보강). 신규 엔티티·토픽 없음(기존 스킬로 흡수).
- AI_CONTEXT.md 갱신 없음 — 새 결정·상태 변화 아님(기존 "구현 완료" 기록에 영향 없는 미구현 후속
  조사), 위키 페이지로 충분.

## [2026-07-16] ingest | 넷플연가 와인 모임 재료비 절감 상담 (source: quick-cdf19ef0-e0cd-443f-923d-828495b8ebe2.md)
- 원본 보관: `raw/conversations/2026-07-15-넷플연가-와인모임-재료비-절감.md`
- 세션 신설: [[2026-07-15-넷플연가-와인모임-재료비-절감]] — 2왕복 quick (2026-07-15 07:07~07:15 UTC).
  사용자가 [[netpeul-yeonga]] 커리큘럼 작성 페이지 원문 전체(이미 작성된 실제 초안 포함)를 붙여넣고,
  블라인드 테이스팅 모임에 쓰는 고가 와인(나파밸리·부르고뉴급) 재료비가 넷플연가 지원액을 크게 초과해
  적자라는 문제 제기 → 어시스턴트가 (1) 콘텐츠 재설계(가격 블라인드·대비 페어·BYOB 확대·시음→매장
  구매 전환), (2) 사용자의 "그래도 부족하다" 재질문에 대해 원가 구조 자체를 조이는 2단계(회차당 2병
  상한·와인 없는 회차·BYOB 정식화·재료비 실비 갹출)로 응답, 4회차 원가 예시표(기수당 11~16만원)까지 산출.
- 엔티티 신설: [[netpeul-yeonga]] — 소셜/취미 정기 모임 플랫폼, 커리큘럼 작성 구조(12항목)·뒤풀이 95%
  관행·웰컴 인터뷰 제도 기록.
- 엔티티 갱신: [[cwc-commerce]] — 엘레망이 개인 와인샵으로서 [[progdesigner]]의 넷플연가 모임과
  연결됨을 재확인(시음→구매 전환·콜키지 뒤풀이 제안), [[progdesigner]] — 커리큘럼 초안의 자기소개
  타이틀/본문 별칭 불일치("John" vs "제이든") 원문 그대로 기록.
- 토픽 신설: [[wine-meetup-cost-reduction]] — 와인 모임 원가 절감 2단계 프레임워크(콘텐츠 재설계→구조
  재설계), 4회차 원가표.
- 토픽 갱신: [[harness-as-business-assistant]] — 사례 추가(이 패턴군에서 손익 계산이 가장 구체적인 케이스).
- AI_CONTEXT.md 갱신: 업무 맥락에 progdesigner의 엘레망 와인샵+넷플연가 모임장 겸업, 재료비 적자
  상담 진행 중 한 줄 추가. 34줄(<40 준수).
- index.md 갱신 (세션1·엔티티1·토픽1 추가).
- 특이사항: 트랜스크립트가 "실제 넷플연가 재료비 지원액이 얼마인지" 되묻는 어시스턴트 질문에서 끝남 —
  사용자 답변·최종 커리큘럼 확정 여부는 소스에 없어 후속 세션 확인 필요. 재료비 실비 갹출 등 규정상
  가능 여부는 어시스턴트도 "넷플연가 채널톡 확인 필요"로 단정하지 않음 — 위키에도 미확정으로 기록.

## [2026-07-16] ingest | "대화 가능?" 확인 세션 (source: 0035eb83-7443-48aa-b80c-cf13e58023ef.md)
- 원본 보관: `raw/conversations/2026-07-15-대화-가능-확인.md`
- 세션 신설: [[2026-07-15-대화-가능-확인]] — 2026-07-15 02:51:59~02:52:01 UTC, 1왕복. "대화 가능?" →
  "네, 가능합니다. 무엇을 도와드릴까요?" 단순 핑성 대화.
- index.md 갱신 (세션1 추가). 신규 엔티티/토픽/스킬 없음 — 실질 내용이 전무한 세션.
- AI_CONTEXT.md 갱신 없음 — 반영할 핵심 사실 없음.
- 특이사항: 타임스탬프가 같은 날 [[2026-07-15-사용량한도-rate-limit-sdk-확인]](02:53 시작)보다 약 1분
  앞서 새 세션 개시 전 확인성 메시지로 추정되나, 직접 연결은 소스에 근거 없어 단정하지 않음.

## [2026-07-16] ingest | ARVIE 화장품 마케팅 전략 재분석 요청 (source: 6cdfe127-26e7-4d5a-b4de-89d378178fb6.md)
- 원본 보관: `raw/conversations/2026-07-15-arvie-화장품-마케팅-재분석.md`
- 세션 신설: [[2026-07-15-arvie-화장품-마케팅-재분석]] — 2026-07-15 01:54~02:11 UTC, 2왕복 quick 채팅.
  코딩 아님 — [[lampas]]를 마케팅 컨설턴트로 사용. K-뷰티 브랜드 "ARVIE" 인스타 피드 사진 1장으로
  마케팅 전략 문의 → 1차 답변(자산 확장형: 럭셔리 무드·비주얼을 앞세워 인지도 확장) → 사용자가
  "위 내용 무시하고 다시" 재요청 → 2차 답변(갭 분석형: "사야 할 이유"가 비어있다는 진단, 무드에서
  효능 증거 레이어로 방향 전환 — 레드 컬러 통일·임상/성분 증거 축적·퍼널 이원화·히어로 1종 집중).
  판매 채널·가격대·타겟 시장 질문에서 트랜스크립트 종료(후속 없음).
- 토픽 갱신: [[harness-as-business-assistant]] (사례 추가 + "재요청 시 프레임 전환" 특징 신설 —
  같은 입력에 "다시"만으로 진단 관점이 자산 확장형↔갭 분석형으로 바뀐 데이터포인트)
- index.md 갱신 (세션1 추가). 신규 엔티티/스킬 없음 — "ARVIE" 브랜드 실체는 소스에 근거 없어
  엔티티화 보류([[2026-07-15-올리브유-마케팅-포지셔닝]]과 동일 판단 기준), 재분석 절차는 얇아 스킬
  미추출(토픽 기록으로 충분).
- AI_CONTEXT.md 갱신 없음 — 기존 "코딩 외 업무 비서·마케팅 컨설팅" 일반 패턴 줄이 이미 포괄.
- 특이사항: 사용자가 재요청 사유를 밝히지 않았음에도 2차 답변이 방법론적으로 더 날카로워짐(경쟁
  구도 인식·소비자 저장≠구매 구분 추가) — 단순 "다시 해줘" 재프롬프트가 품질을 끌어올릴 수 있다는
  관찰이므로 재현성 확인 전까지 일반화하지 않고 이 세션의 단일 사례로 기록.

## [2026-07-16] ingest | 보관 자동저장 동작 확인 + 볼린저 밴드 질문 (source: quick-9c8dd8e5-98f8-489e-9c02-e857323aac60.md)
- 원본 보관: `raw/conversations/2026-07-14-보관-자동저장-확인-볼린저밴드.md`
- 세션 신설: [[2026-07-14-보관-자동저장-확인-볼린저밴드]] — 서로 무관한 두 왕복. (1) 2026-07-14 13:32 UTC:
  "Archive 하면 장기 기억에 저장되는거 맞지?" → [[2026-07-13-보관시-자동-기억저장]] 구현의 사용자 시점
  재확인, stale 게이트(`rememberedAt` 없거나 저장 후 대화가 더 이어진 경우만 저장) + 확인 방법(API
  `remembered: true`, 데몬 로그 `chat archived: <id> (memory ingest queued)`) 신규 언급. (2) 2026-07-15
  01:06 UTC: "볼린저 방식이라는게 뭐지?" → 볼린저 밴드(중심선±표준편차×2, 변동성 시각화·스퀴즈·
  과매수/과매도 보조 지표) 일반 설명, 후속 없이 종료.
- 토픽 갱신: [[long-term-memory-architecture]] (저장 측 자동화 절에 확인 방법 — 로그 문구·API 필드 — 보강)
- index.md 갱신 (세션1 추가). 신규 엔티티/스킬 없음. 볼린저 밴드는 CWC 업무 맥락 없는 순수 금융 상식
  답변이라 신규 토픽 미생성([[2026-07-15-주식투자-10만원-질문]]과 동일 판단 기준).
- AI_CONTEXT.md 갱신 없음 — (1)은 기존 결정("보관 진입점 3개")의 재확인일 뿐 새 핵심 사실 아님, (2)는
  일회성 정보성 질의.
- 특이사항: 두 왕복이 12시간 이상 간격을 두고 같은 quick 파일에 묶임 — [[2026-07-15-삼성전자-주식분할-배너-html-인사이트]]와
  같은 "무관한 두 왕복 결합" 패턴. 파일명 prefix는 첫 메시지 날짜(2026-07-14) 기준.

## [2026-07-16] ingest | quick.html 이미지 드래그 첨부 구현 + 스크린샷 번역 (source: quick-3c682f70-7364-4f44-858e-3f5e39909eb9.md)
- 원본 보관: `raw/conversations/2026-07-14-quick-html-이미지-첨부-구현.md`
- 세션 신설: [[2026-07-14-quick-html-이미지-첨부-구현]] — 서로 무관한 두 왕복. (1) 2026-07-14 09:16~09:57 UTC:
  "quick.html에서도 index.html처럼 드래그하면 이미지 첨부되게 해줘" → `apps/web/public/quick.html`(퀵 채팅
  오버레이)에 `index.html`의 이미지 드래그·붙여넣기·파일선택 첨부 기능을 동일하게 이식, `vite build`로
  `dist/quick.html` 갱신, **Playwright + macOS 시스템 Chrome(`channel:'chrome'`)**으로 실브라우저 종단
  검증(PNG 드롭→썸네일→전송 바디 `images` 포함→말풍선 렌더링, JS 에러 없음). 초반 `quick.html` 위치를
  찾는 `find`가 홈 디렉터리 전체 스캔이라 반복 지연(백그라운드 세션 전환 중 끊김 1회 포함), 결국
  포그라운드 동기 실행으로 해결. (2) 2026-07-14 10:43 UTC: 메신저 대화 캡처 스크린샷 번역 1턴(임대/
  공급업체 관련 짧은 대화) — 대행 작성 아닌 순수 번역, [[harness-as-business-assistant]] 패턴 사례.
- 스킬 신설: [[playwright-system-chrome-verify]] (Playwright 브라우저 바이너리 미설치 시 시스템 Chrome
  `channel:'chrome'` 폴백으로 실브라우저 종단 검증 — 저장소 내부 스크립트 배치·DataTransfer 이벤트
  재현·좁은 검증 항목 명시)
- 엔티티 갱신: [[lampas-harness]] (2026-07-14 quick.html 이미지 첨부 기능 절 신설, 관련 세션 링크 추가)
- 토픽 갱신: [[harness-as-business-assistant]] (스크린샷 번역 사례 추가)
- index.md 갱신 (세션1·스킬1 추가)
- AI_CONTEXT.md 갱신 없음 — index.html 기능을 quick.html에 맞추는 기능 패리티 작업으로, 새 결정이나
  진행 프로젝트 상태 변화 아님(기존 lampas-harness 줄이 포괄). 번역 왕복도 일회성.
- 특이사항: `find` 지연으로 사용자가 "작업이 완료 되었나요?" 두 번 확인 — 백그라운드 프로세스가
  세션 전환 중 끊기는 [[harness-background-process-lifecycle]] 패턴의 또 다른 사례로 볼 수 있으나,
  이번엔 파일 검색(find)이라 별도 토픽 갱신은 보류(이미 알려진 한계의 반복 관찰).

## [2026-07-16] ingest | SRKK 도메인/Scott 확인 + Princ 번역 (source: f07d5a7e-ab17-4858-9faf-77b4dc6a0884.md)
- 원본 보관: `raw/conversations/2026-07-14-srkk-도메인-scott-확인-princ-번역.md`
- 세션 신설: [[2026-07-14-srkk-도메인-scott-확인-princ-번역]] — 2026-07-14 09:17~09:54 UTC, [[2026-07-14-은행환불-거래처매칭]]
  종료 17분 후 이어진 quick 채팅. WhatsApp 대화 스크린샷 다수를 번역 + Scott에게 보낼 대응 메시지를
  영/한으로 단계적으로 다듬음. 두 갈래: (1) "싱가포르 SRKK" 도메인·Microsoft 계정을 승인 없이
  연장하려 한 의혹 → John Lee가 "대표님"에게 보고 → "누구 허락으로 진행했죠?" 질책 → Scott과 협의
  끝에 **CWC Domain·Microsoft 계정 둘 다 미연장으로 최종 확정**. (2) `PRINC CR` 거래 상대방("Princ")
  식별 조사 지속 — 이 세션에서도 확정 안 됨.
- 엔티티 신설: [[srkk]] — "싱가포르 SRKK"(이 세션)와 "SRKK Group"([[2026-07-15-srkk-azure-펀딩메일-번역]])
  이름이 같으나 동일 실체인지는 미확정으로 병기.
- 엔티티 갱신: [[cwc-commerce]] (모순 관찰 — "John Lee"가 이 세션에서 "대표님"에게 보고하는 발신자로
  등장, 기존 "대표=John Lee=progdesigner 가능성" 추정과 배치될 수 있어 단정하지 않고 병기만 추가).
- 세션 갱신: [[2026-07-14-은행환불-거래처매칭]] (후속 절 추가 — SRKK/Scott 플래그 부분 해소, Princ 미해결 유지)
- 스킬 갱신: [[bank-refund-invoice-reconciliation]] (거래 상대방명이 여러 세션에 걸쳐 미해결로 남을 수
  있다는 함정 추가), 세션 갱신: [[2026-07-15-srkk-azure-펀딩메일-번역]] (소급 관찰 — 시점상 하루 차이,
  이름 일치뿐 직접 근거 없음 명시)
- 토픽 갱신: [[harness-as-business-assistant]] (대응 전략 수립 사례 추가 — 상대 질문별 대응·액션
  주체 진단 방식)
- AI_CONTEXT.md 갱신: 업무 맥락에 SRKK 도메인/Microsoft 계정 미연장 확정 + Princ 식별 미해결 한 줄 추가. 37줄(<40 준수).
- index.md 갱신 (세션1·엔티티1 추가)
- 특이사항: 이 세션은 같은 날 앞선 은행환불 세션의 직접 후속(17분 뒤)이라 두 세션을 연결하는 데
  ingest의 초점을 맞춤. "John Lee"↔"대표님" 관계는 트랜스크립트만으로 단정 불가 — 향후 소스로
  재확인 필요 항목으로 남김.

## [2026-07-16] ingest | Clara/Barbara 번역 + CWC-FY Group 위스키 선적분쟁 메일 (source: quick-5bc3f296-7b29-4d41-ba98-77eabe76180a.md)
- 원본 보관: `raw/conversations/2026-07-14-clara-바바라-위스키분쟁-번역.md`
- ⚠️ **소스 파일명 이상**: 이 소스 파일명(`quick-5bc3f296-...md`)은 이미 [[2026-07-14-은행환불-거래처매칭]]
  세션(08:52~09:00 UTC, 은행 거래내역·인보이스 해석 내용)의 소스로 기록돼 있으나, 현재 그 경로의 실제
  내용은 완전히 다른 대화(09:03~09:13 UTC, 이 세션)뿐이다. 기존 은행환불 세션 내용은 수정하지 않고
  그대로 유지, 이번 내용은 새 파일명으로 별도 ingest함 — 두 세션은 시간상 이어지며(08:52→09:03→09:17
  [[2026-07-14-srkk-도메인-scott-확인-princ-번역]]) 내용상으로도 "Princ" 조사가 연속된다.
- 세션 신설: [[2026-07-14-clara-바바라-위스키분쟁-번역]] — Clara/Barbara 메시지·메일 번역 2건 + Princ
  문구 재작성 + **CWC Lab-FY Group 위스키 선적분쟁 이메일 전체**(2024-10~2025-01, 7통) 붙여넣고
  "Princ 관련 있냐" 배제 질의에 답변(무관 확인).
- 엔티티 신설: [[fy-group]] (FY Group Pte Ltd, 싱가포르, CEO Keira Zhang — cwc-lab-singapore의
  위스키 공급처이자 분쟁 상대)
- 토픽 신설: [[cwc-fy-group-whisky-dispute]] (2024-10-28~2025-01-06 전체 타임라인·금액·법적 조치
  언급 정리, 2025-01-06 시점 미해결로 이월)
- 엔티티 갱신: [[cwc-commerce]] (임원 Ryan Lee 추가, 한국 발령·"John과 Kim" 인계 언급, CWC⎢ELEMENTS⎢
  ELEVINO 브랜드 서명 확인), [[cwc-lab-singapore]] (위스키 수입 사업·강남 위스키클럽 추가),
  [[elevino-system]] (브랜드 서명으로 CWC 계열 관계 재확인), [[srkk]] (Scott Jeun/Sylvan Group을
  "등장1 Scott" 동일인 후보로 기록, 확정하지 않음)
- 스킬 갱신: [[bank-refund-invoice-reconciliation]] (배제 후보 확정 절차 추가 — 다른 소스 전수 검색해
  "무관함"도 유효한 진행으로 기록, Princ 미해결 상태가 3개 세션째 이어짐을 갱신)
- 토픽 갱신: [[harness-as-business-assistant]] (사례 추가)
- AI_CONTEXT.md 갱신: 업무 맥락에 위스키분쟁 한 줄 추가, Princ 미해결 세션 수 갱신(두 세션→세 세션). 36줄(<40 준수)
- index.md 갱신 (세션1·엔티티1·토픽1 추가, 관련 엔티티/스킬 설명 보강)
- 특이사항: Barbara 이메일(와이너리·올리브 농장)은 2026-07-15 올리브유 마케팅 세션과 주제상 겹치나
  등장인물·시점이 달라 직접 연결 근거 없어 임의 연결하지 않음. Clara 메시지도 발신자 소속 미상으로
  Princ 조사와의 연관은 추정하지 않음.

## [2026-07-16] ingest | GPT Realtime 음성입력(길게누르기) 구현 (source: 3df735b1-aebf-4c87-83af-2f20f8f70bae.md)
- 원본 보관: `raw/conversations/2026-07-15-gpt-realtime-음성입력-길게누르기.md`
- 세션 신설: [[2026-07-15-gpt-realtime-음성입력-길게누르기]] — 12:37~22:55 장시간 연속 개발 세션.
  전송버튼 길게누르기 → OpenAI Realtime(`gpt-4o-transcribe`) 실시간 음성입력 최초 구현(`b9eac27`) →
  재시작 미반영 발견·수정(dist빌드누락 + IPv6 떠돌이서버 2중 원인) → API크레딧 소진이 `/compact`·
  백그라운드 `memory-ingest` 둘 다 실패시킴 발견 → 시작부분 끊김 버그(마이크가 WebSocket 연결 완료 후에야
  열림) 발견·수정(`938d4e4`, 마이크 선오픈+로컬버퍼링) → 3단계 색상 시각피드백(`7450997`) → 밀어내기
  취소 UX(`5182ee0`) 총 4커밋.
- 스킬 신설: [[realtime-voice-mic-buffer-before-connect]] — push-to-talk 실시간 음성입력의 연결 전
  마이크 버퍼링 패턴 + speech_started 이벤트 기반 3단계 시각 피드백 (재사용 가능한 절차로 추출)
- 스킬 갱신: [[self-hosted-agent-server-ops]] (함정 1에 IPv6 와일드카드 변종 추가, 함정 4 "dist 빌드
  누락" 신설 — 두 함정이 증상이 같아 헷갈리기 쉬움을 명시), [[sdk-claude-code-vs-api-billing]] (API
  크레딧 소진이 백그라운드 memory-ingest까지 실패시키는 파급 범위 절 추가)
- 토픽 갱신: [[long-term-memory-architecture]] (저장(ingest) 실패 모드 — API 크레딧 소진 절 신설.
  저장 자동화됐다고 항상 성공한다고 가정하면 안 됨을 명시)
- 엔티티 갱신: [[lampas-harness]] ("추가 기능 (2026-07-15 세션 — GPT Realtime 음성입력)" 절 신설,
  커밋 4개·배포 함정 2종·크레딧 파급 재확인 반영)
- AI_CONTEXT.md 갱신: 진행 중 프로젝트에 음성입력 기능 완료 한 줄 추가, 확정된 결정에 크레딧 소진→
  백그라운드 ingest 실패 한 줄 추가. 37줄(<40 준수)
- index.md 갱신 (세션1·스킬1 추가, self-hosted-agent-server-ops 설명 보강)
- 특이사항: 소스 파일이 물리적으로 `logs/chats/`가 아니라 `logs/chats/archive/`에 있었다(요청받은 경로에는
  없었음) — find로 archive 하위에서 찾아 확인 후 진행. 이 대화 자체가 진행 중이던 음성입력 기능을
  사용자가 직접 검증(요? / 확인해보자 잘 돼? 등 음성 테스트 발화)한 라이브 사용 흔적을 포함.

## [2026-07-16] ingest | 보관메모리 확인·하네스 재시작·커밋 푸시 세션 (source: caaf92e2-e047-4a4f-a5b4-0f16b988fdf5.md)
- 소스: raw/conversations/2026-07-15-보관메모리확인-하네스재시작-커밋푸시.md (원본 caaf92e2-e047-4a4f-a5b4-0f16b988fdf5.md 복사)
- 세션 신설: [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]]
- 엔티티 갱신: [[lampas-harness]] (재시작 스크립트 동작 방식 불일치 기록·정정, 큐→실행 경로 라인단위
  상세·야간잡 파일명 추가)
- 스킬 갱신: [[self-hosted-agent-server-ops]] (`restart-lampas.sh`가 nohup 지연 패턴을 내장함을 원본 확인
  후 반영), [[detach-long-job-nohup]] (재시작 스크립트를 이 패턴의 사례로 교차연결)
- 토픽 갱신: [[long-term-memory-architecture]] (보관→ingest 3차 재확인, memory-ingest-daily 잡/plist
  파일명 특정)
- index.md 갱신 (세션 1개 추가)
- 모순 기록: `restart-lampas.sh`가 2026-07-11 세션엔 "수동 즉시(pkill 기반)"로 관찰됐으나, 이번 세션+
  같은 날 앞선 GPT Realtime 세션에서 스크립트 원본을 직접 읽어 대조한 결과 현재는 **pkill 없는 nohup
  지연 실행 + launchctl kickstart** 방식임을 확인 — 2026-07-11 이후 스크립트가 재작성된 것으로 추정
  (변경 커밋 미확인).
- AI_CONTEXT.md 갱신 없음 — 이 세션은 기존 결정을 재확인·상세화한 것으로 새로운 핵심 사실(사용자 선호·
  확정 결정·프로젝트 상태 변화) 없음, 40줄 여유 내에서 갱신 불필요로 판단.
