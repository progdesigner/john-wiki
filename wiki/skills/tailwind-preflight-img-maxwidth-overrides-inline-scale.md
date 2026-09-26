---
name: tailwind-preflight-img-maxwidth-overrides-inline-scale
description: Tailwind Preflight의 img{max-width:100%}가 인라인 width로 100% 넘게 키운 이미지를 눌러 위치 계산과 어긋나 보일 때의 진단·수정 절차
created: 2026-09-26
tags: [css, tailwind, img, layout, lampas-studio, lampas-web-edit]
---
# Tailwind Preflight `img` max-width가 인라인 스케일을 누른다

## 언제 쓰는가
캔버스/에디터에서 `<img>` 엘리먼트를 인라인 스타일로 직접 `width`/`left`(또는 `transform`) 계산해
배치하는데, **스케일을 특정 값(보통 부모 폭의 100%) 이상으로 키우는 순간부터 이미지가 찌그러지거나
한쪽으로 쏠려 보이는** 증상. 특히 "스케일은 그대로 커지는데 위치만 이상해진다"는 제보일 때 이 스킬을
먼저 의심한다.

## 절차 (단계별)
1. 프로젝트가 Tailwind CSS를 쓰는지 확인한다 — Preflight(기본 리셋)가 `img, video, ...` 등에
   `max-width: 100%; height: auto;`를 전역으로 건다.
2. 문제의 이미지가 인라인 스타일로 `width`(또는 `transform: scale()`)를 계산해 넣고 있는지 확인한다.
   `max-width: 100%`는 **인라인 `width`와 별개 속성이라 서로 충돌하지 않고 둘 다 적용**된다 — 즉
   `width`가 부모 폭을 넘는 값으로 계산돼도 `max-width: 100%`가 최종 렌더 폭을 부모 폭으로 눌러버린다.
3. 증상 재현 조건을 확인: 스케일이 100% 이하일 때는 안 보이다가, **실제 폭이 부모(캔버스) 폭을
   넘어서는 스케일부터** 문제가 시작된다 — 렌더 폭만 눌리고, `left`(또는 위치 계산)는 원래 의도한
   (안 눌린) 폭 기준 중심 계산값을 그대로 쓰기 때문에 어긋난다.
4. 수정: 해당 이미지 엘리먼트의 인라인 스타일에 `maxWidth: 'none'`(또는 동급 CSS)을 추가해 Preflight의
   제한을 그 엘리먼트에서만 끈다. 전역으로 Preflight를 끄지 않는다 — 다른 곳의 반응형 이미지 안전장치가
   깨진다.
5. **미리보기(DOM `<img>`)와 최종 렌더(canvas 2D 등 별도 경로)가 분리된 파이프라인이면, 렌더 쪽은 이
   버그의 영향을 받지 않을 수 있다** — 수정 범위를 미리보기 컴포넌트로 좁히기 전에 렌더 경로도
   실제로 영향받는지 확인한다(영향 없으면 "미리보기에서만 있던 문제"라고 명시해 사용자 혼란을 줄인다).

## 주의사항 / 함정
- 증상 설명("스케일 올리면 왼쪽으로 길어진다")만 보면 트랜스폼/앵커 포인트 계산 버그로 오인하기 쉽다 —
  먼저 Tailwind Preflight 여부와 인라인 `width`/`maxWidth` 충돌부터 확인하는 게 빠르다.
- `object-fit`/`object-position` 대신 dest-rect(퍼센트 기반 위치·크기)를 직접 계산해 배치하는
  컴포넌트일수록 이 문제에 취약하다 — CSS의 암묵적 제약(max-width 등)이 계산 로직과 별도로 계속
  살아있다는 점을 놓치기 쉽다.

## 출처: [[2026-09-13-lampas-edit-이미지트랙-텍스트효과-원본백업-구현]] ([[lampas-web-edit]] 0.1.5→0.1.6)
