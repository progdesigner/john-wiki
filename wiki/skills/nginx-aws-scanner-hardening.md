---
name: nginx-aws-scanner-hardening
description: nginx access 로그에 자동 취약점 스캐너 정찰이 찍혔을 때 IP 오인 없이 방어를 계층화하는 절차
created: 2026-07-17
tags: [security, nginx, aws, waf, infra]
---
# nginx + AWS 스캐너 방어 절차 (nginx-aws-scanner-hardening)

## 언제 쓰는가
nginx access 로그에 `.env`, `.git`, `phpinfo.php`, `aws-secret.yaml`류 민감 경로를 무작위로 훑는
자동 스캐너 요청(대개 `404`)이 대량으로 찍혔을 때. 특히 요청 IP가 `10.0.0.0/8`·`172.16.0.0/12`·
`192.168.0.0/16` 같은 **사설 대역**이라 ALB/리버스프록시를 거친 트래픽으로 의심될 때 우선 적용.
[[2026-07-17-nginx-스캐너-차단-조사]]에서 정립됨.

## 절차 (단계별)
1. **요청 IP가 사설 대역인지 먼저 확인한다.** 사설 IP(예: `172.31.x.x` AWS VPC 대역)면 이는 실제
   공격자 IP가 아니라 ALB/컨테이너 네트워크의 IP일 가능성이 높다. **이 IP를 Nginx에서 직접 차단하지
   않는다** — 정상 사용자 트래픽 전체가 함께 막힌다.
2. **Nginx에서 민감 경로 패턴을 즉시 차단**한다: dotfile(`/\.`, `.well-known`은 예외), 설정/백업/키
   확장자(`env|ini|log|sql|bak|old|pem|key|yml|...`), 소스/CI 디렉터리(`.git|.svn|.github|
   node_modules`), phpinfo류 진단 경로. `access_log off; log_not_found off; return 404;`로 응답하고
   로그 잡음도 함께 줄인다(디버깅 중엔 `444`보다 `404` 권장).
3. **ALB 뒤 구조라면 real IP를 먼저 복원**한다: `set_real_ip_from`에 ALB 서브넷/보안그룹 CIDR만
   지정(`0.0.0.0/0` 금지 — 헤더 위조 위험), `real_ip_header X-Forwarded-For; real_ip_recursive on;`.
   이 설정 없이 rate limit·IP 차단을 걸면 전부 무의미하다(모든 요청이 같은 프록시 IP로 보임).
4. **Nginx rate limit**을 추가한다: `limit_req_zone`(IP당 초당 요청 수)+`limit_conn_zone`(동시 연결
   수). 3번을 먼저 해야 `$binary_remote_addr`가 실사용자 기준으로 동작한다.
5. **네트워크 경계에서 인바운드를 좁힌다**: EC2 Security Group 80/443은 ALB Security Group에서만
   허용(`0.0.0.0/0` 공개 금지). 이상적 구조는 `인터넷 → CloudFront/ALB + AWS WAF → EC2/Nginx`.
6. **ALB/CloudFront에 AWS WAF**를 붙인다: Managed Rule(`CommonRuleSet`, `KnownBadInputsRuleSet`,
   `AmazonIpReputationList`, `SQLiRuleSet`, 필요시 유료 `BotControlRuleSet`) + Rate-based rule(집계
   기준 IP, 임계치는 일반 서비스 5분당 300~1,000부터 시작해 실트래픽 보고 조정).
7. **과거 로그에서 실제 노출 여부를 별도로 검증**한다: 지금 전부 `404`라고 안심하지 말고
   `grep -E '(\.env|\.git|phpinfo|credentials|secret)' access.log* | grep -E '" (200|206|301|302) '`
   (압축분은 `zgrep`)로 과거에 한 번이라도 성공 응답이 있었는지 확인한다. 있었다면 파일 제거·비밀키
   전량 교체(DB/AWS/API/OAuth)·`.git` 디렉터리 배포 제거·CloudTrail/로그인 로그 점검까지 진행한다.
8. **요청 출처가 사설 대역이면 내부 인스턴스 스캔 가능성도 배제하지 않는다**:
   `aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query
   'Reservations[*].Instances[*].[InstanceId,PrivateIpAddress,LaunchTime]'`로 그 사설 IP를 실제
   인스턴스에 역매핑한다. 자신의 인스턴스가 아니면 침투 가능성 — Security Group을 즉시 좁히고 AWS
   Support에 신고한다.

## 주의사항 / 함정
- **사설 IP를 공격자 IP로 착각해 Nginx에서 차단하는 것이 가장 흔한 실수** — ALB를 거치는 정상
  트래픽까지 함께 죽는다. 항상 "이 IP가 사설 대역인가"부터 확인.
- `set_real_ip_from`을 신뢰 범위 밖(`0.0.0.0/0`)까지 넓히면 공격자가 `X-Forwarded-For` 헤더를
  위조해 IP 기반 차단/rate limit을 무력화할 수 있다. ALB/신뢰 프록시 대역만 지정.
- 로그가 전부 `404`라고 해서 침해 없음으로 단정하지 않는다 — 과거 로그의 `200/206/301/302` 응답
  여부를 별도로 확인해야 진짜 결론이 난다.
- User-Agent가 구형 브라우저 버전(예: Chrome 56.0, 2016년식)이면 위조된 UA일 확률이 높다 — 신뢰
  근거로 쓰지 않는다.
- URI 문자열을 WAF 커스텀 룰에 개별로 끝없이 추가하기보다 AWS Managed Rules + rate limit + Nginx
  차단 조합이 유지보수 관점에서 더 낫다.

## 출처: [[2026-07-17-nginx-스캐너-차단-조사]]
