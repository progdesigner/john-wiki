---
tags: [session, lampas-api-mcp, mcp, oauth, lampas-studio, chatgpt, claude-connector]
created: 2026-09-27
updated: 2026-10-03
---
# lampas-api-mcp — 원격 MCP+OAuth 구현, ChatGPT·Claude 커넥터 연동 (2026-09-27~10-03)

`Tool: codex` 세션, 작업폴더 `lampas-system`. 소스: [[2026-09-27-lampas-api-mcp-oauth-원격mcp-chatgpt클로드연동]](raw). 하나의 터미널이
실제 캘린더로 7일(2026-09-27/28/10-03) 이어진 세션 — environment_context의 `current_date`가 세 번
바뀐다. **2026-09-26 세션**([[2026-09-26-fit-서비스-개선]])이 "다른 세션이 진행 중"이라며 손대지 않고
넘어간 `apps/lampas-api-mcp`의 OAuth·HTTP 서버 작업(미커밋 20개 파일)이 바로 이 세션에서 실제로
완성·배포됐다.

## 요청과 배경

사용자 요청: "`lampas-api-mcp`를 제대로 만들어서 GPT나 클로드와 연계할 수 있게 해줘." 기존 서버는
로컬 `stdio`만 지원(Claude Desktop 전용), 원격 연결·자동 검증 테스트가 없었다.

## 1단계 — 구현 (2026-09-27)

- Claude Desktop용 로컬 `stdio`는 유지, **원격 HTTP MCP + OAuth**를 신규 추가.
- 원격 요청마다 **사용자 인증을 분리** — 다른 계정의 크레딧이 섞여 쓰이지 않도록 설계.
- ChatGPT 웹 연결은 OAuth가 필수. 연결 화면에서 Lampas Secret Key를 검증한 뒤 **MCP 전용 토큰**을
  발급하는 흐름 구현.
- 도구별 과금·변경 여부 명시, 원격 도구가 서버 로컬 파일을 읽지 못하도록 제한.
- 통합 테스트: OAuth 연결→공식 MCP SDK 초기화→도구 호출까지 통과. **서로 다른 두 사용자의 동시
  요청이 각자의 Lampas 인증으로 전달되는 것**을 확인.
- 재시작 후 인증 복원, 로컬 stdio 연결, 업로드·오류 처리까지 검증 + 클라이언트별 설정 예제 정리.
- **완료 보고**: stdio+원격 HTTP MCP, OAuth 인증+사용자별 키 분리, 인증정보 암호화 저장, 업로드
  제한, 오류 처리 보강, 연결 가이드·API 예제·배포 설정. 16개 테스트·타입검사·빌드 통과, 독립 설치
  검증. **단, 운영 배포와 실제 ChatGPT/Claude 계정 연결은 이 단계에서 미실행** — 공개 HTTPS 주소가
  있어야 가능하다는 제약을 명시.

## 2단계 — 배포 및 연결 안내

사용자가 "배포한 뒤에 적용하는 법"을 질문. 기존 `lampas-api` 운영 서버 구성을 확인한 뒤 **기존
HTTPS 주소를 재사용**해 연결 URL을 `https://api.lampas.io/mcp`로 구성(기존 API 경로와 충돌 없음
확인), MCP 프로세스는 별도 포트로 운영. 배포 완료 후 인증 응답·OAuth 연결 화면을 외부에서 직접
확인.

**연결 절차 (당시 안내)**
- ChatGPT: 설정→Security and login→Developer mode→[플러그인 페이지]에서 `+`→이름 `Lampas`+URL
  입력→인증 OAuth, 등록방식 **DCR**(Client ID/Secret 비움)→Lampas 연결화면에서 Secret Key 입력·승인
  →새 대화 `+` 메뉴에서 활성화.
- Claude: Customize→Connectors→+→Add custom connector→이름+URL(Client ID/Secret 비움)→Connect→
  Secret Key 입력→대화 `+ → Connectors`에서 활성화.
- 연결 확인 문구 예시: "Lampas의 이미지 생성 모델과 필요한 크레딧을 조회해줘." 이미지·비디오·오디오
  생성은 연결된 계정의 크레딧을 사용한다고 명시.

## 3단계 — 버그 발견: Origin: null 거부 (2026-09-28)

사용자가 첨부 이미지로 "마지막에 이렇게 되고 어떤 계정으로 연결되는지도 모르겠다"고 재현 불가 문제
제기. 원인 재현: 어시스턴트가 걸어둔 **`Referrer-Policy: no-referrer` 설정 때문에 Chrome이 폼
제출 시 `Origin: null`을 보내고, 서버가 이를 거부**하고 있었음. 1단계에서 "통합 테스트 통과"라고
보고했던 검증은 OAuth init+도구 호출까지였고, **실제 브라우저(Chrome) 폼 제출 자체는 검증하지
않았다**는 격차가 드러난 사례.

수정 방향 두 가지를 동시에 진행:
1. `Referrer-Policy` 설정을 수정해 `Origin` 헤더가 `null`로 깨지지 않게 함.
2. 키 입력 후 **바로 승인되던 흐름을 변경** — 연결될 Lampas 계정과 크레딧 사용 계정을 먼저
   보여준 뒤에만 최종 승인하도록 재설계(계정 오인 연결 방지).

계정 확인 화면 + "연결 후 계정을 조회하는 도구"를 추가, 계정 정보는 **Secret Key로 실제 API 인증한
결과**에서 가져와 그 키 소유자의 이름·이메일·계정 ID·크레딧 잔액을 표시. Chrome에서 잘못된 키
재입력·계정 미리보기·다른 계정으로 변경·최종 승인 후 OAuth 토큰 교환까지 전부 통과 확인 후 재배포,
실제 Chrome 정상 제출도 확인.

**최종 확정 사실**: 연결되는 계정은 **입력한 Secret Key 소유자의 Lampas 계정**이며, 최종 승인 전에
반드시 이름·이메일·계정 ID·크레딧 잔액이 표시된다. → [[lampas-api-mcp]]·
[[remote-mcp-oauth-account-confirmation-and-origin-null-pitfall]]

## 4단계 — 재질문, 미응답 종료 (2026-10-03)

5일 뒤(실제로는 환경 날짜만 2026-10-03으로 바뀜, 같은 터미널) 사용자가 "그래서 ChatGPT에서 연동해서
쓰려면 어떻게 한다고 다시 알려줘"라고 재질문. **소스 트랜스크립트가 이 질문에서 끝나며 어시스턴트
응답이 기록되어 있지 않다** — 답변 여부·내용 모두 미확인.

## 관련
- [[lampas-api-mcp]] (신규 엔티티) · [[lampas-studio]] (`lampas-system` 소속 앱)
- [[remote-mcp-oauth-account-confirmation-and-origin-null-pitfall]] (신규 스킬)
- 선행: [[2026-09-26-fit-서비스-개선]] (같은 작업이 "다른 세션 진행 중"으로만 언급됨) ·
  [[2026-09-26-lampas-system-수정사항]] (같은 OAuth·HTTP 서버 작업이 커밋되지 않은 채 남아있던
  스냅샷)
- [[harness-mcp-bridge]] — 방향이 반대인 인접 개념(하네스가 *외부* MCP를 Claude 세션에 끌어오는 것).
  이 세션은 반대로 `lampas-api-mcp`가 *제공자*로서 ChatGPT/Claude에 노출되는 경우.
