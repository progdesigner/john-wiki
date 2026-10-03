---
name: remote-mcp-oauth-account-confirmation-and-origin-null-pitfall
description: 원격 MCP 서버에 OAuth(DCR) 커넥터를 붙일 때, 어떤 계정으로 연결됐는지 보장하고 Chrome의 Origin:null 거부를 피하는 절차
created: 2026-10-03
tags: [mcp, oauth, security-headers, chatgpt, claude-connector]
---
# 원격 MCP OAuth 커넥터 — 계정 확인 + Origin:null 함정

## 언제 쓰는가

자체 MCP 서버를 ChatGPT·Claude의 "커스텀 커넥터"(원격 MCP, OAuth DCR)로 노출할 때. 특히
"Secret Key 입력 → 승인" 같은 키 기반 인증을 OAuth 뒤에 숨기는 구조라면 반드시 적용한다.

## 절차 (단계별)

1. **로컬/원격 분리**: 기존 로컬 `stdio` 연결(Claude Desktop 등)은 그대로 두고, 별도 경로로
   원격 HTTP MCP + OAuth(DCR)를 추가한다. 기존 API 도메인에 `/mcp` 하위 경로로 붙이면 별도
   인증서·DNS 없이 재사용 가능(별도 포트의 MCP 프로세스로 라우팅).
2. **요청 단위 인증 분리**: 원격 요청마다 호출자의 키를 전달받아 그 요청에만 적용한다. 전역
   싱글턴 인증 상태를 두면 동시 요청 시 다른 사용자의 크레딧이 잘못 차감될 수 있다 — 반드시
   "서로 다른 두 사용자의 동시 요청"으로 통합 테스트한다.
3. **계정 확인 화면을 최종 승인 전에 둔다**: 키 입력 즉시 승인하지 말고, **그 키로 실제 API를
   인증한 결과**(이름·이메일·계정 ID·크레딧 잔액 등)를 먼저 보여준 뒤에만 승인 버튼을 활성화한다.
   "연결은 됐는데 어떤 계정인지 모르겠다"는 사용자 불만은 거의 항상 이 단계 부재가 원인이다.
4. **원격 도구의 파일시스템 접근을 차단**: 로컬 stdio 도구와 같은 코드를 쓰더라도, 원격 경로에서는
   서버 로컬 파일을 읽는 도구를 비활성화하거나 별도 화이트리스트로 제한한다.
5. **실제 브라우저로 end-to-end 제출을 검증한다** — 다음 함정 참고.

## 주의사항 / 함정

- **"OAuth init + 도구 호출 통합 테스트 통과"는 브라우저 폼 제출을 검증하지 않는다.** 이 세션에서
  16개 테스트·타입검사·빌드가 전부 통과했다고 보고했지만, 실제 Chrome에서 연결 화면 폼을 제출하면
  거부되는 버그가 남아 있었다 — "검증 완료" 보고와 실제 사용자 경험 사이의 간극.
- **`Referrer-Policy: no-referrer`를 서버 보안 헤더로 걸어두면 Chrome이 폼 제출 시 `Origin: null`을
  보낸다.** Origin 검증 로직이 `null`을 엄격히 거부하면 정상 사용자의 연결 시도가 전부 막힌다.
  증상은 "연결이 안 된다"/"Invalid origin" 류의 모호한 실패로만 보이고, 서버 로그의 보안 헤더 설정과
  클라이언트(Chrome) 쪽 요청 헤더를 같이 봐야 원인이 드러난다. 수정은 Referrer-Policy를
  `same-origin`/`strict-origin` 등으로 완화하거나, Origin 검증에서 이 특정 제출 경로의 `null`을
  허용하는 쪽으로 한다.
- 계정 확인 화면을 "그 키로 실제 API를 호출해 받은 응답"으로 채워야 한다 — 요청 본문에 담긴 값을
  그대로 되돌려 보여주면 키가 잘못 입력돼도 확인 화면이 통과된 것처럼 보일 수 있다.

## 출처: [[2026-09-27-lampas-api-mcp-oauth-원격mcp-chatgpt클로드연동]] · [[lampas-api-mcp]]
