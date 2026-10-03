---
tags: [entity, lampas-studio, mcp, oauth]
created: 2026-10-03
updated: 2026-10-03
---
# lampas-api-mcp

`lampas-system` 모노레포 내 AI 게이트웨이 MCP 서버(`apps/lampas-api-mcp`). `lampas_*` 이름의
도구로 [[lampas-studio]](`lampas-api`) 기능을 외부 AI 클라이언트에 노출한다. 의존성은 저장소 전체에서
유일하게 `@modelcontextprotocol/sdk@^1.25`+`zod@^3.24.1`를 쓴다(→ [[lampas-system-ai-call-architecture-audit]]).

## 연결 방식 2종

| 방식 | 대상 | 인증 |
|------|------|------|
| 로컬 `stdio` | Claude Desktop | 로컬 설정 파일 |
| 원격 HTTP MCP | ChatGPT·Claude 웹 커넥터 | OAuth(DCR) → Lampas Secret Key 검증 → MCP 전용 토큰 |

운영 연결 URL: `https://api.lampas.io/mcp` — 기존 `lampas-api` HTTPS 도메인을 그대로 재사용하고
MCP 프로세스는 별도 포트로 돌린다(기존 API 경로와 충돌 없음 확인됨).

## 2026-09-26 — 작업 시작(다른 세션, 미커밋)

[[2026-09-26-lampas-system-수정사항]] 세션 당시 이미 `src/oauth*.ts`·`http.ts`·테스트·예제·
`env/process.json` 등 20개 파일이 작업 중이었으나 **다른 세션 소유로 간주돼 커밋되지 않고 남아
있었다**([[2026-09-26-fit-서비스-개선]]도 같은 변경분을 "다른 세션 진행 중"이라 손대지 않고
넘어감).

## 2026-09-27~28 — 구현·배포·버그수정 (origin 세션)

[[2026-09-27-lampas-api-mcp-oauth-원격mcp-chatgpt클로드연동]] 세션이 위 작업을 실제로 완성했다.

- 로컬 `stdio` 유지 + 원격 HTTP MCP·OAuth 신규 구현. **요청마다 사용자 인증을 분리**해 다른 계정의
  크레딧이 섞여 쓰이지 않게 함 — 서로 다른 두 사용자의 동시 요청이 각자의 Lampas 인증으로 전달되는
  것을 통합 테스트로 확인.
- 원격 도구는 서버 로컬 파일을 읽을 수 없도록 제한. 인증정보는 암호화 저장. 업로드 제한·오류 처리
  보강.
- 16개 테스트·타입검사·빌드 통과, 독립 설치 검증 → 운영 배포(`https://api.lampas.io/mcp`).
- **버그**: 서버에 걸어둔 `Referrer-Policy: no-referrer` 때문에 Chrome이 OAuth 연결 폼 제출 시
  `Origin: null`을 보내고 서버가 거부 — "통합 테스트 통과"가 실제 **브라우저 폼 제출은 검증하지
  않은** 격차였음이 드러남(사용자가 "연결이 안 되고 계정도 모르겠다"고 재현해서 발견). 헤더 수정 +
  **키 입력 즉시승인 → 계정 미리보기 후 최종승인**으로 흐름 변경(이름·이메일·계정ID·크레딧 잔액을
  Secret Key의 실제 API 인증 결과로 표시)해 수정·재배포, Chrome 실제 제출 확인.
- **확정 사실**: 연결되는 계정은 **입력한 Secret Key 소유자의 Lampas 계정**이다.

## 2026-10-03 — 재질문 미응답

사용자가 "ChatGPT 연동 방법 다시 알려줘"라고 재질문했으나 소스 트랜스크립트가 거기서 끝나
어시스턴트 응답 여부가 **미확인**으로 남아 있다.

## 관련
- [[lampas-studio]] (`lampas-system`) · [[lampas-system-ai-call-architecture-audit]]
- [[remote-mcp-oauth-account-confirmation-and-origin-null-pitfall]] — 이 세션에서 추출된 스킬
- [[harness-mcp-bridge]] — 반대 방향(하네스가 외부 MCP를 *소비*) 인접 개념, 혼동 주의
- 세션: [[2026-09-27-lampas-api-mcp-oauth-원격mcp-chatgpt클로드연동]]
