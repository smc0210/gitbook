---
description: dockerFile 직접 작성및 Image 파일 만들고 공유하는 프로세스
---

# Project Start

## Dockerfile 작성

각OS에 맞게 docker 설치후 진행

{% hint style="warning" %}
Boot2Docker는 deprecated 됬으니 docker for mac, docker for windows 설치후 docker toolbox 까지 설치를 진행한다
{% endhint %}

참고로 도커는 리눅스 컨테이너이므로 실제로 가상머신에 설치가 되는데, `docker for mac`은 `xhyve`라는 macOS에서 제공하는 가상환경을 사용하고 `docker for windows`는 `Hyper-V`라는 기술을 이용하는데 `Hyper-V`는 professional 이상부터 제공한다 \( Home버전은 안됨!\)

그래서 docker for..시리즈를 사용하지 못하는경우 \(최신 OS가 아닌경우\) Docker machine을 이용해서 사용 가능하기는하나... 문제가 문제가 문제가..문제가..문제가..환경이 환경이...compose는?? compose 없이 어떻게 빌드 compose... 옵션 너무 많아 못외움

`docker image`를 빌드하기 위한 Sample `Dockerfile` 작성

```bash
# 기존에 존재하는 이미지를 베이스로 작업 대게는 이미 official image들이 존재한다
FROM ubuntu:18.04

MAINTAINER Wisdom Shin <smc6711@gmail.com>

# 빌드 중엔 [y/n] 같은 사용자 입력을 받지 못하므로 보통 -y 옵션을 준다
# 그외 -qq 는 로그 보지 않기, 불필요한 문서를 생성하지 않는 --no-doc과 --no-ri 등이 있다
Run apt-get update -y \
    && apt-get install -y nginx \
    && echo "\ndaemon off;" >> /etc/nginx/nginx.conf \
    && chown -R www-data:www-data /var/lib/nginx

# 호스트와 공유할 디렉토리 목록
# docker run 을 할때 -v 옵션으로 맵핑 시켜줄 수 있다. 
# 예) docker run -v /host_directory/data:/data 
VOLUME ["/data", "/etc/nginx/site-enabled", "/var/log/nginx"]

# 아래 CMD 에서 설정한 실행파일이 실행될 경로
WORKDIR /etc/nginx

# 컨테이너가 시작되었을 때 실행할 실행 파일 또는 셸 스크립트
CMD ["nginx"]

# 호스트와 연결할 포트 번호
EXPOSE 80 443
```

## Build Image & run

`build` 명령어로 `Dockerfile`기반으로 도커 이미지를 생성한다, 
이때 하나의 이미지로 여러개의 컨테이너를 생성가능하다.
이미지 없이 `docker run {container}` 를 실행할 경우 해당 컨테이너가 존재한다면 docker hub에서 다운받아서 실행도 가능

```bash
# tag를 입력하지 않으면 태그는 자동으로 latest로 입력됨
docker build --tag hello:0.1 .

# 생성된 docker image 확인
docker images
```

생성한 도커이미지를 기반으로 실행

```bash
docker run --name hello-nginx -d -p 8000:80 -v /Users/wiz/Desktop/dev/docker/docker-1/root/data:/data hello:0.1
```

**docker run option 설명**

| Option | Description |
| :--- | :--- |
| `-d` | 백그라운드에서 실행 |
| `-p` | 호스트와 컨테이너의 포트 연결 `{Host Port}:{Container Port}` |
| `-v` | 호스트와 컨테이너의 볼륨 공유 \[마찬가지로 앞쪽이 호스트 path\] |
| `-e` | 컨테이너 내에서 사용할 환경변수 설정 |
| `--name` | 컨테이너 이름 설정 |
| `--rm` | 프로세스 종료시 컨테이너 자동 제거 |
| `-i` `-t` 줄여서  `-it` | 터미널 입력을 위한 옵션 |

**그외 옵션은** `docker run --help`**로 확인 가능**

> 이때 주의할점은 볼륨 공유는 아무곳이나 가능하지 않고 `docker sharing` 에 설정되어있는 경로만 가능하다 윈도우는 공유 드라이브를 설정하면 되고 Mac은 `preference` &gt; `File sharing` 에서 미리 정의되어있는 공유 패스를 확인 가능하고 추가도 가능하다.

위와 같이 실행했을 경우 `localhost:8000` 접속시 **Welcome to nginx!** 페이지가 정상적으로 떠야 한다.


## docker compose

운영환경에서는 볼륨공유도 필수적이고 그외 여러가지 옵션이 많다보니 단순히 CLI 환경으로 사용하기엔
무리가 있다.

이를 위해 복잡한 설정 관리를 위해 `YAML` 방식의 설정파일을 이용한 `docker compose`라는 툴이 있다.

> docker for mac, docker for windows 는 기본으로 같이 설치되지만 리눅스 환경에서는 별도로 설치가 필요하다

```bash
curl -L "https://github.com/docker/compose/releases/download/1.9.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# test
docker-compose version
```

아래 Sample `docker-compose.yml` 파일처럼 각종 설정등을 정의해주고 `docker-compose up`이라는 명령어만 실행하면 여러가지의 컨테이너와 그에 맞는 설정들로 컨테이너가 손쉬게 생성된다.

```yml
version: '3'

services:
	nginx:
      build:
        context: ./nginx
        args:
          - PHP_UPSTREAM_CONTAINER=php-fpm
          - PHP_UPSTREAM_PORT=9000
      volumes:
        - ../www:/var/www:cached
        - ./logs/nginx/:/var/log/nginx
        - ./nginx/sites/:/etc/nginx/sites-available
      ports:
        - "80:80"
        - "443:443"
      depends_on:
        - php-fpm
  php-fpm:
    build:
      ..생략..
```