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

## [2026-07-16] ingest | 주식 개장 전 매수 가능 시간 질문 (source: 52301a39-c0d4-4aa3-8634-1d55bdbedb9f.md)
- 세션 신설: [[2026-07-15-주식-개장전-매수시간-질문]] — "9시 개장 전에도 매수할 수 있는 시간이 있어?"
  1왕복 quick. 한국 주식 동시호가(08:30~09:00, 09:00 일괄 시가 체결)·정규거래(09:00~15:30) 구조 답변,
  야간 현물 거래 없음 안내.
- 신규 엔티티/토픽/스킬 없음 — 일반 금융 상식 답변, 재사용 가능한 절차나 위키에 남길 고유 사실 없음.
- 기존 세션 [[2026-07-15-주식투자-10만원-질문]]에 상호 링크 추가 — 같은 날 개인 재무 상담 quick 계열.
- index.md 갱신 (세션 1개 추가)
- AI_CONTEXT.md 갱신 없음 — 일회성 정보성 질의, 사용자 선호·확정 결정·프로젝트 상태 변화 없음.

## [2026-07-16] ingest | dark-toss-api 국장 9시 이전 매수 가능 여부 코드 조사 (source: 83593c4a-07ab-465c-b0bd-17104b49a174.md)
- 세션 신설: [[2026-07-15-dark-toss-api-장전매수-코드조사]] — [[progdesigner]]가 "국장 9시 전에도 매수
  가능한지 소스 API로 확인해 달라" 요청(2026-07-15 23:04~23:06 UTC, 2왕복). 어시스턴트가
  `dark-system` 모노레포 `apps/dark-toss-api`(토스증권 API 자동매매 봇) 소스를 file:line 근거로
  조사: 토스 API 응답에 장 세션 필드 파싱 없음, 로컬 시계로 09:00~15:30만 하드코딩 판정,
  `marketHoursOnly`·`autoRegionSwitch` 둘 다 기본값 `false`로 게이트 사실상 비활성, `executeBuy`/
  `manualBuy`엔 시간 체크 자체 없음 → 결론: 코드는 장전 매수를 막지도 인지하지도 않음, 실제 체결
  여부는 토스 서버 쪽에 달림(코드로 확인 불가). `marketHoursOnly:true` 전환 제안했으나 사용자 응답은
  이 소스에 없어 미결정으로 남음.
- 신규 엔티티: [[dark-toss-api]] (토스증권 자동매매 앱), [[dark-system]] (그 앱을 담은 개인 소유
  모노레포 — 그동안 [[works-project-portfolio]]에 미문서화로만 있던 저장소의 정체 최초 확인).
- 신규 스킬: [[config-flag-gate-audit]] — "시스템이 특정 상황을 막는가" 질문에 코드로 답할 때의 추적
  절차(정보 존재 여부 → 대체 판정 로직 → 게이트 호출부 전수 → 플래그 기본값 → 진입점 우회 여부 →
  코드 vs 외부시스템 책임 분리). [[bank-refund-invoice-reconciliation]]과 같은 "근거 우선, 단정
  금지" 계열로 일반화.
- 토픽 갱신: [[works-project-portfolio]] — `dark-system`을 미문서화 목록에서 제외, 표에 엔티티 링크
  연결(단 `apps/` 전체가 조사된 건 아니고 `dark-toss-api` 하나만 확인됨을 명시).
- 기존 세션 [[2026-07-15-주식-개장전-매수시간-질문]]에 상호 링크 추가 — 표면적으로 비슷해 보이나
  하나는 일반 재무상담, 이쪽은 실제 자동매매 코드의 기술 감사임을 명시.
- index.md 갱신 (세션 1개, 엔티티 2개, 스킬 1개 추가)
- AI_CONTEXT.md 갱신 — `dark-system`/`dark-toss-api` 발견 및 장시간 게이트 미작동 사실을 진행 중
  프로젝트 항목에 1줄 추가 (37줄, 40줄 이내 유지).

## [2026-07-16] ingest | Auto 모델 난이도 판정 감사·Fable 5(extreme 티어) 추가·확인UX 개선 (source: 340f19dc-2c7d-4bbe-9e78-d980e3d8e554.md)
- 원본 보관: `raw/conversations/2026-07-15-auto모델-난이도판정-확인ux-개선.md`
- 세션 신설: [[2026-07-15-auto모델-난이도판정-확인ux-개선]] — [[2026-07-15-dark-toss-api-장전매수-코드조사]]
  직후(23:23~23:31 UTC, 5왕복). (1) "Auto가 난이도를 누가 판단하는지" 질문에 `src/server.ts` file:line
  감사 — 판단은 **서버 100% 전담**(`resolveAutoModel()` 3단계 폴백: Claude API `judgeTierClaude()` →
  로컬 LLM `judgeTierLocal()` → 휴리스틱 `heuristicTier()`), 클라이언트는 표시만. (2) "Fable 5도
  판정에 포함시켜 달라" 요청으로 easy/medium/hard 3단계를 **`"extreme"` 티어 신설**해 4단계로 확장,
  `claude-fable-5` 매핑 — 휴리스틱 폴백은 hard까지만(안전망에서 최상위 모델로 안 튀게 의도적 비대칭).
  (3) 진행 중 턴 "■ 중지" 버튼에 **2단계 확인**(첫 클릭→3초 확인창→재클릭해야 실제 중지) 추가.
  (4) 대화 목록 hover 보관 버튼을 제거하고 **롱프레스(0.6초)+확인창** 방식으로 전환. (5) v0.1.27
  빌드 후 `restart-when-idle.sh`로 다른 대화 완료 대기 후 안전 재시작.
- 엔티티 갱신: [[lampas-harness]] — "추가 기능 (2026-07-15 세션 — Auto 모델 난이도 판정 + 확인UX)"
  절 신설(위 5개 항목 상세 + file:line 표), 관련 세션 링크 추가.
- 토픽 갱신: [[model-selection]] — "Auto — 난이도 자동 선택" 절 신설(판정 로직 3단계 폴백표, 티어→모델
  매핑, extreme 설계 의도).
- 신규 스킬: [[destructive-action-inline-confirm]] — 되돌리기 어려운 버튼에 모달 없이 인라인 확인을
  넣는 두 변형(같은 버튼 2회클릭 타임아웃 vs 롱프레스+확인창) 정리. §3·§4 두 구현 사례를 일반화.
- AI_CONTEXT.md 갱신 — [[lampas-harness]] 줄에 Auto 4단계 판정(extreme=Fable 5) 한 줄 추가.
- index.md 갱신 (세션 1개, 스킬 1개 추가)
- 특이사항: Auto 난이도 감사(§1)는 [[config-flag-gate-audit]]과 유사한 "판정 로직 전수 추적" 패턴이나
  이미 커버된 접근이라 별도 스킬로 분리하지 않고 세션 페이지에서만 교차 언급. §3·§4 UI 변경은 실행
  로직(`archiveSession` 등)은 그대로 두고 트리거만 교체 — 배포는 재시작 필요해 §5로 이어짐.

## [2026-07-16] ingest | 음성입력 진동+비프 피드백 (source: 58623de9-163d-408c-b9a5-fce33d5176d4.md)
- [[2026-07-15-gpt-realtime-음성입력-길게누르기]] 세션에서 만든 3단계 시각 피드백(노랑/빨강/초록)에
  이어, 노랑(준비중)→빨강(녹음중) 전환 순간에 진동+비프를 동시에 추가한 1왕복 후속 세션.
  `navigator.vibrate(40)`(iOS Safari 미지원) + 이미 열린 `AudioContext` 재사용 880Hz 비프(~90ms) —
  기기별 지원 격차를 이중 채널로 보완. vite 빌드 확인, 배포는 `npm run build:web`+데몬 재시작 필요
  (이 세션에서 재시작 미실행).
- 신규 세션 페이지: [[2026-07-16-음성입력-진동비프-피드백]]
- 엔티티 갱신: [[lampas-harness]] — "추가 기능 (2026-07-16 세션 — 음성입력 진동+비프 피드백)" 절 신설,
  관련 세션 링크 추가.
- 스킬 갱신: [[realtime-voice-mic-buffer-before-connect]] — "노랑→빨강 전환에 진동+비프 추가" 절 신설,
  제목·description·tags·출처에 반영.
- AI_CONTEXT.md 갱신 — [[lampas-harness]] 줄에 진동+비프 피드백 추가 한 절 반영.
- index.md 갱신 (세션 1개 추가, 스킬 설명 1개 갱신).

## [2026-07-16] ingest | 네이버 데이터 API 콘텐츠 자동화 의사결정 상담 (source: 30d0a80c-2144-4f80-9f3b-945094f7153f.md)
- 원본 보관: `raw/conversations/2026-07-16-네이버데이터api-콘텐츠자동화-의사결정.md` (archive/30d0a80c-...md 복사)
- 세션 신설: [[2026-07-16-네이버데이터api-콘텐츠자동화-의사결정]] — 1왕복 quick (2026-07-16 01:15~01:16 UTC).
  코딩 아님 — [[lampas]]를 콘텐츠 자동화 전략 컨설턴트로 사용.
- 신규 배경 사실: [[progdesigner]]가 **네이버 데이터랩(DataLab) API를 연결한 자동화 사이클**
  (검색 강도 높은 키워드 → 자동 글 작성 → 자동 배포)을 이미 운영 중임이 처음 확인됨. "마케팅과
  상품 얼라인" 언급으로 보아 개인 연재 블로그(study-ai-what)와는 별개 시스템·별개 대상일 가능성이
  높음(단정 보류, [[naver-blog-mcp]]와의 관계도 미확인).
- 내용: "인풋(키워드)-아웃풋(발행) 사이 마케팅-상품 얼라인·시장적합도 판단을 혼자 한다"는 고민에
  6-패턴 프레임워크로 응답 — ①키워드 채점 게이트(ICE 변형, `product-marketing` 스킬 활용)
  ②작성자·심사자 분리(LLM-as-judge) ③배치 승인(주 1회) ④성과 피드백 루프(현재 open-loop인 공백
  지적) ⑤포트폴리오 배분(70/20/10) ⑥신호 다변화(지식iN·커뮤니티 질문 문장). 권장 도입 순서까지 제시,
  설계·제안 단계에서 세션 종료(구현 미착수).
- 토픽 신설: [[content-automation-decision-framework]] (6-패턴 프레임워크 전문 기록)
- 토픽 갱신: [[harness-as-business-assistant]] (사례 추가 — 사진 없는 순수 전략 상담)
- 엔티티 갱신: [[progdesigner]] (콘텐츠 자동화 파이프라인 절 신설), [[naver-blog-mcp]] (별개 시스템
  가능성 메모 + 관계 미확인 명시)
- AI_CONTEXT.md 갱신: 진행 중 프로젝트에 콘텐츠 자동화 파이프라인 한 줄 추가. 38줄(<40 준수).
- index.md 갱신 (세션1·토픽1 추가)
- 특이사항: 신규 스킬 미추출 — 이 세션은 실행된 기술적 절차가 아니라 전략/의사결정 자문이라
  기존 판단 기준([[2026-07-15-올리브유-마케팅-포지셔닝]] 등)과 동일하게 토픽으로만 기록. 파이프라인의
  구체 구현(저장소·스크립트·대상 블로그)은 이 세션만으로 확인 불가 — 후속 세션에서 명확화 필요.

## [2026-07-16] ingest | 스페이스 엔티티 SDK+API+web-ai 구현 (source: 2ea35567-9ed4-4e57-b61d-4449892d66fd.md)
- 원본 보관: `raw/conversations/2026-07-15-스페이스-엔티티-sdk-api-webai-구현.md` (archive/2ea35567-...md 복사)
- 세션 신설: [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]] — 2026-07-15 23:11~2026-07-16 01:14.
  "스페이스 만들기·차량 선택 마무리해달라"는 반복 요청 조사 → Space가 코드베이스에 독립 엔티티로
  아예 없다는 사실 확인 → 방향 확정 후 Actor/Object 파이프라인을 그대로 복제해 Space를 신규
  엔티티로 설계·구현(DB `Space` 모델·API `modules/spaces/`·web-sdk UI 7파일·web-ai 채팅 배경선택
  플로우·촬영 합성 슬롯 연결). 중간에 lampas-web-ai 배포(S3+CloudFront)와 원격 갈라짐 병합
  (`git pull --no-rebase`) push도 처리.
- 엔티티 갱신: [[lampas-studio]] — "Space(공간) 엔티티" 절 신설(DB/API/SDK/합성슬롯/모델배정 요약,
  배포 미진행 명시), 이미지 모델 절에 "Object 촬영은 실제로 Gemini 직접 경로"(CLAUDE.md 요약과
  다름) 주의 사실 추가.
- 신규 스킬: [[clone-sibling-entity-pipeline]] — 기존 엔티티(A)와 동형인 새 엔티티(B) 추가 시
  DB→API→SDK UI→실사용 지점(이미지 슬롯 등)을 계층별로 복제하는 절차. 특히 "먼저 진짜 없는지
  코드로 확인 후 확정 질문 하나만 남기고 멈춘다"는 헛작업 방지 원칙과, 실사용 지점(슬롯)이 경로별로
  상태가 다를 수 있다는 함정을 기록.
- AI_CONTEXT.md 갱신: [[lampas-studio]] 줄에 Space 엔티티 신규 구현·배포 미진행 사실 반영(38줄 유지).
- index.md 갱신 (세션 1개, 스킬 1개 추가).
- 미해결 플래그(기록만, 이 세션이 만든 문제 아님): 병합으로 들어온 원격 커밋에
  `apps/lampas-api/env/.env.development`가 저장소에 커밋된 채 존재 — 개발용 시크릿 노출 가능성,
  사용자 확인 권고했으나 이 세션 내 확인은 없었음.

## [2026-07-16] ingest | quick.html 작업 폴더 선택기 + v0.1.27 커밋·push 확인 (source: quick-62e7d2bc-6326-421e-84ac-e7beb0bffdc9.md)
- 원본 보관: `raw/conversations/2026-07-16-quick-작업폴더선택기-커밋푸시.md`
- 세션 신설: [[2026-07-16-quick-작업폴더선택기-커밋푸시]] — "밀린 코드 커밋·push해줘" 1왕복 quick.
  커밋 `c100edd` → `main → origin/main` push 확인. 포함된 5개 변경 중 4개(롱프레스 보관확인·
  중지버튼 재클릭 확인·음성 진동+비프·extreme 티어)는 [[2026-07-15-auto모델-난이도판정-확인ux-개선]]·
  [[2026-07-16-음성입력-진동비프-피드백]]에 이미 기록된 작업의 반영 확인. 나머지 1개
  **quick.html 작업 폴더 선택기**(첫 턴 이후 대화가 폴더에 묶임)는 위키에 처음 등장하는 신규 사실.
- 엔티티 갱신: [[lampas-harness]] — "추가 기능" 절에 quick.html 작업 폴더 선택기 항목 추가,
  v0.1.27 변경 5종 전체가 커밋 `c100edd`로 반영·push됨을 명시.
- AI_CONTEXT.md 갱신: lampas-harness 줄에 v0.1.27 커밋(`c100edd`) push 완료 사실 반영(38줄 유지).
- index.md 갱신 (세션 1개 추가).

## [2026-07-16] ingest | quick.html 작업 폴더 선택기 + Auto 모델 구현 세션 (source: 605d1768-15e5-4e60-9a21-74ba9d9f2993.md)
- 원본 보관: `raw/conversations/2026-07-16-quick-html-폴더선택기-auto모델-구현.md`
- 세션 신설: [[2026-07-16-quick-html-폴더선택기-auto모델-구현]] — "quick.html 에도 어느 폴더로 할건지와
  Auto 를 선택할 수 있게 해줘" 1왕복 quick(02:16~02:20). [[2026-07-16-quick-작업폴더선택기-커밋푸시]]
  (02:34~, 이미 위키에 기록됨)이 "신규 확인, 구체 구현 코드는 미확인"으로 남겨뒀던 quick.html 작업
  폴더 선택기의 **실제 구현 세션**임을 확인(18분 먼저 실행) — index.html의 폴더 선택·잠금·Auto 모델
  로직을 quick.html에 이식. 부수적으로 vite `public/` 정적 파일은 전체 빌드 없이 cp만으로 반영
  가능하다는 배포 요령이 신규로 드러남.
- 엔티티 갱신: [[lampas-harness]] — "quick.html에 작업 폴더 선택기 추가" 항목을 구현 상세(폴더
  선택·잠금·Auto 모델·배포 요령)로 보강, "구체 구현 코드 미확인" 플래그 해소.
- 스킬 갱신: [[work-folder-sandboxing]](퀵 채팅 오버레이로 UI 확장 사실 추가),
  [[self-hosted-agent-server-ops]](함정4 반대 사례 — `public/` 정적 파일 cp 배포 요령 추가).
- 주제 갱신: [[model-selection]] — Auto 옵션이 quick.html로도 확장됐음을 반영.
- index.md 갱신 (세션 1개 추가). AI_CONTEXT.md는 이미 v0.1.27/커밋 `c100edd` 반영 완료 상태라 추가
  갱신 없음(38줄 유지).

## [2026-07-16] ingest | SRKK 인보이스 독촉 스레드 번역 + "James 확인" (source: quick-58839510-9c3b-4782-9e9e-6eb53e7c8184.md)
- 원본 보관: `raw/conversations/2026-07-16-srkk-인보이스-james확인.md`
- 세션 신설: [[2026-07-16-srkk-인보이스-james확인]] — 2026-07-16 02:15 UTC, 1왕복 quick 채팅.
  SRKK Consulting Pte Ltd 발 연체 인보이스 독촉 이메일 스레드(5통)를 최신순 한국어 번역. 정식
  법인명·주소가 처음 확인(`srkk.com`, 21 Woodlands Close 싱가포르)되어 [[srkk]] 등장1(CWC
  도메인/Microsoft 계정 건)과 주제가 정확히 일치. Scott Jeun(`scottjeun@thesylvangroup.com`)·
  Clara K(`Clara.K@thesylvangroup.com`, 둘 다 Sylvan Capital Management Pte. Ltd./The Sylvan
  Group 소속) 이메일이 헤더로 확정되어 등장1의 "Scott" 및 [[fy-group]] cc의 "Scott Jeun"과 동일인
  심증이 크게 강화됨. "James가 실제로 했는지 확인 후 결제" → "James가 했다고 답장"으로 최종 정리.
- 엔티티 갱신: [[srkk]] — 등장3(SRKK Consulting Pte Ltd 인보이스 스레드) 신설, 등장1의 Scott 신원
  강화 절 재구성, 등장1↔등장3 시점 관계(인보이스 납기일이 등장1 세션보다 앞서 모순 아닐 가능성) 명시.
  [[fy-group]] — Scott Jeun 동일인 심증 강화 기록 추가. [[sylvan-korea]] — "The Sylvan
  Group"/"Sylvan Capital Management Pte. Ltd."(싱가포르)와의 미확정 연결 절 신설(이름 일치 + CWC
  관여 정황, 직접 근거는 아직 없음).
- index.md 갱신 (세션1 추가, srkk 엔티티 한줄요약 갱신). AI_CONTEXT.md 갱신 없음 — 업무 맥락 절의
  "srkk 미연장 확정" 기록은 이미 [[srkk]] 페이지로 상세가 넘어가 있고, 이번 세션은 그 벤더 신원을
  구체화·강화할 뿐 새로운 확정된 결정이 아니라 40줄 예산 내에서 반영 생략.
- 특이사항: 신규 스킬 없음(순수 번역 1턴). "James"는 결제 여부를 좌우하는 핵심 인물로 처음 등장하나
  성·소속·이메일 불명이라 엔티티 미생성. Clara K가 [[2026-07-14-clara-바바라-위스키분쟁-번역]]의
  "Clara"와 동일인인지는 이번 ingest에서 그 세션을 재조회하지 않아 단정하지 않음(향후 확인 필요).

## [2026-07-16] ingest | lampas-web-product 신규앱(제품 이미지 분석+마케팅) 구현 (source: a566ac46-0364-46bd-840d-24e332e91529.md)
- 원본 보관: `raw/conversations/2026-07-16-lampas-web-product-신규앱-구현.md`
- 세션 신설: [[2026-07-16-lampas-web-product-신규앱-구현]] — 01:40~01:59 UTC. "제품 이미지 올리면 분석+
  마케팅 방향" 신규 앱을 병렬 조사(프론트 베이스 비교·백엔드 vision 관례 조사) → 스캐폴딩 → 구현 →
  검증 → 커밋push까지 한 세션에 완료. `lampas-web-ai`를 슬림 복사 베이스로 신규 앱 `lampas-web-product`
  (포트 8236) 생성, 백엔드는 기존 `products`(Prisma CRUD) 모듈과 분리한 독립 무상태 공개 모듈
  `product-insights`(`@Public()`, `GeminiService.analyzeProductMarketing()` 신설, `analyzeObjectTraits`
  패턴 재사용) 신설. 사용자가 음성입력 오인식으로 추정되는 파편화된 문장을 3회 연속 보냈으나 어시스턴트가
  추측 대신 매번 명확화 질문 → 최종 "알아서 해줘" 위임에 AIDA 프레임 광고카피+채널별 해시태그+복사버튼으로
  개선. 커밋 2건 push(`8f1b066` 제품앱, `d4c4ffd` 전날 Space 모듈 잔여분).
- 엔티티 갱신: [[lampas-studio]] — `lampas-web-product`/`product-insights` 섹션 신설(배포 미완 상태 명시,
  CloudFront ID 플레이스홀더), 관련 세션 링크·태그 추가.
- 스킬 신설: [[new-app-scaffold-from-slim-base]] — 모노레포에 새 프론트 앱+백엔드 모듈을 슬림 기존 앱
  복사로 스캐폴딩하는 절차(포트/스크립트 네이밍, 공개 엔드포인트 가드 패턴, 무관 workspace install
  실패 시 부분 검증 등). [[clone-sibling-entity-pipeline]](엔티티 복제)의 앱 레벨 자매 스킬.
- 스킬 갱신: [[deploy-sandbox-pnpm-shim]] — 샌드박스가 `/tmp`·`/var/folders`를 막을 때 shim을 저장소
  안에 만드는 함정, 저장소 전체 install이 무관한 워크스페이스 오류로 막힐 때 새 앱만 부분 검증하는
  우회 절차 추가.
- AI_CONTEXT.md 갱신: [[lampas-studio]] 항목에 `lampas-web-product` 추가+커밋 해시 반영, 배포 미완 상태
  명시(39줄 유지, 예산 내).
- index.md 갱신 (세션1, 스킬1 추가, lampas-studio 한줄요약에 앱 추가).

## [2026-07-16] ingest | 기억버튼 보관통합 + NaN토큰버그수정 (source: 7922701e-084b-431c-beee-70d528304f78.md)
- 원본 보관: `raw/conversations/2026-07-16-기억버튼-보관통합-NaN토큰버그수정.md`
- 세션 신설: [[2026-07-16-기억버튼-보관통합-NaN토큰버그수정]] — 02:36~02:41 UTC, 2왕복. (1) 상단
  "기억에 저장" 버튼을 "기억에 보관"으로 개명하고 호출 대상을 저장-only `/api/remember`에서 기존
  보관 API `/api/sessions/archive`로 교체(새 엔드포인트 신설 없음) — 아카이빙+memory-ingest 큐잉+새
  대화 전환이 버튼 하나로 통합. (2) 사용량 배지 "◔ NaN 토큰" 표시 버그: `usage: null`을
  `Object.assign({}, null, {...})`로 병합해 필드 결손 → NaN. `loadUsage()`/`updateUsage()` 두 곳
  방어적 수정.
- 엔티티 갱신: [[lampas-harness]] — 2026-07-16 섹션 신설(버튼 통합+NaN버그), 보관 자동화 절 세션
  링크에 이번 세션 추가, "관련" 세션 목록 갱신.
- 토픽 갱신: [[long-term-memory-architecture]] — "저장 진입점 3개" 서술에 갱신 절 추가: 진입점
  (1)수동 버튼과 (2)보관 자동이 UI 레벨에서 동일 동작으로 수렴(엔드포인트는 백엔드에 둘 다 남아
  있으나 UI에서 저장-only 경로 소멸), 옛 `/api/remember` 잔존 여부는 미확인으로 명시.
- 스킬 신설: [[null-merge-nan-display]] — `Object.assign({}, possiblyNull, {...})` null 병합이
  필드를 못 채워 산술 연산이 NaN으로 새는 버그 진단·수정 절차(생성측+표시측 이중 방어). 유사하지만
  다른 버그인 [[loading-state-key-mismatch]](키 불일치)와 구분 기준 명시.
- AI_CONTEXT.md 갱신: [[john-wiki]] 항목의 "저장 진입점 3개" 서술을 버튼 개명+통합 사실에 맞게
  수정(38줄 유지, 예산 내).
- index.md 갱신 (세션1, 스킬1 추가).

## [2026-07-16] ingest | 음성입력 3-4초 잘림 수정 (source: d2db463c-ceb1-486b-a8db-19586dddfb55.md)
- 원본 보관: `raw/conversations/2026-07-15-음성입력-3-4초-잘림-수정.md`
- 세션 신설: [[2026-07-15-음성입력-3-4초-잘림-수정]] — 15:29:49~15:33:37 UTC, 3턴. 음성입력 앞부분
  3~4초 잘림 신고 → 원인(토큰발급+WebSocket 연결 완료 전까지 마이크 미캡처) 분석 → 마이크 선오픈+
  로컬 버퍼(`pending`)+연결후 플러시로 수정, `build:web` 반영. 커밋은 이 대화 내에서 미완료로 종료.
- ⚠️ 모순 기록: 이 소스의 원인·해결책 서술이 기존 [[2026-07-15-gpt-realtime-음성입력-길게누르기]]
  세션의 "6번 항목"(22:28~22:30, 커밋 `938d4e4`)과 사실상 동일한데, 시각·커밋 여부가 다름(이 세션은
  15:29~15:33·커밋 미언급). 같은 편집 사건이 서로 다른 아카이브 파일에 다른 시각으로 기록된
  이상 정황으로 추정, [[2026-07-14-clara-바바라-위스키분쟁-번역]]에서 관찰된 것과 같은 계열. 기존
  세션 내용은 수정하지 않고 양쪽에 상호 참조 각주만 추가.
- 세션 갱신: [[2026-07-15-gpt-realtime-음성입력-길게누르기]] — "⚠️ 다른 소스와의 중복/모순" 절 추가.
- 스킬 갱신: [[realtime-voice-mic-buffer-before-connect]] — 출처 절에 중복 소스 각주 추가.
- index.md 갱신 (세션 1 추가).
- AI_CONTEXT.md는 변경 없음 — 이미 반영된 기능(마이크 버퍼링) 외 새로운 지속적 사실 없음, 아카이브
  이상 정황은 세션 레벨 상세로 충분.

## [2026-07-16] ingest | Auto 모델 난이도 자동선택 최초 구현 (source: 31bf215a-c71a-44f7-bd1d-a04a26dad4f2.md)
- 원본 보관: `raw/conversations/2026-07-15-auto모델-기능-최초구현.md`
- 세션 신설: [[2026-07-15-auto모델-기능-최초구현]] — 2026-07-15 22:42~22:52 UTC. "Auto 기능 넣어서 난이도별
  모델 자동선택 만들어줘, 판정 모델은 재량껏" 요청 → 모델 드롭다운 "Auto" 옵션 최초 구현(커밋 `802af89`,
  v0.1.25). **[[2026-07-15-auto모델-난이도판정-확인ux-개선]](같은 날 23:23~23:31, 31분 후)이 file:line
  감사한 "기존 Auto 로직"의 실제 원본 구현 세션임을 확인** — 시간순 재구성.
- 핵심 신규 사실: 판정 1순위로 고른 Claude Haiku 4.5(API)가 **`ANTHROPIC_API_KEY` 크레딧 잔액 0으로
  400 거절**됨을 검증 중 발견 — 기존 [[sdk-claude-code-vs-api-billing]]·[[long-term-memory-architecture]]가
  기록해 온 "크레딧 소진이 `/compact`·`memory-ingest`를 실패시킨다"는 관찰의 근본 원인이 처음 명시적으로
  확정됨. 이에 따라 API→[[rapid-mlx]] 로컬 LLM→휴리스틱 3단 폴백 체인 설계, 실사용 예시 7건으로 로컬
  판정 7/7 검증(초기 오분류는 few-shot 보정으로 수정).
- 스킬 신설: [[llm-judge-fallback-chain]] — LLM 분류/라우팅 판정을 유료 API→로컬 LLM→휴리스틱으로 저하
  설계하는 재사용 절차(모호하면 상위 티어, 최상위 티어는 안전망 도달 불가, few-shot 검증).
- 엔티티 갱신: [[lampas-harness]] (Auto 최초 구현 절 신설, 감사 세션과 순서 명확화), [[rapid-mlx]]
  (Auto 판정의 실질적 1순위 경로가 됨을 명시).
- 토픽 갱신: [[model-selection]] (Auto 절에 최초 구현 세션·판정모델 선택 근거·크레딧 0 확인·검증
  방법론 반영).
- 스킬 갱신: [[sdk-claude-code-vs-api-billing]] (크레딧 소진 근본원인이 "잔액 0"임을 이 세션 근거로 확정).
- AI_CONTEXT.md 갱신: [[lampas-harness]] 줄에 "Haiku 4.5 API 판정이 크레딧 잔액 0으로 실패 중, rapid-mlx가
  실질 경로" 한 문장 추가. 38줄(<40 준수).
- index.md 갱신 (세션1·스킬1 추가).
- 특이사항: 이 세션과 [[2026-07-15-auto모델-난이도판정-확인ux-개선]]은 같은 날 31분 간격의 구현→감사
  관계로, 기존 위키가 감사 세션만 기록하고 있던 것을 원본 구현 세션으로 보강한 사례. 두 세션 모두 원본
  내용은 수정하지 않고 상호 참조만 추가.

## [2026-07-16] ingest | "내 말이 잘 들려?" 대화 테스트 확인 세션 (source: 5bfe8481-a923-4a56-86bf-f6b34bc03f1f.md)
- 원본 보관: `raw/conversations/2026-07-15-대화테스트-확인.md`
- 세션 신설: [[2026-07-15-대화테스트-확인]] — 2026-07-15 22:51:46~22:52:06 UTC, 1왕복 초단타. "내 말이
  잘 들려?" → "네, 잘 들립니다" → "대화 테스트를 하려고 해" → 어시스턴트가 테스트 종류(음성 입력/모델
  선택/일반 텍스트/기능 확인)를 되물은 데서 소스 종료, 사용자 답 없음.
- index.md 갱신 (세션1 추가). 신규 엔티티/토픽/스킬 없음 — [[2026-07-15-대화-가능-확인]]과 동일한 유형의
  콘텐츠 가치 없는 핑(ping) 세션.
- AI_CONTEXT.md 갱신 없음 — 반영할 핵심 사실 없음.
- 특이사항: 시간대가 같은 날 [[2026-07-15-gpt-realtime-음성입력-길게누르기]] 세션(12:37~22:55)의 마지막
  항목(22:53~22:55) 직전과 겹친다. 문구·어시스턴트가 먼저 제시한 "음성 입력 테스트" 선택지로 미루어
  구현 중이던 음성입력 기능을 별도 quick 채팅에서 시험해보려 한 시도로 추정되나, 소스 자체엔 이를
  뒷받침하는 직접 근거(전사 결과·음성 사용 흔적)가 없어 추정으로만 세션 페이지에 기록하고 단정하지 않음.

## [2026-07-16] ingest | dark-upbit/toss 트레이딩앱 기능개발·배포 (source: 88e6cc29-d646-4e0b-980c-03cfbe2efa6d.md)
- 원본 보관: `raw/conversations/2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포.md`
- 세션 신설: [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] — 2026-07-15 21:52~22:38 UTC,
  [[dark-system]] 내 dark-upbit-api/web·dark-toss-api/web 4앱을 넘나든 장시간 코딩 세션(7커밋,
  main `4f306fa..9033b73`). Upbit 초단타(scalping) 스타일을 전면차단→자동전환만제외+수동허용으로
  전환하고 엔진을 `ai`→`signal`로 바꿔 구조적으로 LLM 호출 차단, 모바일 표 가로스크롤+확대방지(두 웹),
  LLM 모델 카탈로그 날짜 스냅샷 필터(두 API), Toss 국장/미장 자동 장이동에 "양쪽 마감 시 자동판단
  일시정지·개장 시 재개" 추가, 초기화 시 LLM 비용 집계도 초기화(두 API). 매 기능 단위로 배포+커밋+검증
  반복.
- 엔티티 신설: [[dark-upbit-api]], [[dark-upbit-web]], [[dark-toss-web]] — 이 세션에서 [[dark-system]]
  내 4개 앱 구성이 위키에 처음 확인됨(이전엔 [[dark-toss-api]]만 조사돼 있었음, 그 조사 세션은 이
  세션보다 26분 뒤인 23:04에 있었음).
- 엔티티 갱신: [[dark-toss-api]] (자동 장이동 마감 일시정지/재개 구현 이력·모델 카탈로그 필터·초기화
  비용 초기화 반영), [[dark-system]] (확인된 앱 4개로 갱신)
- 스킬 신설: [[trading-feature-flag-auto-exclude-manual-allow]] — "자동은 막되 수동은 허용" 요청 시
  플래그 개념 전환(전면차단→자동전용제외)+엔진교체로 구조적 위험경로(LLM 호출) 차단+수동선택 보호
  3단계 패턴.
- 스킬 갱신: [[deploy-sandbox-pnpm-shim]] (dark-system 4앱 배포로 저장소 비특정적임 재확인, gitignore된
  프로덕션 env 파일 복구 함정 추가), [[config-flag-gate-audit]] (이 세션에서 구현된 기능을 "부가 기능에
  딸린 안전장치" 함정의 구체 사례로 역참조)
- 토픽 갱신: [[works-project-portfolio]] (dark-system 행에 4앱 기능개발 세션 반영, 관련 엔티티·세션 링크 보강)
- AI_CONTEXT.md 갱신: 진행 중 프로젝트의 dark-system 줄을 "dark-toss-api만 확인"에서 "트레이딩 봇 4앱
  확인(upbit/toss 자매 앱)"으로 갱신. 38줄(<40 준수, 줄 수 불변으로 교체).
- index.md 갱신 (세션1·엔티티4(신설3+갱신 표기)·스킬1 추가, dark-system 관련 엔티티 한 줄 요약 갱신).
- 특이사항: 이 세션(21:52~22:38)이 시간상 [[2026-07-15-dark-toss-api-장전매수-코드조사]](23:04~)보다
  **먼저** 있었으나, 위키엔 나중 세션이 먼저 ingest돼 dark-system·dark-toss-api 엔티티가 이미 존재했음
  — 이번 ingest로 실제 시간 순서를 각 페이지에 명시하고, 두 세션이 서로 모순되지 않고 보완 관계임을
  확인(자동 장이동 마감 일시정지 기능은 이 세션에서 구현된 것을 나중 조사 세션이 그대로 관찰한 것).
  "모델 선택에 날짜가 붙은 긴 모델명" 요청이 처음엔 하네스 자체 `/model` 목록인지 트레이딩앱 LLM
  드롭다운인지 모호해 어시스턴트가 두 차례 되물은 뒤 진행됨 — 세션 페이지에 사실 그대로 기록.

## [2026-07-16] ingest | 세션 상주 프로세스로 백그라운드 작업 생존 구현 (source: 1508d8bd-39d4-4150-92e7-c957261638fa.md)
- 원본: `raw/conversations/2026-07-15-세션상주프로세스-백그라운드작업생존-구현.md`
- 세션 신설: [[2026-07-15-세션상주프로세스-백그라운드작업생존-구현]] — 채팅 중 Agent/백그라운드
  Bash/Workflow가 턴 종료로 끊기던 버그를, 턴마다 서브프로세스를 새로 만들던 구조를 **대화당 상주
  Claude 프로세스**(유휴 10분 생존, 완료 시 모델 자동 재개)로 교체해 근본 수정. 백그라운드 작업
  상태 SSE/`/api/queue` 노출 + 개별 중지 API + 활동 표시줄 UI. 커밋 `d20b439`, 재시작은 지연·분리
  실행으로 안전하게 배포.
- 토픽 갱신: [[harness-background-process-lifecycle]] — 예약 타이머·다운로드에 이어 **증상 3(채팅
  Agent/Workflow)**을 추가하고, 이번엔 우회가 아니라 근본 아키텍처로 해결됐음을 명시(일반 원칙에
  "상주 프로세스가 유휴 10분까지 자동 생존" 갱신).
- 엔티티 갱신: [[lampas-harness]] — "추가 기능 (2026-07-15 세션 — 세션 상주 Claude 프로세스)" 섹션
  신설, 관련 세션 링크 추가.
- 스킬 갱신: [[deploy-sandbox-pnpm-shim]] (인자 스캐너 함정의 새 변종 — 커밋 메시지 슬래시 문자열도
  차단됨을 추가), [[self-hosted-agent-server-ops]] (함정2 restart-kills-own-turn 대응이 이 세션에서도
  정확히 지켜졌음을 재확인 사례로 추가).
- index.md 갱신 (세션 1개 추가).
- 특이사항: 이 세션은 [[harness-background-process-lifecycle]]이 규명한 "턴은 배경작업보다 수명이
  짧다" 원칙이 예약 타이머·다운로드에 이어 채팅 자체에도 적용된 사례이자, 처음으로 우회(detach/서버
  내장화)가 아니라 근본 아키텍처 변경으로 해결한 사례.

## [2026-07-16] ingest | lampas-system 구조 분석 + web-ai 프롬프트분할·샷변경·되돌리기 + Space 설계 (source: 2465ae9d-d81c-4e29-ad4d-aa7f7ce8513f.md)
- 원본 보관: `raw/conversations/2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계.md`
- 세션 신설: [[2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계]] — 2026-07-15 11:39~12:26 UTC.
  ① lampas-system(9앱 모노레포) 구조 분석, CLAUDE.md 문서-실제 괴리 3건 발견. ② `lampas-web-ai`가
  사용자 지정으로 주요 앱 승격, 구조 정밀 분석(actorFlow.ts 단일 2,658줄 상태머신 3개). ③ SDK의 Object
  프롬프트 6필드 분할 구조를 web-ai에 이식(프롬프트 항목별 수정 칩), 배포(ai.lampas.io)+커밋 2건
  (`05cda59`/`19b95e8`). ④ Object 촬영 "샷 변경" 칩 추가, 배포+커밋 `77e1b69`. ⑤ 채팅 되돌리기("이전"
  명령+메시지별 ✏️ 재시작, 상태 스냅샷 방식) 구현, 배포+커밋 `4e0fe77`. ⑥ Space(공간) 신규 엔티티 설계
  (category='space' 재사용안, spaceType/timeOfDay/lighting/mood/description 5필드) — 하네스 권한 채널
  "Stream closed" 장애로 구현 착수 직전 세션 종료, **코드 변경 없음**.
- 엔티티 신설: [[lampas-web-ai]] (구조·핵심 동작 방식·2026-07-15 기능 3종 상세)
- 엔티티 갱신: [[lampas-studio]] (앱 구성·CLAUDE.md 괴리 3건 추가, Space 섹션에 "선행 설계와의 차이" 절
  추가 — 이 세션의 최소변경(category 재사용) 설계가 저녁 세션의 독립 `Space` 모델 신설과 다름을 병기)
- 세션 갱신: [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]] (같은 날 23:11~ 세션 상단에 이 선행
  세션과의 관계 참고 추가 — 시간상 앞서지만 이 세션이 그 설계를 참조하지 않고 재조사했음을 명시)
- AI_CONTEXT.md 갱신: [[lampas-studio]] 줄에 `lampas-web-ai` 주요 앱 승격·actorFlow.ts 단일파일 위험·
  기능 3종 배포 사실 추가. 39줄(<40 준수).
- index.md 갱신 (세션1·엔티티1 추가)
- 특이사항: 이 소스는 같은 날 저녁(23:11~) 세션보다 11시간 이상 앞서지만 늦게(2026-07-16) ingest됨 —
  두 세션 모두 "Space가 코드베이스에 없다"를 독립적으로 확인했고 설계 전략이 달라 모순이 아니라 순차
  탐색으로 기록. "Stream closed" 권한 채널 장애는 이 세션에서도 2회 재발(기존 [[lampas-harness]] 함정과
  일치, 다음 사용자 메시지로 자연 복구) — 별도 갱신 불필요할 만큼 이미 잘 문서화된 패턴.

## [2026-07-16] ingest | memory-ingest 크레딧 버그 근본 수정 + 실패 35건 재처리 (source: e2f0f24a-b483-4030-956b-e2c1fa11d8e3.md)
- 원본 보관: `raw/conversations/2026-07-16-메모리인제스트-크레딧버그-근본수정.md`
- 세션 신설: [[2026-07-16-메모리인제스트-크레딧버그-근본수정]] — 02:42~02:48 UTC. "보관해도 위키 저장이
  안 되는 것 같다" 제보 → `queue/failed/`에서 07-16 08:33 이후 `memory-ingest` 잡 전부 "Credit balance
  is too low" 실패 확인 → 근본 원인: 큐 실행기 `src/runner.ts`가 인터랙티브 채팅(`server.ts`의
  `claudeAuthEnv()`)과 달리 `ANTHROPIC_API_KEY` 제거 처리가 없어 크레딧 0 키로 API 종량 시도 후 실패.
  07:27 이후 보관 35건이 이 버그로 미반영 누적. 수정: (1) `runner.ts`에 동일 키 제거 처리 추가, (2)
  야간 안전망 잡(`memory-ingest-daily.job.json`)이 `chats/` 최상위만 훑던 것을 `chats/archive/`까지
  확장. 데몬 재시작(사용자 확인 후, 반복 "재처리해줘" 7회)+실패 35건 재큐잉 완료.
- 엔티티 갱신: [[lampas-harness]] (2026-07-16 세션 절 신설 — runner.ts 수정·잡 범위 확장·재시작 내역)
- 토픽 갱신: [[long-term-memory-architecture]] ("저장 실패 모드 — API 크레딧 소진" 절에 근본원인 규명·
  수정 내용 추가, 2026-07-15 관찰의 후속 해결로 명시)
- 스킬 갱신: [[sdk-claude-code-vs-api-billing]] (크레딧 소진 파급 범위의 실제 사례로 이 세션 추가, "채팅
  경로 수정이 다른 진입점에 자동 전파되지 않는다"는 교훈 신설), [[self-hosted-agent-server-ops]] (함정2에
  반복 확인요청 대응 사례 추가 — 같은 요청이 여러 번 재전송되면 응답 전달 레이스를 의심하고 재시도)
- AI_CONTEXT.md 갱신: [[john-wiki]] 줄에 이 수정 사실 추가. 38줄(<40 준수).
- index.md 갱신 (세션 1개 추가)
- 특이사항: [[sdk-claude-code-vs-api-billing]]과 [[long-term-memory-architecture]]가 2026-07-15에
  "크레딧 소진이 memory-ingest도 실패시킨다"를 관찰만 하고 정확한 원인은 미확인으로 남겨뒀던 것을,
  이 세션이 코드 레벨에서 완전히 규명하고 수정까지 완료한 후속 사례.

## [2026-07-16] ingest | SRKK 인보이스 스레드 중복 재노출 (source: ddafacd1-4636-4273-be17-946127d5c923.md)
- 원본 보관: `raw/conversations/2026-07-16-srkk-인보이스-james확인-0243.md`
- 세션 신설: [[2026-07-16-srkk-인보이스-james확인-0243]] — 2026-07-16 02:43 UTC, 1왕복 quick 채팅.
  기존 [[2026-07-16-srkk-인보이스-james확인]](02:15 UTC, 아카이브 `quick-58839510-...`)과 사용자
  메시지(이메일 스레드 5통)가 글자 단위로 100% 동일. 어시스턴트 응답만 독립 재생성되어 형식이
  다름. [[2026-07-14-clara-바바라-위스키분쟁-번역]]·[[2026-07-15-음성입력-3-4초-잘림-수정]]과 같은
  계열의 아카이브 재노출/중복 quick 채팅 이상 정황으로 추정.
- 세션 갱신: [[2026-07-16-srkk-인보이스-james확인]] — "⚠️ 다른 소스와의 중복" 절 추가, 상호 참조.
- 엔티티/토픽/스킬: 신규·변경 없음 — [[srkk]] 등장3 정보는 이미 원 세션에 전부 반영됨, 새 사실 없음.
- AI_CONTEXT.md: 변경 없음 — 지속적 핵심 사실 추가 없음(중복 정황은 세션 레벨 상세로 충분).
- index.md 갱신 (세션 1개 추가).

## [2026-07-16] ingest | tts-stream(ElevenLabs) 구현 착수 (source: b94bb46c-e6f6-419d-a6dc-3c7a21ea0a03.md)
- 원본 보관: `raw/conversations/2026-07-16-tts-stream-elevenlabs-구현착수.md`
- 세션 신설: [[2026-07-16-tts-stream-elevenlabs-구현착수]] — 봇 메시지를 ElevenLabs로 실시간 읽어주는
  `tts-stream` 기능 요청. 참고 예시 `dbs/talk-system`(=[[toktalk]])은 작업 폴더 밖이라 [[work-folder-sandboxing]]
  가드가 읽기조차 차단(읽기 전용 우회 시도도 실패 — SDK 레벨 폴더 제한으로 재확인). 사용자 무응답으로
  어시스턴트가 표준 ElevenLabs 스트리밍 API(모델 `eleven_flash_v2_5`, 보이스 env `ELEVENLABS_VOICE_ID`
  오버라이드)로 독자 구현 착수. "server.ts에 ElevenLabs 상수 추가" 직후 소스 종료 — **실제 구현·커밋
  완료 여부 미확인**.
- 엔티티 갱신: [[lampas-harness]] (제안된 기능 절 신설 — tts-stream 상태 미확인), [[toktalk]] (참고
  예시로 지목됐으나 접근 실패 절 신설)
- 스킬 갱신: [[work-folder-sandboxing]] (파일 도구 차단이 하네스 가드보다 아래, SDK 레벨까지 내려간다는
  디테일 보강)
- AI_CONTEXT.md 갱신: [[lampas-harness]] 줄에 tts-stream 착수·미확인 상태 한 줄 추가. 38줄(<40 준수).
- index.md 갱신 (세션 1개 추가).
- 특이사항: 원문에 동일한 사용자 요청 텍스트가 6회 그대로 반복 등장(뒤 5회는 40초 안에 몰림) —
  [[idempotent-message-retry]]의 msgId 멱등성이 실행 중복은 막은 것으로 보이나, 트랜스크립트 자체엔
  중복 항목이 그대로 남음. [[2026-07-16-srkk-인보이스-james확인-0243]] 계열의 로그/아카이브 중복
  이상 정황과 유사해 세션 페이지에 병기.

## [2026-07-16] ingest | 롱프레스설정-자동음성재생-구현 (source: ae5121bf-92a9-424a-97ef-1ef1b498adc4.md)
- 원본을 raw/conversations/2026-07-16-롱프레스설정-자동음성재생-구현.md로 보관.
- wiki/sessions/2026-07-16-롱프레스설정-자동음성재생-구현.md 신규 작성.
- wiki/entities/lampas-harness.md 갱신: 신규 "배경 롱프레스 설정 열기 + 자동 음성 재생" 섹션 추가,
  tts-stream(ElevenLabs) 섹션에 이 세션과의 관계(대체/병행 미확정) 교차주석.
- wiki/skills/destructive-action-inline-confirm.md 갱신: 변형B 메커니즘이 비파괴적 트리거(설정 열기)로도
  재사용된 사례를 "관련 패턴" 절로 추가.
- AI_CONTEXT.md lampas-harness 항목 갱신 (tts-stream 미확인 상태 + 이 세션의 완결된 대안 병기).
- index.md 갱신 (세션 1개 추가).
- 특이사항: 이 세션이 목표(봇 답변 읽어주기)는 같지만 방식(브라우저 Web Speech API vs ElevenLabs
  스트리밍)이 다른 별개 구현을 완성·커밋까지 마쳐, 약 1시간 전 미완 상태로 끝난
  [[2026-07-16-tts-stream-elevenlabs-구현착수]]와의 관계(대체 vs 병행)가 불명확한 채로 남음 — 관련
  페이지 3곳에 명시적으로 병기.

## [2026-07-16] ingest | 봇메시지-클릭읽어주기-커밋푸시 (source: quick-32526692-0042-4430-865f-ae6128ee1420.md)
- 원본을 raw/conversations/2026-07-16-봇메시지-클릭읽어주기-커밋푸시.md로 보관.
- wiki/sessions/2026-07-16-봇메시지-클릭읽어주기-커밋푸시.md 신규 작성 — "모두 commit 해줘"→
  "푸시 해줘" 2왕복 quick 운영 세션(09:21~09:22 UTC). 구현 코드는 소스에 없음, diff 요약만 확인.
- wiki/entities/lampas-harness.md 갱신: 신규 "봇 말풍선 클릭 읽어주기/정지 토글 + 충돌마커 정리,
  v0.1.36" 섹션 추가(커밋 `4cff973`→rebase→`9e45de9`), 관련 세션 링크 보강.
- wiki/skills/rebase-local-feature-onto-refactored-remote.md 갱신: "충돌 마커 전체 검색" 단계가
  과거 실제로 누락됐던 사례를 함정으로 추가, 빌드 통과와 별개로 grep 검색을 명시적 단계로 취급하도록 보강.
- AI_CONTEXT.md 갱신: lampas-harness 줄에 세 번째 TTS 트리거(클릭 읽어주기, v0.1.36) + 버전 간극
  관찰 한 문장 추가. 38줄(<40 준수).
- index.md 갱신 (세션1 추가).
- 특이사항: 이 세션은 순수 커밋/푸시 운영이라 "봇 말풍선 클릭 읽어주기" 기능의 실제 구현 방식
  (Web Speech API vs ElevenLabs `tts-stream`)을 판별할 근거가 소스에 없음 — 같은 날의 두 TTS
  시도([[2026-07-16-tts-stream-elevenlabs-구현착수]] 미완·[[2026-07-16-롱프레스설정-자동음성재생-구현]]
  완료)와의 관계를 세 갈래 병기, 확정하지 않음. 위키에 기록된 직전 버전(0.1.30)과 이 세션의 버전
  (0.1.36) 사이 5개 버전 간극도 원인 미확정으로 병기(누락 ingest 가능성 플래그).

## [2026-07-16] ingest | 봇메시지-클릭읽어주기-구현 (source: ed6c6f76-b3aa-41b5-861b-72de11e3b96d.md)
- 원본을 raw/conversations/2026-07-16-봇메시지-클릭읽어주기-구현.md로 보관.
- wiki/sessions/2026-07-16-봇메시지-클릭읽어주기-구현.md 신규 작성 — 09:10~09:17 UTC, 3왕복.
  [[2026-07-16-봇메시지-클릭읽어주기-커밋푸시]](09:21~09:22, 4분 뒤)가 diff만 보고 커밋했던
  기능의 실제 구현 세션. 기존 `speak()`/`stopSpeech()` Web Speech API 인프라를
  `speak(md, bubble)`로 확장·재사용해 봇 말풍선 클릭 읽기/재클릭 정지 토글 구현, 링크·이미지
  클릭 예외 처리. 부수적으로 직전 병합 커밋 `dca1422`가 package.json·index.html에 남긴 충돌
  마커로 빌드가 깨져 있던 것 발견·수정(package.json 0.1.32, index.html CATEGORIES 양쪽 병합).
  Playwright 실브라우저 검증 완료, 이 세션 끝에서는 커밋하지 않고 사용자에게 위임.
- wiki/sessions/2026-07-16-봇메시지-클릭읽어주기-커밋푸시.md 갱신: "구현 방식 미상"으로 남겨뒀던
  두 지점(요약·관찰 섹션)에 취소선 + "[해소: ...]" 표기로 Web Speech API 확정 사실 명시(원문은
  보존, 덮어쓰지 않음).
- wiki/entities/lampas-harness.md 갱신: ElevenLabs tts-stream 섹션에 "클릭 읽어주기는 이 경로가
  아님" 해소 노트 추가, v0.1.36 섹션을 구현 세션 발견 내용으로 재작성(충돌마커 원인 `dca1422`
  특정, 카테고리 병합 방식, Playwright 검증 명시), 세션 링크 목록에 신규 세션 추가.
- wiki/skills/rebase-local-feature-onto-refactored-remote.md 갱신: 함정7 각주의 소스를
  커밋푸시 세션→구현 세션으로 교체(실제 마커 발견 지점), dca1422 커밋 특정.
- AI_CONTEXT.md 갱신: lampas-harness 줄의 "구현 방식 미확인" 표현을 "Web Speech API로 확정"으로
  수정 + dca1422 충돌마커 발견 한 문장 추가. 38줄(<40 준수).
- index.md 갱신: 구현 세션 신규 추가, 커밋푸시 세션 요약에 해소 사실 반영.
- 결론: 같은 날 있었던 TTS 관련 세 시도의 관계가 이번 ingest로 명확해짐 — ①ElevenLabs
  tts-stream(완료 미확인, 독립) ②Web Speech API 자동재생(완료, 3d16ed5) ③Web Speech API를
  재사용한 클릭 읽어주기(완료, 4cff973→9e45de9) — ①은 ②③과 무관, ③은 ②의 확장.

## [2026-07-16] ingest | 잔여작업 커밋·원격 머지(dca1422)·재시작요청 (source: quick-7fe2aec6-0083-4544-8cc2-83047933cefd.md)

- 새 세션 페이지: [[2026-07-16-잔여작업커밋-원격머지-재시작요청]] — presets 폴더 삭제 커밋
  `7b27897` → `git pull`+`merge`로 3충돌("원격 우선" 일괄) 해결 → 병합 커밋 **`dca1422`** 생성·
  push(09:07~09:08) → "재시작해줘" 요청엔 안내만(실행 미확인, [[self-hosted-agent-server-ops]]
  함정2 패턴).
- **핵심 발견**: 어시스턴트가 "완벽합니다. 모든 충돌이 해결되었어요"라고 자체 보고했으나 실제로는
  `package.json`·`apps/web/public/index.html`에 충돌 마커가 남은 채 push됨 — 이 버그는 같은 날
  더 늦게(09:10~09:22) [[2026-07-16-봇메시지-클릭읽어주기-구현]]·
  [[2026-07-16-봇메시지-클릭읽어주기-커밋푸시]] 두 세션에서 "직전 병합 커밋 dca1422가 원인"으로만
  간접 언급됐던 것인데, 이번 ingest로 그 병합 세션의 실제 경위(merge 선택·검증 생략·거짓 완료
  보고)가 확인됨.
- 갱신: wiki/entities/lampas-harness.md(v0.1.36 섹션에 dca1422 생성 경위 추가, 세션 링크 추가) ·
  wiki/skills/rebase-local-feature-onto-refactored-remote.md(함정7 각주에 원본 세션·"완벽 해결"
  거짓 보고 반례 추가, updated 필드 보강) · wiki/sessions/2026-07-16-봇메시지-클릭읽어주기-구현.md·
  -커밋푸시.md(취소선+[해소] 표기로 마커 원본 커밋 특정 사실 반영, 원문 보존) · index.md(신규 세션
  추가, 관련 두 세션 요약에 교차링크 보강).
- raw/conversations/2026-07-16-잔여작업커밋-원격머지-재시작요청.md 보관(원본 아카이브 파일명:
  `quick-7fe2aec6-0083-4544-8cc2-83047933cefd.md`).
- AI_CONTEXT.md는 변경 안 함 — 이 사실은 위키 세션·엔티티·스킬 페이지로 충분히 커버되는 일회성
  운영 사건(거짓 완료 보고 반례)이라 40줄 한도 내 핵심 요약에는 포함하지 않음.

## [2026-07-16] ingest | "사용 영역" 페르소나 기능 전면 폐기 (source: 9592b195-a694-4bc6-a9e7-06da0d3d7038.md)

- 새 세션 페이지: [[2026-07-16-사용영역-페르소나-폐기]] — 04:26~04:32Z. 사용자가 "사용영역을
  일반/이미지만 남기고 페르소나 이제 쓰지 마" 지시 → 병렬 조사 에이전트로 6영역 페르소나
  시스템 아키텍처 전수 파악(프론트 이중 파일 `apps/web/index.html`+`public/index.html`,
  백엔드 `src/server.ts` 3개 주입 경로, `/api/presets` API, `presets/*.md` 저장소) 후 전부 삭제.
- **핵심 연결**: 이 세션이 그동안 "presets 폴더 삭제"라고만 기록돼 있던 커밋 `7b27897`
  ([[2026-07-16-잔여작업커밋-원격머지-재시작요청]] 09:07 세션에서 커밋됨)의 **실제 작업
  원본**임이 확인됨 — 그 세션엔 결과 커밋만 있고 무엇을 왜 지웠는지가 없었는데, 이번 ingest로
  전체 경위가 채워짐.
- 갱신: wiki/topics/use-area-personas.md(⚠️ 폐기 배너 + "폐기(2026-07-16)" 절 추가, 기존
  6영역 설계 본문은 과거 기록으로 보존) · wiki/entities/lampas-harness.md(신규
  "기능 폐기 (2026-07-16 세션 04:26~04:32)" 절 추가, 2026-07-08 페르소나 항목에 폐기 각주,
  관련 세션 링크 보강).
- 신규 스킬: wiki/skills/full-stack-feature-removal-audit.md — 이중관리 프론트+백엔드 여러
  실행경로에 흩어진 기능을 잔재 없이 제거하는 조사→삭제(UI→API→로직→저장소)→재grep 검증
  절차. 이 저장소의 "프론트 이중 파일 비동기화" 함정과 "값은 전송되지만 무해"/"동명이의 코드"
  두 오판 주의사항 포함.
- raw/conversations/2026-07-16-사용영역-페르소나-폐기.md 보관(원본 아카이브 파일명:
  `9592b195-a694-4bc6-a9e7-06da0d3d7038.md`).
- index.md 갱신: 신규 세션·스킬 추가, use-area-personas 토픽 한 줄 요약에 폐기 사실 반영.
- AI_CONTEXT.md는 변경 안 함 — 페르소나 기능은 애초에 AI_CONTEXT 요약에 언급된 적 없고
  (모델선택·과금·백그라운드생존 등 더 큰 항목들로 40줄 한도가 이미 꽉 참), 이 폐기 사실은
  토픽·엔티티 페이지로 충분히 조회 가능해 상시 주입 요약에는 포함하지 않음.

## [2026-07-16] ingest | 람파스 개발일지 블로그 초안 + SEO 태그 (source: d9c24760-325f-4104-8566-8a857d598b71.md)
- raw/conversations/2026-07-16-람파스개발일지-블로그-seo태그.md 보관(원본 아카이브 파일명:
  `d9c24760-325f-4104-8566-8a857d598b71.md`).
- 세션 요약 신설: wiki/sessions/2026-07-16-람파스-개발일지-블로그-seo최적화.md — "지난주 진행한 것
  블로그로 써줘" 요청에 어시스턴트가 git 커밋 47개(7/9~7/16)를 직접 읽어 4주제 개발일지 초안 작성 →
  SEO 최적화·태그 30개 후속. 발행은 이 트랜스크립트 안에서 미확인.
- 신규 스킬: wiki/skills/dev-log-blog-from-git-log.md — 원문 없이 요청받았을 때 git 커밋 로그로
  블로그 개발일지 초안을 쓰는 절차(주제 클러스터링→개발일지 톤→플랫폼 제약 반영→SEO 후속→발행은
  별도 확인).
- wiki/skills/naver-blog-tag-seo.md 갱신: "본문을 사람에게 받아야 한다"는 전제가 기존 발행 글에만
  적용되고, 어시스턴트 자신이 새로 쓰는 글에는 적용되지 않는다는 갈래 추가.
- wiki/entities/naver-blog-mcp.md·wiki/entities/progdesigner.md 갱신: `study-ai-what` 연재가
  아키텍처 소개 1편에서 주간 개발일지 형식으로 확장된 정황 반영.
- index.md 갱신: 신규 세션·스킬 라인 추가.
- AI_CONTEXT.md는 변경 안 함 — 기존 "블로그/SEO 태그 요청 반복" 서술이 이미 이 패턴을 포괄하고,
  이번 세션의 실제 발행 여부도 미확인이라 상시 요약에 추가할 만한 확정 사실이 없음.

## [2026-07-16] ingest | launchctl 불필요 서비스 점검 (source: quick-d10bd265-ef3c-4a3d-8872-b36ee8ddabaf.md)
- raw/conversations/2026-07-16-launchctl-불필요서비스-점검.md 보관(원본 아카이브 파일명:
  `quick-d10bd265-ef3c-4a3d-8872-b36ee8ddabaf.md`).
- 세션 요약 신설: wiki/sessions/2026-07-16-launchctl-불필요서비스-점검.md — `launchctl list` 전체
  출력(약 560줄) 붙여넣고 "불필요한 건 없는지 판단해봐" 1왕복. watchman/Adobe CC/OneDrive 업데이터를
  제거 후보로, lampas-harness·rapid-mlx·redis·postgresql을 필수 유지로 분류. 되물음("확인해볼까요?")
  이후 사용자 응답 없이 소스 종료 — 실제 실행 여부 미확인. 콘텐츠 가치 낮음.
- 신규 토픽: wiki/topics/macos-launchctl-cleanup-candidates.md — 위 분석을 재사용 가능한 참고 목록으로
  정리(다음 정리 시 출발점).
- wiki/entities/lampas-harness.md 갱신: Redis/PostgreSQL 실사용 여부가 이 하네스 소스에서 미확인이라는
  미해결 질문을 "운영 사실" 절에 추가 + 세션 링크 반영.
- index.md 갱신: 신규 세션·토픽 라인 추가.
- 스킬 추출 안 함 — 데몬화 절차([[macos-launchd-daemon]])와 달리 이번 건은 일반적 판단(무엇이 필요한
  프로세스인지 분류)이라 재사용 가능한 별도 절차로 보기 어려움.
- AI_CONTEXT.md는 변경 안 함 — 일회성 시스템 점검 질문이고 확정된 결정·실행 결과가 없어 상시 요약에
  넣을 확정 사실이 없음.

## [2026-07-17] ingest | 하얏트 빙수 추천 (source: 15f9eae5-464e-41ad-956b-7339edcb323b.md)
- raw/conversations/2026-07-17-하얏트-빙수-추천.md 보관(원본 아카이브 파일명:
  `15f9eae5-464e-41ad-956b-7339edcb323b.md`).
- 세션 요약 신설: wiki/sessions/2026-07-17-하얏트-빙수-추천.md — "하야트에 빙수 뭐가 유명해?" 개발
  무관 개인 질문 3왕복. 어시스턴트가 처음엔 개발 무관이라며 답을 거절했다가 사용자 재촉("응 웹으로
  검색해줘" → "너가 할.")에 웹 검색 후 그랜드 하얏트 서울 로비 라운지 카페 '갤러리'의 카이막
  애플망고 빙수(11만원)·수박 빙수(9만원)를 추천, 출처 링크 1건 제공.
- entity/topic 페이지 신설·갱신 안 함 — 그랜드 하얏트 서울/빙수는 위키 내 다른 소스에서 재등장하지
  않는 1회성 개인 취향 질문이라 재사용 가치가 낮음.
- 스킬 추출 안 함 — 재사용 가능한 절차가 아니라 단순 웹 검색 후 답변.
- AI_CONTEXT.md는 변경 안 함 — 확정된 결정·진행 중 프로젝트 상태 변화 없는 1회성 개인 질문.

## [2026-07-16] ingest | 청구 기업 용어 질문 (source: quick-5e500a49-4a40-43c4-ba7b-efd4309764a6.md)
- raw/conversations/2026-07-16-청구기업-용어질문.md 보관(원본 아카이브 파일명:
  `quick-5e500a49-4a40-43c4-ba7b-efd4309764a6.md`).
- 세션 요약 신설: wiki/sessions/2026-07-16-청구기업-용어질문.md — "청구 기업이라는게 뭐야?" 4왕복
  quick 채팅. 일반 회계/B2B 정의(인보이스 발급 측)로 시작해 "주식상장에서 청구 기업"으로 맥락을
  좁혀 재질문. 어시스턴트가 증권거래 공식 용어가 아님을 밝히고 상장 신청 기업/청약 기업/발행
  기업/수탁 기업 등 후보를 제시했으나 사용자 확인 전에 소스 종료 — 최종 의미 미확정.
- entity/topic 페이지 신설·갱신 안 함 — 위키 내 다른 소스에서 "청구 기업"이라는 표현이 처음
  등장했고, 프로젝트나 실제 업무(예: [[bank-refund-invoice-reconciliation]])와 명시적으로
  연결되지 않아 재사용 가치가 낮음. 세션 페이지에서 인접 주제로만 링크.
- 스킬 추출 안 함 — 재사용 가능한 절차가 아니라 단순 용어 정의 질문.
- AI_CONTEXT.md는 변경 안 함 — 확정된 결정·진행 중 프로젝트 상태 변화 없는 1회성 용어 질문.

## [2026-07-17] ingest | 볼린저 밴드·평균회귀 트레이딩 전략 질문 (source: quick-32526692-0042-4430-865f-ae6128ee1420.md)
- raw/conversations/2026-07-16-볼린저밴드-평균회귀-트레이딩전략-질문.md 보관(원본 아카이브 파일명:
  `quick-32526692-0042-4430-865f-ae6128ee1420.md`, 대화 발생 2026-07-16 22:06~22:18 UTC).
- 세션 요약 신설: wiki/sessions/2026-07-16-볼린저밴드-평균회귀-트레이딩전략-질문.md — 볼린저
  밴드→평균회귀→"둘 중 뭐가 유리해?" 3왕복 quick 채팅. [[2026-07-14-보관-자동저장-확인-볼린저밴드]]와
  같은 주제(볼린저 밴드)가 재등장했으나, 이번엔 세 답변 모두 [[dark-toss-api]]를 명시적으로 언급하며
  "이 봇에 적용한다면" 프레임으로 설명하고 마지막엔 혼합 전략(평균회귀 기본+추세감지 시 모멘텀 전환)
  까지 제안 — 프로젝트 연결이 반복·구체적이라는 점에서 이전 판단 기준(신규 페이지 미생성)을 넘어섬.
  실제 코드 구현은 없었음.
- 토픽 신설: wiki/topics/trading-strategy-mean-reversion-bollinger.md — 볼린저 밴드(도구) vs
  평균회귀(전략) 개념 정리 + dark-toss-api 적용 시나리오 비교표, 상태를 "미구현·상담 단계"로 명시.
- 엔티티 갱신: [[dark-toss-api]] — "전략 후보" 섹션 신설(볼린저/평균회귀 논의 링크, 미구현 명시), updated 2026-07-17.
- index.md 갱신 (세션1·토픽1 추가).
- 스킬 추출 안 함 — 재사용 가능한 절차가 아니라 금융 개념 설명·전략 상담.
- AI_CONTEXT.md는 변경 안 함 — 확정된 결정이나 프로젝트 상태 변화 없음(전략은 아직 제안 단계일 뿐 구현 없음).
- 특이사항: 같은 주제의 2026-07-14 세션과 모순은 없음 — 그때는 프로젝트 맥락 없는 순수 질문이라
  페이지 미생성이 맞는 판단이었고, 이번엔 맥락이 추가돼 재평가한 것으로 두 로그 항목 모두 유효.

## [2026-07-17] ingest | 청구기업 리스트 산업트렌드 분석 (source: quick-016d7ff7-be49-4c76-9a77-769a428a19b5.md)
- 원본 보관: `raw/conversations/2026-07-16-청구기업-산업트렌드-분석.md`
- 세션 신설: [[2026-07-16-청구기업-산업트렌드-분석]] — 2026-07-16 22:30~22:33, 한국 상장예비심사 청구
  기업 표(2021-10~2026-07, 약 150개사)를 두 차례 붙여넣고 "산업 트랜드" 질문 반복 → "너가 볼 땐
  어떻게 생각해?"로 어시스턴트 자체 견해까지 3왕복.
- 토픽 신설: [[korea-ipo-filing-trends-2021-2026]] — "3개 파도"(2차전지/AI·반도체/바이오) 프레임 →
  2026 상반기 4파도(보안·항공우주·자산재평가) 확장 → 최종 정제된 관점(2차전지 옥석가리기, AI는
  "산업인프라화"가 핵심, 반도체는 후공정·전력화, 방산은 공급망 인증, 현금창출형 전통산업 귀환, 바이오는
  개별이벤트 산업) + 향후 우선순위 표. 외부 데이터 미검증 한계 명시.
- 세션 갱신: [[2026-07-16-청구기업-용어질문]] — 직전(22:27~22:29) 세션에서 미확정으로 남았던 "청구
  기업" 용어가 이 세션의 실사용례("상장예비심사 청구" 제출 기업)로 정황상 해소됨을 기록. 사용자가
  명시적으로 확인해준 것은 아니므로 "확정"이 아닌 "정황상 해소"로 표기.
- 엔티티 갱신: [[progdesigner]] — "관심사: 주식/투자 리서치" 절 신설, 기존 개별 세션들
  (10만원 질문·개장전 매수시간·볼린저밴드 등)을 하나의 반복 관심사 패턴으로 묶어 명시.
- 토픽 갱신: [[trading-strategy-mean-reversion-bollinger]] — 인접 토픽 링크 추가.
- index.md 갱신 (세션1·토픽1 추가).
- AI_CONTEXT.md 갱신 없음 — 일회성 리서치 질문이라 프로젝트 상태·확정 결정에 해당하지 않음(40줄 예산 유지).
- 스킬 추출 안 함 — 재사용 가능한 절차가 아니라 시장 리서치/의견 요청.
- 특이사항: 표 원문 자체에 오탈자로 보이는 값들(청구일 "RO인" 등)이 있어 그대로 인용됐음을 topic
  페이지에 명시. 개별 기업 사실은 검증되지 않은 사용자 제공 원문 그대로임을 강조.

## [2026-07-17] ingest | needtovent.io 서비스 추측 (source: 0430b02e-9c9c-49b5-9709-1816bf7cbb1d.md)
- 원본 보관: `raw/conversations/2026-07-16-needtovent-io-서비스-추측.md`
- 세션 신설: [[2026-07-16-needtovent-io-서비스-추측]] — 2026-07-16 23:39~23:44 UTC, [[progdesigner]]가
  제3자 웹사이트 `https://needtovent.io/`의 정체를 물은 3왕복 quick 채팅. 어시스턴트가 실제 페이지
  탐색·웹 검색 없이 도메인명·랜딩 카피("Chat with me")만으로 AI 대화/사람 연결 익명 대화/개인용
  공유링크 세 가지 서비스 모델 가설을 제시했으나 확정하지 못하고 종료.
- 엔티티·토픽 신규/갱신 없음 — `needtovent.io`는 진행 중인 어떤 프로젝트와도 연결점이 없는 순수
  추측성 브레인스토밍이라 근거가 부족한 가설을 엔티티 페이지로 승격하지 않음.
- index.md 갱신 (세션 1개 추가).
- AI_CONTEXT.md 갱신 없음 — 일회성 제3자 서비스 추측이라 사용자 프로필·진행 중 프로젝트·확정된
  결정 어디에도 해당하지 않음(40줄 예산 유지).
- 스킬 추출 안 함 — 재사용 가능한 절차가 아니라 근거 없는 추측 답변.
- 특이사항: 콘텐츠 가치 낮음으로 분류(다른 저가치 quick 세션들과 동일 패턴). 소스에 사용자의 실제
  의도(유사 서비스 기획 검토 등)가 드러나지 않아 추정하지 않고 미확정으로 남김.

## [2026-07-17] ingest | nginx 스캐너 차단·정체 조사 (source: quick-d136f814-6d8a-4b37-9212-8ce1b882b647.md)
- 원본 보관: `raw/conversations/2026-07-17-nginx-스캐너-차단-조사.md`
- 세션 신설: [[2026-07-17-nginx-스캐너-차단-조사]] — nginx access 로그에 찍힌 `.env`/`.git`/`phpinfo`류
  정찰성 404 대량 요청(단일 사설 IP `172.31.10.37`) 붙여넣고 차단법 질의 → Nginx 경로 차단+rate
  limit+real IP 복원+AWS WAF Managed Rules 계층 방어 제시, 이어서 "뭐가 이걸 하고 있나" 질문에 자동
  스캐너/봇넷/Shodan류 인덱싱/**내부 인스턴스 침투 가능성** 4가지 가설과 `aws ec2 describe-instances`
  역매핑 확인법 제시. 어느 프로젝트 서버인지는 소스에 미상, 후속 확인 여부도 미확인.
- 스킬 신설: [[nginx-aws-scanner-hardening]] — 사설 IP를 공격자로 오인해 직접 차단하지 말 것(핵심
  함정)을 중심으로 Nginx 차단 규칙·real IP 복원·rate limit·Security Group 축소·AWS WAF·과거 로그
  200 응답 검증·내부 인스턴스 역매핑까지 8단계 절차로 정리.
- index.md 갱신 (세션1·스킬1 추가). 엔티티/토픽 신규 없음 — 서버 정체가 미상이라 임의 엔티티 생성
  보류(위키 전반의 "미상 연결 임의 생성 금지" 관례 적용).
- AI_CONTEXT.md 갱신 없음 — 특정 프로젝트에 귀속되지 않은 1회성 보안 상담, 사용자 선호·확정 결정·
  진행 중 프로젝트 상태 변화 없음(40줄 예산 유지).
- 특이사항: 스캐너 요청 IP가 AWS VPC 사설 대역이라는 점을 놓치면 방어책이 역효과(정상 트래픽 차단)를
  낼 수 있다는 경고가 두 턴 모두의 핵심 축. 서버 소유/프로젝트가 확인되면 이 세션·[[progdesigner]]
  엔티티를 갱신할 것.

## [2026-07-19] ingest | desktop/web 아이콘 통일 세션 (2026-07-18) (source: eaf17006-4706-406d-935c-02385ac58edd.md)
- 원본 보관: `raw/conversations/2026-07-18-desktop-web-아이콘-통일.md`
- 세션 신설: [[2026-07-18-desktop-web-아이콘-통일]] — "desktop 아이콘으로 web static 아이콘을 바꿔줘"
  요청. 1차 시도에서 방향을 반대로 잡아(web 남색 사운드바 아이콘을 desktop에 이식) `install-desktop.sh`
  재실행(Dock/Finder 반영)까지 사용자 승인 하에 완료했다가, 사용자가 "반대로 한거 같은데?"로 정정 →
  git 상태 확인 후 desktop 원본(주황 face) 복원 → 올바른 방향(desktop→web)으로 재작업·커밋 완료.
  `apps/web/static/icon-512.png`·`icon-192.png` 교체, `icon-384.png` 신규 생성, desktop `icon.icns`
  재생성.
- 스킬 신설: [[icon-source-of-truth-before-replace]] — "A를 B에 맞춰라" 요청에서 재생성 파이프라인의
  "원본 파일" 방향과 요청자가 "유지하고 싶은 디자인" 방향을 분리해 판단하는 절차, 모호하면 실행 전
  한 줄 확인 권장.
- 엔티티 갱신: [[lampas-harness]] (2026-07-18 세션 절 신설, 관련 세션 목록에 추가, updated 2026-07-19)
- index.md 갱신 (세션1·스킬1 추가)
- AI_CONTEXT.md 갱신 없음 — 소규모 브랜딩/기능 세션이라 40줄 예산 내 유지할 만큼 장기 핵심 사실 아님
  (2026-07-15 desktop File 메뉴 세션과 동일 판단 기준).
- 특이사항: 방향 오인을 이미 시스템 반영(재설치)까지 실행한 뒤 발견 → 롤백이 단순 파일 되돌리기보다
  커진 사례. 커밋 해시는 소스에 명시되지 않음(마지막 턴에서 "완료됐습니다" 보고만 있고 해시 미노출).

## [2026-07-19] ingest | Works 전체 git 저장소 저장 세션 (2026-07-18) (source: 73d2a722-3760-4eb8-98b9-a54c58c6a973.md)
- 원본 보관: `raw/conversations/2026-07-18-works-전체저장.md`
- 세션 신설: [[2026-07-18-works-전체저장]] — "Works 의 모든 git 저장소 저장해줘" 요청. [[works-project-portfolio]]
  12개 저장소 중 미커밋 변경이 있던 4개([[john-wiki]]·[[dark-system]]·[[lampas-harness]]·[[lampas-studio]])를
  각각 커밋·push. [[dark-system]]은 원격에 새 커밋이 있어 push가 한 번 거부됐고 `git pull --rebase`로
  통합 후 재push. 나머지 8개는 변경 없어 손대지 않음. 2026-07-15 pull 방향 최신화 세션의 대칭 작업(save
  방향)이라 판단, works-project-portfolio.md에 노트 추가.
- 스킬 신설: [[multi-repo-bulk-commit-push]] — 다중 저장소 일괄 commit+push 절차(변경 있는 저장소만
  커밋, push 거부는 pull --rebase로 우선 해소, 구조적 충돌이면 [[rebase-local-feature-onto-refactored-remote]]로
  승격). 기존 [[multi-repo-safe-bulk-update]](pull 방향)와 짝을 이루는 반대 방향 루틴이라 대칭 스킬로
  분리 신설, 서로 상호 링크.
- 엔티티 갱신: [[dark-system]]·[[lampas-harness]]·[[lampas-studio]] (각각 "기타 변경 (2026-07-18)"
  절 신설, 관련 세션 목록에 추가, updated 2026-07-19). [[john-wiki]]는 이 세션이 위키 저장소 자신의
  git 이력(메타)이라 엔티티 페이지 갱신 생략.
- index.md 갱신 (세션1·스킬1 추가)
- AI_CONTEXT.md 갱신 없음 — 소스가 최종 보고 요약뿐인 매우 짧은 routine 운영 세션이라 40줄 예산 내
  유지할 만큼 장기 핵심 사실 아님(2026-07-18 desktop/web 아이콘 세션과 동일 판단 기준).
- 특이사항: 소스가 어시스턴트 최종 보고 텍스트만 담고 있어 각 저장소의 실제 diff·커밋 해시는 확인 불가
  (john-wiki 자체 커밋 내용도 "sdk 과금 스킬 수정 + 세션 기록 2건"이라는 한 줄 요약뿐).

## [2026-07-19] ingest | 람파스 차별화전략·용어정리·Works저장 quick 세션 (2026-07-17~18) (source: quick-bcec85c0-6a94-41d2-b64f-3366a6029ce7.md)
- 원본 보관: `raw/conversations/2026-07-17-람파스-차별화전략-용어-works저장-quick.md`
- 세션 신설: [[2026-07-17-람파스-차별화전략-용어-works저장-quick]] — 3개 화제. ①"힉스필드와
  차별화하려면?" 질문에 Actor/Object/Space 3축 콘텐츠 엔진·개체 결합·차별화 요소 6가지·포지셔닝
  문장·4단계 로드맵 상세 분석. ②개체 등록 동사 논의(스카우트/섭외/영입 뜻 차이)→Actor=스카우트/
  Object=선정/Space=매핑 추천으로 귀결. ③하루 뒤 같은 quick 채팅에서 "Works 전체 저장해줘" 요청이
  작업 폴더 샌드박싱(`lampas-harness`로 제한)에 막혀 대화 중단 — 3분 뒤 다른(제한 없는) 세션에서 동일
  요청이 재시도되어 성공한 것이 이미 위키에 있는 [[2026-07-18-works-전체저장]].
- 토픽 신설: [[lampas-actor-object-space-positioning]] — 전략 분석 전체 내용(Higgsfield 대비 포지셔닝,
  Actor/Object/Space 각 축 설명, 결합 시나리오, 차별화 요소 6가지, 포지셔닝 문장 후보, 로드맵, 용어
  추천). **상담 결과일 뿐 사용자의 명시적 채택 확정 기록은 없음**을 페이지 상단에 명시.
- 엔티티 갱신: [[lampas-studio]] — "포지셔닝 전략" 절 신설, 관련 세션/토픽 링크 추가.
- 스킬 갱신: [[work-folder-sandboxing]] — quick 채팅이 특정 작업 폴더에 샌드박싱되어 `~/Works` 전체
  접근이 막힌 추가 실사례로 이 세션을 기록.
- 토픽 갱신: [[works-project-portfolio]] — [[2026-07-18-works-전체저장]] 항목에 "3분 전 quick
  채팅에서 같은 요청이 먼저 차단됐었다"는 선행 사실 보강.
- index.md 갱신 (세션1·토픽1 추가)
- AI_CONTEXT.md 갱신 없음 — 전략 분석은 상담 결과일 뿐 사용자가 확정한 결정이 아니고, Works 저장
  화제는 이미 반영된 기존 사실(2026-07-18-works-전체저장)의 보강 배경일 뿐이라 40줄 예산 내 유지할
  만큼 새로운 장기 핵심 사실 아님.

## [2026-07-19] ingest | 인스타그램 추천 계정 알림 끄기 quick 세션 (2026-07-18) (source: quick-855ee19c-6240-49ac-b2e5-3f103dafbe4f.md)
- 원본 보관: `raw/conversations/2026-07-18-인스타그램-추천계정-알림끄기.md`
- 세션 신설: [[2026-07-18-인스타그램-추천계정-알림끄기]] — 개발 무관 개인 질문 2왕복. 인스타그램
  추천 계정/게시물 알림 끄는 방법 웹 검색 답변 → 1차 답변 메뉴 경로가 실제 앱과 안 맞아 "최신 인스타
  그램으로 다시" 재요청 → 갱신된 경로(설정 및 개인정보→알림→게시물, 스토리 및 댓글) 제시, 푸시만
  꺼질 뿐 피드 내 추천은 완전히 못 끈다는 한계 명시.
- index.md 갱신 (세션1 추가). 신규 엔티티/토픽/스킬 없음 — 절차가 소비자 앱 설정 안내 수준으로 얇고
  재사용 가치 낮음(하얏트 빙수·needtovent.io 세션과 동일 판단 기준).
- AI_CONTEXT.md 갱신 없음 — 콘텐츠 가치 낮은 1회성 개인 질문, 장기 핵심 사실 없음.
- 특이사항: 이 세션의 "인스타그램"은 [[instagram-reference-integration]](lampas-studio 레퍼런스 이미지
  통합 기능)과 무관한 개인 소비자 앱 맥락 — 동명이의 혼동 방지를 세션 페이지에 명시.

## [2026-07-19] ingest | web-ai 배포·등록플로우 2단계화·사진 우선 분류 구현 세션 (2026-07-18) (source: 4c7e05b4-5b19-46e1-aa5b-66b13c3e23b9.md)
- 원본 보관: `raw/conversations/2026-07-18-web-ai-등록플로우-사진분류-배포.md`
- 세션 신설: [[2026-07-18-web-ai-등록플로우-사진분류-배포]] — 05:57~14:06 UTC 장시간 세션, 6개 왕복.
  ①web-ai 단독 배포(corepack pnpm shim, `.bin` 심볼릭 링크 잔재 발견·정리). ②"최신 코드로 업데이트"
  모호 지시 명확화→git pull 선택→변경 요약(lampas-app-toss→lampas-app-photobooth 리네이밍, Space 모듈
  백엔드 확장, web-www landing 재디자인, 설계문서 3건 추가). ③lampas-api/web-ai/web-www/app-photobooth
  4앱 순차 배포(web-product는 인프라 미비로 계속 제외). ④web-ai 새 대화 시작을 등록하기/촬영하기
  2칩으로 개편. ⑤등록하기를 "사진 먼저→Gemini Vision 분류(Actor/Object/Space)→해당 플로우 연결"로
  재구현(신규 백엔드 엔드포인트 `POST /actors/classify-registration-photo`). ⑥위 ④⑤ 배포 완료.
- 엔티티 갱신: [[lampas-web-ai]] — 새 대화 2칩화·사진 우선 분류 플로우 절 신설. [[lampas-studio]] —
  앱 리네이밍 반영, Space 모듈 API 배포 완료 기록(단 SDK는 미배포 추정으로 명시), web-www 재디자인·
  설계문서 절 신설, 2026-07-18 works-전체저장 세션과의 커밋 시점 관계를 추정으로 병기.
- 스킬 갱신: [[deploy-sandbox-pnpm-shim]] — corepack이 `ln -sf`로 `.bin` 심볼릭 링크를 잘못 남기는
  새 함정 사례 추가.
- AI_CONTEXT.md 갱신: [[lampas-studio]] 절에 Space API 배포 완료(SDK는 미배포 추정)·web-ai 등록 플로우
  개편 사실 반영, 40줄 예산 유지(38줄).
- index.md 갱신 (세션1 추가).
- 특이사항: Space가 "3앱 모두 배포 필요"(2026-07-15~16 기록)에서 API 1개만 진전됐을 뿐 SDK(생성 UI)
  미배포 추정이라 사용자 입장에서 Space는 여전히 완전히 열리지 않은 상태 — 다음 세션에서 SDK 배포
  여부 재확인 필요.

## [2026-07-19] ingest | .env 파일 평문 노출 사고 + PWA 세이프 에어리어 여백 수정 (source: 2a66b6ce-6111-4edb-9741-a3902fc162e8.md)
- 원본 보관: `raw/conversations/2026-07-17-env읽기-pwa세이프에어리어-여백수정.md` — **⚠️ 원본에 실제 API 키/토큰
  (CLAUDE_CODE_OAUTH_TOKEN·ANTHROPIC_API_KEY·OPENAI_API_KEY·GOOGLE_API_KEY·ELEVENLABS_API_KEY·HARNESS_TOKEN)이
  평문 노출되어 있어, 이 위키(git 추적) 사본에는 레닥트(접두사만 남김) 후 저장함. 원본 로그 파일 자체는
  CLAUDE.md 규칙대로 손대지 않음.**
- 세션 신설: [[2026-07-17-env읽기-pwa세이프에어리어-여백수정]] (2026-07-17 12:50~12:55 UTC, 무관한 두 화제).
  ①사용자가 "`.env` 파일을 그대로 읽어줘" 요청 → 어시스턴트가 위험 고지 없이 전체 키 값을 표로 채팅
  응답에 출력, `logs/chats/archive/`에 평문 영구 저장된 상태(재발급 여부 미확인). ②"PWA일 때 상단 여백이
  없어졌다" 제보 → `header`에 `env(safe-area-inset-top)` 누락이 원인으로 진단·수정
  (`padding-top: calc(10px + env(safe-area-inset-top))`), 후속으로 `footer` 하단 패딩 `10px→0`도 제거,
  웹 빌드+서버 재시작(PID 74969)까지 완료.
- 토픽 신설: [[secrets-plaintext-exposure-pattern]] — 이 사고를 [[toktalk]](커밋 `0664ea9`)·
  [[cwc-system]](프로덕션 env 평문 커밋) 기존 두 사례와 묶어 반복 패턴으로 문서화, 권장 대응(값 마스킹·
  재발급·위키 ingest 시 레닥트 원칙) 기록. 두 기존 entity 페이지에 상호링크 추가.
- 스킬 신설: [[pwa-safe-area-inset-padding]] — `env(safe-area-inset-*)` + `calc()` 패턴, 방향별
  추가/제거 구분, iOS 실기기 재확인 필요성.
- 엔티티 갱신: [[lampas-harness]] (2026-07-17 세션 두 섹션 신설 — 보안 관찰 + PWA 기능), [[toktalk]]·
  [[cwc-system]] (보안 주의 절에 패턴 토픽 링크 추가).
- index.md 갱신 (세션1·토픽1·스킬1 추가).
- AI_CONTEXT.md 갱신 없음 — PWA 수정은 소규모 기능(기존 판단 기준과 동일), `.env` 노출 사고는
  일회성 사건이라 CLAUDE.md 규칙상 AI_CONTEXT 편입 대상 아님(entity·topic 페이지로 충분).
- 특이사항: 이 ingest 자체가 "위키 저장소에 실제 비밀을 옮기지 않는다"는 원칙을 처음 실천한 사례 —
  이후 유사 소스 ingest 시 참고할 선례로 [[secrets-plaintext-exposure-pattern]]에 명시.

## [2026-07-19] ingest | 하네스 재시작 + PWA 이름/아이콘 최초 설정 + Vite manifest 버그 수정 (source: 8028c121-b640-4550-942e-3ae0c2e27e42.md)

## [2026-07-19] ingest | dark-system 신호 판정·스타일별 리스크 설계 코드 업데이트 (source: 123fdfff-b54f-4a00-9192-4fb4083f247c.md)
- 원본: raw/conversations/2026-07-17-dark-system-신호리스크-설계-코드업데이트.md (2026-07-17T12:30 UTC, 1왕복)
- 세션 페이지 신설: [[2026-07-17-dark-system-신호리스크-설계-코드업데이트]] — "최신 코드 업데이트해줘"
  요청에 어시스턴트가 [[dark-upbit-api]]/[[dark-toss-api]] 공통 `decide-signal.ts`(신호 판정) 신설,
  `trading.config.ts`/`trading.service.ts` 확대, 웹 UI 개편, 신규 엔드포인트(`/trading/status`,
  `/trading/signal`), 설계 문서(`2026-07-17-per-style-signal-risk-design.md`)를 보고. 커밋 해시·
  line 번호·검증 절차가 전혀 없는 자기 보고뿐이라 "미검증" 표시로 기록.
- 엔티티 갱신: [[dark-toss-api]]·[[dark-upbit-api]](decide-signal.ts 섹션 신설, 미검증 명시),
  [[dark-system]](기타 변경에 항목 추가).
- 토픽 갱신: [[trading-strategy-mean-reversion-bollinger]] — "2026-07-17 기준 미구현" 서술을
  덮어쓰지 않고, 같은 날짜에 나온 이 보고와의 연결 가능성만 병기(단정 안 함) — 다음 코드 확인
  세션에서 확정할 것.
- index.md 갱신 (세션 1 추가, dark-toss-api·trading-strategy 토픽 한 줄 요약 갱신).
- AI_CONTEXT.md 갱신 없음 — 미검증 자기 보고 상태라 40줄 예산을 쓸 만큼 확정된 핵심 사실이 아직
  아님. 코드로 확인되면 그때 반영.

## [2026-07-19] ingest | 힉스필드 1000 크레딧 원화 환산 (source: quick-b0636c59-06ac-4e52-9686-638e8eeed667.md)
- 원본: raw/conversations/2026-07-17-힉스필드-크레딧-원화환산.md (2026-07-17T12:05~12:06 UTC, 1왕복 quick).
- 세션 페이지 신설: [[2026-07-17-힉스필드-크레딧-원화환산]] — "힉스필드 1000 크레딧 원화 가치?"
  질문에 기준별(Top-up 팩/Pro 월간·연간/Max 연간) 달러·원화 환산표 제시. Top-up 팩 기준
  1,000 크레딧 ≈ 6만 6천 원 어림값 결론. 공식 가격표 대조 없는 어시스턴트 단발 추정임을 명시.
- 엔티티 신설: [[higgsfield]] — 경쟁사 페이지가 이제까지 없어 신설. 가격 정보 + 기존
  [[lampas-actor-object-space-positioning]] 토픽의 포지셔닝 요약을 옮겨 정리.
- 토픽 갱신: [[lampas-actor-object-space-positioning]] 관련 섹션에 [[higgsfield]] 엔티티 링크와
  이 세션 참고 링크 추가(같은 날 사전 조사로 추정, 단정 안 함).
- index.md 갱신 (세션 1개, 엔티티 1개 추가).
- 스킬 추출 없음 — 단순 통화 환산 답변, 재사용 가능한 절차 아님.
- AI_CONTEXT.md 갱신 없음 — 일회성 가격 질문, 40줄 예산을 쓸 핵심 지속 사실 아님.

## [2026-07-19] ingest | Fable 5 주간 사용량 1%당 API 환산 비용 질문 — 미답변 (source: quick-3c3b0c09-ba81-422c-9229-8cb60b8f971c.md)
- 원본: raw/conversations/2026-07-17-fable-주간사용량-api비용환산-질문.md (2026-07-17T07:06~07:08 UTC, quick).
- 세션 페이지 신설: [[2026-07-17-fable-주간사용량-api비용환산-질문]] — "Fable 5를 주간 사용량이
  1% 늘 때마다 실제 API로 썼다면 비용이 얼마냐"는 질문에 어시스턴트가 답하기 전 07:08 UTC에
  사용자가 턴을 중지시켜 **답변 없이 종료**. 소스에 실제 계산·환산은 없음.
- 토픽 갱신: [[claude-model-pricing]] — "미해결 — 구독 '주간 사용량 %'의 API 비용 환산" 절 신설.
  Fable 5 API 단가는 이미 있으나 "주간 한도 100%=몇 토큰"이라는 환산 기준이 위키에 없어 계산을
  완성할 수 없음을 기록, [[sdk-claude-code-vs-api-billing]]의 `seven_day` rate limit 훅과 연결.
- 스킬 갱신: [[sdk-claude-code-vs-api-billing]] updated 날짜만 갱신(내용 변경 없음, 교차참조 확인).
- index.md 갱신 (세션 1개 추가, claude-model-pricing 토픽 한 줄 요약 갱신).
- 스킬 추출 없음 — 질문만 있고 절차·해결 없음.
- AI_CONTEXT.md 갱신 없음 — 답변 없이 종료된 1왕복 질문, 확정된 핵심 사실이 없어 40줄 예산을
  쓸 근거 없음.

## [2026-07-19] ingest | 전문직 대상 릴스 제작 에이전시 프롬프트 다듬기 (source: quick-8463c419-97f1-47f9-af27-d37f0ecd5126.md)
- 원본 보관: raw/conversations/2026-07-17-전문직-릴스에이전시-프롬프트다듬기.md (2026-07-17T06:31~06:32 UTC, 1왕복 quick).
- 세션 페이지 신설: [[2026-07-17-전문직-릴스에이전시-프롬프트다듬기]] — 의사·회계사·변호사 등
  전문직 대상 퍼스널 브랜딩·사업홍보 릴스 제작 올인원 에이전시의 초안 카피 프롬프트를 붙여넣고
  "다듬어줘" 요청. 어시스턴트가 정보 손실 없이 회사소개→제공서비스→핵심강조포인트→지시사항
  4단 구조로 재정리 + 실행 품질을 높일 후속 정보(문구 종류·톤·바꿀 원본 문구) 3가지를 역질문.
  트랜스크립트는 사용자 답변 없이 종료, 최종 카피 산출은 미확인.
- 스킬 신설: [[prompt-structuring-for-execution]] — 초안 프롬프트를 정보 손실 없이 표준 구조로
  재구성하고 누락된 실행 조건을 최대 3가지로 되묻는 절차. "다듬어줘"와 "만들어줘"를 구분할 것을
  주의사항으로 명시.
- 토픽 갱신: [[harness-as-business-assistant]] — 사례 추가(사진/데이터 해석이 아닌 "프롬프트
  자체를 다듬어달라"는 메타 요청으로 이 패턴군에서 가장 프롬프트 엔지니어링에 가까운 케이스),
  관련 스킬 목록에 [[prompt-structuring-for-execution]] 추가.
- index.md 갱신 (세션 1개·스킬 1개 추가).
- AI_CONTEXT.md 갱신 없음 — 일회성 카피 상담(에이전시 이름·확정 결과 미상), 40줄 예산을 쓸
  지속 핵심 사실 아님. 기존 "코딩 외 업무 비서" 패턴 줄이 이미 이 사례군을 포괄.
- 특이사항: 에이전시 이름·브랜드는 소스에 미상이라 임의 엔티티 생성하지 않음.

## [2026-07-19] ingest | 전문직 대상 릴스 제작 에이전시 프롬프트 다듬기 — 1차 시도 (source: quick-656234d9-6c52-4b1c-85b0-a377990f0e4f.md)
- 원본 보관: raw/conversations/2026-07-17-전문직-릴스에이전시-프롬프트다듬기-1차시도.md
  (2026-07-17T06:30:29~06:30:41 UTC, 1왕복 quick). 동일 초안 프롬프트를 다루는 위 세션(06:31:27~,
  "**다시** 다듬어줘")보다 1분 앞선 최초 요청 — 별도 quick 세션 파일로 남아 있다가 뒤늦게 발견·ingest됨.
- 세션 페이지 신설: [[2026-07-17-전문직-릴스에이전시-프롬프트다듬기-1차시도]] — 같은 초안에 대해
  4단 구조 재정리 대신 타겟/핵심가치/포지셔닝 요약 3항목만 제시하고 곧바로 산출물 종류(홈페이지
  헤드라인/소개문단/서비스카피/릴스스크립트/광고포스트) 5가지를 되묻는, 완성도가 낮은 응답.
  사용자 답변 없이 종료.
- 세션 페이지 갱신: [[2026-07-17-전문직-릴스에이전시-프롬프트다듬기]] — 1차 시도 상호 참조 추가,
  "다시"라는 표현이 선행 시도의 증거임을 명시.
- 스킬 갱신: [[prompt-structuring-for-execution]] — 4단 구조 재정리는 재요청(2차 시도)에서 나온
  응답이며 1차 시도는 표준 미달 사례임을 주의사항에 명시, 가능하면 첫 시도부터 이 절차를 적용해
  재요청을 유도하지 않는 것이 목표라고 추가.
- 토픽 갱신: [[harness-as-business-assistant]] — 사례 한 줄 보강 + "재요청 시 프레임 전환" 관찰에
  두 번째 데이터포인트 추가([[2026-07-15-arvie-화장품-마케팅-재분석]]의 프레임 전환과 달리 이번엔
  **완성도 자체**가 재요청만으로 상승).
- index.md 갱신 (세션 1개 추가, 기존 세션 요약 문구에 "1차 시도" 존재 반영).
- AI_CONTEXT.md 갱신 없음 — 일회성 카피 상담의 선행 시도일 뿐, 40줄 예산을 쓸 지속 핵심 사실 아님.
- 특이사항: raw 두 소스 파일(656234d9/8463c419)은 초안 프롬프트 본문이 동일하지만 어시스턴트 응답과
  타임스탬프가 달라 진짜 중복이 아님 — 아카이브 파일명 재사용 이상 정황([[2026-07-14-clara-바바라-위스키분쟁-번역]]
  등)과는 다른 케이스로 구분.

## [2026-07-19] ingest | 전문직 대상 릴스 제작 에이전시 카피 완성본 (source: quick-bfc56172-db43-49f5-95d9-d94923a1f775.md)
- 원본 보관: raw/conversations/2026-07-17-전문직-릴스에이전시-카피완성본.md (2026-07-17T06:26:44~06:27:12
  UTC, 1왕복 quick). 동일 초안(의사·회계사·변호사 대상 퍼스널 브랜딩+사업홍보 릴스 제작 에이전시)을 다루는
  기존 두 세션([[2026-07-17-전문직-릴스에이전시-프롬프트다듬기-1차시도]] 06:30, [[2026-07-17-전문직-릴스에이전시-프롬프트다듬기]]
  06:31)보다 각각 약 4분·5분 앞선, 실제로는 세 세션 중 **가장 이른 시도**임이 뒤늦게 드러남.
- 세션 페이지 신설: [[2026-07-17-전문직-릴스에이전시-카피완성본]] — "프롬프트"란 단어 없이 "위 내용을
  다듬어줘"라 요청, 역질문 없이 헤드라인 3안·서브헤드라인·소개 문구·CTA 옵션까지 **완성된 마케팅 카피를
  곧바로 산출**한 유일한 버전.
- 세션 페이지 갱신: [[2026-07-17-전문직-릴스에이전시-프롬프트다듬기-1차시도]], [[2026-07-17-전문직-릴스에이전시-프롬프트다듬기]] —
  둘 다 정정 노트 추가. "재요청이 응답 완성도를 끌어올린다"던 기존 서술이, 시간순으로는 완성도가
  단조 증가가 아니라 완성본(06:26)→요약뿐(06:30, 최저)→구조화(06:31) 순임이 드러나며 재검토 대상이 됨.
  더 유력한 원인 가설: 사용자가 "프롬프트"라는 단어로 대상을 메타화했는지 여부.
- 토픽 갱신: [[harness-as-business-assistant]] — 해당 사례 불릿에 정정 노트 추가, "재요청 시 프레임
  전환" 관찰도 이번 발견으로 재검토 필요함을 명시(Arvie 사례의 프레임 전환 관찰 자체는 유효로 유지).
- 스킬 갱신: [[prompt-structuring-for-execution]] — 이 절차는 사용자가 "프롬프트를 다듬어줘"라 명시할
  때 적용되며, "내용을 다듬어줘"처럼 대상이 다르면 곧바로 최종 결과물을 산출하는 것이 맞을 수 있다는
  주의사항 추가. 출처 목록에 새 세션 추가.
- index.md 갱신 (세션 1개 추가, 기존 두 세션 요약 문구도 새 세션 대비 시점 표현으로 일부 수정).
- AI_CONTEXT.md 갱신 없음 — 일회성 카피 상담의 세 번째(실제로는 최초) 시도일 뿐, 에이전시 이름·최종
  채택 카피 모두 미상. 40줄 예산을 쓸 지속 핵심 사실 아님. 기존 "코딩 외 업무 비서" 패턴 줄이 이미
  포괄.
- 특이사항: 에이전시 이름·브랜드는 이번 소스에도 미상 — 임의 엔티티 생성하지 않음(기존 두 세션과 동일 판단).

## [2026-07-19] ingest | 릴스 대행사 영어 프로젝트 이름 짓기 quick 채팅 (source: quick-64e4b144-8b11-4b3a-a345-3f51581c9e79.md)
- 신규 세션: [[2026-07-17-릴스대행사-영어이름-질문]] — 1왕복 quick(05:55 UTC), "릴스 대행사" 영어
  프로젝트명 후보(직접적/넓게/브랜디/AI자동화 4카테고리) 제시, 방향 역질문에 답변 없이 종료. 최종
  이름 미상.
- 연결 근거: 같은 날 약 31~36분 뒤 시작되는 [[2026-07-17-전문직-릴스에이전시-카피완성본]] →
  [[2026-07-17-전문직-릴스에이전시-프롬프트다듬기-1차시도]] → [[2026-07-17-전문직-릴스에이전시-프롬프트다듬기]]
  (전문직 대상 퍼스널 브랜딩 릴스 제작 에이전시 카피 3세션)와 동일 프로젝트로 추정되는 최초 접점 —
  단, 이 세션 자체엔 "전문직" 언급이 없어 정황적 연결(확정 아님)로 기록.
- 세션 페이지 갱신: 위 세 세션 모두 "관련"에 이 새 세션 링크 추가.
- 토픽 갱신: [[harness-as-business-assistant]] — 사례 목록에 "가장 이른 단계(작명)" 사례로 추가.
- index.md 갱신 (세션 1개 추가).
- AI_CONTEXT.md 갱신 없음 — 이름 미확정·일회성 브레인스토밍, 40줄 예산을 쓸 지속 핵심 사실 아님.
- 특이사항: 에이전시 이름 여전히 미상 — 임의 엔티티 생성하지 않음(관련 세 세션과 동일 판단).

## [2026-07-19] ingest | Works 저장소 일괄 최신화(pull, 07-17) (source: 8c3faa34-5f61-48af-b8af-639c3273237f.md)
- 세션 신설: [[2026-07-17-works-저장소-일괄최신화-pull]] — "모든 프로젝트를 최신화 해줘"(2026-07-17
  04:36~04:38 UTC). 기존 [[multi-repo-safe-bulk-update]] 스킬을 두 번째로 재사용한 사례.
- 결과: bokziri-system/[[cwc-system]]/[[elevino-system]](dev)/[[lampas-studio]](lampas-system) 4개
  pull 완료. arca8-system/talk-system(=[[toktalk]])/goraesa-system/elevino-system 4개가 `dev`
  브랜치임을 최초 확인. [[dark-system]]은 로컬 변경 1개+원격 4커밋 뒤처짐으로 보류, 처리 방식을
  물었으나 사용자 응답 없이 소스 종료 — 미해결. [[john-wiki]]는 로컬 변경 4개·원격 뒤처짐 없어 pull
  불필요로 확인.
- 스킬 갱신: [[multi-repo-safe-bulk-update]] — zsh `@{u}` glob 확장 깨짐 함정 신규 추가(bash 명시+
  선행 fetch로 해결).
- 엔티티 갱신: [[dark-system]](보류 상세)·[[cwc-system]](pull 커밋)·[[elevino-system]](dev 브랜치+
  pull 커밋)·[[lampas-studio]](07-15 리베이스 상태에서 07-17 깨끗한 pull로 정상화)·[[john-wiki]]
  (Works 일괄 pull에서 매번 예외 처리되는 패턴 명시).
- 토픽 갱신: [[works-project-portfolio]] — 저장소별 표에 07-17 상태·브랜치 정보 추가.
- index.md 갱신 (세션 1개 추가, 07-17 세션 중 시간순 최초로 배치).
- AI_CONTEXT.md 갱신 없음 — 운영성 세션이고 핵심 사실(스킬 존재·Works 12개 저장소)은 이미 반영돼 있음,
  dark-system 보류는 사용자 미응답으로 미해결이라 확정 사실 아님.

## [2026-07-20] lint | 아카이브 중복 패턴 통합·모순 정정·인덱스 점검
- 신규 토픽: [[chat-archive-duplication-anomaly]] — `logs/chats/` 파일명 재사용·같은 사건의 이중 기록·
  트랜스크립트 내 메시지 반복 3개 하위 패턴을 4개 세션([[2026-07-14-clara-바바라-위스키분쟁-번역]]·
  [[2026-07-15-음성입력-3-4초-잘림-수정]]·[[2026-07-15-gpt-realtime-음성입력-길게누르기]]·
  [[2026-07-16-srkk-인보이스-james확인-0243]]·[[2026-07-16-tts-stream-elevenlabs-구현착수]])에서
  묶어 통합 정리(원인 미확정, 다음 조사 항목 명시). 각 세션 페이지에 상호 링크 추가.
- 모순 정정: [[sdk-claude-code-vs-api-billing]] — "API 사용 토글이 켜진 상태에서만 `/compact`가
  API로 과금됐다"는 07-15 기록이, 07-17 근본원인 조사(`compactClaudeSession`이 `claudeAuthEnv()` 없이
  `options.env` 미전달)로 **토글 상태와 무관했음**이 밝혀져 정정. 원 서술은 지우지 않고 정정 각주로 남김.
- 깨진 링크 성격 참조 정리: `sdk-claude-code-vs-api-billing.md`의 `[[runner.ts]]`(페이지 없는 파일명
  링크)를 실제 근거 세션 [[2026-07-16-메모리인제스트-크레딧버그-근본수정]]으로 교체.
- 자기참조 버그 수정: [[john-wiki]] 엔티티 페이지의 페이지 규칙 서술이 위키링크 문법 자체를
  `[[위키링크]]`로 표기해 실제 위키링크처럼 파싱될 수 있던 문제를 말로 풀어쓰는 서술로 수정.
- 크로스링크 보강: [[llm-judge-fallback-chain]] 스킬을 origin 세션([[2026-07-15-auto모델-기능-최초구현]]·
  [[2026-07-15-auto모델-난이도판정-확인ux-개선]])에 역링크 추가 (기존엔 스킬→세션 단방향만 존재).
- index.md 갱신 (신규 토픽 1개 추가).
- 자동 점검(스크립트) 결과: `[[위키링크]]` 파싱 기준 깨진 링크 0건(모든 대상이 wiki/ 페이지 또는
  `raw/conversations/` 원본 파일로 실존 확인) · index.md는 `wiki/{sessions,entities,topics,skills}/`
  실제 파일 목록과 완전히 일치(양방향 누락 0건) · 인바운드 링크 없는 페이지 3건은 모두 기존에도
  `low-value` 태그가 붙어 있던 1회성 개인 질문 세션([[2026-07-16-needtovent-io-서비스-추측]]·
  [[2026-07-17-하얏트-빙수-추천]]·[[2026-07-18-인스타그램-추천계정-알림끄기]])로, 이 위키의 기존
  저가치 세션 처리 관행과 일치해 강제 크로스링크 추가 없이 유지.
- AI_CONTEXT.md: 38줄로 40줄 예산 이내 확인, 크레딧 잔액 0 등 핵심 사실이 07-20 기준으로도 여전히
  유효함(재충전 언급 없음)을 재확인 — 재증류 불필요 판단, 내용 변경 없음.

## [2026-07-27] lint | 위키 전체 건강 점검 (2)
- **자동 점검(스크립트)**: `[[위키링크]]` 파싱 기준 깨진 링크 0건(유일한 매치 `[[#피드백]]`는
  [[2026-07-17-컨텍스트압축-api과금-수정]] 내부 문서-내 앵커 참조로 정상, 위키링크 아님) · 인바운드
  링크 없는 고아 페이지는 직전 07-20 lint와 동일한 3건 유지([[2026-07-16-needtovent-io-서비스-추측]]·
  [[2026-07-17-하얏트-빙수-추천]]·[[2026-07-18-인스타그램-추천계정-알림끄기]], index.md 카탈로그
  링크는 제외하고 판단 — 모두 콘텐츠 가치 낮은 1회성 개인 질문 세션으로 기존 저가치 세션 처리 관행에
  따라 강제 크로스링크 추가 없이 유지) · index.md는 `wiki/{sessions,entities,topics,skills}/` 실제
  파일 목록(세션86·엔티티24·토픽23·스킬39)과 완전히 일치(양방향 누락 0건). raw/conversations 원본과
  wiki/sessions 세션 페이지 대응도 전수 확인 — 불일치 2건([[2026-07-08-장기기억-provider-연동-설계]],
  [[2026-07-16-람파스-개발일지-블로그-seo최적화]])은 모두 세션 페이지가 원본 파일명보다 명확한 제목으로
  의도적으로 개명된 기존 사례로, 문제 아님.
- **모순 정정**: [[john-wiki]] 엔티티의 "저장 진입점 3개(수동 🧠 버튼·보관 🗄 자동·야간 자동 ingest)"
  서술(2026-07-13 시점)이 2026-07-16 UI 통합("기억에 저장"→"기억에 보관" 개명, 보관 API로 일원화)
  이후에도 갱신되지 않고 남아 있던 것을 발견·각주로 정정(원 서술 보존 + `[정정, 2026-07-16]` 절 추가,
  근거 [[2026-07-16-기억버튼-보관통합-NaN토큰버그수정]]). `AI_CONTEXT.md`는 이미 정정된 버전을
  반영하고 있었음(엔티티 페이지만 낡아 있던 사례).
- **신규 topic 생성 없음**: entities/topics 전체 재검토 결과 자기 페이지 없이 반복 언급되는 미해결
  개념(예: "Princ" 은행거래 상대방 미스터리, "study-ai-what" 블로그 연재)은 모두 기존 스킬·토픽·
  AI_CONTEXT.md에 일관되게 교차링크되어 있어 신규 페이지 생성 기준 미충족으로 판단(단독 페이지 없이도
  기존 페이지들에서 추적 가능).
- **AI_CONTEXT.md 재증류**: 38→36줄. 진행 중 프로젝트 절의 TTS 버전별 구현 서사(v0.1.27→0.1.30→0.1.36,
  병합 커밋 충돌 스토리 등)와 lampas-studio 절의 중간 빌드 과정 서술을 현재 상태 중심으로 압축(세부
  이력은 이미 wiki/sessions·wiki/entities에 있어 손실 없음). 핵심 사실(크레딧 잔액 0·구독 과금 기본·
  Space 배포 상태 등)은 모두 보존.
- 결과: 깨진 링크 0·고아 0. 신규 엔티티/세션/스킬 없음, 엔티티 정정 1건(john-wiki).

## [2026-07-19] ingest | moon run 명령어 언어스택 질문 (source: quick-e6244786-77ee-4392-abbc-d187c93307cc.md)
- quick 채팅 1왕복(2회 질의). `moon run web:dev`(5173)/`api:dev`(8787)/`desktop:dev` 명령어를 보고
  "언어가 뭐냐"→"node 기반이냐" 질문, 어시스턴트가 "moon 도구 자체가 Rust라 프로젝트도 Rust"라는
  근거 약한 논리로 답변. 소스에 저장소/경로 언급 전무 — 어느 프로젝트인지 미상.
- 검증: `~/Works` 하위 실제 저장소에 `.moon`/`moon.yml` 등 moonrepo 설정 파일 없음 확인.
  [[lampas-harness]](TS/claude-agent-sdk, 웹 8787/API 3787)와 포트 조합도 다름 — 기존 위키가 추적
  중인 프로젝트와 연결 근거 없음.
- 세션 → [[2026-07-19-moon-run-언어스택-질문]]. 콘텐츠 가치 낮은 1회성 질문으로 기존 저가치 세션
  처리 관행에 따라 엔티티/토픽 신규 생성·AI_CONTEXT.md 갱신 없이 기록만 남김.
