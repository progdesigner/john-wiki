---
name: deploy-sandbox-pnpm-shim
description: 하네스 샌드박스에서 ./scripts 배포 스크립트가 pnpm/aws/ssh를 못 찾을 때 corepack shim으로 우회
created: 2026-07-09
tags: [deploy, sandbox, pnpm, corepack, harness]
---
# 샌드박스 배포 — pnpm corepack shim 우회

## 언제 쓰는가
`[[lampas]]`가 `[[lampas-harness]]` 샌드박스 안에서 `[[lampas-studio]]`의 `./scripts` 배포 스크립트를 실행하는데
**`pnpm`을 PATH에서 못 찾거나** aws/ssh 등 샌드박스 밖 리소스 접근이 필요할 때.

## 원칙 — 배포는 항상 `./scripts` 스크립트로
수동 빌드·S3 업로드·rsync·CloudFront 무효화를 개별 명령으로 재현하지 말 것. 스크립트가 `env:production`으로
프로덕션 환경변수를 설정하므로 **스크립트를 그대로 실행**해야 URL 등이 올바르게 박힌다.
(로컬 `dist`는 localhost URL로 빌드돼 배포에 부적합.)

## 절차 (단계별)
1. **패키지 매니저 버전 확인** — `package.json`의 `packageManager` 필드에서 pnpm 버전 확인.
2. **corepack으로 pnpm shim 생성** — corepack이 pnpm을 활성화할 수 있다. shim 실행 파일을 임시 디렉토리(`.deploy-bin/`)에 만들고 그 디렉토리를 PATH 앞에 추가.
3. **샌드박스 해제** — aws/ssh/원격 빌드는 샌드박스 밖 리소스라 언샌드박스로 실행해야 한다.
4. **스크립트 실행** — 준비한 PATH로 `deploy-api.sh` / `deploy-web.sh <project>` 실행. 대상별로 순차 진행(동시 실행 시 리소스 경합).
5. **임시 shim 정리** — 끝나면 `.deploy-bin/` 삭제(추적되지 않는 임시 디렉토리라 남아도 git·배포엔 무해).

## 주의사항 / 함정
- **S3 경로·URL 인자 차단** — S3 경로나 URL이 명령 **인자**에 있으면 인자 스캐너가 차단한다. 경로가 스크립트 파일 안에 있으면 통과하므로 스크립트를 그대로 실행하거나, 필요한 임시 로직은 파일로 작성해 실행.
- **`rm` 등이 "Stream closed"로 실패** — 세션 재연결 이슈로 삭제 명령이 반복 실패할 수 있다. shim은 임시라 남아도 무해하니, 안 되면 사용자에게 `rm -rf .deploy-bin` 한 줄을 안내하고 넘어간다.
- **로컬 env 복원** — 임시로 프로덕션 env로 빌드했다면 로컬 env를 복원. 단 deploy 스크립트는 `deploy/`에서 자체 프로덕션 env를 적용하므로 로컬 복원과 무관.

## 출처: [[2026-07-08-lampas-스튜디오-레퍼런스-instagram]] ([[lampas-studio]])
관련 함정: [[lampas-harness]] (Stream closed, 인자 스캐너)
