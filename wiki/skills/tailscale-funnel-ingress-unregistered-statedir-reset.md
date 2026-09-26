---
name: tailscale-funnel-ingress-unregistered-statedir-reset
description: Tailscale Funnel을 켰는데(IngressEnabled=true, funnel 권한 확인됨) 공개 인그레스·DNS가 몇 시간이 지나도 등록되지 않을 때 — logout/login이나 콘솔 삭제 재등록으로도 안 풀리면 tailscaled 상태 디렉터리를 통째로 교체(머신 키 재발급)한다
created: 2026-09-26
tags: [tailscale, funnel, networking, diagnosis, macos]
---
# Tailscale Funnel 인그레스 미등록 → statedir 교체

## 언제 쓰는가
`tailscale funnel <port>` 실행이 성공하고(`Hostinfo.IngressEnabled=true` 로그 확인, 권한 `funnel`도
있음) 로컬 serve 매핑도 정상 응답하는데, **공개 DNS에 A/AAAA 레코드가 안 뜨고 인그레스 서버(예:
`103.84.155.153/.217`)에 SNI로 직접 접속해도 "모르는 이름"이라며 TLS가 끊기는 상태**가 30분~1시간을
넘겨도 안 풀릴 때. [[dns-propagation-stale-resolver-diagnosis]]로 "레코드가 이미 있는데 리졸버만
뒤처졌다"는 가능성을 먼저 배제한 뒤에도 그렇다면(권위 서버 자체에 레코드가 없음) 이 스킬로 넘어간다.

## 절차 (단계별)
1. **포트·설정을 먼저 의심하지 마라.** 같은 백엔드를 다른 포트로 다시 매핑해도(443→8443→17452 등)
   증상이 똑같다면 포트는 원인이 아니다 — 자칫 "Funnel이 특정 포트만 되나?"로 오인하기 쉽다(구버전
   제약과 혼동하기 쉬움; 최신 버전에선 tailnet 전용 임의 포트도 Funnel이 된다).
2. **logout → login을 시도해도 효과가 없으면, 노드 키만 바뀌고 머신 키는 그대로라는 걸 의심하라.**
   같은 `~/.local/state/tailscaled` 상태 디렉터리를 재사용하는 한 재로그인·콘솔에서 머신 삭제 후
   재등록 모두 **노드 키(node key)만** 새로 발급된다. 컨트롤 서버가 문제 삼는 게 머신 키(machine
   key) 단위라면 이 조치들은 근본적으로 효과가 없다.
3. **완전히 새 신원으로 가려면 상태 디렉터리 자체를 교체해야 한다.**
   1. `launchctl bootout`(또는 해당 OS의 서비스 정지 명령)으로 tailscaled를 내린다.
   2. 기존 상태 디렉터리를 삭제하지 말고 **날짜 붙여 백업**해 둔다(예:
      `mv ~/.local/state/tailscaled ~/.local/state/tailscaled.bak-YYYYMMDD`) — 문제가 statedir이
      아니었을 경우 되돌릴 수 있게.
   3. tailscaled를 다시 bootstrap하면 새 머신 키로 빈 상태 디렉터리가 생성된다.
   4. Tailscale 관리 콘솔 **Machines**에서 기존(구 머신 키) 노드 레코드를 삭제한다 — 이름 충돌
      (`이름-1`처럼 접미사가 붙는 것)을 피하려면 로그인 전에 삭제하는 것이 안전하다.
   5. `tailscale login --hostname=<원하는 이름>`으로 새 신원 등록.
4. **Funnel을 다시 걸고 반응 속도를 확인하라.** 머신 키가 실제 원인이었다면 보통 **초 단위**로
   인그레스·공개 DNS가 등록된다(관찰 사례: 15초). 이전엔 30분~1시간 이상 폴링해도 안 되던 것과
   대비되는 뚜렷한 신호다 — 여전히 몇 분 이상 걸리면 다른 원인(예: 진짜 DNS 전파 지연)일 수 있다.
5. **등록 확인 후에도 실사용 접속이 안 되면 [[dns-propagation-stale-resolver-diagnosis]]로 넘어가라.**
   서버 쪽(컨트롤)이 등록을 끝냈어도, 사용자 기기가 쓰는 리졸버(예: 국내 통신사 기본 DNS)가 이전
   실패 시점의 NXDOMAIN을 네거티브 캐싱 중일 수 있다 — 이건 서버 쪽에서 조치할 수 없는 별개
   구간이다.

## 주의사항 / 함정
- **모든 국지적 조치를 다 시도한 뒤에만 이 스킬을 쓸 것.** 관리 콘솔 Funnel 배지 확인, 다른
  호스트명으로 실험, 다른 포트로 실험 등 저비용 확인을 먼저 거쳐 "노드/포트/호스트명 단위 문제가
  아니다"를 배제하는 게 좋다 — statedir 교체는 그 노드의 로컬 상태를 통째로 새로 만드는 조치라
  가볍게 반복하기엔 비용이 있다(다른 설정도 함께 초기화됨).
- 이 문제는 **같은 테일넷에 두 번째 이상의 Funnel 노드를 등록하려 할 때** 관찰됐다 — 이미 Funnel이
  정상 동작 중인 노드(예: 하네스)가 있는 상태에서 새 노드를 추가하는 시나리오. 컨트롤 서버가 특정
  머신 키의 두 번째 Funnel 노드 활성화를 막는 서버 측 상태 문제일 가능성이 있으며, 근본 원인은
  Tailscale 쪽(비공개)이라 확정할 수 없다 — 재현되면 지원 문의가 정석 경로다.
- statedir을 새로 만들면 **테일넷 IP가 바뀐다**(이 사례에서 두 번 연속으로 바뀜) — 그 IP를 하드코딩한
  방화벽 규칙·설정이 있다면 함께 갱신해야 한다.
- 임시 우회로 **이미 Funnel이 되는 다른 노드에 경로를 얹는 방법**(예: `기존노드:포트 → 신규 백엔드`)이
  즉시 동작하는 대안이 될 수 있다 — statedir 교체 전에 급하게 공개 접속이 필요하면 이쪽을 먼저
  검토할 것. 단 주소·포트가 원하는 것과 달라지고, 임시로 얹은 뒤엔 반드시 원복해야 한다(원본 노드의
  Funnel 노출 범위가 의도치 않게 넓어지는 것을 막기 위해).

## 출처: [[2026-09-13-lampas-agent-clips-tailscale-주소변경]]
`lampas-agent-clips`(현재는 [[lampas-agent]]로 병합)를 새 노드명 `lampas-system`으로 Tailscale
Funnel에 올리려던 시도가 logout/login·콘솔 삭제 재등록 두 차례 모두 실패한 뒤, tailscaled 상태
디렉터리를 통째로 새로 만들어(머신 키까지 교체, 기존 상태는 `~/.local/state/tailscaled.bak-20260915`에
백업) 15초 만에 해결된 사례에서 정립됨. 최종 주소는 `https://lampas-system.tail0e32ab.ts.net`
(포트 없음, 443 Funnel).
