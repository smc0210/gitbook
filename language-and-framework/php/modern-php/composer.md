# Composer

## 1. Install

* php &gt; 5.3.2
* openssl extention 활성화

### 1-1. Linux/Mac OS

```bash
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin/

sudo ln -s /usr/local/bin/composer.phar /usr/local/bin/composer
```

### 1-2. Windows

설치프로그램을 다운받아 실행 - [설치경로 URL](https://getcomposer.org/Composer-Setup.exe%20)

```bash
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin/

sudo ln -s /usr/local/bin/composer.phar /usr/local/bin/composer
```

## 2. Get started

설치 및 환경변수\(경로\) 설정이 완료됬다면 명령어 없이 `composer`만 실행시 사용가능한 명령어가 표기된다.

{% code-tabs %}
{% code-tabs-item title="composer" %}
```text
$ composer
Cannot load Xdebug - it was already loaded
   ______
  / ____/___  ____ ___  ____  ____  ________  _____
 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 1.5.3 2017-11-30 18:29:43

Usage:
  command [options] [arguments]

Options:
  -h, --help                     Display this help message
  -q, --quiet                    Do not output any message
  -V, --version                  Display this application version
      --ansi                     Force ANSI output
      --no-ansi                  Disable ANSI output
  -n, --no-interaction           Do not ask any interactive question
      --profile                  Display timing and memory usage information
      --no-plugins               Whether to disable plugins.
  -d, --working-dir=WORKING-DIR  If specified, use the given directory as working directory.
  -v|vv|vvv, --verbose           Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  about           Shows the short information about Composer.
  archive         Creates an archive of this composer package.
  browse          Opens the package's repository URL or homepage in your browser.
  clear-cache     Clears composer's internal package cache.
  clearcache      Clears composer's internal package cache.
  config          Sets config options.
  create-project  Creates new project from a package into given directory.
  depends         Shows which packages cause the given package to be installed.
  diagnose        Diagnoses the system to identify common errors.
  dump-autoload   Dumps the autoloader.
  dumpautoload    Dumps the autoloader.
  exec            Executes a vendored binary/script.
  global          Allows running commands in the global composer dir ($COMPOSER_HOME).
  help            Displays help for a command
  home            Opens the package's repository URL or homepage in your browser.
  info            Shows information about packages.
  init            Creates a basic composer.json file in current directory.
  install         Installs the project dependencies from the composer.lock file if present, or falls back on the composer.json.
  licenses        Shows information about licenses of dependencies.
  list            Lists commands
  outdated        Shows a list of installed packages that have updates available, including their latest version.
  prohibits       Shows which packages prevent the given package from being installed.
  remove          Removes a package from the require or require-dev.
  require         Adds required packages to your composer.json and installs them.
  run-script      Runs the scripts defined in composer.json.
  search          Searches for packages.
  self-update     Updates composer.phar to the latest version.
  selfupdate      Updates composer.phar to the latest version.
  show            Shows information about packages.
  status          Shows a list of locally modified packages.
  suggests        Shows package suggestions.
  update          Upgrades your dependencies to the latest version according to composer.json, and updates the composer.lock file.
  upgrade         Upgrades your dependencies to the latest version according to composer.json, and updates the composer.lock file.
  validate        Validates a composer.json and composer.lock.
  why             Shows which packages cause the given package to be installed.
  why-not         Shows which packages prevent the given package from being installed.
```
{% endcode-tabs-item %}
{% endcode-tabs %}

프로젝트 설정은 `composer.json`파일에 기술하며 의존성 항목은 `require`항목으로 표시되고 개발단계에서만 필요한 패키지는 `require-dev` 항목으로 표시된다.

```javascript
{
    "name": "laravel/laravel",
    "description": "The Laravel Framework.",
    "keywords": ["framework", "laravel"],
    "license": "MIT",
    "type": "project",
    "require": {
        "php": "^7.1.3",
        "fideloper/proxy": "^4.0",
        "laravel/framework": "5.6.*",
        "laravel/tinker": "^1.0"
    },
    "require-dev": {
        "filp/whoops": "^2.0",
        "fzaninotto/faker": "^1.4",
        "mockery/mockery": "^1.0",
        "nunomaduro/collision": "^2.0",
        "phpunit/phpunit": "^7.0"
    }
    // ...생략
}
```

{% hint style="info" %}

패키지명에 버전이 아닌 브랜치를 적어줄 수도 있다.

* dev-master  : 릴리즈 되지 않은 master 브랜치의 마지막 버전을 가져옴
* dev-develop : 릴리즈 되지 않은 develop 브랜치의 마지막 버전을 가져옴

```javascript
{
    "require": {
        "lesstif/php-jira-rest-client": "dev-master"
    }
}
// or

{
    "require": {
        "lesstif/php-jira-rest-client": "dev-develop"
    }
}
```

## 3. CLI Command

{% code-tabs %}
{% code-tabs-item title="basic command" %}
```bash
# `composer.json`이 작성된 경로에서 패키지 설치
composer install

# `composer.json`이 변경되었을 경우 패키지 업데이트
composer update

# 현재 composer 설정 조회
composer config --list

# 전역 설정 조회
composer global config --list

# 설정
composer config repositories.foo vcs https://github.com/foo/bar

# 전역 설정
composer global config minimun-stability dev

# require 
# 버전 없이 쓸 때는 따옴표를 안쳐도 된다.
# 버전 앞에 ':' 대신 '='를 쓸 수도 있다.
composer require vendor/package
composer require "monolog/monolog": "1.13.*"

# 개발 의존성 require
composer require "monolog/monolog": "1.13.*" -dev

# remove
composer remove monolog/monolog monolog/monolog2

# search - 현재 프로젝트의 저장소를 검색
composer search monolog

# show - 사용가능한 패키지 목록 조회
composer show

# composer.json 리프레쉬
composer dump-autoload
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
composer로 설치한 라이브러리는 `autoload.php` 에 등록되므로 사용시 일일이 `require`구문을 사용하지 않고 다음 문장 하나만 적어주면 된다. `require 'bendor/autoload.php`
{% endhint %}

## 4. Composer 속도 향상

### 1. packagist 미러사이트 등록

```bash
# 국내 미러사이트 등록
composer config -g repos.packagist composer https://packagist.kr

# 원복
composer config -g --unset repositories.packagist
```


### 2. Prestissimo

패키지 다운로드를 병렬로 처리해주는 [prestissimo](https://github.com/hirak/prestissimo) 추가 패키지 설치 

공개된 벤치결과에 따르면 `create-project` 로 라라벨 설치시  288초에서 26초로 속도향상이 있다고 한다.

```bash
# install
composer global require hirak/prestissimo

# remove
composer global remove hirak/prestissimo
```