---
description: Ubuntu 16.04 LTS 버전 기준 (2018.08)
---

# Get Started

`Ubuntu`에 가장 기본적인 개발환경셋팅하기

테스트 환경및 구성할 내용은 아래 리스트와 같다.

* AWS LightSail 
  * 512MB RAM, 1vCPU, 20GB SSD
  * 서울리전
  * ap-northeast-2a
* Ubuntu 16.04 LTS
* apache 2.4
* PHP 7.X
* MariaDB

## 1. OS & basic info check

```bash
# 리눅스 버전 체크
uname -a

# Ubuntu 버전 체크
cat /etc/issue

# 더 자세한 Ubuntu 버전 체크
lsb_release -a

# 하드용량체크 
df -h

# 메모리 체크
free -m

# CPU 코어수 체크
cat /proc/cpuinfo | grep processor | wc -l
cat /proc/cpuinfo | grep processor

# 자세한 CPU 제원 체크
cat /proc/cpuinfo
```

## 2. Basic Settings

{% hint style="info" %}
모든 명령어는 `root`권한이라고 가정하고 `sudo` 명령어는 따로 기술하지 않음
{% endhint %}

```bash
# root권한으로 변경
sudo su

# 사용자 확인 - 콘솔상에서도 명시적으로 확인할수 있다
whoaimi

# 패키지 목록 갱신
apt-get update

# 설치되어있는 프로그램 최신버전으로 설치
apt-get upgrade

# 시스템 시간 설정
# 입력시 GUI 환경이 나올면 Asia->Seoul 순으로 선택해주면 된다.
dpkg-reconfigure tzdata

# hostname 설정 - lightsail 상의 ip로 입력되어 있다. 
# 보통은 해당 서버에 연결할 도메인명으로 설정
vi /etc/hostname

# 변경한 hostname 적용 - 재접속해야 적용됨
hostname -F /etc/hostname
```

## 3. Apache2

### 3-1. 설치

```bash
# apache2 설치
apt-get install apache2

# version Check - 적성일 기준  Apache/2.4.18 설치
apache2 -v
```

이 시점에서 도메인을 입력했을때 apache2 기본화면이 나와야 하며, 그 경로는 `var/www/html/index.html` 이다.

{% hint style="warning" %}
물론 서버셋팅과는 별도로 lightsail 혹은 그외의 서비스에서 DNS 셋팅과 도메인연결을 해야 한다.
{% endhint %}

### 3-2. 모듈 활성/비활성화

```bash
a2enmod rewrite
a2enmod headers
a2enmod ssl
a2dismod -f autoindex

# ...추가예정
```

### 3-3. Web access 되면 안될 파일들 접근제어

```bash
# apache 설정파일 열기
vi /etc/apache2/apache2.conf
```

중간에 `<FilesMatch “^\.ht”>` 구문 다음에 아래 코드를 추가한다.

```markup
# deny file, folder start with dot
<DirectoryMatch "^\.|\/\.">
    Require all denied
</DirectoryMatch>

# deny (log file, binary, certificate, shell script, sql dump file) access.
<FilesMatch "\.(?i:log|binary|pem|enc|crt|conf|cnf|sql|sh|key|yml|lock|gitignore)$">
    Require all denied
</FilesMatch>

# deny access.
<FilesMatch "(?i:composer\.json|contributing\.md|license\.txt|readme\.rst|readme\.md|readme\.txt|copyright|artisan|gulpfile\.js|package\.json|phpunit\.xml|access_log|error_log|gruntfile\.js|config)$">
    Require all denied
</FilesMatch>

# Allow Lets Encrypt Domain Validation Program
<DirectoryMatch "\.well-known/acme-challenge/">
    Require all granted
</DirectoryMatch>
```

## 4. PHP

```bash
# php 설치 - 작성일 기준 PHP 7.0.30 버전 설치됨
apt-get install php

# 필요한 모듈들 설치
apt-get install {모듈명}
```

