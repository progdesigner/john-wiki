---
name: idempotent-message-retry
description: 네트워크 오류로 요청이 날아가지 않게 msgId 멱등성 + 클라이언트 자동 재전송을 구축
created: 2026-07-07
tags: [reliability, retry, idempotency, network]
---
# 멱등 메시지 재시도 (network error로 요청 소실 방지)

## 언제 쓰는가
대기/실행 중 network error가 나면 요청이 통째로 날아가는 문제를 막을 때.
자동 재전송을 넣되 **같은 메시지가 두 번 실행되지 않게** 해야 할 때.

## 절차 (단계별)
1. **클라이언트가 msgId 발급** — 각 메시지에 안정적인 고유 id를 붙여 요청에 포함.
2. **서버 멱등성(dedupe)** — `handleChat`이 msgId를 기준으로 판별:
   - 이미 완료된 msgId → 턴 재실행 없이 즉시 `done (dedup: true)` 응답.
   - 실행 중인 msgId → 재실행 없이 원본 턴 완료를 기다렸다가 결과 반환.
   - 히스토리(jsonl)에도 msgId당 **한 번만** 저장(중복 append 방지).
3. **클라이언트 자동 재전송** — 연결 복구 감지 시 최대 5회 자동 재전송.
4. **놓친 응답 복원** — 재연결 시 끊긴 동안의 응답을 히스토리에서 자동 복원.
5. **수동 fallback** — 자동 재시도가 모두 실패하면 "다시 시도" 버튼을 남겨 수동 재개.

## 검증 (실제 서버로 확인)
1. 정상 완료 후 같은 msgId 재전송 → 재실행 없이 즉시 `done (dedup: true)`.
2. 실행 중 같은 msgId 재전송 → 재실행 없이 원본 완료 후 알림.
3. 메시지를 두 번씩 보내도 jsonl엔 각각 한 번씩만 저장.

## 주의사항 / 함정
- ChatSession을 생성하는 경로가 여러 곳이면 모두 dedupe를 거치게 할 것.
- 멱등 키는 클라이언트가 재전송 시 **동일하게 유지**해야 의미가 있다(재발급 금지).

## 출처: [[2026-07-06-lampas-harness-구축]] ([[lampas-harness]])
