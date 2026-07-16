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
- [[2026-07-14-clara-바바라-위스키분쟁-번역]] — 은행환불 세션(~09:00) 직후·srkk 세션(09:17~) 직전 공백을 메우는 quick. Clara/Barbara 메시지·메일 번역 + Princ 문구 재작성 + CWC Lab-FY Group 위스키 선적 분쟁 이메일 전체 붙여넣기(Princ 무관 확인). 소스 파일명이 은행환불 세션과 동일한데 내용이 전혀 달라 아카이브 파일명 재사용 이상 정황 기록
- [[2026-07-14-srkk-도메인-scott-확인-princ-번역]] — 위 세션 17분 후 이어진 quick. WhatsApp 스크린샷 다중 번역, SRKK 도메인/Microsoft 계정 무단 연장 의혹→Scott과 협의해 미연장 확정, Princ 거래 상대방 식별은 여전히 미해결
- [[2026-07-15-올리브유-마케팅-포지셔닝]] — 사용 가능 스킬 목록(마케팅·Remotion 번들, 커밋 ef36c5f) + 올리브유 사진 프리미엄 포지셔닝 상담(ICP 선행 → product-marketing 착수 제안)
- [[2026-07-15-과금모드-토글-컨텍스트표시]] — 하네스에 Claude 과금 모드 토글(API vs 구독 OAuth) + 구독 모드 컨텍스트 잔여 표시 구현. 기본 과금이 구독(OAuth)임을 코드로 확인(기존 기록과 모순)
- [[2026-07-15-사용량한도-rate-limit-sdk-확인]] — 5시간/주간 사용량 한도 게이지 표시 가능 여부 조사(위 세션 후속, 02:53). SDK엔 훅이 이미 있는데 하네스는 미사용 확인. 어시스턴트가 "불가능"→"가능"으로 자체 정정한 사례
- [[2026-07-15-아우보카사-공동구매-단가설계]] — 인플루언서 공동구매 단가표 사진 1장 해석(1왕복 quick). 할인 티어 설계 의도·인플루언서 수익 10%+·시뮬레이션(실질 병당 판매가) 풀이
- [[2026-07-15-works-프로젝트-최신화-lampas-system-리베이스]] — Works 12개 저장소 일괄 최신화 + lampas-system(=lampas-studio) 로컬 미커밋 18파일을 원격 9커밋(리팩터) 위로 충돌 없이 rebase·push
- [[2026-07-15-dark-system-저장소-클론]] — `~/Works/dark` 폴더 생성 + `git@github.com:progdesigner/dark-system.git`을 `~/Works/dark/dark-system`에 최초 클론(2왕복). Works 최신화 세션의 선행 이벤트, cwc-system 클론(06:42)보다도 4분 앞섬
- [[2026-07-15-cwc-system-저장소-클론]] — `git@github.com:cwc-commerce/cwc-system.git`을 `~/Works/cwc/cwc-system`에 최초 클론(1왕복). 이후 두 세션(Works 최신화·멤버십 크레딧 설계)의 선행 이벤트
- [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]] — cwc-system에 elevino DB를 그대로 재사용하는 멤버십 검색+크레딧 지급/차감 관리자 기능 설계 완료, 구현 착수 직전 하네스 권한 채널 장애로 중단(실제 구현 미완)
- [[2026-07-15-데스크톱-file메뉴-new-window]] — lampas-harness desktop(Electron) File 메뉴에 New Window(Cmd+Shift+N) 추가 + 쌓여있던 변경사항 3커밋으로 push
- [[2026-07-15-srkk-azure-펀딩메일-번역]] — SRKK Group의 MS Azure 펀딩 영업메일 영→한 번역 요청(1왕복 quick). 대행 작성 아닌 순수 번역 사례
- [[2026-07-15-주식투자-10만원-질문]] — "10만원으로 주식 거래 가능한가?" 개인 재무 상담 1왕복 quick. 국내/미국 주식(소수점 매매) 가능 여부·현실적 제약 답변
- [[2026-07-15-삼성전자-주식분할-배너-html-인사이트]] — 무관한 두 왕복이 한 quick 파일에 묶임: (1) 삼성전자 2018년 50:1 액면분할 이력 질문, (2) 사진 첨부 "정리해봐" → 배너 디자인을 AI가 통째로 HTML로 변환하는 워크플로우 인사이트 + 셀프서비스 툴 제품 아이디어
- [[2026-07-15-넷플연가-와인모임-재료비-절감]] — 넷플연가 커리큘럼 페이지 원문 전체 붙여넣기 → 와인 블라인드 테이스팅 모임 재료비 적자 상담. 콘텐츠 재설계+원가 구조 재설계 2단계, 회차별 원가표 산출
- [[2026-07-15-대화-가능-확인]] — "대화 가능?" 1왕복 초단타 세션. 콘텐츠 가치 없음, [[2026-07-15-사용량한도-rate-limit-sdk-확인]] 직전 시점의 단순 핑성 대화
- [[2026-07-15-arvie-화장품-마케팅-재분석]] — K-뷰티 브랜드 인스타 피드 사진 마케팅 상담, "다시 분석해줘" 재요청으로 자산 확장형→갭 분석형(무드→효능 증거) 답변 프레임 전환 관찰
- [[2026-07-14-보관-자동저장-확인-볼린저밴드]] — 무관한 두 왕복: (1) 보관(Archive)→memory-ingest 자동저장 동작 사용자 재확인(stale 게이트·로그 문구·`remembered` 필드 확인법), (2) 볼린저 밴드 개념 설명 1왕복 quick
- [[2026-07-14-quick-html-이미지-첨부-구현]] — 무관한 두 왕복: (1) quick.html에 index.html과 동일한 이미지 드래그 첨부 기능 이식 + Playwright·시스템 Chrome 실브라우저 검증, (2) 메신저 캡처 스크린샷 번역 1왕복
- [[2026-07-15-gpt-realtime-음성입력-길게누르기]] — 전송버튼 길게누르기 GPT Realtime 음성입력 구현(12:37~22:55 장시간 연속 세션). 최초 구현→dist빌드누락/IPv6 떠돌이서버 발견·수정→API크레딧 소진이 /compact·백그라운드 memory-ingest 둘 다 실패시킴 발견→시작부분 끊김 버그 수정(마이크 선오픈+버퍼링)→3단계 색상 피드백→밀어내기 취소 UX 4커밋
- [[2026-07-15-보관메모리확인-하네스재시작-커밋푸시]] — 위 세션 직후(22:55~23:00) 짧은 운영 세션: 보관→memory-ingest 배선 코드 재확인(2차, 라인단위)+야간잡 파일명(`memory-ingest-daily`) 특정, `restart-lampas.sh`로 하네스 안전 재시작(지연 nohup 방식임을 확인, 2026-07-11 기록의 "즉시 pkill" 서술과 불일치 발견), 밀린 로컬 커밋 7개 origin 푸시
- [[2026-07-15-주식-개장전-매수시간-질문]] — "9시 개장 전에도 매수 가능한가?" 1왕복 quick. 국내 주식 동시호가(08:30~09:00)·정규거래(09:00~15:30) 구조 설명. [[2026-07-15-주식투자-10만원-질문]]과 같은 계열 개인 재무 상담
- [[2026-07-15-dark-toss-api-장전매수-코드조사]] — 같은 날 저녁, `dark-system` 내 토스증권 자동매매 앱 `dark-toss-api`가 국장 9시 이전 매수를 실제로 막는지 코드로 조사. 결론: 안 막음(marketHoursOnly 기본 false, manualBuy는 게이트 자체 없음) — 미문서화였던 dark-system 정체 최초 확인
- [[2026-07-15-auto모델-난이도판정-확인ux-개선]] — 위 세션 직후(23:23~23:31). Auto 모델 난이도 판정 로직 file:line 감사(서버 100% 전담 확인) + extreme 티어(Fable 5) 신설 + 중지버튼 2단계 확인·대화목록 롱프레스 보관확인 UI 패턴 2종 + v0.1.27 빌드·유휴시 재시작
- [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]] — "스페이스 만들기" 조사 요청 → Space가 코드베이스에 아예 없다는 사실 확인 후 그 자리에서 Actor/Object 파이프라인을 복제해 신규 엔티티로 설계·구현(DB+API+SDK+web-ai 3앱, 배포는 미진행). 중간에 lampas-web-ai 배포+원격 갈라짐 병합 push도 처리 (23:11~익일 01:14)
- [[2026-07-16-음성입력-진동비프-피드백]] — 전송버튼 길게누르기 음성입력 노랑→빨강 전환에 진동(`navigator.vibrate`, iOS 미지원)+비프(기존 `AudioContext` 재사용, 880Hz) 동시 피드백 추가. [[2026-07-15-gpt-realtime-음성입력-길게누르기]] 3단계 색상 피드백의 후속
- [[2026-07-16-네이버데이터api-콘텐츠자동화-의사결정]] — 네이버 DataLab API 연동 키워드 자동 글쓰기·배포 파이프라인(신규 확인 사실)의 마케팅-상품 얼라인 판단 자동화 상담 1왕복 quick. 6-패턴 프레임워크(채점게이트·LLM-as-judge·배치승인·피드백루프·포트폴리오배분·신호다변화) 제안
- [[2026-07-16-quick-html-폴더선택기-auto모델-구현]] — "quick.html에도 폴더선택·Auto 넣어줘" 1왕복 quick(02:16~02:20). 위 세션이 flag했던 quick.html 작업 폴더 선택기의 실제 구현(index.html 로직 이식) + Auto 모델 추가. vite `public/` 정적 파일은 cp만으로 반영되는 배포 요령 신규 확인
- [[2026-07-16-quick-작업폴더선택기-커밋푸시]] — "밀린 코드 커밋·push해줘" 1왕복 quick. v0.1.27 5개 변경(롱프레스 보관확인·중지버튼 재확인·진동비프·extreme티어 4개는 기록됨 확인) + **quick.html 작업 폴더 선택기**(첫 턴 이후 대화가 폴더에 묶임, 신규 확인 사실) 커밋 `c100edd` push 완료
- [[2026-07-16-srkk-인보이스-james확인]] — SRKK Consulting 인보이스 독촉 이메일 스레드(5통) 번역. 정식 법인명·Scott Jeun/Clara K(The Sylvan Group) 신원 확정, [[srkk]] 등장1과의 시점 관계 정리, "James 확인 후 결제" 결론
- [[2026-07-16-srkk-인보이스-james확인-0243]] — 위 세션과 글자 단위로 동일한 이메일 스레드가 28분 뒤 다른 아카이브 파일명으로 재노출·재번역된 quick 채팅. 아카이브 중복 이상 정황 기록, 새 사실 없음
- [[2026-07-16-lampas-web-product-신규앱-구현]] — "제품 이미지 올리면 분석+마케팅 방향" 신규 앱 `lampas-web-product`(포트 8236) + 백엔드 `product-insights` 공개 모듈을 처음부터 스캐폴딩·구현·검증·커밋push. lampas-web-ai 슬림 베이스 복사, AIDA 카피 개선 후속
- [[2026-07-16-기억버튼-보관통합-NaN토큰버그수정]] — 상단 "기억에 저장"→"기억에 보관" 버튼 개명 + 보관 API로 통합(저장 진입점 3개→UI상 사실상 2개로 수렴) + 사용량 배지 "◔ NaN 토큰" 표시 버그(null 병합) 수정, 2왕복
- [[2026-07-16-메모리인제스트-크레딧버그-근본수정]] — "보관해도 위키 저장이 안 되는 것 같다" 제보 → 큐 실행기(`runner.ts`)가 채팅과 달리 `ANTHROPIC_API_KEY` 제거 처리 누락, 크레딧 0으로 보관 35건 ingest 실패 규명·수정 + 데몬 재시작·재큐잉
- [[2026-07-15-음성입력-3-4초-잘림-수정]] — 음성입력 앞부분 3~4초 잘림 신고→마이크 선오픈+버퍼링 수정(단독 소스, 15:29~15:33). [[2026-07-15-gpt-realtime-음성입력-길게누르기]] 6번 항목(22:28~22:30, 커밋 938d4e4)과 서술이 사실상 동일 — 시각·커밋 불일치를 아카이브 이상 정황으로 기록
- [[2026-07-15-auto모델-기능-최초구현]] — "Auto 기능 만들어줘" 요청으로 난이도 자동선택(easy/medium/hard) 최초 구현(22:42~22:52, 커밋 802af89). 판정모델 Haiku 4.5 선택 근거 + `ANTHROPIC_API_KEY` 크레딧 잔액 0 확인(API 판정 400 실패 근본원인) + rapid-mlx 로컬판정 7/7 검증. [[2026-07-15-auto모델-난이도판정-확인ux-개선]](31분 후)이 감사한 원본 구현 세션
- [[2026-07-15-대화테스트-확인]] — "내 말이 잘 들려?" 1왕복 초단타 세션(22:51:46~22:52:06). 콘텐츠 가치 없음, [[2026-07-15-gpt-realtime-음성입력-길게누르기]] 마지막 항목 직전과 시간대 겹침 — 음성입력 테스트 시도로 추정(미확정)
- [[2026-07-15-dark-upbit-toss-트레이딩앱-기능개발-배포]] — dark-system 내 dark-upbit-api/web·dark-toss-api/web 4앱을 넘나든 장시간 기능개발 세션(21:52~22:38, 7커밋). 초단타 자동제외/수동허용 전환+구조적 LLM 차단, 모바일 표 가로스크롤+확대방지, 모델 카탈로그 날짜필터, 토스 장마감 자동판단 일시정지/재개, 초기화 시 비용도 초기화. dark-system 앱 구성이 위키에 처음 노출된 세션
- [[2026-07-15-세션상주프로세스-백그라운드작업생존-구현]] — 채팅 중 Agent/Workflow 백그라운드 작업이 턴 종료로 끊기던 버그를 대화당 상주 Claude 프로세스로 근본 수정(11:50~12:08 구현·검증) + 재시작·커밋 배포(12:27~12:28, `d20b439`). 커밋 메시지 슬래시 문자열 차단 함정 신규 발견
- [[2026-07-15-웹ai-프롬프트분할-샷변경-되돌리기-space설계]] — 오전 세션(11:39~12:26). lampas-system 구조 분석(문서-실제 괴리 3건)+lampas-web-ai 주요 앱 승격+기능 3종(Object 프롬프트 항목별 수정·샷 변경·채팅 되돌리기) 구현·배포·커밋+Space 설계(권한 채널 장애로 구현 전 중단). [[2026-07-15-스페이스-엔티티-sdk-api-webai-구현]](같은 날 23:11~)보다 11시간 앞선 선행 설계, 두 세션 전략 차이 병기
- [[2026-07-16-tts-stream-elevenlabs-구현착수]] — 봇 메시지 ElevenLabs 실시간 읽어주기(`tts-stream`) 요청. 참고 예시 `dbs/talk-system`(=[[toktalk]])은 작업 폴더 밖이라 읽기조차 차단(SDK 레벨 확인) → 표준 API로 독자 구현 착수, "server.ts 상수 추가" 직후 소스 종료·완료 여부 미확인. 동일 사용자 메시지 6회 반복 노출 관찰
- [[2026-07-16-롱프레스설정-자동음성재생-구현]] — 위 세션 약 1시간 후. 채팅 빈 배경 롱프레스(≈0.5초)→설정 열기 + 자동 음성 재생 토글(브라우저 Web Speech API, ko-KR, 서버비용 없음) 구현·커밋 `3d16ed5` push 완료. ElevenLabs `tts-stream`과 목표는 같으나 방식이 달라 대체/병행 여부 미확정
- [[2026-07-16-봇메시지-클릭읽어주기-구현]] — 봇 말풍선 클릭 시 읽어주기/정지 토글(세 번째 TTS 트리거) 구현 세션(09:10~09:17). 기존 `speak()`/`stopSpeech()` Web Speech API 인프라 재사용으로 확정(ElevenLabs와 무관), 직전 병합 커밋(`dca1422`)이 남긴 충돌 마커로 빌드 실패 중이던 것 발견·수정(package.json 0.1.32, index.html CATEGORIES 병합), Playwright 실브라우저 검증. 커밋은 하지 않고 다음 세션에 위임
- [[2026-07-16-봇메시지-클릭읽어주기-커밋푸시]] — "모두 commit 해줘"→"푸시 해줘" 2왕복 quick 운영 세션(위 구현 세션 4분 뒤). 커밋 `4cff973`→rebase→`9e45de9` push, v0.1.36. 직전 기록 버전(0.1.30)과 5개 버전 간극 관찰. 구현 방식 미상이었던 부분은 위 세션 발견으로 해소됨

## Entities

- [[lampas-harness]] — Claude Agent SDK 기반 웹 하네스 (맥미니 데몬, 큐, 채팅 UI)
- [[lampas-studio]] — Lampas AI 이미지 생성 스튜디오 제품 (lampas-api + lampas-web-sdk + lampas-web-product, sdk.lampas.io)
- [[lampas-web-ai]] — lampas-studio 내 대화형 AI 스튜디오 앱(ai.lampas.io), 2026-07-15부터 주요 앱. actorFlow.ts 단일 상태머신 파일(2,658줄)
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
- [[netpeul-yeonga]] — 소셜/취미 정기 모임 플랫폼(넷플연가). progdesigner가 엘레망 와인샵 기반 블라인드 테이스팅 모임 모임장으로 참여
- [[srkk]] — SRKK Consulting Pte Ltd(도메인/Microsoft 계정·인보이스 건, 등장1·3은 사실상 동일 확정) vs SRKK Group(Azure 펀딩 영업메일, 등장2)의 동일성은 여전히 미확정
- [[fy-group]] — FY Group Pte Ltd(싱가포르), CEO Keira Zhang. cwc-lab-singapore에 이탈리아 위스키를 공급하다 선적 지연 분쟁의 상대방이 됨
- [[dark-system]] — progdesigner 개인 소유 pnpm 모노레포(`~/Works/dark/dark-system`). 최소 4앱 확인: dark-upbit-api/web, dark-toss-api/web(자매 앱), 나머지 앱은 미조사
- [[dark-upbit-api]] — dark-system 내 Upbit(코인) API 자동매매 서비스. 초단타 스타일 자동제외/수동허용+엔진교체로 구조적 LLM 차단, 모델 카탈로그 날짜필터
- [[dark-upbit-web]] — dark-upbit-api 대시보드 웹. 모바일 표 가로스크롤·확대방지·스타일 드롭다운 하드코딩 사고 사례
- [[dark-toss-api]] — dark-system 내 토스증권 API 자동매매 서비스. 기본 설정상 장시간 게이트 없음(marketHoursOnly false), manualBuy는 시간체크 경로 자체가 없음. 국장/미장 자동 장이동 시 양쪽마감 자동판단 일시정지·재개 구현됨
- [[dark-toss-web]] — dark-toss-api 대시보드 웹. 모바일 표 가로스크롤·확대방지·장마감 일시정지 배지

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
- [[ai-design-to-html-banner-conversion]] — 배너 디자인 이미지를 AI가 이미지 조각 조합 없이 통째로 HTML로 변환하는 워크플로우 인사이트 + 셀프서비스 툴 제품 아이디어
- [[wine-meetup-cost-reduction]] — 와인 블라인드 테이스팅 모임 재료비 구조 재설계: 콘텐츠 재설계(가격 블라인드·BYOB)→원가 구조 재설계(회차당 2병 상한·재료비 실비) 2단계 프레임워크
- [[cwc-fy-group-whisky-dispute]] — CWC Lab-FY Group 간 이탈리아 위스키(강남 위스키클럽용) 선적 지연·환불·법적 조치 언급된 장기 분쟁 타임라인(2024-10~2025-01, 2025-01-06 시점 미해결)
- [[content-automation-decision-framework]] — 네이버 DataLab API 키워드 자동화 파이프라인의 마케팅-상품 얼라인 의사결정 6-패턴 프레임워크(채점게이트→배치승인→피드백루프 순 도입 권장)

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
- [[self-hosted-agent-server-ops]] — 에이전트 턴을 호스팅하는 서버 재시작·종료·디버그 안전 규칙(stray-port·IPv6 변종·restart-kills-own-turn·pkill-prod·stale-dist-build)
- [[naver-blog-mcp-posting]] — naver-blog-mcp 발행: 순수 텍스트 본문·SRT 세션 갱신(headed 캡차)·서버 버그 대응
- [[naver-blog-tag-seo]] — 네이버 블로그 SEO 태그 30개 생성(본문 근거 필수·대표+롱테일 배분·무관 인기태그 저품질 위험)
- [[bank-refund-invoice-reconciliation]] — 애매한 은행 환입/입금을 인보이스와 대조·검증하고 거래처 회신 잡기(코드 단정 금지·차액 명시·통화 확인·다른 소스 전수 검색으로 배제 후보 확정)
- [[sdk-claude-code-vs-api-billing]] — Claude Agent SDK 실행이 구독(OAuth) 과금인지 API 종량인지 판별하고 options.env로 전환·5시간/주간 사용량 한도 조회(usage_EXPERIMENTAL/rate_limit_event)
- [[multi-repo-safe-bulk-update]] — 다중 git 저장소 일괄 업데이트 시 미커밋 변경 저장소만 골라 보류
- [[rebase-local-feature-onto-refactored-remote]] — 원격이 구조를 리팩터한 코드 위에 로컬 미커밋 기능을 rebase로 이식
- [[credit-ledger-balance-pattern]] — 원장(ledger) 방식 크레딧 잔액 시스템 이식 시 확인할 것들(네이밍 역전·동시성·검색 대상 불일치)
- [[playwright-system-chrome-verify]] — Playwright 브라우저 바이너리 미설치 시 macOS 시스템 Chrome(`channel:'chrome'`)으로 실브라우저 종단 검증
- [[realtime-voice-mic-buffer-before-connect]] — 누르고-말하기 실시간 음성입력에서 연결 전 마이크 선오픈+로컬 버퍼링으로 시작부분 유실 방지, 3단계(노랑/빨강/초록) 시각 피드백 + 진동/비프
- [[config-flag-gate-audit]] — "시스템이 특정 상황을 막는가" 질문에 코드로 답할 때: 정보 존재 여부→대체 판정 로직→게이트 호출부 전수→플래그 기본값→진입점 우회 여부→코드 vs 외부시스템 책임 분리 순 추적
- [[destructive-action-inline-confirm]] — 되돌리기 어려운 버튼(중지·보관)에 모달 없이 인라인 확인: 같은 버튼 2회클릭 타임아웃 방식 vs 롱프레스+확인창 방식
- [[clone-sibling-entity-pipeline]] — 기존 엔티티(Actor/Object)와 동형인 새 엔티티(Space) 추가 시 DB→API→SDK UI→실사용 지점을 계층별로 그대로 복제하는 절차
- [[new-app-scaffold-from-slim-base]] — 모노레포에 새 프론트 앱+백엔드 모듈 추가 시 기존 앱 중 슬림한 쪽을 복사 베이스로 고르고 독립 공개 모듈로 배선하는 절차
- [[null-merge-nan-display]] — `Object.assign({}, null, {...})`이 필드를 못 채워 산술 연산이 NaN으로 새는 버그 진단(생성측 빈객체 안만들기+표시측 `?? 0` 둘 다 방어)
- [[llm-judge-fallback-chain]] — LLM 분류/라우팅 판정을 유료 API→로컬 LLM→휴리스틱 3단으로 저하 설계(모호하면 상위 티어, few-shot 검증, 최상위 티어는 안전망 도달 불가)
- [[trading-feature-flag-auto-exclude-manual-allow]] — "자동은 막되 수동은 허용" 요청 시 전면차단→자동전용제외 플래그 전환+엔진교체로 구조적 위험경로(LLM 호출) 차단, 수동선택 보호
