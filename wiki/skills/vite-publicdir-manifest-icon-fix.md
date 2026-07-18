---
name: vite-publicdir-manifest-icon-fix
description: Vite root가 public/이고 publicDir:false일 때 PWA manifest.json·아이콘 경로가 base64/해시로 깨지는 문제 진단·수정
created: 2026-07-19
tags: [vite, pwa, manifest, build, static-assets, lampas-harness]
---
# vite-publicdir-manifest-icon-fix

## 언제 쓰는가
Vite 프로젝트에서 PWA `manifest.json`을 만들고 `index.html`에 링크했는데, 홈 화면 추가 시
아이콘이 안 뜨거나 깨질 때. 특히 Vite 설정이 `root: 'public/'` + `publicDir: false`처럼
**공개 정적 디렉토리를 빌드 루트로도 쓰면서 자동 복사(publicDir)는 꺼둔 구성**일 때 발생하기 쉽다.

## 절차 (단계별)
1. **증상 재현**: 브라우저 개발자도구 Network 탭 또는 `curl`로 `/manifest.json`과 그 안의 아이콘
   URL을 직접 요청해 실제 응답(200/404, Content-Type, 파일명)을 확인한다.
2. **원인 구분**: `index.html`에서 `<link rel="manifest">`나 `<img>`로 **직접 참조된 파일**은
   Vite 에셋 파이프라인을 타고 base64 인라인되거나 해시된 파일명(`icon-192.a1b2c3.png`)으로
   바뀐다. 반면 **manifest.json 파일 내부**에 텍스트로 적힌 아이콘 경로(`"/icon-512.png"`)는
   Vite가 파싱하지 않으므로 원래 문자열 그대로 남는다 — 두 경로 체계가 어긋나며 깨진다.
3. **수정**: manifest.json + 아이콘들을 Vite 에셋 파이프라인 **밖**에 두는 별도 정적 디렉토리
   (예 `static/`)로 옮기고, `publicDir: 'static'`으로 설정해 Vite가 빌드 시 그대로 `dist/`에
   복사하게 한다. `index.html`의 참조 경로는 해시 없는 절대경로(`/manifest.json`,
   `/icon-192.png`)로 유지.
4. **재빌드 후 검증**: `dist/manifest.json`을 직접 열어 아이콘 경로가 해시 없이 그대로인지 확인 +
   `curl`로 manifest·아이콘 둘 다 200 확인.
5. **재시작 필요 여부 판단**: 정적 파일은 대부분 서버가 매 요청마다 디스크에서 읽으므로(코드 변경이
   아니라 자산 교체), **서버 프로세스 재시작 없이 즉시 반영**된다 — 자동으로 재시작부터 하지 말고
   먼저 배포된 파일을 curl로 확인해 재시작이 실제로 필요한지 판단한다.

## 주의사항 / 함정
- `publicDir: false`가 의도적으로 설정돼 있다면(예: `root`를 이미 `public/`로 잡아 중복 복사를
  막으려는 의도) 그 이유를 먼저 파악할 것 — 무작정 `publicDir: true`로 되돌리면 다른 빌드 동작이
  깨질 수 있다. 별도 디렉토리 분리가 더 안전한 이유.
- manifest.json 안의 상대/절대 경로는 Vite가 절대 다시 써주지 않는다 — 아이콘 파일의 실제 배포
  경로와 수동으로 일치시켜야 한다.
- 정적 파일 vs 재시작 필요 여부는 [[self-hosted-agent-server-ops]] 함정4("`public/` 정적 파일은
  build 없이 cp만으로 반영")와 같은 계열의 판단 — 서버 코드(`src/`) 변경이 아니라 자산 파일
  변경이면 재시작이 불필요할 가능성이 높다.

## 출처: [[2026-07-17-harness재시작-pwa아이콘이름-manifest-vite버그수정]] ([[lampas-harness]])
