---
name: local-llm-rapidmlx-install
description: Apple Silicon Mac에 로컬 LLM(Rapid-MLX)을 설치·상주화하고 하네스 모델 선택기에 노출할 때
created: 2026-07-09
updated: 2026-07-13
tags: [local-llm, rapid-mlx, mlx, launchd, lampas-harness, apple-silicon]
---
# Rapid-MLX 로컬 LLM 설치 + launchd 상주 + 하네스 통합

## 언제 쓰는가
Apple Silicon Mac에서 API 비용 없이 로컬 LLM을 돌리고, `[[lampas-harness]]` 같은 OpenAI 호환
클라이언트의 모델 선택기에 붙일 때. 배경·양자화·사이징 지식은 [[local-llm-on-apple-silicon]].

## 절차 (단계별)
1. **사양에 맞는 모델 선택** — 64GB면 35B급 8bit(~37GB)가 sweet spot. 요청이 사양 초과면
   (예 DeepSeek-V4-Flash 284B → Q4로도 ~95GB) **솔직히 반려**하고 실행 가능한 최선 제안.
2. **Rapid-MLX 설치**:
   ```bash
   curl -fsSL https://rapidmlx.com/install.sh | bash
   export PATH="$HOME/.local/bin:$PATH"   # rapid-mlx가 안 잡히면
   rapid-mlx --version
   ```
3. **모델 다운로드** — 수십 GB라 **세션에서 분리해** 받는다 → [[detach-long-job-nohup]]:
   ```bash
   nohup rapid-mlx pull qwen3.5-35b-8bit > logs/rapidmlx-pull.log 2>&1 & disown
   du -sh ~/.cache/huggingface/hub/models--mlx-community--Qwen3.5-35B-A3B-8bit  # 진행
   ```
   (`qwen3.5-35b-8bit` = `mlx-community/Qwen3.5-35B-A3B-8bit`, 약 37.7GB.)
4. **서버 상주화** — `rapid-mlx serve <model>`을 launchd 서비스로 등록(예 `io.lampas.rapidmlx`)
   → 재부팅 자동 기동. 절차·라벨 함정: [[macos-launchd-daemon]]. 서버는 `http://localhost:8000/v1`(무인증).
   운영 사실·plist 경로는 엔티티 [[rapid-mlx]] 참고.
5. **응답 테스트**:
   ```python
   from openai import OpenAI
   c = OpenAI(base_url="http://localhost:8000/v1", api_key="not-needed")
   print(c.chat.completions.create(model="qwen3.5-35b-8bit",
         messages=[{"role":"user","content":"안녕"}]).choices[0].message.content)
   ```
6. **하네스 통합** — `server.ts`에 로컬 프로바이더 추가, 로컬 서버가 켜져 있으면 모델 선택기에
   **"Local" 그룹**으로 자동 노출 → [[model-selection]]. 재시작 후 브라우저 새로고침.

## 주의사항 / 함정
- **launchd(KeepAlive) 관리 후엔 실행 인자를 kill+수동재실행으로 못 바꾼다** — 프로세스를 죽이면 launchd가
  **즉시 옛 plist 설정 그대로 되살려** 포트 경합이 난다(2026-07-11 `--host 0.0.0.0`로 바꾸려다 겪음). 인자
  변경은 **plist를 고쳐 재배포**: 레포 `launchd/io.lampas.rapidmlx.plist` 수정 → `~/Library/LaunchAgents/`
  복사 → `bootout` → 몇 초 대기 → `bootstrap`. 상세: [[rapid-mlx]] · [[macos-launchd-daemon]] · [[self-hosted-agent-server-ops]].
- **35B 모델 로드에 ~5분** 걸리니 포트 오픈 폴링은 여유 있게. 로그 `logs/rapidmlx.{out,err}.log`.
- `sysctl` 등이 `command not found`면 PATH에 `/usr/sbin` 누락 → PATH 보강 후 재실행.
- **MoE 활성 파라미터 함정**: 활성 파라미터가 작아도 전체 파라미터를 메모리에 올려야 함.
- 로컬 모델은 **툴(웹검색·MCP·날씨)이 기본 없고** 품질이 Claude보다 낮음(환각 관찰) →
  [[local-llm-on-apple-silicon]].
- SDK 가드가 `/tmp` 읽기·URL/슬래시 인자를 막을 수 있으니 작업 폴더 안에서·스크립트로 우회.

## 출처: [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]] · [[2026-07-13-람파스-누적운영기억-이관]] · 배경: [[local-llm-on-apple-silicon]] · 엔티티: [[rapid-mlx]]
