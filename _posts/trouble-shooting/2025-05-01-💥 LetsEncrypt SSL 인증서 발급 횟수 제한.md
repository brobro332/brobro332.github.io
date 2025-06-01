---
title: 💥 LetsEncrypt SSL 인증서 발급 횟수 제한
date: 2025-05-01 23:57:00 +0900
categories:
  - Trouble-Shooting
tags:
  - Trouble-Shooting
  - LetsEncrypt
---

### 문제 상황
- `Nginx`의 `default.conf`나 `Script` 파일이 제대로 작성되어 있음에도 `LetsEncrypt` `SSL` 인증서 발급에 계속 실패했다.


### 문제 원인
```bash
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Requesting a certificate for subdomain.iptime.org
An unexpected error occurred:
AttributeError: can't set attribute
Ask for help or search for solutions at https://community.letsencrypt.org. 
See the logfile /var/log/letsencrypt/letsencrypt.log or re-run Certbot with -v for more details.
```
- 인증서 발급 실패 후 `docker logs nginx` 명령어를 입력하면 상기 로그를 확인할 수 있다.

```bash
{
  "type": "urn:ietf:params:acme:error:rateLimited",
  "detail": "too many certificates (5) already issued for this exact set of domains in the last 168h0m0s, 
  	retry after 2025-05-02 07:31:44 UTC: 
  	see https://letsencrypt.org/docs/rate-limits/#new-certificates-per-exact-set-of-hostnames",
  "status": 429
}
```
- `docker exec -it nginx /bin/bash` 명령어로 `Nginx` 컨테이너로 들어가서 `/var/log/letsencrypt/letsencrypt.log` 파일을 확인하면 된다.
- 로그를 보면 인증서 발급 요청에 대한 횟수 제한이 있었음을 알 수 있고, 언제 다시 요청이 가능한지 확인 가능하다.
- 결론적으로 `Nginx`의 `default.conf`나 `Script` 파일을 계속 수정하면서, 인증서 발급 요청에 대한 횟수 제한 이상으로 요청한 것이 문제의 원인이었다.


### 해결 방법
- 찾아보니 인증서 발급과 인증서 갱신에 대한 테스트 명령어가 따로 있다고 한다.

#### ✅ 인증서 발급 테스트
```bash
certbot certonly --staging --webroot --webroot-path=/var/www/certbot --email ${CERTBOT_EMAIL} --agree-tos --no-eff-email -d 도메인
```

![](/assets/image/Pasted%20image%2020250601143625.png)
- `--staging` 옵션은 실제 인증서 발급 서버가 아닌 테스트 서버와 통신하도록 설정하는 옵션이다.
- 실제 인증서 발급보다는 조금 느슨한 기준을 적용해서 인증서를 발급해 준다.
- 이때 발급 되는 인증서는 실제 유효하지 않으며, 브라우저 등에서 신뢰 되지 않는다.

```bash
2025-05-01 13:32:32,856:DEBUG:certbot._internal.plugins.selection:Requested authenticator webroot and installer <certbot._internal.cli.cli_utils._Default object at 0xffffafdb4390>
2025-05-01 13:32:32,856:DEBUG:certbot._internal.cli:Var staging=True (set by user).
2025-05-01 13:32:32,856:DEBUG:certbot._internal.cli:Var server={'staging', 'dry_run'} (set by user).
2025-05-01 13:32:32,856:DEBUG:certbot._internal.cli:Var account={'server'} (set by user).
2025-05-01 13:32:32,856:DEBUG:certbot._internal.cli:Var staging=True (set by user).
2025-05-01 13:32:32,856:DEBUG:certbot._internal.cli:Var server={'staging', 'dry_run'} (set by user).
2025-05-01 13:32:32,856:DEBUG:certbot._internal.cli:Var authenticator=webroot (set by user).
2025-05-01 13:32:32,859:DEBUG:certbot._internal.cli:Var webroot_path=/var/www/certbot (set by user).
2025-05-01 13:32:32,859:DEBUG:certbot._internal.cli:Var webroot_path=/var/www/certbot (set by user).
2025-05-01 13:32:32,859:DEBUG:certbot._internal.cli:Var webroot_map={'webroot_path'} (set by user).
2025-05-01 13:32:32,860:DEBUG:certbot._internal.storage:Writing new config /etc/letsencrypt/renewal/도메인.conf.
2025-05-01 13:32:32,864:DEBUG:certbot._internal.display.obj:Notifying user:
Successfully received certificate.
```
- 테스트에 성공하면 `Nginx` 컨테이너 내 `/var/log/letsencrypt/letsencrypt.log`에서 상기 로그를 확인할 수 있다

#### ✅ 인증서 갱신 테스트
```bash
certbot renew --dry-run
```
- 만약 만료일 30일 전에 도래하지 않을 경우 아무 동작도 하지 않는다.
- 만료일 30일 전부터 해당 명령어를 통해 인증서를 갱신할 수 있다.

```bash
# docker logs nginx
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations, all simulated renewals succeeded:
  /etc/letsencrypt/live/도메인/fullchain.pem (success)
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

# docker exec -it nginx /bin/bash
# cd /var/log/letsencrypt
# cat letsencrypt.log
2025-05-01 13:46:04,141:DEBUG:certbot._internal.cli:Var dry_run=True (set by user).
2025-05-01 13:46:04,141:DEBUG:certbot._internal.cli:Var server={'dry_run', 'staging'} (set by user).
2025-05-01 13:46:04,141:DEBUG:certbot._internal.cli:Var dry_run=True (set by user).
2025-05-01 13:46:04,141:DEBUG:certbot._internal.cli:Var server={'dry_run', 'staging'} (set by user).
2025-05-01 13:46:04,141:DEBUG:certbot._internal.cli:Var account={'server'} (set by user).
2025-05-01 13:46:04,183:INFO:certbot.ocsp:Cannot extract OCSP URI from /etc/letsencrypt/archive/도메인/cert1.pem
2025-05-01 13:46:04,187:INFO:certbot._internal.renewal:Certificate not due for renewal, but simulating renewal for dry run
2025-05-01 13:46:04,187:INFO:certbot._internal.renewal:Non-interactive renewal: random delay of 95.38242860717801 seconds
```
- 테스트에 성공하면 로그를 통해 성공 여부를 확인할 수 있다.

#### ✅ 횟수 제한에 걸려있는데 급하게 인증서가 필요하다면?
```
certbot certonly --webroot --webroot-path=/var/www/certbot --email ${CERTBOT_EMAIL} --agree-tos --no-eff-email -d 서브도메인A -d 서브도메인B
```
- 인증서 발급과 갱신 프로세스에는 문제가 없고 횟수 제한에 걸려 있는데, 급하게 해당 서버를 이용해야 하는 상황이라면 다음과 같이 진행할 수 있다.
	1. 가비아 등 도메인 서비스에서 서브 도메인을 추가하여 동일 `IP`로 설정
	2. 인증서 발급 시 위의 명령어 실행


### 회고
- 테스트는 최대한 실제 운영 환경에 가깝게 하자는 주의여서 조금 찝찝하지만 `ChatGPT`는 두 테스트 명령어를 통과하면 실제 운영 환경에서도 성공적으로 인증서 발급 및 갱신이 가능할 것이라고 한다.