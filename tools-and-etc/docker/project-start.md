---
description: dockerFile 직접 작성및 Image 파일 만들고 공유하는 프로세스
---

# Project Start

## Dockerfile

각OS에 맞게 docker 설치후 진행

{% hint style="warning" %}
Boot2Docker는 deprecated 됬으니 docker for mac, docker for windows 설치후 docker toolbox 까지 설치를 진행한다
{% endhint %}

참고로 도커는 리눅스 컨테이너이므로 실제로 가상머신에 설치가 되는데, `docker for mac`은 `xhyve`라는 macOS에서 제공하는 가상환경을 사용하고 `docker for windows`는 `Hyper-V`라는 기술을 이용하는데 `Hyper-V`는 professional 이상부터 제공한다 \( Home버전은 안됨!\)

그래서 docker for..시리즈를 사용하지 못하는경우 \(최신 OS가 아닌경우\) Docker machine을 이용해서 사용 가능하기는하나... 문제가 문제가 문제가..문제가..문제가..환경이 환경이...compose는?? compose 나의 compose...

`docker image`를 빌드하기 위한 `Dockerfile` 작성

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

`Dockerfile`기반으로 도커 이미지를 생성

```bash
# tag를 입력하지 않으면 태그는 자동으로 latest로 입력됨
docker build --tag hello:0.1 .

# 생성된 docker image 확인
docker images
```

생성된 도커이미지 실행

```bash
docker run --name hello-nginx -d -p 8000:80 -v /Users/wiz/Desktop/dev/docker/docker-1/root/data:/data hello:0.1
```

**docker run option 설명**

1. `-d` : 백그라운드에서 실행
2. `-p` : 호스트와 컨테이너의 포트 연결 \({Host Port}:{Container Port}\)
3. `-v` : 호스트와 컨테이너의 볼륨 공유 \(마찬가지로 앞쪽이 호스트 path\)

| Option | Description |
| :--- | :--- |
| `-d` | 백그라운드에서 실 |
| `-p` | dd |
| `-v` | daa |
| `-e` | aad |
| `-name` | dss |
| `-rm` | dssss |
| `-i` `-t` 줄여서  `-it`  | d |
| `-link` | ds |

> 이때 주의할점은 볼륨 공유는 아무곳이나 가능하지 않고 `docker sharing` 에 설정되어있는 경로만 가능하다 윈도우는 공유 드라이브를 설정하면 되고 Mac은 `preference` &gt; `File sharing` 에서 미리 정의되어있는 공유 패스를 확인 가능하고 추가도 가능하다.

위와 같이 실행했을 경우 `localhost:8000` 접속시 **Welcome to nginx!** 페이지가 정상적으로 떠야 한다.

## docker command

### 컨테이너에서 호스트로 파일 복사하기

`docker cp {container name}:{container path} {host path}`

이때 컨테이너 이름은 이미지 이름이 아닌 `run` 명령어 실행시 `--name`옵션으로 준 이름을 말한다.

```bash
docker cp hello-nginx:/etc/nginx/nginx.conf ./
```

### 컨테이너 상세 정보 조회

`docker inspect {image or container name}`

```text
docker inspect hello-nginx
```

