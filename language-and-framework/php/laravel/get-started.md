# Get Started

## 1. Development environment setting

### Mac 환경 \(권장\)

### 윈도우 환경 \(비추\)

로컬 개발환경 셋팅은 여러가지 방법이 있지만 대표적으로 다음과 같은 방법이 있다

* docker
* Homestead
* apm, bitnami, laragon 등의 이미 구성되어있는 패키지 설치 \(PHP+Apache+Mysql 등이 이미 구성되어있으며 대다수 윈도우 환경에도 설치 프로그램을 지원한다\)

각각 장단점이 있지만 대체적으로 상기 리스트의 위로 갈수록 설정이 복잡하지만 그만큼 운영과 거의 동일한 환경을 구축할 수 있고, 아래로 갈수록 설정은 간단하지만 그만큼 기능이 한정되고 딱 로컬개발환경 정도다

![php-laravel-getStarted-1](../../../.gitbook/assets/php_laravel_get-started-1.png)

[laragon - https://laragon.org/](https://laragon.org/)을 설치후 설치된 경로 내부의 php.exe를 찾아서 해당경로를 윈도우 Path 환경변수로 잡아준다

#### requirements

* php &gt; 5.5.9
* php Extensions
  * OpenSSL
  * PDO
  * Mbstring
  * Tokenizer

Laragon의 경우 extention활성화는 관리 메뉴에서 체크 형식으로 손쉽게 가능하다. 그게 아닌 docker나 로컬 서버를 구축한 경우 `bash` 창에서 명령어로 확인 가능하다.

```bash
# Git Bash$ php --version # PHP 5.5.30$ php -m # Git Bash 에서는 파이프 (`|`) 연산자가 먹지 않아 `grep` 명령을 쓸 수 없다.# Windows Command Prompt$ php --version # PHP 5.5.30$ php -m | findstr openssl$ php -m | findstr pdo$ php -m | findstr mbstring$ php -m | findstr tokenizer
```

#### laravel install

`Laravel Installer` 를 사용해서 설치하며 그전에 `Composer`가 설치되어 있지 않다면 설치한다.

[Composer Downdload URL\]\]](https://getcomposer.org/download/) 에서 윈도우즈용 을 다운받아 설치한다. 설치중에 PHP 경로를 각자 로컬에 설치된 php\(laragon일 경우 laragon 내부의 php 경로\)를 잡아준다.

```bash
# composer 명령어가 작동하지 않는다면 환경변수를 점검한다.$ composer --version# laravel installer를 global로 설치$ composer global require "laravel/installer"
```

{% hint style="info" %}
`Laravel` command를 어떤 경로에서든 사용할수 있도록 Path 환경변수에 설정한다. 예시\) `C:\Users\{User}\AppData\Roaming\Composer\vendor\bin`
{% endhint %}

```bash
$ laravel new ProjectName$ cd ProjectName$ php artisan --verison# laragon일 경우 별도로 서버를 켜줄 필요 없이 정상작동한다 .( 물론 웹서버가 켜져있는 상태에서)$ php artisan serve
```

이후 `localhost:8000`으로 접근시 laravel 화면이 보인다면 정상이다.

## 2. Larvel Global Setting

### .env 생성

.env 파일이 없을경우 생성

```bash
$ cp .env.example .env
```

### APP\_KEY

laravel 에서 사용하는 암호화 알고리즘의 키로 만약 설정되어 있지 않다면 아래 명령어로 키를 생성한다

```bash
php artisan key:generate
```

### DB Connection

실제 외부 DB 서버가 있다면 해당 연결정보를 작성하면 되고 local에 설치되어 있는 DB 정보를 적어도 된다

DB\_HOST=localhost DB\_DATABASE=testDB DB\_USERNAME=root DB\_PASSWORD=

{% hint style="info" %}
laragon을 설치했다면 mysql이 설치되어있을테고 port번호와\(다른데서 사용하는 포트와 충돌나지 않도록\) root 패스워드를 한번 셋팅해주면 해당 접속정보로 접근 가능하다
{% endhint %}

