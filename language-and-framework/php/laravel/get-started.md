# Get Started

## 1. environment setting

### hosts file modify

> windows 기준 경로
> C:\Windows\System32\drivers\etc


```xml
127.0.0.1  laravel.course.test
127.0.0.1  lumen.course.test
```

### 도커 이미지 로드
```bash
docker rm course

# 현재 로드된 이미지 리스트
dodcker images

# 이미지 리스트에서 삭제(갱신)할 이미지 hash값 복사
docker rmi {image hash}

docker load -i laravel.tar
```

### 도커 실행
```bash
//docker run -it --name course -p 8000:80 -p 3308:3306 -v /c/Users/course:/usr/local/apache2/htdocs -v C:\Users\MCShin\.ssh:/home/course/.ssh centos7:laravel /bin/bash
docker run -it --name course -p 8000:80 -p 3308:3306 -v D:\minda\course:/usr/local/apache2/htdocs -v C:\Users\MCShin\.ssh:/home/course/.ssh centos7:laravel /bin/bash
```


### container exec

```bash
# 컨테이너가 중지되었을 경우 실행
docker start course

# exit 시 container down
# 종료시 ctrl + p,q 로 종료
docker attach course

# (권장)
docker exec -it -u course course /bin/bash

```
### 계정 변경 및 laravel 생성

```bash
cd ~/apache2/htdocs

// 계정변경 
sudo su course
laravel new laravel_test
lumen new lumen_test
```

## 2. laravel basic command
> 라라벨 artisan(cli) 기본 커맨드


```bash
php artisan make:{command} {name}

#controller
php artisan make:controller UserController
php artisan make:controller ExceptionController
php artisan make:controller ValidateController

#middleware
php artisan make:middleware Login

# help 
php artisan help
```

> middleware 를 생성한 후 `/app/Http/Kernel.php`에 등록을 해줘야 한다.(*대소문자 주의*)

```php
protected $routeMiddleware = [
    /* ...기존 코드 생략 */
    'login' => \App\Http\Middleware\Login::class,
]
```
> laravel setting

```bash
composer install

# .env APP_KEY create command
php artisan key:generate
```

## 3. Route

> `routes/web.php` ,`routes/api.php`에 route 작성
> `method`, `uri`, `controller
> api.php 작성예시
```php
Route::put('/welcome', function () {
    return view('welcome');
});

Route::get('/registers/create', 'RegisterController@create');

Route::group(['prefix' => '/user', 'middleware' => 'login'], function () {
    // Route::get('/', 'UserController@index');
    // Route::get('/{id?}', 'UserController@index')->middleware('login');
    Route::get('/{id?}', 'UserController@index');
    Route::put('/', 'UserController@update');
    Route::delete('/', 'UserController@delete');
    Route::post('/', 'UserController@store');
});

Route::get('/validation', 'ValidationController@index');
```
## 4. Controller

> `Route`에서 연결된 `Controller` 작성예시


```php
class UserController extends Controller
{
    public function index(Request $request, int $id = null)
    {
        dd('hello world');
        // d('test');

        // dd(config('ip', 100));

        // $callTop = $this->callTop();

        // $callBottom = $this->callBottom();

        return $this->response([
            'result'  => 'ok',
            'message' => trans('resources.ok'),
        ]);
    }

    public function test(Request $request)
    {
        $users = User::all();
        foreach ($users as $user) {
            dump($user->toArray());
        }
    }

    private function callTop()
    {
        dump('callTop called');
    }

    private function callBottom()
    {
        dd('callBottom called');
    }
}
```

## 5. Model

> `app/` 하위 경로에 모델 생성


```bash
# User 모델 생성
php artisan make:model User
```

> `app/User` 작성예시

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public $table      = 'users';
    public $primaryKey = 'id';
    const CREATED_AT   = 'created_at';
    const UPDATED_AT   = 'updated_at';
    public $fillable = [
        'login_id',
        'password',
    ];
}

```
> `/database/migrations` 폴더 생성 후 `make:migration` 명령어 실행


```bash
# app/User.php 기반으로 마이그레이션 
php artisan make:migration create_users
php artisan migrate

# db(mysql)접속 후 확인
mysql -u homestead -psecret
```


---
### 번외. rlwrap

```bash
# Install rlwrap
$ cd /usr/local/src
$ wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
$ rpm -Uvh epel-release-6-8.noarch.rpm
$ yum install rlwrap
```
```bash
# 도커 진입 (attach 로 진입해야함)
docker start course
docker attach course

# vim 설정
cd
vi .bashrc
```

> alias 추가후 적용 (`source`)

```xml
alias php="rlwrap php"
```



---

## 6 . ORM

### 6-1. Select

> php artisan tinker 진입후

```bash
# ---------------
# 추가적인 메소드[get()] 호출 없이 바로 결과값 반환
# ---------------
# 모든레코드
App\User::all()
# 첫 레코드
App\User::first()
# pk = 1 찾기
App\User::find(1)

# ---------------

# get() 메소드까지 써줘야 결과 반환
App\User::where('login_id', 'minda')->get()
App\User::where('id', 1)->get()

App\User::orderBy('id', 'desc')->get()
App\User::orderBy('id', 'desc')->limit(1)->get()
App\User::orderBy('id', 'desc')->take(1)->get() //limit와 동일
App\User::orderBy('id', 'desc')->offset(1)->limit(1)->get()

#array 형식은 비권장 (AND)
App\User::where(['login_id' => 'minda', 'password' => 1])->get()
#권장 (AND)
App\User::where('login_id', 'minda')->where('password', 1)->get()

# (OR)
App\User::where('login_id', 'minda')->orWhere('password', 2)->get()

# select
App\User::where('login_id', 'minda')->select('password')->get()
App\User::where('login_id', 'minda')->select('login_id', 'password')->get()

# 콜렉션 all()이 아닌 바로 데이터 조회
App\User::where('login_id', 'minda')->select('login_id', 'password')->first()

# like (두번째 파라미터는 operaate)
App\User::where('login_id', 'like', 'minda%')->get()
```
{% hint style="info" %}
`UserController.php` 작성예시
Array 방식은 `toArray()`로 변환후 `array_get()`메소드를 추가적으로 해줘야 에러를 방지(잘못된 값 혹은 빈값)
Collection 방식은 기본값으로 빈 값과 잘못된 값은 처리를 해준다.
{% endhint %}

```php
// Array
$users = User::where('login_id', 'minda')
            ->select('login_id', 'password')
            ->get()
            ->toArray();
foreach ($users as $user) {    
    //dump($user['login_id'] ?? null);    
    dump(array_get($user, 'login_id2', 0));
}

// Collection
$users = User::where('login_id', 'minda')
            ->select('login_id', 'password')
            ->get();
foreach ($users as $user) {
    dump($user->login_id2, $user->password);
}
```

### 6-2. Update & delete

{% hint style="info" %}
debug bar
get()->toSql()
{% endhint %}

```bash
App\User::find(1)->update(['password' => '1111'])
App\User::find(1)->delete();
```


### 6-3. Accessor
> User `Model` `app/User.php` 

```php
public function getCreatedAtAttribute($value)
{
    //dd($value);
    return date('Y-m-d', strtotime($value));
}
```

> User `Controller` `app/Http/Controller/UserController.php` 

```php
$users = User::where('login_id', 'minda1')
            ->select('login_id', 'password', 'created_at')
            ->get();
foreach ($users as $user) {
    dump($user->created_at);
}
```

### 6-4. Mutator

> User 모델 `app/User.php` 

```php
public function setPasswordAttribute($value)
{
    $this->attributes['password'] = ucfirst($value);
}
```

> User `Controller` `app/Http/Controller/UserController.php` 

```php
User::find(1)
            ->update(['password' => 'hello']);
        dd(User::find(1));
```

### 6-5. Relationships

> Company 모델 `app/Company.php` 

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Company extends Model
{
    public $fillable = [
        'name'
    ];
}
```

> User `Model` `app/User.php` 


```php
public $fillable = [
    'company_id', //<-- company_id 추가
    'login_id',
    'password',
];

public function company()
{
    return $this->belongsTo(Company::class, 'company_id','id')
        ->select('id', 'name');
}
```

> User `Controller` `app/Http/Controller/UserController.php` 

```php
$users = User::with('company')
                    ->where('login_id', 'like', 'minda%')
                    ->select('id', 'login_id', 'password', 'company_id')
                    ->get();
foreach ($users as $user) {
    dd($user->company->id, $user->company->name );
}
```

> ORM 마저
> User.php 모델


```php
//public $timestamps = false;

public $casts = [
    'password' => 'integer',
];

// 컨트롤러에서 toArray()로 변환시 필요 
public $appends = [
    'hash',
];

// 생략...

public function getHashAttribute()
{
    //dd($value);
    return 'minda_'.$this->password;
}

```

> relations 


## 7. Code Review
