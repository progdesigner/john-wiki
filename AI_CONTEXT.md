---
tags: [ai-context, summary]
created: 2026-07-12
updated: 2026-07-16
---
# AI_CONTEXT — 핵심 기억 요약

> 하네스가 매 대화 시작 시 이 파일을 시스템 프롬프트에 주입한다. 40줄 이내 유지 (규칙: CLAUDE.md).

## 사용자
- [[progdesigner]] (John, bacchus.dev@gmail.com) — CWC([[cwc-commerce]]) 소속 개발자·디자이너. **응답은 항상 한국어**(도구의 사람 읽는 필드까지). 기억은 비자명한 것만 저장, 임시 스크립트는 `tools/`·운영은 `scripts/`. 네이버 블로그 `study-ai-what`에 람파스 기억 시스템 공개 연재 → 블로그/SEO 태그 요청 반복([[naver-blog-tag-seo]]).

## 진행 중 프로젝트
- [[lampas-harness]] — Claude Agent SDK 웹 하네스. 맥미니 launchd 데몬(웹 8787/API 3787), 원격 접속은 Tailscale 사설 VPN 권장. 기본 모델 claude-opus-4-8, "Auto" 선택 시 서버가 easy/medium/hard/**extreme**(Fable 5, v0.1.27~) 4단계로 난이도 판정해 자동 배정([[model-selection]]). `.cursor/mcp.json` 외부 MCP를 Claude 세션에 노출([[harness-mcp-bridge]]) — 예 [[naver-blog-mcp]]. 로컬 LLM은 [[rapid-mlx]](launchd 상주). 프로젝트 스킬 시스템+대량 스킬 번들 보유(마케팅·Remotion영상·개발운영, 커밋 ef36c5f). 전송버튼 길게누르기 GPT Realtime 음성입력 구현 완료, 노랑→빨강 전환에 진동+비프 피드백 추가(2026-07-16)([[realtime-voice-mic-buffer-before-connect]]). v0.1.27(롱프레스 보관확인·중지버튼 재확인·진동비프·extreme티어·quick.html 작업폴더선택기) 커밋 `c100edd` push 완료.
- [[john-wiki]] — 이 위키. 공통 장기 기억 저장소. 저장 진입점: 상단 "기억에 보관" 버튼(2026-07-16~ 옛 "기억에 저장"에서 개명, 보관 API로 통합)·대화목록 롱프레스 보관·야간 자동 ingest — 앞 둘은 이제 동일 동작. 능동 조회(검색 tool)는 미완.
- [[toktalk]] — 에피소드 beat 플레이·배경 전환 시스템 개발 중.
- 코딩 외 [[harness-as-business-assistant]]: 이메일 대행·인보이스 대조·마케팅 컨설팅(사진 첨부 해석) 반복.
- [[progdesigner]]가 네이버 DataLab API 연동 키워드 자동 글쓰기·배포 파이프라인을 이미 운영 중(별도 시스템, study-ai-what과 무관 추정) — 마케팅-상품 얼라인 판단 자동화 상담 → [[content-automation-decision-framework]].
- [[lampas-studio]] — 스튜디오 레퍼런스·Instagram 통합. 로컬 저장소명 `lampas-system`. `pnpm install`이 저장소 전체에서 실패(누락된 `@iileex/shared`, origin/main도 동일 — 기존 이슈). Actor·Object에 이어 세 번째 촬영 대상 **Space(공간)** 엔티티(DB+API+SDK+web-ai)와 신규 독립 앱 **`lampas-web-product`**(제품 이미지→마케팅 분석, 포트 8236, `product-insights` 공개 API)를 커밋·push 완료(`d4c4ffd`/`8f1b066`) — **배포는 둘 다 아직 안 됨**(Space 3앱 + product-insights S3/CloudFront ID 미발급, 재개 시 우선 확인).
- `~/Works` 하위 git 저장소 12개 → [[works-project-portfolio]] (다수는 아직 내용 미조사). 그중 [[dark-system]](개인 소유)은 앱 [[dark-toss-api]](토스증권 자동매매 봇) 확인됨 — 기본 설정상 장시간 게이트 없음(`marketHoursOnly:false`), 수동매수도 시간체크 없음 → 08시대 매수 주문 가능, 안전 전환 여부는 미결정.
- [[cwc-system]]에 [[elevino-system]] DB(원장 방식 크레딧)를 그대로 재사용해 멤버십 검색+크레딧
  지급/차감 관리자 기능을 이식하는 설계 완료, **구현은 하네스 권한 장애로 미완**(재개 필요) →
  [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]].

## 확정된 결정
- 원격 접속: 공개 노출 회피(하네스가 맥 전체 제어) — Tailscale 사설 VPN 권장, 공유기 포트포워딩 비권장. (근거: 2026-07-06 세션; 당시 실제 설치는 미진행 — 설치 여부·구체 방식은 재확인 필요.)
- 장기 기억은 git markdown 위키(사람이 감사 가능) — SQLite 아님.
- 로컬 LLM은 Rapid-MLX 상주 ([[local-llm-rapidmlx-install]]).
- 사용자 대면 이름은 **"람파스"**로 표기 (내부 식별자 env `HARNESS_*`·저장소명 `lampas-harness`는 유지). → [[lampas]]
- 2026-07-12: 위키 회수(recall) 연결 — AI_CONTEXT.md 상시 주입 + index.md 능동 조회 + 스킬 카탈로그 노출 + 야간 자동 ingest.
- 2026-07-15: 하네스 Claude 실행은 **기본 Claude Code 구독(OAuth) 과금** (API 종량 아님). 대화별 "API 사용" 토글로 전환 가능, 구독 모드는 컨텍스트 잔여율 표시. → [[sdk-claude-code-vs-api-billing]]
- 2026-07-15: **API 크레딧 소진은 `/compact`뿐 아니라 백그라운드 `memory-ingest`(이 위키 야간 적재)도 실패시킴** — 위키 최신성은 로그로 주기적 확인 필요. → [[long-term-memory-architecture]]

## 업무 맥락
- CWC 엘레망 광화문 사무실: [[sylvan-korea]] 공간 공동 사용(전대) 동의 절차 진행 중 (2026-07 기준), 임대인 측 [[dongwon-building]].
- [[progdesigner]]는 엘레망 와인샵 운영자 겸 [[netpeul-yeonga]] 와인 블라인드 테이스팅 모임 모임장. 고가 와인 재료비 적자 해소를 위한 콘텐츠·원가 재설계 상담 진행 → [[wine-meetup-cost-reduction]].
- 2026-07-14: [[srkk]](싱가포르) CWC 도메인·Microsoft 계정 승인 없는 연장 의혹 → Scott과 협의해 **양쪽 다 미연장으로 확정**. 은행거래 `PRINC CR`의 상대방("Princ") 식별은 세 세션에 걸쳐 아직 미해결.
- 2026-07-14: [[cwc-lab-singapore]]가 [[fy-group]](싱가포르, Keira Zhang)로부터 수입하는 이탈리아 위스키(강남 위스키클럽용) 선적 지연 분쟁 — 환불 US$220,920.10·지연배상금·법적 조치 언급, 2025-01-06 시점 미해결 → [[cwc-fy-group-whisky-dispute]]. "Princ" 미스터리와는 무관 확인됨.
