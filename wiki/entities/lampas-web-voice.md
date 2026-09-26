---
tags: [entity, app, lampas-studio, voice, tts, react, nestjs, s3]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-voice ("Voice", `voice.lampas.io`)

`[[lampas-studio]]` 저장소(`lampas-system`)의 앱. 여러 TTS·음성 모델 중 하나를 골라 보이스를 만드는
웹. [[2026-09-24-voice레퍼런스오디오-생성삭제-카피pulse모델선택]] 세션에서 이 위키에 처음 노출됨 —
저장소 루트 `AGENTS.md` 3라인 앱 목록(Lampas/Dalar/Talk)에도, 기존에 기록된 스포츠 클립 파이프라인
앱 목록에도 등장하지 않아, `[[lampas-web-tools]]`·`[[lampas-web-copy]]` 등과 마찬가지로 문서상 앱
목록과 실제 앱 부분집합이 다름을 한 번 더 확인시켜주는 사례.

## 모델 카탈로그 — Seed Audio 1.0만 레퍼런스 오디오 수락
`GET /v1/voice/models`가 내려주는 카탈로그 각 항목에 `acceptsReferenceAudio` 플래그가 있다.
2026-09-24 기준 이 플래그가 **true인 모델은 Seed Audio 1.0 하나뿐**이고, xAI·[[gemini]]·
[[elevenlabs]] 계열 모델은 전부 프리셋 보이스만 제공(오디오 업로드로 클론할 대상이 없음). 이
카탈로그 자체가 `[[atlas-cloud]]` 경유인지는 이 세션 소스로 확인되지 않음(미검증) — Seed Audio·
ElevenLabs가 이 앱에서 나란히 옵션으로 뜨는 것은 `[[toktalk]]`이 텍스트 LLM에 AtlasCloud 카탈로그를
쓰면서도 실시간 음성은 xAI를 직접 쓰는 패턴과 구조적으로 유사(생성 방식별로 라우팅 경로가 갈림).

## 레퍼런스 오디오 업로드로 보이스 클론 (최초 구현, 2026-09-24)
- **API**: `POST /v1/voice/reference-audio` 신설 — multipart `audio` 필드, mp3·wav·m4a·aac·ogg·
  webm·flac, 20MB 이하, 과금 없음. S3 `voice/references/{userId}/`에 저장하고 공개 URL 반환. 그
  URL을 기존 `POST /voices`의 `referenceAudioUrl`로 넘기면 클론 소스가 된다. `acceptsReferenceAudio`가
  false인 모델에 `referenceAudioUrl`을 넘기면 **400**으로 거부(서버가 카탈로그 기준으로 재검증,
  프론트 UI 숨김에만 의존하지 않음).
- 형식 검증은 순수 함수 `validateReferenceAudioFile`로 분리 — 브라우저가 MIME을 비워 보내면 파일명
  확장자로 판별하는 폴백 포함.
- **웹**: 모델 카드에 "오디오 업로드 가능/불가" 배지. Seed Audio를 고르면 업로드 버튼+공개 URL
  입력이 함께 나타나고, 파일 선택 시 로컬 미리듣기, 생성 시 업로드 진행률 표시. 우선순위는
  **파일 → URL → 자동 캐논 샘플** 순, 순수 로직은 `lib/reference-audio.ts`로 분리. 구버전 API처럼
  카탈로그에 `acceptsReferenceAudio` 플래그가 없으면 모델 id가 `reference-clone` 방식인지로 판별하는
  하위호환 경로도 있음.
- 검증: lampas-api jest 35건(신규 5건), lampas-web-voice vitest 17건(신규 7건), tsc·vite build 통과.
- 배포: lampas-api **0.1.138**. 운영 카탈로그에서 Seed Audio만 `acceptsReferenceAudio:true`로
  내려오는 것, 업로드 엔드포인트가 미인증 요청에 401을 돌려주는 것까지 배포 후 확인.

## 생성 내역 소프트 삭제 (다음날 후속, 2026-09-25 추정, API 0.1.139)
- `voice_generations` 테이블에 `deletedAt` 컬럼 추가. 수동 DDL `prisma/manual/2026-09-25-voice-generations-deleted-at.sql`을
  `tools/apply-voice-generations-deleted-at-ddl.sh`로 API 배포 전 운영 RDS에 선적용(순서 절차는
  [[prod-ddl-before-deploy-with-drift-check]] 참고), 로컬 DB에도 동일 적용.
- `DELETE /v1/voice/generations/:id` — 자기 소유 행에 `deletedAt`만 기록(S3 오디오 파일은 남김). 목록
  조회는 삭제된 행을 제외. 남의 행이거나 이미 삭제된 행이면 404.
- 웹: 생성 내역 카드마다 "삭제" 버튼, 확인창 뒤 목록에서 바로 사라짐 —
  [[destructive-action-inline-confirm]] 계열의 표준 확인창 패턴.
- 검증: API 37건, 웹 17건 통과. 배포: lampas-api 0.1.139(PM2 재시작 0회 정상 기동, 신규 DELETE
  경로 미인증 401 확인), lampas-web-voice(S3+CloudFront 무효화).
- **배포 후 커밋 규칙 확정**: "어떤 작업이든 배포까지 하면 항상 커밋"을 이 세션에서 메모리에 저장.
  전날 레퍼런스 오디오 업로드 작업과 이날 삭제 기능을 한 커밋(`e3f96b53`)으로 묶고, 배포 스크립트가
  올린 버전 번호(package.json 등)는 별도 커밋(`2230d2b7`)으로 분리 — 세션 시작 전부터 있던 무관한
  `apps/lampas-web-voice/index.html` 리포터 스크립트 변경은 커밋에서 제외. 푸시는 하지 않음. 절차
  성격은 [[selective-hunk-commit-shared-file]]과 같은 계열(무관한 변경분 배제)이나 이번엔 파일 전체
  단위 배제.

## 관련
- 상위 제품: [[lampas-studio]] (저장소 `lampas-system`)
- 음성 프로바이더: [[elevenlabs]](프리셋 보이스만, 레퍼런스 오디오 미지원) · [[gemini]] · [[atlas-cloud]](Seed Audio 경유 여부 미확인)
- 배포 절차: [[prod-ddl-before-deploy-with-drift-check]]
- 세션: [[2026-09-24-voice레퍼런스오디오-생성삭제-카피pulse모델선택]]
