---
tags: [session, translation, business, finance, quick-chat]
created: 2026-07-16
updated: 2026-07-16
---
# 2026-07-16 SRKK 인보이스 독촉 스레드 번역 + "James 확인" 결론

1왕복 quick 채팅 (2026-07-16 02:15 UTC). 코딩 아님 — 이메일 스레드 전체(5통, 최신이 맨 위)를
번역해 달라는 요청. [[harness-as-business-assistant]] 패턴, 순수 번역 사례.
원본: `raw/conversations/2026-07-16-srkk-인보이스-james확인.md`

## 스레드 내용 (오래된 순)
1. **SRKK Accounts Receivable → Ryan Lee, Sheryl Lim (cc Nurhaziqah binti Saruan)** — 연체
   독촉장 S5-PRMD001031. 인보이스 2건 연체: S5-405128(고객참조 `CWC_Q_1070886`, 납기
   2026-06-05, SGD 130.80), S5-405157(`CWC_Q_1070833`, 납기 2026-06-12, SGD 392.40).
   합계 VAT포함 SGD 523.20. 2025-05-01부터 연체 잔액에 월 1%(연 12%) 연체료 부과 안내.
   발신 도메인 `srkk.com` / 참조 `srkk.com.sg`, 주소 21 Woodlands Close, 07-10 Primz
   Bizhub, Singapore 737854 — 정식 법인명 **SRKK Consulting Pte Ltd**.
2. **Scott Jeun(`scottjeun@thesylvangroup.com`) → John(`john@cwc.sg`), cc Clara
   K(`Clara.K@thesylvangroup.com`)** — 위 독촉장을 포워딩만 함(본문 없음).
3. **Clara K → Scott, John** — SRKK는 **서명된 견적서**를 수령한 시점에 이미 도메인 갱신·
   라이선스 서비스를 진행했기 때문에 인보이스 취소·크레딧노트 발행이 불가하며, 결제 전까지
   독촉 메일이 계속될 것이라 안내.
4. **Scott → Clara K, John** — "**James가 실제로 했는지 확인해달라. 안 했으면 지불하면
   안 된다**"고 요청.
5. **Clara K → Scott(, John)** — "**James가 했다고 답장했다**"고 최종 확인. (서명:
   Sylvan Capital Management Pte. Ltd., Guoco Tower #30-01A, 1 Wallich Street,
   Singapore 078881.)

어시스턴트는 5통 전체를 최신순으로 번역하고 끝에 스레드 요약("James가 서명/승인을 했다고
확인되어 인보이스 취소는 불가하고 결제로 정리되는 흐름")을 덧붙였다.

## 관찰 — 기존 [[srkk]] 기록과의 연결
- **정식 법인명 확정**: [[srkk]] "등장1"(2026-07-14, CWC 도메인·Microsoft 계정 승인 없는
  연장 의혹)의 "SRKK(싱가포르)"가 이번 스레드로 **"SRKK Consulting Pte Ltd"**(`srkk.com`,
  싱가포르)라는 정식 법인명·주소가 처음 확인됨. 인보이스 항목이 정확히 "domain renewal and
  licence services"로, 등장1의 주제(CWC 도메인/Microsoft 계정 연장)와 일치한다.
- **Scott 신원 강화**: `scottjeun@thesylvangroup.com`, **The Sylvan Group / Sylvan
  Capital Management Pte. Ltd.**(싱가포르, Guoco Tower) 소속임이 이메일 헤더로 확정됨.
  등장1의 "Scott"(WhatsApp 스크린샷, 이메일 미노출)과 동일인이라는 심증이 강해졌으나, 등장1
  트랜스크립트 자체에는 이메일 주소가 없어 **100% 확정은 아님** — 다만 "도메인 연장" 주제
  일치 + John(`john@cwc.sg`) 관여가 겹쳐 정황 증거가 이전보다 훨씬 강함.
- **Clara K 신규 등장**: `Clara.K@thesylvangroup.com`, Sylvan Capital Management 소속
  회계 담당으로 보임. [[2026-07-14-clara-바바라-위스키분쟁-번역]] 세션에서도 "Clara" 이름이
  등장했었는지는 별도 확인 필요(해당 세션 재조회 없이 이 ingest에서는 단정하지 않음).
- **잠재적 모순**: 등장1 세션의 최종 결론은 "**두 건 모두 연장 안 함으로 확정**"
  (`Yes, it's confirmed that we will not extend for both`)이었다. 그런데 이번 스레드에서는
  SRKK가 "**서명된 견적서를 수령해 이미 도메인 갱신을 진행했다**"며 결제를 요구하고, Scott도
  "James가 실제로 했는지"를 확인한 뒤 결제를 용인하는 쪽으로 정리된다 — 즉 **도메인 갱신이
  실제로는 진행되었고 결제도 이루어지는 흐름**으로 보인다. 두 스레드가 서로를 명시적으로
  언급하지 않아 완전히 같은 갱신 건인지, 아니면 등장1 결정 **이전에** 이미 진행되어 있던
  별개의(또는 선행하는) 인보이스인지는 소스만으로 단정할 수 없다. 시간순으로는 인보이스 납기일
  (2026-06-05, 2026-06-12)이 등장1 세션(2026-07-14)보다 **앞서** 있어, 이 인보이스 건은 등장1의
  "미연장 확정" 대화 **이전에 이미 발생한 사건**일 가능성이 높다 — 즉 모순이 아니라, 과거에
  이미 진행된 갱신 건의 결제를 뒤늦게 정리하는 것과 향후 갱신을 안 하기로 한 결정이 **별개
  시점의 별개 결정**일 수 있다. 확정 짓지 않고 두 기록을 병기한다.
- **"James"**: 이 스레드에서 결제 여부를 좌우하는 핵심 인물로 처음 등장(성·소속·이메일 불명).
  별도 엔티티 생성은 보류(정보 부족) — 향후 소스에서 신원이 드러나면 페이지화.

## ⚠️ 다른 소스와의 중복
같은 이메일 스레드(글자 단위 동일)가 28분 뒤(02:43 UTC) 다른 아카이브 파일명으로 다시
quick 채팅에 들어와 재번역됨 — [[2026-07-16-srkk-인보이스-james확인-0243]]. 아카이브
재노출 이상 정황으로 추정, 새 사실 없음.

## 관련
- 엔티티: [[srkk]](갱신) · [[cwc-commerce]] · [[fy-group]](Scott Jeun 관련 교차 참조)
- 세션: [[2026-07-14-srkk-도메인-scott-확인-princ-번역]](등장1 원 세션) ·
  [[2026-07-15-srkk-azure-펀딩메일-번역]](등장2, 별개 SRKK Group) ·
  [[2026-07-14-clara-바바라-위스키분쟁-번역]](Scott Jeun cc 최초 등장 세션) ·
  [[2026-07-16-srkk-인보이스-james확인-0243]](28분 뒤 중복 재노출)
- 패턴: [[harness-as-business-assistant]]
