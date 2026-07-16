---
tags: [topic, claude-agent-sdk, model, config]
created: 2026-07-07
updated: 2026-07-16
---
# 하네스 모델 선택

`[[lampas-harness]]`에서 요청마다 실행 모델을 고르는 방식과 그 배경.

## 배경 — 모델 미고정 문제
코드·잡·`.env`·launchd·`.claude/settings.json` 어디에도 `model`이 지정돼 있지 않았다.
`query()` 옵션에 `systemPrompt: {preset: "claude_code"}`, `settingSources: ["project"]`만 있고 `model`은 없음.
→ SDK가 Claude Code CLI의 **기본 모델 별칭("default")**을 따르므로, CLI 버전/계정에 따라
조용히 다른 모델로 실행돼 비용·성능이 달라질 수 있다(재현성 위험). 코드만 봐서는 ID 확정 불가.

## 해결 — UI 모델 선택기
채팅 입력창 바로 위 드롭다운. 선택값을 `localStorage`에 저장하고 매 요청에 전송.
- 기본값: **`claude-opus-4-8`**
- Claude 전 모델 + OpenAI 라이브 목록(`/api/models`가 계정 접근 가능 모델을 라이브로 반환).

서버가 내려준 Claude 목록(2026-07-07 시점):
```
claude-fable-5, claude-opus-4-8(기본), claude-opus-4-7, claude-opus-4-6,
claude-sonnet-5, claude-sonnet-4-6, claude-haiku-4-5-20251001,
claude-opus-4-1-20250805, claude-opus-4-5-20251101, claude-sonnet-4-5-20250929
```
드롭다운은 페이지 로드 시 `/api/models`를 1회만 받으므로, 목록 갱신엔 브라우저 새로고침 필요.

## 권장
- 최고 성능: `claude-opus-4-8` / 비용 절감: `claude-sonnet-5`.
- 코드 고정이 필요하면 `.env`의 `HARNESS_MODEL`을 chat/runner 양쪽에서 읽게 배선.
- 모델별 API 단가·Claude Code 시간당 비용 추정은 [[claude-model-pricing]] 참조 (기본 Opus 4.8 = 시간당 ~$10~25).

## 로컬→고급 모델 수동 에스컬레이션 (2026-07-11 관찰)
로컬 LLM(`qwen3.5-35b-8bit`, [[local-llm-on-apple-silicon]])이 첫 시도를 하고, 결과가 부실하면
사용자가 **"고급 모델로 다시 실행해줘"**로 상위 모델(Claude)에 재위임하는 패턴이 실사용에서 확인됨.
- 사례: desktop 설치 스크립트를 로컬 모델이 실행 불가 상태로 생성(권한/Electron 바이너리/launchctl 순서
  3중 오류) → 상위 모델이 재작성·검증. → [[2026-07-11-desktop-퀵채팅-설치-스크립트]]
- **관찰**: launchd·Electron 같은 macOS 특화 devops 영역은 로컬 모델이 그럴듯하지만 실행 불가한 코드를 내는
  경향. 도메인 지식 필요 작업은 상위 모델 권장.
- **UX 신호**: 작은 모델이 git commit·복잡 작업 요청에 "상위 모델이 처리하는 것이 적절합니다"라며 위임하면,
  사용자가 같은 요청을 2~4회 재전송하게 됨. 라우팅/에스컬레이션이 사용자에게 불투명.

## 프로바이더 모델이 목록에 하나도 안 뜰 때 (2026-07-13)
`/api/models`가 특정 프로바이더(예 Google) 모델을 하나도 안 내려주면, **셸의 빈 env 변수가 `.env`의 진짜
키를 가리는** 함정을 먼저 의심하라. `GOOGLE_API_KEY=`(빈 export) → dotenv가 `.env` 값을 안 넣음 →
`googleModels()`가 `[]` 반환 → Google 그룹이 통째로 사라진다. 2026-07-08~11 미해결 "Google Models 401"의
유력한 근본 원인. 진단·수정: [[env-empty-var-shadows-dotenv]].

## Claude 과금 모드 토글 (2026-07-15)
Claude 모델을 고르면 입력창 하단에 "API 사용" 체크박스가 뜬다 — 켜면 API 종량, 끄면 Claude Code
구독(OAuth) 과금. 하네스 기본은 **구독(OAuth)**이다. 구독 모드에선 컨텍스트 모달이 비용($) 대신
컨텍스트 잔여율을 보인다. 판별·전환 절차: [[sdk-claude-code-vs-api-billing]] · 비용 비교: [[claude-model-pricing]].
→ [[2026-07-15-과금모드-토글-컨텍스트표시]]

## "Auto" — 난이도 자동 선택 (v0.1.25 도입, 2026-07-15 extreme 확장)
모델 드롭다운의 "Auto — 난이도 자동 선택" 옵션. **판단은 서버(`src/server.ts`) 100% 전담**, 클라이언트는
표시만 한다(옵션 노출 865-869행, 결과 표시 `data.auto` 1675행) — [[2026-07-15-auto모델-난이도판정-확인ux-개선]]에서
file:line 근거로 확인.

`runChatTurn()`(3300-3306행)이 auto 감지 시 `resolveAutoModel()`(716-737행) 호출, 3단계 순차 폴백:
1. **Claude API 판정** `judgeTierClaude()`(669-690행) — `claude-haiku-4-5`(`LAMPAS_AUTO_JUDGE`로 재정의)
2. **로컬 LLM 판정** `judgeTierLocal()`(692-714행) — rapid-mlx, 5초 타임아웃
3. **휴리스틱 폴백** `heuristicTier()`(661-667행) — 키워드(`구현|리팩터|디버그|배포|fix|refactor|...`)·
   메시지 길이(>300자 hard, >80자 medium)·사진 첨부(medium 이상). LLM 판정 실패해도 턴은 안 막힘.

**티어→모델 매핑** (env로 재정의): easy→`claude-haiku-4-5`(`LAMPAS_AUTO_EASY`),
medium→`claude-sonnet-5`(`LAMPAS_AUTO_MEDIUM`), hard→`claude-opus-4-8`(`LAMPAS_AUTO_HARD`),
**extreme→`claude-fable-5`**(`LAMPAS_AUTO_EXTREME`, 2026-07-15 신설).

**extreme 티어 설계 의도**: hard보다 명백히 상위(대규모 아키텍처 개편, 다중 시스템 교차 리팩터,
프로덕션 장애 대응, 보안 설계)로 제한, 모호하면 hard 선택하도록 프롬프트에 명시(값비싼 모델 남용 방지).
**휴리스틱 폴백은 hard까지만** — extreme은 실제 LLM 판정으로만 도달 가능, 판정 실패 안전망에서
근거 없이 최상위 모델로 튀지 않게 하는 의도적 비대칭.

## 관련
- [[lampas-harness]] / [[lampas]] / [[2026-07-06-lampas-harness-구축]] / [[2026-07-11-desktop-퀵채팅-설치-스크립트]] / [[2026-07-13-람파스-누적운영기억-이관]] / [[2026-07-15-auto모델-난이도판정-확인ux-개선]]
- [[local-llm-on-apple-silicon]] / [[env-empty-var-shadows-dotenv]]
