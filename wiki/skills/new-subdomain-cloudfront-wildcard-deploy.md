---
name: new-subdomain-cloudfront-wildcard-deploy
description: 기존 와일드카드 인증서를 쓰는 도메인 아래에 완전히 새 서브도메인(신규 앱)을 배포할 때 CloudFront·DNS·검증 순서
created: 2026-09-26
tags: [deploy, cloudfront, dns, aws, lampas-studio]
---
# 새 서브도메인 CloudFront 배포

## 언제 쓰는가
같은 루트 도메인에 이미 와일드카드 인증서·배포 스크립트 체계가 있는 모노레포에서, **완전히 신규인
서브도메인**(예: 새 앱 `fit.lampas.io`)을 처음 공개할 때. 기존 앱을 재배포하는 것과 달리 CloudFront
배포 자체가 없어서 새로 만들어야 한다.

## 절차 (단계별)
1. 기존 배포 스크립트(`./scripts/deploy-web.sh <project>`)가 요구하는 `apps/<project>/deploy.json`을
   먼저 만든다 — `s3Path`/`cloudFrontId`/`awsProfile` 스키마. `cloudFrontId`는 아직 없으니 플레이스홀더로
   시작.
2. 새 서브도메인용 **CloudFront 배포를 신규 생성**한다. 인증서는 **새로 발급하지 않고 기존 루트 도메인
   와일드카드 인증서**(예: `*.lampas.io`)를 그대로 붙인다 — 서브도메인 하나 추가는 신규 ACM 인증서
   요청·검증 없이 CloudFront 대체 도메인 이름(CNAME)만 추가하면 된다.
3. `deploy.json`의 `cloudFrontId`를 실제 발급된 ID로 채운 뒤, 표준 배포 스크립트로 빌드·S3 업로드·
   캐시 무효화까지 정상 경로로 진행한다.
4. CloudFront 배포는 전 세계 전파(Deployed 상태)까지 시간이 걸린다 — 무효화 완료와 별개로 배포 상태
   전파를 기다려야 한다.
5. DNS(A/CNAME) 레코드를 CloudFront 도메인으로 연결한다.
6. **검증은 반드시 공개 DNS 조회 결과 기준으로 한다.** 작업 환경(샌드박스·로컬 셸)의 DNS 캐시는 방금
   생성한 레코드의 이전 상태(예: NXDOMAIN)를 들고 있을 수 있어, 로컬에서 안 열린다고 실패로 단정하지
   않는다 — 별도 공개 리졸버로 조회한 IP/CNAME로 직접 HTTPS·화면 동작을 검증한다.

## 주의사항 / 함정
- 신규 서브도메인이라고 새 ACM 인증서부터 발급하려 하면 검증(DNS/이메일) 대기로 배포가 불필요하게
  지연된다 — 루트 와일드카드 인증서 재사용이 가능한지 먼저 확인.
- 작업 환경 DNS 캐시와 실제 공개 DNS 상태가 다를 수 있다는 점을 배포 검증 실패로 오판하지 않는다.
- 배포는 반드시 저장소의 `./scripts/deploy-*.sh`로만 한다(수동 S3/CloudFront 개별 명령 금지) —
  이 원칙은 [[lampas-studio]] 제품 CLAUDE.md/AGENTS.md에 명문화되어 있다.

## 관련
- [[dead-external-cdn-domain-migration]] — 같은 "와일드카드 인증서 재사용" 원칙을 공유하지만, 신규
  앱 공개가 아니라 죽은 외부 도메인에서 기존 자산을 이전하는 시나리오(S3 복사+DB URL 치환+3rd-party
  DNS 병목 포함)라 별도 스킬로 분리됨.

## 출처: [[2026-09-25-lampas-web-fit-구축-배포]]
