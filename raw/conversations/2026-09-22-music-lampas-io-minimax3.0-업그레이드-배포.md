# Music 개선

Tool: claude
Started: 2026-09-22T23:21:26.647Z
Working directory: /Users/progdesigner/Works/lampas/lampas-system
Source: /Users/progdesigner/.claude/projects/-Users-progdesigner-Works-lampas-lampas-system/2b5ed18c-f701-43ba-8bdb-56b96e319f16.jsonl

## user

music.lampas.io 모델을 minimax 3.0 으로 바꿔죠

## assistant

lampas-web-music 과 API 쪽에서 minimax 음악 모델이 어디에 지정돼 있는지 찾아보겠습니다.

## assistant

파라미터 형태(prompt·lyrics·is_instrumental·format 등)가 2.6 과 동일하니 그대로 교체 가능합니다. 이제 웹·API 오버라이드·테스트·문서를 수정합니다.

## assistant

music.lampas.io 의 생성 모델을 `minimax/music-3.0` 으로 바꿨습니다. 배포는 아직 하지 않았습니다.

**바뀐 것**
- `apps/lampas-web-music/src/lib/api.ts` 의 `MUSIC_MODEL` 을 2.6 → 3.0 으로 교체. Atlas 스키마를 확인한 결과 요청 필드(prompt·lyrics·is_instrumental·format·sample_rate·bitrate)가 2.6 과 동일해서 호출 코드는 그대로입니다.
- `model-pricing.overrides.ts` 의 web-music 큐레이션 항목을 3.0 으로 갱신. 3.0 은 이미 생성 카탈로그에 곡당 150크레딧(PER_REQUEST)으로 들어 있어 과금은 동일하게 동작합니다. 2.6 항목도 카탈로그에 남아 있습니다.
- 유닛 테스트(`model-pricing.spec.ts`)를 3.0 기준으로 바꾸고 2.6 유지 케이스를 추가. 9개 통과.
- 문서: CLAUDE.md 게이트웨이 표, docs 의 credits.md·other-apis.md 예시를 3.0 으로 갱신.
- web-music 타입체크 통과.

**참고**
- 3.0 은 최대 5분 곡, prompt 2,000자·lyrics 3,500자 제한입니다. 현재 UI 에 길이 제한 로직이 없어 그대로 두었습니다.
- tools.lampas.io 의 `music-gen` 툴은 여전히 2.6 입니다. 요청 범위가 music 이라 손대지 않았습니다.

반영하려면 아래 순서로 배포하면 됩니다(오버라이드 변경은 API 에도 있음).

```
./scripts/deploy-api.sh lampas-api
./scripts/deploy-web.sh lampas-web-music
```

## user

작업이 완료 되면 배포해

## assistant

배포 전에 lampas-api 스키마 드리프트 확인 절차(메모리)를 먼저 보고 진행합니다.

## assistant

드리프트 없음. Prisma 클라이언트를 lampas 스키마로 생성한 뒤 API 배포, 웹 배포는 병렬로 시작합니다.

## assistant

배포 완료했습니다. music.lampas.io 가 이제 `minimax/music-3.0` 으로 생성합니다.

**진행 내용**
- 운영 DB 와 Prisma 스키마 드리프트 확인 결과 차이 없음. lampas 스키마로 Prisma 클라이언트를 재생성한 뒤 배포했습니다.
- lampas-api 배포 완료. PM2 에서 `lampas-api` 0.1.137 이 정상 기동 중입니다.
- lampas-web-music 배포 완료. S3 업로드와 CloudFront 무효화까지 끝났습니다.

**검증**
- 운영 API 모델 목록에서 `minimax/music-3.0` 이 곡당 150크레딧(PER_REQUEST)으로 노출됩니다.
- music.lampas.io 의 실제 배포 번들에 `minimax/music-3.0` 만 들어 있는 것을 확인했습니다.

**참고**
- 스모크 테스트용 `.env.smoke` 가 없어 두 스크립트 모두 배포 후 스모크는 건너뛰었습니다.
- 이번 API 배포에는 작업 트리에 있던 다른 미커밋 변경(dalar 스튜디오·flow-works·pulse 등)도 함께 올라갔습니다. 변경분은 아직 커밋하지 않았습니다.
- tools.lampas.io 의 음악 툴은 여전히 2.6 입니다.
