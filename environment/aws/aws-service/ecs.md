---
description: 'ECS, ECR'
---

# ECS

## 1. ECS

### ECS란?


### ecs-cli

ECS login
```bash
$(aws ecr get-login --no-include-email --region ap-northeast-2)
```

ECS cluster 생성
```
ecs-cli up --capability-iam --size 1 --instance-type t2.micro --cluster-config test-cli --keypair dev-iamport --subnets subnet-0923dca1a7cae5faa --security-group sg-0401b6e17b08eb789 --vpc vpc-0fcf055cd82dff70f

ecs-cli compose up --cluster test-cli
```

PHP와 Laravel에서 요구하는 php extension 설치
```bash
# root말고 기본 사용자 ec2-usre로 진행 ( Amazon Linux 기본 사용자명이 ec2-user )
su -l ec2-user

sudo yum update -y
sudo amazon-linux-extras install -y php7.2
sudo yum install php-cli php-xml php-json php-mbstring php-process php-common php-fpm php-zip git -y
```

source clone
```bash
cd ${REMOTE_APP_CODE_HOST_PATH}
git clone source_code_repository
```


```bash
cd ~
sudo curl -sS https://getcomposer.org/installer | sudo php
sudo mv composer.phar /usr/local/bin/composer
sudo ln -s /usr/local/bin/composer /usr/bin/composer

sudo composer install
```

만약 log 권한 에러가 발생할 경우 호스트에서 아래와 같이 처리
```bash
sudo chown -R ec2-user:ec2-user storage/
sudo chown -R ec2-user:ec2-user bootstrap/cache
sudo chmod -R 777 storage bootstrap/cache
```

