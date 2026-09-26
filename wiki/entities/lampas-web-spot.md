---
tags: [entity, project, product, stub, lampas-studio, map, naver]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-web-spot

`[[lampas-studio]]` 저장소(`lampas-system`) 내 신규 앱 — **식당 지도 앱**. 2026-09-24 저녁 세션
([[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]])에서 원격 커밋 `e07db82f`
"feat(spot): lampas-web-spot 식당 지도 앱 신규 (27 파일)"로 처음 노출됐다 — **이 세션이 직접 구현한
것이 아니라**, 다른(미상) 세션의 작업이 원격에 이미 있던 것을 pull로 받아 로컬 병합 커밋에 포함시킨
것뿐이다. 실제 구현 내용(기능·화면·API 구조)은 이 위키 소스로 아직 조사되지 않음.

## 관찰된 정보 (병합 커밋 메타데이터뿐)
- 파일 27개로 신규 스캐폴딩된 앱으로 추정(다른 lampas-system 신규 앱과 비슷한 규모).
- `.env.local`·`env/.env.production`에 **Naver 지도 클라이언트 ID**만 있음 — 네이버 지도 API 기반
  식당 지도 서비스로 추정. 다른 앱의 env 커밋 관례대로 이 키도 함께 git에 커밋됨(프론트 공개 키라
  당장 문제는 아니라고 병기됐으나 비공개 전환 여부는 미결정).

## 열린 질문
- 정확한 기능 범위(리뷰 수집? 추천? 예약?), 배포 도메인, 어느 제품 라인(Lampas/Dalar/Talk 중 무엇)
  소속인지 미확인 — 다음 세션에서 코드 직접 조사 필요.
- 이 앱을 실제로 만든 세션의 원본 소스가 위키에 아직 ingest되지 않음.

## 관련
- 상위 저장소: [[lampas-studio]] (`lampas-system`)
- 세션: [[2026-09-24-studio개선-seedance미니-노드툴바-멀티커밋푸시]]
