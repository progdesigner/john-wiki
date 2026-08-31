---
tags: [ai-context, summary]
created: 2026-07-12
updated: 2026-08-31
---
# AI_CONTEXT — 핵심 기억 요약

> 하네스가 매 대화 시작 시 이 파일을 시스템 프롬프트에 주입한다. 40줄 이내 유지 (규칙: CLAUDE.md).

## 사용자
- [[progdesigner]] (John, bacchus.dev@gmail.com) — CWC([[cwc-commerce]]) 소속 개발자·디자이너. **응답은 항상 한국어**(도구의 사람 읽는 필드까지). 기억은 비자명한 것만 저장, 임시 스크립트는 `tools/`·운영은 `scripts/`. 네이버 블로그 `study-ai-what`에 람파스 기억 시스템 공개 연재 → 블로그/SEO 태그 요청 반복([[naver-blog-tag-seo]]).

## 진행 중 프로젝트
- [[lampas-harness]] — Claude Agent SDK 웹 하네스. 맥미니 launchd 데몬(웹 8787/API 3787), 원격은 Tailscale 사설 VPN 권장. 기본 모델 claude-opus-4-8, "Auto"는 easy/medium/hard/extreme(Fable 5) 4단계 자동 배정([[model-selection]]) — 판정 1순위 Haiku 4.5(API)는 **`ANTHROPIC_API_KEY` 크레딧 잔액 0으로 실패 중**, [[rapid-mlx]] 로컬 LLM이 실질 판정 경로. `.cursor/mcp.json` 외부 MCP를 Claude 세션에 노출([[harness-mcp-bridge]], 예 [[naver-blog-mcp]]). 프로젝트 스킬 번들 보유(마케팅·Remotion·운영, 커밋 `ef36c5f`). 음성입력(길게누르기 GPT Realtime, 노랑→빨강 전환+진동/비프)·TTS 3중 트리거(롱프레스 자동재생·봇말풍선 클릭읽기, 모두 Web Speech API 기반 확정; ElevenLabs `tts-stream`은 착수 후 완료 여부 미확인) 구현 완료([[realtime-voice-mic-buffer-before-connect]]). 배경작업이 턴 종료로 끊기던 버그를 대화당 상주 프로세스로 근본 수정(커밋 `d20b439`) → [[harness-background-process-lifecycle]]. 최신 확인 버전 v0.1.37(2026-07-17 빌드; 커밋 확인된 최신은 v0.1.36 `9e45de9`).
- [[john-wiki]] — 이 위키. 저장 진입점: 상단 "기억에 보관" 버튼(2026-07-16~ 구 "기억에 저장"에서 개명, 보관 API로 통합)·대화목록 롱프레스 보관·야간 자동 ingest — 앞 둘은 이제 동일 동작. 능동 조회(검색 tool)는 미완.
- [[toktalk]] — 에피소드 beat 플레이·배경 전환 시스템 개발 중.
- 코딩 외 [[harness-as-business-assistant]]: 이메일 대행·인보이스 대조·마케팅 컨설팅(사진 첨부 해석) 반복.
- [[progdesigner]]가 네이버 DataLab API 연동 키워드 자동 글쓰기·배포 파이프라인을 이미 운영 중(별도 시스템, study-ai-what과 무관 추정) → [[content-automation-decision-framework]].
- [[lampas-studio]] — 이미지 생성 스튜디오 제품(로컬 저장소명 `lampas-system`). `pnpm install`이 저장소 전체에서 실패(누락된 `@iileex/shared`, 기존 이슈). [[lampas-web-ai]]가 주요 앱(actorFlow.ts 단일 거대 파일에 상태머신 다수, ~2,658줄). Actor·Object에 이어 세 번째 엔티티 **Space**(DB+API+SDK+web-ai)와 신규 앱 **lampas-web-product**(제품 이미지→마케팅 분석, 포트 8236) 구현. 2026-07-18 기준: Space 백엔드는 배포 완료, `lampas-web-sdk`(Space 생성 UI)·`lampas-web-product`는 인프라 미발급으로 미배포. web-ai 등록하기 플로우에 사진 우선 Gemini Vision 자동분류(Actor/Object/Space) 신설·배포 완료 → [[2026-07-18-web-ai-등록플로우-사진분류-배포]].
- `~/Works` 하위 git 저장소 12개 → [[works-project-portfolio]] (다수 미조사). [[dark-system]](개인 소유)은 트레이딩 봇 4앱 확인 — [[dark-upbit-api]]/[[dark-toss-api]]+각 web. [[dark-toss-api]] 기본 설정상 장시간 게이트 없음(`marketHoursOnly:false`), 2026-07-17 `decide-signal.ts` 신호 판정 로직 추가 보고(미검증).
- [[cwc-system]]에 [[elevino-system]] DB(원장 방식 크레딧)를 재사용해 멤버십 검색+크레딧 지급/차감 관리자 기능을 이식하는 설계 완료, **구현은 하네스 권한 장애로 미완**(재개 필요) → [[2026-07-15-cwc-system-멤버십-크레딧-이식-설계]].

## 확정된 결정
- 원격 접속: Tailscale 사설 VPN 권장, 공유기 포트포워딩 비권장 (설치 여부는 미확인).
- 장기 기억은 git markdown 위키(사람이 감사 가능) — SQLite 아님.
- 로컬 LLM은 Rapid-MLX 상주 ([[local-llm-rapidmlx-install]]).
- 사용자 대면 이름은 **"람파스"** (내부 식별자·저장소명 `lampas-harness`는 유지) → [[lampas]]
- 위키 회수(recall) 연결: AI_CONTEXT.md 상시 주입 + index.md 능동 조회 + 스킬 카탈로그 노출 + 야간 자동 ingest.
- 하네스 Claude 실행은 **기본 Claude Code 구독(OAuth) 과금**(API 종량 아님). 대화별 "API 사용" 토글로 전환 가능 → [[sdk-claude-code-vs-api-billing]]
- **API 크레딧 소진은 `/compact`뿐 아니라 백그라운드 `memory-ingest`도 실패시킴**(세 번째 확인된 누락 진입점: `runner.ts`·`compactClaudeSession`) — 위키 최신성은 로그로 주기적 확인 필요 → [[long-term-memory-architecture]]

## 업무 맥락
- CWC 엘레망 광화문 사무실: [[sylvan-korea]] 공간 공동 사용(전대) 동의 절차 진행 중(2026-07 기준), 임대인 측 [[dongwon-building]].
- [[progdesigner]]는 엘레망 와인샵 운영자 겸 [[netpeul-yeonga]] 와인 블라인드 테이스팅 모임장. 재료비 적자 해소 콘텐츠·원가 재설계 상담 → [[wine-meetup-cost-reduction]].
- [[srkk]](싱가포르) 도메인·Microsoft 계정 무단 연장 의혹 → Scott과 협의해 미연장 확정. 은행거래 "Princ" 상대방 식별은 여전히 미해결.
- [[cwc-lab-singapore]]가 [[fy-group]](Keira Zhang)로부터 수입하는 이탈리아 위스키 선적 지연 분쟁 — 환불 US$220,920.10·법적 조치 언급, 2025-01-06 시점 미해결 → [[cwc-fy-group-whisky-dispute]]. "Princ" 미스터리와는 무관 확인됨.
