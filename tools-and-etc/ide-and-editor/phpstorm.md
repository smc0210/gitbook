# PhpStorm

## TOC

* [phpstorm](phpstorm.md#phpstorm)
  * [TOC](phpstorm.md#toc)
  * [1. ide-helper](phpstorm.md#1-ide-helper)
  * [2. php-cs-fixer](phpstorm.md#2-php-cs-fixer)
    * [2-2. Usage](phpstorm.md#2-2-usage)
  * [3. xdebug](phpstorm.md#3-xdebug)
  * [4. phpunit](phpstorm.md#4-phpunit)
  * [5. PHPStorm Setting](phpstorm.md#5-phpstorm-setting)
    * [5-1. git bash inside phpstorm](phpstorm.md#5-1-git-bash-inside-phpstorm)

## 1. ide-helper

IntelliJ 계열 (PHPStorm 포함)에서 Laravel 문법에 대한 지원이 아직 완벽하지 않아서 Route의 컨트롤러를 찾지 못한다거나 Facade를 찾아가지 못하는 등의 불편한 사항들을 해결해주는 패키지 

[laravel-ide-helper Github repository](https://github.com/barryvdh/laravel-ide-helper)

### 1-1. install

```bash
# composer package 설치
composer require --dev barryvdh/laravel-ide-helper
```

`config/app.php` 파일의 `providers` 배열에 해당 클래스 추가
```bash
Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider::class
```
{% hint style="info" %}
만약 Laravel 5.5 이상을 사용할경우 패키지를 `Auto-Discovery` 하므로 위 과정 생략가능
{% endhint %}

```php
// 상기 방법처럼 수동으로 등록하거나 혹은 아래와 같은 방법으로 처리 가능
// app/Providers/AppServiceProvider.php 

public function register()
{
  if($this->app->environment() !== 'production') {
    $this->app->register(\Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider::class);
  }
}
```



{% hint style="info" %}
만약 checkout이나 소스 연동 문제로 `정의로 이동` 기능이 작동 안할경우 아래 커맨드로 갱신
{% endhint %}

```bash
php artisan ide-helper:generate
```

이때 생성되는 ide-helper 파일은 `.gitignore` 에 등록해서 공유제외

## 2. IDE Custom formatting (선택사항 - code sniffer 와 cs-fixer로 대체가능 )

IntelliJ Preferences > Editor > Code Style > PHP

우측 상단 Set from > predefined style > PSR1/PSR2 클릭

코드 파일에서 `option + command + l` 입력시 위에서 지정한 코드 포맷 형식으로 변환됨

IDE 기본 제공기능인만큼 기능이 부족해서 codesniffer 와 php-cs-fixer를 사용하기를 권장함


## 3. Code Sniffer & php-cs-fixer

![code sniffer pc](../../.gitbook/assets/phpstorm_1.png)

Code Sniffer 저장소 [squizlabs/php\_codesniffer](https://packagist.org/packages/squizlabs/php_codesniffer)

### 3.1 Code Sniffer install

```bash
composer global require "squizlabs/php_codesniffer=*"
```

Preferences > Editor > Inspections > PHP > Quality tools > PHP Code Sniffer validation 체크

### 3.2 Code Sniffer Configure

```bash
# phpcs 경로 복사
$ which phpcs
/Users/{username}/.composer/vendor/bin/phpcs
```

Preferences > Language & Frameworks > PHP > Quality tools > Code Sniffer configuration 클릭
팝업창이 뜨면 PHP Code Sniffer path 란에 위에서 복사한 경로 붙여넣기 후 오른쪽 Validate 체크

Preferences > Editor > Inspections > PHP > Quality tools > PHP Code Sniffer validation > 우측 설정창의 Coding standard PSR2로 설정되어 있는지 확인

### 3.3 Cs-Fixer install

php-cs-fixer 저장소 [friendsofphp/php-cs-fixer](https://packagist.org/packages/friendsofphp/php-cs-fixer)

```bash
composer global require friendsofphp/php-cs-fixer
```

```bash
# php-cs-fixer 경로 복사
$ which php-cs-fixer
/Users/{username}/.composer/vendor/bin/php-cs-fixer

# php-cs-fixer fixer -h 로 옵션 확인 가능
```
Preferences > Language & Frameworks > PHP > Quality tools > PHP CS Fixer configuration 클릭
팝업창이 뜨면 PHP CS Fixer path 란에 위에서 복사한 경로 붙여넣기 후 오른쪽 Validate 체크

### 3-4. Usage

inline 방식
```bash
# php-cs-fixer option Rule로 변경
fix --rules=@PSR2,@Symfony,no_unused_imports,indentation_type $FileDir$/$FileName$
```

{% hint style="info" %}
위와 같이 터미널에 입력하는 형식으로 사용가능하지만 편의상 IDE에 등록후 각종 Rule을 config file로 작성한후 단축키를 이용하여 사용하길 권장
{% endhint %}

config file 방식
```text
# config파일로 설정
fix --config=.php_cs $FileDir$/$FileName$
```

[.php_cs config file 다운로드](https://gist.github.com/smc0210/b107f968671012cb454ae01dbdd588d3)

## 4. xdebug

[PHPStorm - xdebug 셋팅](https://www.jetbrains.com/help/phpstorm/configuring-xdebug.html)

> php.ini

```markup
; Off -> On 으로 수정
implicit_flush = On


; 최하단에 하기 추가 (경로설정 주의)
[XDebug] 
;; Only Zend OR (!) XDebug
zend_extension="C:\laragon\bin\php\php-7.1.14-Win32-VC14-x64\ext\php_xdebug-2.6.0-7.1-vc14-x86_64.dll" xdebug.remote_enable=true
xdebug.remote_host=localhost 
xdebug.remote_port=8000
xdebug.remote_handler=dbgp 
xdebug.profiler_enable=1 
xdebug.profiler_output_dir="C:\laragon\tmp"
```

laragon 트레이 아이콘 우클릭 &gt; PHP &gt; Extension 에서 xdebug설정

> chrome 확장도구 설치

[xdebug 크롬확장도구](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc/related)

## 5. phpunit

[https://phpunit.de/getting-started/phpunit-7.html](https://phpunit.de/getting-started/phpunit-7.html)

## 6. PHPStorm Setting

### 6-1. git bash inside phpstorm

![](../../.gitbook/assets/22222.png)

**6-1-1.** `Settings` **&gt;** `Tools` **&gt;** `Terminal`

Shell path 란에 입력

```bash
# 32-bit version of Git
"C:\Program Files (x86)\Git\bin\sh.exe" -login -i

# 64-bit version of Git
"C:\Program Files\Git\bin\sh.exe" -login -i
```

> 그래야 bash\_profile 적용되서 환경변수및 기타 설정을 동일하게 쓸 수 있다.

**6-1-2.** `Settings` **&gt;** `Version Control` **&gt;** `Git`

```bash
C:\Program Files (x86)\Git\bin\git.exe

# or this if you're using the 64-bit version of Git:

C:\Program Files\Git\bin\git.exe
```

