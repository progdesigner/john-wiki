---
name: local-asset-fingerprint-s3-backup-recovery
description: 브라우저 로컬 저장소(OPFS 등)에만 존재하는 반입 파일을 fingerprint 기준으로 S3에 1회 백업하고, 다른 기기에서 없으면 자동 재다운로드하는 절차
created: 2026-09-26
tags: [s3, presigned-url, browser-storage, cross-device, lampas-studio, lampas-web-edit]
---
# 로컬 전용 파일의 fingerprint 기준 S3 백업·자동복구

## 언제 쓰는가
사용자가 드래그/드롭 등으로 로컬 파일을 반입해 브라우저 저장소(OPFS·IndexedDB 등)에만 저장하는
에디터·앱에서, "다른 컴퓨터/브라우저로 세션을 열면 파일이 없다"는 문제를 해결할 때. 서버는 그 파일의
존재를 모르고, 클라이언트 로컬 저장소는 기기 종속이라는 게 근본 원인이다.

## 절차 (단계별)
1. **먼저 기존 유사 영속화 패턴을 찾는다** — 같은 백엔드에 이미지/영상/오디오를 S3에 올리는 다른
   모듈(예: 시나리오·씬 저장 기능)이 있으면 그 헬퍼(`persistImage`/`uploadBuffer`/
   `getPresignedPutUrl` 류)를 그대로 재사용한다. 새로 설계하지 않는다.
2. **fingerprint 정의**: 파일 자체의 내용을 안정적으로 구분할 수 있는 값(예: `size-lastModified-
   sanitizedName`)을 키로 쓴다. 해시가 아니어도 되지만, 같은 파일을 다시 반입했을 때 같은 키가
   나와야 중복 백업을 피할 수 있다.
3. **DB에 자산 테이블 신설**(예: `EditSessionAsset`): `userId`(또는 계정 단위) + `fingerprint` +
   S3 키/URL + 메타데이터. 사용자당 1회만 백업되도록 fingerprint 중복 체크를 먼저 한다.
4. **엔드포인트 3개가 보통 필요**: presign(클라이언트가 직접 S3로 업로드할 URL 발급) → confirm(업로드
   완료를 서버에 통보, DB row 확정) → 조회(fingerprint로 기존 백업 존재 여부/URL 확인). 대용량 파일은
   서버를 거치지 않고 브라우저→S3 직접 업로드(presigned PUT)로 API 바디 크기 제한을 피한다 →
   [[proxy-body-limit-413-appears-as-network-error]].
5. **백업 훅 지점**: 로컬 저장 함수(예: `saveClipFile`) 호출 직후, 로그인 상태일 때만 fire-and-forget
   으로 업로드를 트리거한다. 클립/파일 교체 플로우(예: `replaceClipMedia`)에도 같은 훅을 반드시
   중복 적용한다 — 신규 반입 경로 하나만 고치면 교체 경로에서 재발한다.
6. **자동복구 훅 지점**: 세션을 열 때(또는 활성 세션이 바뀔 때) 로컬 저장소를 확인해 없는 fingerprint를
   모으는 기존 "누락 파일 감지" 로직이 있다면, 그 지점에 서버 조회→다운로드→로컬 재저장 순서를
   끼워 넣는다. 기존엔 "파일 없음" 표시만 하던 지점이 최적의 삽입 지점이다.
7. DDL을 API 배포보다 먼저 운영에 적용한다(테이블 없이 새 코드부터 배포하면 크래시) →
   [[prod-ddl-before-deploy-with-drift-check]].

## 주의사항 / 함정
- 이미지/영상 등 슬롯 스키마에 `fingerprint`(로컬)와 `url`(공개 CDN) 두 필드가 이미 있는 기능(예:
  템플릿 이미지 슬롯)이 있다면, 이 백업 인프라가 바로 그 `url`이 비었을 때의 최종 폴백이 될 수 있다
  → [[template-image-slot-fingerprint-vs-url]].
- S3 버킷 CORS 설정에 새 프런트엔드 도메인이 빠져 있으면 백업 업로드가 **조용히 실패**할 수 있다 —
  프리사인 직접 업로드를 쓰는 다른 기능(예: 갤러리 업로드)을 나중에 추가하면서 CORS를 점검하다
  이 백업이 실은 실패하고 있었다는 게 뒤늦게 드러난 사례가 있다.
- 신규 자산 테이블을 만들 때 기존 `Resource` 같은 범용 업로드 기록 테이블에 끼워 넣을지, 전용 테이블을
  새로 만들지 판단 필요 — 소유자(`userId`/세션 ID) 연관 컬럼이 범용 테이블에 없다면 전용 테이블이
  더 간단하다.

## 출처: [[2026-09-13-lampas-edit-이미지트랙-텍스트효과-원본백업-구현]] ([[lampas-web-edit]] `EditSessionAsset`)
