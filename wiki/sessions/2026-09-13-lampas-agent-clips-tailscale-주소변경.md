---
tags: [session, lampas-agent, tailscale, funnel, dns, networking, self-hosted-ops]
created: 2026-09-26
updated: 2026-09-26
---
# lampas-agent-clips — Tailscale 주소 변경 마라톤

`Tool: claude` 터미널 세션(작업 폴더 `Works/lampas/lampas-system`). 2026-09-13T15:30:59Z 시작,
`logs/terminals/archive/971bcdce-fe1b-4685-986c-8aef7fcb7f1d.md`에서 회수, 2026-09-26 뒤늦게 ingest.
본문 중 "어제(2026-09-14 00:51)"·"10분 더 기다렸지만" 같은 서술로 보아 **실제로는 최소 09-13~09-15
사흘에 걸쳐 이어진 세션**이다. 당시 `lampas-agent-clips`는 아직 `apps/lampas-agent`로 병합되기 전
([[2026-09-18-lampas-agent-omnara분석-durable-run구현]] 이전) 독립 앱이었다 — [[lampas-agent]] 엔티티의
"최초 노출 시점 재재정정 — 2026-09-12" 절이 다루는 시기 바로 다음날.

## 배경
`lampas-agent-clips` 데몬(로컬 포트 7452, launchd `io.lampas.clips.daemon`)은 `CLIPS_HOST=127.0.0.1`
루프백에만 바인드되고, `tailscale serve`/`funnel` 프록시로만 외부에 노출된다(`scripts/setup-tailscale.sh`).
세션 시작 시점 실제 접속 주소는 `https://progdesigner-mac-mini.tail43b73a.ts.net:8443` → 127.0.0.1:7452.

## 처리한 요청 (시간순)

1. **"8443과 다른 주소로 실제 agent 포트에 연결 가능한가?"** — 답변: 가능. (1) 같은 노드에 다른
   Funnel 포트를 하나 더 매핑(권장, TLS 자동) vs (2) `CLIPS_HOST`를 tailscale IP로 바꿔 직접 바인드
   (평문 HTTP, tailnet 내부 한정). 당시 Funnel은 443/8443/10000만 허용된다고 (나중에 틀린 것으로
   드러나는) 구버전 제약을 전제로 설명.
2. **"주소만 바꿔서 다시 연결해줘"** → 10000번 포트로 같은 백엔드(7452)에 두 번째 Funnel 매핑 추가.
   **"연결됐습니다"라고 보고했으나 이는 로컬 설정만 확인한 것으로 틀린 답변이었다** — 실제로는
   `progdesigner-mac-mini` 노드가 전날(2026-09-14 00:51)부터 테일넷에서 삭제된 상태(`404 node not
   found`)라 8443·10000 모두 실제로는 접속 불가였음. **로컬 설정 확인 ≠ 엔드투엔드 접속 확인**이라는
   교훈이 이 세션 전체를 관통한다.
3. **"이번엔 lampas-system 이름으로 다시 연결해줘"** — `lampas.dev@` 계정(하네스와 같은 테일넷,
   접미사 `tail0e32ab`)으로 재로그인, 노드명 `lampas-system`. 포트는 `실제포트+10000=17452`
   컨벤션으로 tailnet 전용 serve. **테일넷이 바뀌어** 기존 맥북(`progdesigner7@` 테일넷)에서는 이
   주소가 안 보이게 됨 — 사용자에게 명시적으로 경고.
4. **"funnel 도 해야하지 않아?"** — 여기서 이전 설명(포트 제약)이 틀렸음이 드러남: 실제로는
   17452로도 Funnel이 바로 성공(버전 1.98.8, 포트 제한이 구버전 제약이었음). Google
   OAuth 원본·S3 CORS 등록 필요성 재확인.
5. **S3 CORS + Google OAuth 등록** — S3 `lampas.io` CORS(`elevino` 프로필) PUT으로 오리진 교체,
   인증서 발급, 저장소 문서(`deploy.json`·`CLAUDE.md`·`setup-tailscale.sh`·`LOG.md`) 갱신. **매번 주소가
   바뀔 때마다 이 3종(OAuth 원본·S3 CORS·저장소 문서)을 함께 갱신해야 하는 패턴이 이후 계속 반복됨.**
6. **"원래 포트+10000만 tailscale로 연결되도록 되돌려줘"** → 17452 하나만 tailnet 전용으로 정리.
   Funnel은 이 테일넷 권한상 443/8443/10000뿐이라 17452는 공개 불가 — tailnet 전용이 유일한 형태.
7. **"포트 없이 443으로 해줘"** → 443 Funnel 하나만 노출(`https://lampas-system.tail0e32ab.ts.net`).
   OAuth 원본·S3 CORS 재등록.
8. **"접속이 안됨" → "머신을 다시 등록할까, 아니면 포트 공유 문제일까?"** — 포트 공유 가설은
   기각(하네스와 clips는 별개 노드, userspace 모드라 OS 포트 비점유, 과거 443+8443 동시 Funnel
   전례 있음). **새 노드 신원으로 재등록**(logout→콘솔에서 머신 삭제→재로그인)을 시도했으나
   테일넷 IP만 바뀌고(`100.96.184.84`→`100.92.159.82`) 인그레스·DNS 미등록은 그대로.
9. **"인그레스 문제란 뭐야?"** 설명 요청에 Funnel 동작 구조(DNS→인그레스 서버 SNI 라우팅→
   DERP→노드→serve→백엔드)를 도식으로 설명 — 노드 쪽(③)은 정상, 컨트롤 서버가 담당하는
   ①(공개 DNS)·②(인그레스 라우팅 표 등록)가 안 되고 있다고 진단.
10. **10분 폴링 재시도**해도 동일 — 노드 쪽에서 할 수 있는 조치(권한 확인·설정 동일·세션 재수립·
    새 노드 신원)를 모두 시도했지만 소용없어 "컨트롤이 이 테일넷의 두 번째 Funnel 노드를 활성화 안
    하는 서버 측 문제"로 결론, 3가지 대안 제시(① 하네스 노드에 얹기 ② tailnet 전용 유지 ③ Tailscale
    지원 문의).
11. **"1번으로 적용해봐"** → `lampas-harness` 노드(이미 Funnel 정상)에 `:8443 → 127.0.0.1:7452`
    추가, 즉시 인터넷에서 200 확인. S3 CORS를 이 오리진으로 임시 전환.
12. **"lampas-harness 8443은 다시 제거해"** → 원복(하네스는 443→8787 하나만), `lampas-system`
    443 설정은 유지(tailnet 전용). S3 CORS도 `lampas-system` 주소로 복구.
13. **"연결은 언제쯤 될까?"** — "기다리면 되는 문제가 아니다"로 명확히 답변(전파 지연 범위를
    훨씬 넘겼고 노드 신원을 바꿔도 같은 결과). Tailscale 지원 문의용 정보 정리 + 저비용 확인 두
    가지 제안(관리 콘솔 Funnel 배지 확인 / 다른 호스트명 실험).
14. **"완전히 다시 연결해보자"** — **결정적 조치**: 이전까지의 logout/login·콘솔 삭제 재등록은
    모두 같은 상태 디렉터리(`~/.local/state/tailscaled`)를 재사용해 **머신 키(machine key)는 그대로
    였고 노드 키만 바뀌었다**는 점을 재구성 — 이번엔 tailscaled 상태 디렉터리 자체를 통째로 새로
    만들어(`launchctl bootout` → 기존 상태 `~/.local/state/tailscaled.bak-20260915`로 백업 →
    재생성 → bootstrap) **머신 키까지 새로** 발급, 콘솔에서 구 머신 삭제 후 `lampas-system`으로
    재등록.
15. **결과: 15초 만에 인그레스 등록, 공개 DNS도 게시됨.** 이전 시도들과 유일하게 다른 변수(머신
    키 교체)가 원인이었다는 결론 — 자세한 절차와 함정은 [[tailscale-funnel-ingress-unregistered-statedir-reset]]
    스킬로 추출.
16. **최종 확인**: 권위 DNS(dnsimple)·공개 리졸버(1.1.1.1/8.8.8.8/9.9.9.9/OpenDNS) 모두 정상 응답,
    인그레스 IP 직접 접속 200. 유일하게 남은 지연은 **이 맥미니가 쓰는 KT DNS(168.126.63.1/.2)의
    네거티브 캐시** — 이전 실패 시점의 NXDOMAIN을 최대 5분 캐싱 중이라 자연 해소 대기.
    [[dns-propagation-stale-resolver-diagnosis]] 스킬과 동일한 패턴의 재확인 사례(다른 앱·다른
    시점에서도 같은 원인으로 재현됨을 뒷받침).

## 핵심 결론
- **로컬(자기 자신) 검증만으로 "연결됐습니다"라고 보고하면 틀릴 수 있다** — 이 맥미니는
  userspace-networking이라 자기 tailnet URL로 루프백이 안 되므로, 최종 접속 확인은 항상 다른 기기
  (맥북/폰)에서 해야 한다고 반복 강조됨.
- **Tailscale Funnel이 "설정은 맞는데 인그레스에 안 뜨는" 증상의 근본 원인은 기존 사례(DNS 리졸버
  캐시 지연)와 다른 새로운 패턴** — 컨트롤이 특정 **머신 키**에 대해 Funnel 인그레스 게시를 막고
  있던 상태로, logout/login이나 콘솔에서 머신을 지우고 재등록해도 (노드 키만 바뀌고 머신 키는
  그대로라) 해결되지 않았다. tailscaled 상태 디렉터리 전체를 새로 만들어 머신 키까지 바꾸자 즉시
  해결됨.
- 최종 주소 `https://lampas-system.tail0e32ab.ts.net`(포트 없음, 443 Funnel)는 이후 [[lampas-agent]]
  엔티티 페이지가 "Fixs 절"·"영상 수집" 절에서 전제로 삼는 그 주소의 **origin story**다 — 지금까지
  위키에 "접속 주소"로만 기록돼 있던 것의 유래가 이 세션에서 규명됨.
- 주소가 바뀔 때마다 **Google OAuth 승인된 JavaScript 원본**과 **S3 `lampas.io` CORS AllowedOrigins**
  두 가지를 함께 갱신해야 하며, 저장소 문서(`deploy.json`·`CLAUDE.md`·`setup-tailscale.sh`·`LOG.md`)도
  매번 동기화됨 — 커밋은 세션 내내 한 번도 하지 않음(다음 세션에 위임 추정).
- `setup-tailscale.sh`의 소켓 자동 감지(`pgrep … | head -1`)가 **하네스 소켓을 먼저 잡아 엉뚱한
  데몬에 적용되는 버그**를 발견 — `--socket` 명시 지정으로 우회했고, 이후 스크립트 자체를
  "harness 제외 + `TS_SOCKET` 오버라이드" 방식으로 수정.

## 관련
- [[lampas-agent]] (엔티티 — `lampas-agent-clips` 독립 앱 시절의 접속 주소 확정 경위를 이 세션이
  채움, "최초 노출 시점 재재정정" 절 다음날 사건)
- [[lampas-harness]] (임시로 8443 경로를 빌려준 쪽 — 즉시 원복됨)
- [[tailscale-funnel-ingress-unregistered-statedir-reset]] (이 세션에서 신설한 스킬)
- [[dns-propagation-stale-resolver-diagnosis]] (세션 말미 KT DNS 캐싱 지연 재확인 — 다른 시점·다른
  앱에서도 같은 패턴이 재현됨을 뒷받침)
- [[self-hosted-agent-server-ops]] (같은 소켓 자동 감지 버그 계열 — 함정 5 CLI 경로 drift와 유사한
  "여러 데몬이 뒤섞이는" 운영 함정)
- [[2026-09-12-하네스-폴더선택버그-모바일키버튼-tailscale전용주소-정지버튼-cli전역전환]] (하네스 쪽
  전용 Tailscale 주소를 다루는 병행 계열 세션, tail0e32ab 접미사가 하네스와 lampas-system 양쪽에
  등장하는 배경)
