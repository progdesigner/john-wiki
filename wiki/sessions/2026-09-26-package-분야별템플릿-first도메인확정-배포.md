---
tags: [session, lampas-studio, lampas-web-package, dalar-web-first, instagram, deploy, persona, dns]
created: 2026-09-26
updated: 2026-09-26
---
# Package 분야별 템플릿화 + First 도메인 확정·배포 (2026-09-26)

`Tool: codex`, 작업 디렉터리 `lampas-system`(2026-09-26T09:19:01Z 시작). 원본 파일명은 "Dalar
인스타그램 관리"이지만 실제 내용은 **[[lampas-web-package]] AI 작성 규칙의 분야별 일반화**와
**[[dalar-web-first]] 제작 링크 도메인 확정·배포**다(제목과 본문 불일치 — 기존에도 반복 관찰된
패턴, [[dalar-web-first]] "AGENTS.md 모순" 절 참고).

## 요청과 원인 진단

사용자: `admin.first.dalar.ai/samples`의 샘플을 `package.lampas.io/new`로 올리는데 결과물이
"너무 스포츠 느낌"이라 페르소나·분야에 따라 완전히 달라져야 하고, `first.lampas.io`에 맞는
게시물 정보를 입력해 게시할 수 있어야 한다고 요청.

원인: 화면 예시뿐 아니라 **AI 작성 규칙 자체가 "하이라이트 소개 + 시청 안내"로 고정**돼 있었음
(스포츠 클립 파이프라인에서 유래 — [[lampas-web-package]] 2026-09-20 세션에서 확정한 톤 5종
템플릿이 스포츠 전용으로 굳어 있던 것). 첫 조사에서 "First는 반려동물 AI 영상 서비스"로 잘못
짚었다가, 재확인 후 **"아기 사진으로 돌잔치 영상을 만드는 서비스"**로 정정 — First 실체를
Package 쪽 세션에서 직접 조사·확정한 것은 이번이 처음.

## 구현

- **분야별 템플릿 + 직접 입력**: First·뷰티·푸드·교육·스포츠 5개 분야 템플릿과, 템플릿에 없는
  경우를 위한 직접 입력(페르소나·타겟 독자·게시 목적)을 함께 지원하도록 재설계. 기존 "하이라이트
  소개 + 시청 안내" 스포츠 전용 작성 규칙을 걷어내고, 분야·페르소나·타겟·게시 목적에 따라 문체와
  본문 구성이 달라지는 구조로 변경.
- **First 샘플 → Package 프리필**: `admin.first.dalar.ai/samples`에서 "Package로 보내기"를
  누르면 영상·장면 수를 전달하고, 돌잔치 영상용 설정·제목·본문(수정 가능)·제작 링크가 미리
  채워짐. 이후 기존 게시 흐름 그대로 진행.
- 테스트 45개 + 웹 앱 2개(Package·First 추정) 빌드 통과 확인 — **이 시점엔 운영 배포·실제
  게시물 발행 전**.

## 배포 — API 변경 분리 + 도메인 결정 유예

- `lampas-api`에 이번 패키징 수정과 무관한 **다른 작업의 미완료 변경**이 섞여 있어, 이번 수정만
  분리해 배포하기로 결정([[selective-hunk-commit-shared-file]]과 같은 계열의 "공유 변경분에서
  이번 작업분만 분리" 패턴 재사용).
- **`first.lampas.io`는 이 시점까지 DNS가 연결돼 있지 않음**을 확인 — 하지만 사용자 요청 문구에
  이미 이 주소가 있었으므로, 기존 운영 도메인(`first.dalar.ai`)으로 우선 배포를 진행하면서 사용자
  에게 "제작 링크를 어느 주소로 쓸지" 비동기 질문(`send_user_message_question_reply`)을 던져둠.
- 분리한 API 전체 테스트 1,221개 통과 후 운영 배포·DB 연결 정상 확인. 웹 두 개(Package·First)는
  도메인 확정 응답을 기다리며 대기.
- 응답이 오지 않은 상태에서 **정상 운영 중인 `first.dalar.ai`를 기본값으로 채택**해 우선 웹
  배포를 마무리(`first.lampas.io` 연결은 별도 과제로 남김) — 그 직후 비동기 질문 응답이 도착:
  "기존 first.dalar.ai 사용". 결과가 이미 채택한 기본값과 일치해 그대로 확정.
- 배포 완료 후 API·웹 두 개 모두 운영 응답·변경 반영 확인.
- 세션 마지막, 사용자가 "`first.lampas.io`는 잘못 말한 것 — `first.dalar.ai`만 하면 됨"이라고
  명시적으로 정정 → **`first.lampas.io` 연결 시도 자체가 처음부터 불필요했음이 확정**. 어시스턴트는
  이미 `first.dalar.ai`만 반영해 배포했다고 재확인.

## 도메인 사실 정리 (모순 아님, 사용자 단순 착오)

- `first.dalar.ai` — First 실제 운영 도메인(`[[dalar-web-first]]` 기존 기록과 일치).
- `first.lampas.io` — 이 세션에서만 등장한 주소, DNS 미연결 확인, **사용자가 최종적으로 "잘못
  말한 것"이라 정정**하여 이 위키에 존재하는 유일한 언급이 곧 폐기된 셈. 다른 세션·페이지에
  이 주소 언급 없음(이 ingest 시점 기준 그랩 확인).

## 관련
- 엔티티: [[lampas-web-package]](분야별 템플릿화로 갱신) · [[dalar-web-first]](Package 연동
  절 갱신, First 실체 재확인)
- 상위: [[dalar]] · [[lampas-studio]](저장소 `lampas-system`)
- 스킬: [[selective-hunk-commit-shared-file]](API 변경분 분리 배포에 재사용)
