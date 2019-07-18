---
description: Docker 컨테이너 구성을 위한 OS 셋팅
---

# For Docker

- Ubuntu 16.04 LTS 버전 AMI 사용


## 1. Basic setting

몇몇 패키지의 경우 초기화 설정을 선택하는 항목이 뜨는 경우가 있는데 기존 버전 유지로 선택해서 진행
```bash
apt update

apt upgrade
```

사용자 추가
```bash
# 사용자 추가
adduser user1

# 사용자 로그인
su -l user1

# 사용자 로그아웃
exit
```

aws cli 설치

```bash
# aws cli 설치를 위해 pip3 설치 ( 16.04에 기본적으로 phthon3가 설치되어 있음)
apt install python3-pip

su -l user1

pip3 install awscli --upgrade --user

# iam user 정보를 입력해준다.
aws congigure
```

이때 사용할 IAM에 ECR 권한이 필요하니 AWS managemnet console 에서 미리 설정이 필요하다
- AmazonEC2ContainerRegistryFullAccess
- AmazonEC2ContainerServiceFullAccess
- AmazonEC2ContainerServiceforEC2Role

NPM 설치
```bash
apt install npm
```


## 2. docker install

도커 설치는 자동 설치 스크립트를 사용해서 설치하는것이 가장 간편하다.
```bash
curl -fsSL https://get.docker.com/ | sudo sh

docker version
```

도커는 기본적으로 `root`권한이 필요하니 사용자를 docker 그룹에 추가해준다
```bash
sudo usermod -aG docker ${username}
```

{% hint style="warning" %}
도커를 실행하기 위한 최소 kernel 버전은 3.10.x 으로 Ubuntu 14.04 버전 이상만 사용하면 된다
{% endhint %}

docker compose 설치
```bash
curl -L "https://github.com/docker/compose/releases/download/1.9.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

docker-compose version
```

## 3. image pull

도커를 설치만 해도 **Docker hub** 에서 이미지를 `pull` 하는건 가능하지만 개인저장소에서 이미지를 가져오는건 인증과정이 필요하다
여기서는 aws ecr을 사용한다는 가정하에 진행


```bash
# docker login ( ecr 권한이 설정되어 있어야 함)
$(aws ecr get-login --no-include-email --region ap-northeast-2)

# 레지스트리에 있는 저장소 목록 조회
aws ecr describe-repositories

docker pull ${repository_url}:${tag}
```


```bash
ecs-cli up --capability-iam --size 1 --instance-type t2.micro --cluster-config test-cli --keypair dev-iamport --subnets subnet-0923dca1a7cae5faa --security-group sg-0401b6e17b08eb789 --vpc vpc-0fcf055cd82dff70f
```