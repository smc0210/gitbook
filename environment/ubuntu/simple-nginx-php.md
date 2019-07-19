---
description: 'Nginx, php-fpm, ...'
---

# Simple Nginx/php

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

# 디테일한 메모리 체크
vmstat -s

# CPU 코어수 체크
cat /proc/cpuinfo | grep processor | wc -l
cat /proc/cpuinfo | grep processor

# 자세한 CPU 제원 체크
cat /proc/cpuinfo
```

## 2. Basic Settings

```bash
#패키지  목록 업데이트
apt update

# 설치된 프로그램 최신버전 설치
apt upgrade

# 시스템 시간 설정 ( Asia - Seoul 순으로 선택)
dpkg-reconfigure tzdata
```

## 3. Package Repository

```bash
# 최신 버전 등록
vi /etc/apt/sources.list
```

아래 코드를 맨아래 추가

```bash
# Nginx
deb http://nginx.org/packages/mainline/ubuntu/ xenial nginx
deb-src http://nginx.org/packages/mainline/ubuntu/ xenial nginx
```

![snp1](../../.gitbook/assets/snp_1.png)

nginx 보안키 다운로드 및 적용

```bash
cd /root
wget http://nginx.org/keys/nginx_signing.key
apt-key add nginx_signing.key
rm nginx_signing.key
```

![snp](../../.gitbook/assets/snp_2.png)

PPA 개인패키지 저장소를 사용하여 php-7.X 버전 설치

{% hint style="info" %}
Ubuntu 제작자중 한명이며 Debian에서 일하고 있는 ondrej 저장소를 이용
{% endhint %}

```bash
apt-get install software-properties-common

add-apt-repository ppa:ondrej/php

# 추가된 보안키 목록 확인
apt-key list

# 추가된 저장소 갱신
apt update
```

## 4. Package Install

### Nginx

```text
apt install nginx

service nginx restart
```

여기까지 했을때 Public Ip 로 접근시 아래와 같은 화면이 떠야한다.

> 파일 위치는 /usr/share/nginx/html/index.html

![snp](../../.gitbook/assets/snp_3.png)

### PHP-FPM

```bash
# 원하는 버전 검색
apt search php

apt install php7.2-fpm
```

`PHP`와 `PHP-FPM` 버전 확인

```text
php -v

php-fpm7.2 -v
```

![snp](../../.gitbook/assets/snp_4.png)

일반적인 PHP 모듈들과 라라벨에서 필요로하는 모듈등 설치

```bash
apt install -y php7.2-cli \
php7.2-common \
php7.2-curl \
php7.2-json \
php7.2-xml \
php7.2-gd \
php7.2-mbstring \
php7.2-mysql \
php7.2-bcmath \
php7.2-dev openssl \
php7.2-zip
```

PHP Default timezone 설정

```bash
vi /etc/php/7.2/fpm/php.ini
vi /etc/php/7.2/cli/php.ini
```

두개의 파일 모두 `date.timezone`을 찾아서 주석해제후 아래와 같이 원하는 timezone 입력

![snp](../../.gitbook/assets/snp_5.png)

```text
service php7.2-fpm restart
```

### Nginx와 PHP-FPM설정

**Nginx 사용자 권한 변경**

```bash
vi /etc/nginx/nginx.conf
```

아래 이미지에서 2번째 라인 user값을 `www-data`로 변경한다

![snp](../../.gitbook/assets/snp_6.png)

```bash
service nginx restart
```

**Nginx에서 PHP 확장자를 FPM으로 요청하도록 설정**

```bash
vi /etc/nginx/conf.d/default.conf
```

> `default.conf` 파일을 아리 기본구문으로 변경 \(기존데이터는 백업\)

```markup
server {
    listen       80 default_server;
    server_name  localhost;
    root /var/www/public;


    location / {
        index  index.php index.html;
        try_files $uri $uri/ /index.php?$args;
    }

    # Allow Lets Encrypt Domain Validation Program
    location ^~ /.well-known/acme-challenge/ {
        allow all;
    }

    # Block dot file (.htaccess .htpasswd .svn .git .env and so on.)
    location ~ /\. {
        deny all;
    }

    # Block (log file, binary, certificate, shell script, sql dump file) access.
    location ~* \.(log|binary|pem|enc|crt|conf|cnf|sql|sh|key)$ {
        deny all;
    }

    # Block access
    location ~* (composer\.json|contributing\.md|license\.txt|readme\.rst|readme\.md|readme\.txt|copyright|artisan|gulpfile\.js|package\.json|phpunit\.xml)$ {
        deny all;
    }

    location ~ [^/]\.php(/|$) {
        fastcgi_split_path_info ^(.+?\.php)(/.*)$;
        if (!-f $document_root$fastcgi_script_name) {
            return 404;
        }

        fastcgi_pass unix:/run/php/php7.2-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }
}
```

**fastcgi\_params 변경**

```bash
vi /etc/nginx/fastcgi_params
```

> `fastcgi_params` 파일을 변경

```text
fastcgi_param   QUERY_STRING            $query_string;
fastcgi_param   REQUEST_METHOD          $request_method;
fastcgi_param   CONTENT_TYPE            $content_type;
fastcgi_param   CONTENT_LENGTH          $content_length;

fastcgi_param   SCRIPT_FILENAME         $document_root$fastcgi_script_name;
fastcgi_param   SCRIPT_NAME             $fastcgi_script_name;
fastcgi_param   PATH_INFO               $fastcgi_path_info;
fastcgi_param   PATH_TRANSLATED         $document_root$fastcgi_path_info;
fastcgi_param   REQUEST_URI             $request_uri;
fastcgi_param   DOCUMENT_URI            $document_uri;
fastcgi_param   DOCUMENT_ROOT           $document_root;
fastcgi_param   SERVER_PROTOCOL         $server_protocol;

fastcgi_param   GATEWAY_INTERFACE       CGI/1.1;
fastcgi_param   SERVER_SOFTWARE         nginx/$nginx_version;

fastcgi_param   REMOTE_ADDR             $remote_addr;
fastcgi_param   REMOTE_PORT             $remote_port;
fastcgi_param   SERVER_ADDR             $server_addr;
fastcgi_param   SERVER_PORT             $server_port;
fastcgi_param   SERVER_NAME             $server_name;

fastcgi_param   HTTPS                   $https;

# PHP only, required if PHP was built with --enable-force-cgi-redirect
fastcgi_param   REDIRECT_STATUS         200;
```

```bash
service nginx restart
```

여기까지 했을때 `phpinfo()`를 통해 FPM 적용여부를 확인할 수 있다.

![snp](../../.gitbook/assets/snp_7.png)

## 5. Laravel Settings

### install

```bash
# 1. composer 설치
curl -s http://getcomposer.org/installer | php && \
  echo "export PATH=${PATH}:/var/www/vendor/bin" >> ~/.zshrc && \
  mv composer.phar /usr/local/bin/composer

# 2. 라라벨 인스톨러 설치
composer global require laravel/installer

# 3. $PATH 에 추가
export PATH=~/.composer/vendor/bin:$PATH

# 4. laravel
laravel new test
```

> storage permission 관련 에러시 아래 실행

```bash
chown -R $USER:www-data storage
chown -R $USER:www-data bootstrap/cache
chmod -R 775 storage
chmod -R 775 bootstrap/cache
```

라라벨 인스톨러를 통해 설치할경우 `.env`파일이 생성되어 있고 `artisan key:generate` 명령어도 실행되어 있지만 다른 방법\(composer 설치등..\)으로 설치했을 경우 `.env`파일과 라라벨 키를 수동으로 생성해줘야 한다.

### DB connection

라라벨의 경우 `sqlite`, `mysql`, `pgsql`, `redis` 등의 드라이버를 지원하며 따라서, 서버에서 직접 DB에 붙는게 아니라면 서버에 `mysql-client`등의 데이터베이스 클라이언트를 설치할 필요는 없다.

.env 파일에 DB 접속정보를 입력하 migrate로 연결을 테스트해본다.

{% hint style="info" %}
공인아이피 통신트래픽은 과금 되니, 서버간 통신은 내부아이피로 진행한다.
{% endhint %}

```bash
artisan migrate
```

laravel 5.4버전 이후로 기본 데이터베이스 `character set`을 이모지를 지원하는 `utf8mb4`로 변경했는데 따라서 DB character set이 `utf8`형 일 경우 `Specified key was too long; max key length` 오류가 발생하며 `AppServiceProvider`에 기본 문자열 길이를 제한하여 해결한다.

![snp](../../.gitbook/assets/snp_8.png)

