---
name: env-empty-var-shadows-dotenv
description: 새 API 키/설정을 .env에 넣었는데 기능·목록에 안 나타날 때 — 셸의 빈 env 변수가 .env 값을 가리는지 진단·수정
created: 2026-07-13
tags: [dotenv, env, config, nodejs, lampas-harness, debugging]
---
# 빈 env 변수가 .env의 진짜 값을 가리는 함정

## 언제 쓰는가
`.env`에 API 키·설정을 분명히 넣었는데 **기능·모델 목록·프로바이더에 하나도 안 나타날 때.** 특히 "다른
키는 되는데 이 키만 안 됨"이면 이 함정을 먼저 의심한다. (`[[lampas-harness]]`에서 `.env`에 Google 키가
있는데도 모델 선택기에 Google 모델이 하나도 안 뜬 사례 — 유력한 "Google Models 401" 근본 원인.)

## 원인
`dotenv`의 `config()`는 **override:false가 기본** — 이미 존재하는 env 변수는 안 덮어쓴다. 그런데 셸에
`GOOGLE_API_KEY=`처럼 **빈 값으로 export**돼 있으면, 키가 "존재"하므로 dotenv가 `.env`의 진짜 값을
안 넣는다. `googleModels()` 같은 코드가 빈 문자열을 보고 즉시 `[]`를 반환 → 기능이 통째로 사라진다.
(OPENAI/ANTHROPIC 키는 셸에 실제 값이 있어 우연히 정상 동작해, 문제를 특정 프로바이더로 오인하기 쉽다.)

## 절차 (단계별)
1. **실행 프로세스의 실제 env를 의심** — 코드/`.env`가 맞아 보여도 `ps eww <서버PID>`로 실행 중
   프로세스의 env를 떠서 문제 변수가 **비어 있는지** 확인한다.
2. **셸/launchd 환경에서 빈 export를 찾는다** — `.zshrc`·`.zprofile`·plist `EnvironmentVariables`에
   `KEY=`(빈 값)가 있는지.
3. **코드로 방어** — dotenv 로드 후 `parsed`를 순회하며 **미설정이거나 빈 문자열**(`!process.env[key]`)인
   변수만 `.env` 값으로 채운다:
   ```ts
   const { parsed } = dotenv.config();
   for (const key in parsed) {
     if (!process.env[key]) process.env[key] = parsed[key];  // 빈 값도 .env로 덮어씀
   }
   ```
4. **재시작 후 재확인** — 실행 프로세스가 옛 env를 들고 있으니 데몬 재시작 필요 → [[macos-launchd-daemon]].

## 주의사항 / 함정
- 순수 `override:true`로 dotenv를 돌리면 **의도적으로 셸에서 준 값**까지 덮어쓸 수 있으니, 위처럼
  "빈 값일 때만" 채우는 게 안전하다.
- 증상이 "401 인증 오류"로 보일 수도, "목록이 비어 있음"으로 보일 수도 있다. 둘 다 같은 뿌리일 수 있다.

## 출처: [[2026-07-13-람파스-누적운영기억-이관]] ([[lampas-harness]] · [[model-selection]])
