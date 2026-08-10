---
tags: [entity, organization, vendor, uncertain]
created: 2026-07-16
updated: 2026-08-03
---
# SRKK

이름이 같은 등장이 여럿 있으나 **완전히 동일 실체인지는 확정되지 않음** — 다만 등장1과 등장3은
2026-07-16 ingest로 정황 증거가 크게 강화됨(아래 참고).

## 등장 1 — "SRKK(싱가포르)" 도메인/Microsoft 계정 건
[[2026-07-14-srkk-도메인-scott-확인-princ-번역]] (및 선행 [[2026-07-14-은행환불-거래처매칭]])에서
등장. **John Lee**가 "대표님"에게 "싱가포르 SRKK 관련하여 말씀드릴 내용이 있다"고 보고하는 것으로
시작, 이후 "누구 허락으로 진행했죠?" 질책 → **CWC Domain**과 **Microsoft 계정**을 승인 없이
연장하려 한(또는 연장한) 정황으로 이어짐. 담당자로 보이는 **Scott**과의 협의 끝에 두 건 모두
"연장 안 함"으로 확정.
- Scott의 소속(=SRKK 소속 담당자인지, 별개 벤더/개인인지)은 트랜스크립트만으로 단정 불가했으나,
  **등장3(아래)에서 이메일 헤더로 `scottjeun@thesylvangroup.com`임이 확인**되어 별개 벤더/파트너
  담당자(The Sylvan Group 소속) 쪽에 무게가 실림.
- "싱가포르"라는 표현으로 보아 [[cwc-lab-singapore]]와 지리적으로 겹치나, 별개 실체로 보임(문맥상
  도메인/Microsoft 계정을 관리하는 IT 벤더/파트너 성격).

## 등장 2 — "SRKK Group" MS Azure 펀딩 영업메일
[[2026-07-15-srkk-azure-펀딩메일-번역]]에서 등장. **SRKK Group**이 Microsoft FY2027 펀딩
프로그램(Azure 마이그레이션 시 최대 SGD 35,000 지원)을 안내하는 영업 콜드메일 발신처. 말레이시아/
싱가포르 소재 MS 파트너사로 추정.

## 등장 3 — "SRKK Consulting Pte Ltd" 인보이스 독촉 스레드 (2026-07-16 ingest)
[[2026-07-16-srkk-인보이스-james확인]]에서 정식 법인명·주소가 처음 확인됨: **SRKK Consulting
Pte Ltd**, `accountsreceivable@srkk.com` / `@srkk.com.sg`, 21 Woodlands Close, 07-10 Primz
Bizhub, Singapore 737854. 인보이스 2건(S5-405128 `CWC_Q_1070886` SGD130.80, S5-405157
`CWC_Q_1070833` SGD392.40, 합계 SGD523.20)이 **"domain renewal and licence services"**
명목으로 연체 — 등장1과 주제가 정확히 일치한다. 스레드 당사자:
- **Scott Jeun** (`scottjeun@thesylvangroup.com`) — "James가 실제로 했는지 확인 후, 안 했으면
  지불 보류"를 요청.
- **Clara K** (`Clara.K@thesylvangroup.com`) — 둘 다 **Sylvan Capital Management Pte. Ltd.
  / The Sylvan Group**(싱가포르, Guoco Tower) 소속. SRKK는 서명된 견적서 수령 시점에 이미
  서비스를 진행해 인보이스 취소 불가라 안내, 최종적으로 "James가 했다고 답장했다"며 결제 쪽으로
  정리.
- **John** (`john@cwc.sg`) — cc로 전 스레드에 관여.

**등장1과의 관계**: 인보이스 납기일(2026-06-05, 2026-06-12)이 등장1 세션 시점(2026-07-14)보다
앞서 있어, 등장1의 "두 건 모두 연장 안 함으로 확정"이라는 결론과 **시점상 모순되지 않을 가능성이
높다** — 이 인보이스는 그 결정 이전에 이미 진행되어 있던 (또는 별개의) 갱신 건의 뒤늦은 결제
정리로 보인다. 다만 두 스레드가 서로를 명시적으로 언급하지 않아 완전히 동일한 갱신 건인지는
단정하지 않는다.

## "Scott" 신원 (등장1 ↔ 등장3, 그리고 위스키 분쟁)
[[2026-07-14-clara-바바라-위스키분쟁-번역]] 세션에서 확인된 CWC-FY Group 위스키 분쟁 이메일에도
**Scott Jeun**(`scottjeun@thesylvangroup.com`, The Sylvan Group 소속)이 cc로 지속 등장한다.
등장3(2026-07-16 ingest)에서 이메일 헤더로 **동일 인물임이 사실상 확정**됨 — "등장1"의 담당자
Scott, 위스키 분쟁의 cc Scott Jeun, 등장3의 발신자 Scott Jeun이 모두 도메인/CWC 관련 스레드에서
겹친다. 다만 등장1 원 트랜스크립트(WhatsApp 스크린샷)에는 이메일 주소가 노출되지 않아 텍스트
근거만으로는 여전히 100% 확정은 아니다.

## 미확정 — 등장2("SRKK Group")와 등장1·3의 관계
- 등장1·3은 **SRKK Consulting Pte Ltd**(`srkk.com`)로 사실상 동일 실체가 확정됐으나, 등장2
  "SRKK Group"(MS Azure 펀딩 영업메일 발신처)이 이와 같은 법인인지는 여전히 불확실 — 둘 다
  Microsoft/도메인 관련 서비스를 다루고 싱가포르와 연관된다는 공통점은 있으나, 등장2 세션에는
  "SRKK Consulting"이라는 정식 명칭이나 Scott/Clara/John이 전혀 등장하지 않는다. 이름 일치
  이상의 직접 근거가 아직 없어 **단정하지 않는다**.

## 관련
- 세션: [[2026-07-14-srkk-도메인-scott-확인-princ-번역]], [[2026-07-14-은행환불-거래처매칭]], [[2026-07-14-clara-바바라-위스키분쟁-번역]], [[2026-07-15-srkk-azure-펀딩메일-번역]], [[2026-07-16-srkk-인보이스-james확인]]
- 엔티티: [[cwc-commerce]] · [[cwc-lab-singapore]] · [[fy-group]] (Scott Jeun) · [[sylvan-korea]] (Sylvan Group 미확정 연결)
- Scott/Clara 신원 추적 통합 페이지: [[scott-jeun-sylvan-group]]
