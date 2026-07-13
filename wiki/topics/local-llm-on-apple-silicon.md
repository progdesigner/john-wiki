---
tags: [topic, local-llm, apple-silicon, mlx, quantization, reference]
created: 2026-07-09
updated: 2026-07-09
---
# Apple Silicon에서 로컬 LLM 돌리기

`[[progdesigner]]`의 Mac(M4 Pro / 20코어 GPU / **64GB 통합 메모리** / 2TB)에서 로컬 LLM을 골라
설치·통합한 배경 지식. 실제 설치는 `[[lampas-harness]]`에 통합됨(2026-07-08~09 세션).
출처: [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]].

## 세 가지 축 (모델 선택 = a·b·c의 조합)
- **a. 모델 크기(파라미터)** — 7B / 32B / 70B / … 클수록 이해·생성 품질↑, 메모리·연산↑.
- **b. 양자화(quantization)** — 가중치를 더 적은 비트로 압축해 메모리 절약:
  - `FP16` 원형에 가깝고 정밀·안정 / 메모리 많이 씀.
  - `Q8_0` 8비트, 메모리 절약·빠름 / 약간의 정밀도 손실.
  - `Q4_K_M` K-quant 4비트. **K**=가중치를 블록 단위로 나눠 블록마다 스케일을 다르게(중요 레이어엔
    더 정확한 비트) / **_M**=품질 중간 등급(S=더 절약·손실↑, L=품질↑·메모리↑). 메모리 크게 절약,
    보통 손실 눈에 띄지 않으나 수학·정밀 연산엔 영향.
- **c. 백엔드/가속** — 실행 하드웨어 경로:
  - **Metal**(llama.cpp `-DGGML_METAL=ON`) — Apple GPU 직접, UMA 덕에 CPU↔GPU 이동 오버헤드 적음.
    7B급에선 prefill+decode가 MLX보다 약간 빠르다는 평.
  - **MLX**(Rapid-MLX) — Apple MLX 프레임워크, compute graph(op fusion). 70B급 대형에서 유리하다는 보고.
  - **CPU 전용** — 느림, 대형은 비현실적.

## 런타임 옵션
- **Rapid-MLX** — MLX 기반, OpenAI 호환 서버(`http://localhost:8000/v1`, 기본 무인증).
  `curl -fsSL https://rapidmlx.com/install.sh | bash` → `rapid-mlx pull <model>` → `rapid-mlx serve <model>`.
  이 세션에서 채택(v0.10.5). → 설치 절차: [[local-llm-rapidmlx-install]]
- **llama.cpp** — C++/GGUF, `brew install llama.cpp` 또는 소스 빌드(`cmake -DGGML_METAL=ON`).
- **Ollama** — 앱형 관리·전환 UX 좋음.

## 사양별 모델 사이징 (64GB 기준 관찰)
- 64GB에서는 **35B급 8bit(~37GB)** 정도가 현실적 상한이자 sweet spot.
  채택 모델: `qwen3.5-35b-8bit` = `mlx-community/Qwen3.5-35B-A3B-8bit`, 약 37.7GB(35.2GiB).
- 70B급도 Q4_K_M로는 가능 범위로 거론됨.
- **거부 사례 — DeepSeek-V4-Flash**: 284B(토큰당 활성 13B) 모델. **전체 284B를 메모리에 올려야 하고
  Q4_K_M로도 ~95GB VRAM 필요** → 64GB로 실행 불가. MoE의 "활성 파라미터"가 작아도
  **전체 파라미터가 메모리를 먹는다**는 함정.
- 규칙: 요청받은 "최고 사양"이 사양을 넘으면 **솔직히 반려하고** 실행 가능한 최선을 제안.

## 로컬 모델의 한계 (이 세션 관찰)
로컬 `qwen3.5-35b-8bit`로 대화해보니 Claude 대비 품질이 눈에 띄게 낮고 환각 경향:
- "MCP 연결 돼?"에 *Multi-Controller Protocol*이라고 지어냄.
- **웹검색·날씨·MCP 등 툴은 별도 배선 없이는 불가** — 로컬 LLM은 순수 텍스트 생성 엔진.
- 장점은 **API 비용 0 + 오프라인**. 품질이 중요하면 Claude, 비용/프라이버시면 로컬.

## 관련
- 세션: [[2026-07-08-스케줄러-로컬llm-사용영역페르소나]]
- 스킬: [[local-llm-rapidmlx-install]] · [[macos-launchd-daemon]]
- 하네스 통합: [[model-selection]] (모델 선택기 "Local" 그룹) · [[lampas-harness]]
