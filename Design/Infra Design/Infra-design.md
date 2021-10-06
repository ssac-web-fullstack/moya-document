# Infra Design

개발 환경은 Docker

서버는 Oracle Cloud (moya.topician.com)

#

# **1. Docker**

## **이미지 다운로드(예시)**

[Docker Hub](https://hub.docker.com/r/tmddhks0104/moyadev)

```

docker pull tmddhks0104/moyadev

docker container run -it --name "moyadev" --hostname moyadev -v /Users/yu/workspace/sfd:/home/workspace -p 80:80 -p 7101:7101 -p 7201:7201 tmddhks0104/moyadev /bin/bash

```

## **세부 내역**

CentOS : 8.3.2011

Nginx : 1.14.1

node : 14.17.6

npm : 6.14.15

pm2 : 5.1.1

python3 : 3.6.8

epel-release, which, telnet, rsync, openssh-clients

m1 Mac 환경에서 volta가 설치가 불가능하여 node, npm은 따로 설치, pm2는 npm을 통해 설치하였습니다.

---

## **.bashrc**

- alias ll='ls -al'
- alias moya='ssh -i ~/.ssh/id_rsa_oci opc@moya'
- cd
- #nginx

alias moya를 이용하여 private key로 Oracle Cloud 에 접속하게 됩니다. 뒤쪽의 moya는 공인IP를 가리킵니다.

private key 파일은 따로 전달드리도록 하겠습니다.

접속 할 계정은 각자 변경하시면 됩니다.

(오라클 클라우드에서는 root 계정외에 기본적으로 opc라는 계정이 생깁니다.)

Docker 컨테이너 접속 시 바로 home 디렉토리로 이동하게 설정

Docker 컨테이너 접속 시 nginx를 자동으로 시작하게 하려면 주석만 해제해주시면 됩니다.

---

## **/etc/nginx/nginx.conf**

```bash

worker_processes 1;

```

nginx 구동시에 cpu 1장만 사용하도록 설정하였습니다.

## **/etc/nginx/conf.d/local.conf**

```bash

server {

listen 80;

server_name local.moya.com;

location / {

root   /home/workspace/www/moya;

index  index.html;

try_files $uri /index.html;

}

}

```

server_name은 본인 pc의 hosts에

```bash

127.0.0.1       local.mydeal.com

```

이런식으로 설정해주어야 접속이 가능합니다.

root 위치는 임의로 지정해두었습니다.

---

## **rsync**

오라클 클라우드의 /home/opc 폴더에 있는 check.txt 파일을 현재 docker의 폴더로 전송합니다.

```bash

rsync -avz -e 'ssh' moya:/home/opc/check.txt .

```

hosts 파일에

```bash

<공인 IP> moya

```

이런식으로 설정해두시면 공인 IP자리에 moya를 대신 사용할 수 있습니다

#

# **2. Oracle Cloud**

- Web Server (Nginx)
- DB Server (MySQL)

개인 로컬 pc에 alias를 걸어 놓으면 바로 접속이 가능합니다.

```

alias moya='ssh -i ~/.ssh/id_rsa_oci opc@moya'

alias moyadb='ssh -i ~/.ssh/id_rsa_oci opc@moyadb'

```

뒤쪽의 moya, moyadb는 공인 IP를 나타냅니다.

---

## **2.1 Web Server**

CentOS : 8.4.2105

Nginx : 1.14.1

volta : 1.0.5

node : 14.17.6

npm : 6.14.15

pm2 : 5.1.1

---

## HTTPS

- Let's Encrypt 설치하였습니다.
- 2개월마다 인증서 갱신을 위하여 crontab 설정하였습니다.

---

## **2.2 DB Server**

## MySQL

- db명 : moyadb
- 계정 : ysw, lkn, kms (각 계정 모두 moyadb에 모든 권한 설정했습니다.)

```
ALTER USER mydeal IDENTIFIED WITH mysql_native_password BY 'yyyy';
```

mydeal 자리에 계정의 이름, yyyy자리에 비밀번호를 넣으면 비밀번호 변경이 가능합니다.

```
set global max_connections=100;
```

최대 연결 수를 151(default)에서 100으로 설정하였습니다.

## mysqlsh

- 설치하였습니다.

#

# **3. System Architecture**

![System Architecture](./System%20Architecture/moya-system-architecture01.png)
