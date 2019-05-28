---
description: docker comand snippet
---

# Command

## basic

```bash
# 실행중인 컨테이너 확인
docker ps

# 중지된 컨테이너 까지 확인
docker ps -a

docker stop ${container_id}

docker rm ${container_id}

# pull 은 최신 이미지를 다운받는다 
docker pull [OPTIONS] NAME[:TAG|@DIGEST]

# 이미지 확인
docker images

docker rmi ${image_id}

# path 미 입력시 동일한 경로의 Dockerfile로 이미지 생성
docker build [OPTIONS] PATH | URL | -

# 이미지로 컨테이서 실행 ( 이미지 없을경우 docker hub에서 다운받을지 물어보고 다운받아 실행)
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

## `docker cp` 컨테이너에서 호스트로 파일 복사하기

`docker cp {container name}:{container path} {host path}`

이때 컨테이너 이름은 이미지 이름이 아닌 `run` 명령어 실행시 `--name`옵션으로 준 이름을 말한다.

```bash
docker cp hello-nginx:/etc/nginx/nginx.conf ./
```

## `docker inspect` 컨테이너 상세 정보 조회

`docker inspect {image or container name}`

```text
docker inspect hello-nginx
```

## 한번에 중지된 컨테이너 일괄 삭제

컨테이너 혹은 이미지를 지우려면 보통 아래의 과정으로 진행한다

```bash
# container id 확인
docker ps -a

# 여러개의 컨테이너를 삭제할 수 있지만 갯수가 많다면????
docker rm ${container_id1} ${container_id2}

# image id 확인
docker images

docker rmi ${image_id1} ${image_id2}
```

컨테이너 아이디를 하나하나 확인 해서 지우기 귀찮을경우 아래 커맨드로 중지된 컨테이너를 한번에 지울수 있다.

```bash
docker rm -v $(docker ps -a -q -f status=exited)
```

## 컨테이너 로그 보기

일반적인 리눅스 명령어와 동일하게 `--tail`, `-f` 같은 옵션을 그대로 사용 가능

```bash
docker logs --tail 10 ${container_id}

docker logs -f ${container_id}
```

주의할 점은 이때 출력되는 로그는 표준 스트림의 `stdout`, `stderr` 을 수집해서 보여주는 형식이므로 프로그램의 로그 설정을 파일이 아닌 표준출력 형식으로 바꿔야 docker log로 볼 수 있다

그리고 로그파일은 `json` 형식으로 저장되므로 운영 환경일 경우 용량에 주의해야 하며
보통은 이대로 무식하게 사용하지 않고 aws의 cloudwatch 같은 다른 로그 서비스에 스트립을 전달해서 구현한다.

## 컨테이너 명령어 실행 (exec)

`docker exec [OPTIONS] CONTAINER COMMAND [ARG...]`

컨테이너 내부로 들어가서 작업을 해야하거나 혹은 컨테이너에 명령어를 실행시켜야 할 경우가 있는데 이를 위한 명령어가
이미 도커에 존재한다.
명령어 실행 외에 컨테이너 내부로 진입을 하는 방법은
일반적인 생각으로 `SSH`를 이용해 접속하려고 하는 경우도 있는데 이는,
**권장하는 방법은 아니**고 이미 docker에서 이를 위한 명령어를 제공하고 있으므로 **하지 말라는 짓은 하지 말자**

```bash
docker-compose exec workspace bash

docker exec -it ${workspace_container_id} bash

docker-compose exec --user=username workspace bash
```

보통은 workspace 에서 작업을 할테지만(그러라고 만든거니까..) 그 외의 컨테이너도 진입가능하다





