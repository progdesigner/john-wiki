---
tags: [ai-context, summary]
created: 2026-07-12
updated: 2026-10-01
---
# AI_CONTEXT — 핵심 기억 요약

> 하네스가 매 대화 시작 시 이 파일을 시스템 프롬프트에 주입한다. 40줄 이내 유지 (규칙: CLAUDE.md).

## 사용자
- [[progdesigner]] (John, bacchus.dev@gmail.com) — CWC([[cwc-commerce]]) 소속 개발자·디자이너. **응답은 항상 한국어**(도구의 사람 읽는 필드까지). 기억은 비자명한 것만 저장. 네이버 블로그 `study-ai-what`에 람파스 기억 시스템 공개 연재 중 → [[naver-blog-tag-seo]].

## 진행 중 프로젝트
- [[lampas-harness]] — Claude Agent SDK 웹 하네스(맥미니 launchd 데몬, 원격은 Tailscale 권장). Auto 모델 4단계(easy~extreme/Fable 5) → [[model-selection]] — 판정 1순위 Haiku 4.5(API)는 **`ANTHROPIC_API_KEY` 크레딧 잔액 0으로 계속 실패 중**, [[rapid-mlx]] 로컬 LLM이 실질 판정 경로. `apps/wiki`(위키 브라우징 뷰어)·`apps/browser`(AI 조작 Chromium, WebContentsView) 신설 완료. 기본 과금은 Claude Code 구독(OAuth), API 종량 아님 → [[sdk-claude-code-vs-api-billing]].
- [[john-wiki]] — 이 위키. 저장 경로: "기억에 보관" 버튼·대화목록 롱프레스·야간 자동 ingest(앞 둘은 동일 동작으로 통합) + `apps/wiki` 사람용 브라우징. 능동 조회(검색 tool)는 미구현.
- [[toktalk]] — 사만다(Her) 페르소나 확정, `app.toktalk.ai`+Toss 미니앱(`talk-app-toss-samantha`) 운영 중. 서브앱 `virtual.toktalk.ai`([[tavus]] 기반 사진 아바타+실시간 영상통화) 배포 완료. NSFW는 플러팅 허용/노골적 표현 거부 경계 확정.
- [[lampas-studio]] (저장소 `lampas-system`) — 최대 활성 프로젝트, Lampas+Dalar+Talk 3개 제품 라인. **미해결 모순 2건**: DB 종류(2026-07 코드확인 PostgreSQL vs `AGENTS.md` 명시 MySQL), [[lampas-web-spot]] 지도 프로바이더("OSM 확정" 발표 후 카카오 키 재발급 — 최종 미정). 스포츠 클립 파이프라인([[lampas-agent]] Clips/Pulse/Fixs + Copy/Reels/Edit/Package/Flow/Tools/Trends/Status/Voice/Music/Spot 자매 앱군)이 최대 활동 영역. [[dalar]] 라인의 소비자 제품 [[dalar-web-first]](`first.dalar.ai`, AI 돌잔치 영상)가 실사용 운영 중. [[posthog]] 사용자 여정·결제 전환 분석을 36개 웹 전체에 도입 완료(2026-09-29, First 퍼널 우선) — 결제 완료 실수신은 미검증.
- `~/Works` 저장소 12개 → [[works-project-portfolio]] (다수 미조사). [[dark-system]](개인 트레이딩 봇 4앱). [[cwc-system]]/[[elevino-system]]: 프로덕션이 실제로는 cwc-system의 `apps/elevino-*`에서 배포되고 있었음이 2026-09-25 장애로 드러나 복구(다운타임 23분) → [[prod-rollback-source-of-truth-verify]].

## 확정된 결정
- 원격 접속: Tailscale 사설 VPN(공유기 포트포워딩 비권장), 설치·가동 확인됨.
- 장기 기억은 git markdown 위키(사람이 감사 가능) — SQLite 아님.
- 로컬 LLM은 Rapid-MLX 상주 ([[local-llm-rapidmlx-install]]).
- 사용자 대면 이름은 **"람파스"** (내부 식별자·저장소명 `lampas-harness`는 유지) → [[lampas]]
- 위키 회수(recall) 연결: AI_CONTEXT.md 상시 주입 + index.md 능동 조회 + 스킬 카탈로그 + 야간 자동 ingest.
- `/compact`·백그라운드 memory-ingest의 API 과금 누락 진입점 3곳(`runner.ts`·`compactClaudeSession` 등)은 모두 발견·수정 완료 → [[long-term-memory-architecture]]
- 배포까지 진행하면 항상 커밋한다 (2026-09-24 확정 규칙).

## 업무 맥락
- CWC 엘레망 광화문 사무실: [[sylvan-korea]] 공간 공동 사용(전대) 동의 절차 진행 중(2026-07 기준), 임대인 측 [[dongwon-building]].
- [[progdesigner]]는 엘레망 와인샵 운영자 겸 [[netpeul-yeonga]] 와인 블라인드 테이스팅 모임장 → [[wine-meetup-cost-reduction]].
- [[srkk]](싱가포르) 도메인·MS 계정 무단연장 의혹은 미연장으로 확정. 은행거래 "Princ" 상대방 식별은 미해결.
- [[cwc-lab-singapore]]가 [[fy-group]]로부터 수입하는 위스키 선적 지연 분쟁, 2025-01-06 시점 미해결 → [[cwc-fy-group-whisky-dispute]].
