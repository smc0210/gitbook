---
description: 명령어 위주의 간단한 소개 모음집
---

# Snippet

## 1. route

`project/routes/web.php`이 기본 라우트

### 1-1. basic

```php
Route::get('/', function () {
    return view('welcome');
});
```

`return view('welcome');` 의 **welcome** 은 `resources/views/welcome.blade.php`를 의미한다

```php
Route::get('/', function () {
    return view('errors.503');
});
```

`views` 하위 폴더의 블레이드 엔진은 `.` 또는 `/`로 구분한다.

### 1-2. data binding

// with\(\)를 이용한 전달방법

```php
Route::get('/', function () {
    return view('index')->with([
        'greeting' => 'Good morning ^^/',
        'name'     => 'Appkr'
    ]);
});

// view()의 두번째 인자로 전달하는 방법
Route::get('/', function () {
    return view('index', [
        'greeting' => 'Ola~',
        'name'     => 'Laravelians'
    ]);
});
```

### 1-3. Controller routing

일반적으로 라우트에서 직접 뷰를 호출하거나 데이터를 직접 넘기지 않고 먼저 컨트롤러를 호출하게 된다 코드이그나이터의 클래스명@메소드와 동일한 방식

```php
# indexController 의 index()를 호출
Route::get('/', 'IndexController@index');

#Name 부여 
Route::get('posts', [
    'as'    =>  'posts.index',
    'uses'  =>  'PostsController@index'
]);
```

{% hint style="info" %}
라우트에 이름을 부여할 경우 `route('posts.index');`와 같은 방식으로 view나 기타 다른곳에서도 편하게 재사용할 수 있다.
{% endhint %}

### 1-4. Resource Route

`RESTful` 원칙에 맞게 `@method`없이 컨트롤러에 연결시키는 방식

```php
# resource route의 경우 자동으로 name이 부여됨
Route::resource('posts', 'PostsController');

# 하위 resource route 생성 - 
Route::resource('posts.comments', 'PostCommentController');
```

{% hint style="success" %}
하위 리소스 라우트 생성 후 `php artisan route:list` 명령어로 뭐가 달라졌는지 확인해보자 설명보다는 한번만 보면 단번에 이해될듯
{% endhint %}

## 2. blade

`laravel`의 템플릿 엔진

### 2-1. `{{ }}`

`echo`와 동일

```php
<!-- <?= $hello; ?> -->
{{ $hello }}
```

### 2-2. `{{-- --}}`

HTML 주석으로 컴파일

```php
{{-- count(range(1, 10)) --}} <!-- count() 자체가 실행안됨. 즉, 아무것도 출력되지 않음 -->
<!-- {{  count(range(1, 10)) }} --> <!-- 주석 안에 10이 표시됨 -->
```

### 2-3. `@foreach`

```php
<ul>
  @foreach($items as $item)
    <li>{{ $item }}</li>
  @endforeach
</ul>
```

### 2-4. `@if`

```php
@if($itemCount = count($items))
  <p>There are {{ $itemCount }} items !</p>
@else
  <p>There is no item !</p>
@endif
```

### 2-5. `@forelse`

`@foreach`와 `@if` 의 결합

```php
@forelse($items as $item)
  <p>The item is {{ $item }}</p>
@empty
  <p>There is no item !</p>
@endforelse
```

### 2-6. `@yield`, `@extends`, `@section`, `@include`

헤더, 푸터 등 공통된 부분을 사용할때 사용하는 레이아웃

> layout.blade.php

```markup
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Laravel Toy1 Essential</title>
    @yield('style')
</head>
<body>
    @yield('content')

    @yield('script')

    @include('footer')
</body>
</html>
```

> content.blade.php

```markup
@extends('master')

@section('style')
    <style>
        body {background:#fefefe;}
    </style>
@stop

@section('content')
    Your content Here !!
@stop

@section('script')
    <script>
        console.log('Hello Blade!');
    </script>
@stop
```

> footer.blade.php

```markup
<footer>
    <p>This is footer</p>
</footer>
```

## 3. artisan

### 3-1. basic

```bash
# tinker 호출
php artisan tinker
```

### 3-2. make

```bash
# model 생성
php artisan make:model Post

# Controller 생성
php artisan make:controller IndexController

# Route 목록 확인
php artisan route:list

# resource controller 생성
php artisan make:controller PostsController --resource
```

### 3-3. DB migration

```bash
# migration 생성
php artisan make:migration create_posts_table

# migration
php artisan migrate

# rollback
php artisan migrate:rollback

# reset - 모든 마이그레이션을 롤백 한 후 데이터베이스 초기화
php artisan migrate:reset

# refresh - reset 실행 후 마이그레이션을 다시 시도
php artisan migrate:refresh 

# field 추가 
php artisan make:migration add_name_to_authors_table
```

## 4. Query

```bash
php artisan tinker

# select
DB::select('select * from posts');

# insert
DB::insert('insert into posts(title, body) values(?, ?)', ['Second Title', 'Second Body']);

#update 
DB::update('update posts set title="Modified Title" where id = ?', [2]);
```

## 5. Query builder

```bash
php artisan tinker

DB::table('posts')->get(); # SELECT * FROM posts

DB::table('posts')->first();

DB::table('posts')->find(2);

DB::table('posts')->where('id', '=', 1)->get(); 
DB::table('posts')->where('id', 1)->get();
DB::table('posts')->whereId(1)->get();

DB::table('posts')->select('title')->get();
```

## 6. Eloquent ORM

간단하게는 `JPA`, `Django ORM` 등과 같은 모델간에 관계를 맺어주는 구현체로 사용방법만 다르지 다루는 내용은 거의 비슷하고 `Query builder`의 메소드는 거의 사용할 수 있다.

`artisan`을 이용해서 모델을 만들고 확인해보면 모델이 `Eloquent`를 하위에 존재하는걸 볼 수 있다.

```php
// 생성된 model 상단선언부
use Illuminate\Database\Eloquent\Model;
```

{% hint style="info" %}
테이블 이름은 복수로 모델 이름은 단수로 한다. table : users model : user 만약 이 규칙과 다르다면 모델에 명시적으로 선언해줘야 한다. \(`protected $table = 'users';`\)
{% endhint %}

### 6-1. basic

```bash
# tinker 진입후
php artisan tinker

# 생성된 모델을 사용해서 조회
App\Author::get(); # == DB::table('authors')->get();

# instance 생성후 데이터베이스에 insert
$author = new App\Author;
$author->email = 'foo@bar.com';
$author->password = 'password';
$author->save(); # 메모리에만 존재하던 인스턴스를 데이터베이스에 저장한다.
```

### 6-2. basic config

`Eloquent ORM` 에는 정해져있는 몇가지 규칙들이 있는데 그중 하나가 `updated_at`과 `created_at`이며 \(Django 네이밍과 동일하니 이해가 빠를듯\) 만약 해당하는 컬럼이 없거나 이름이 다를경우 `model`에 명시적으로 표기해주거나 `timestaps`옵션을 꺼줘야 한다

> `project/app/User.php`

```php
class User extends Model
{
    public $timestamps = false;
}
```

### 6-3. create

`save()`메소드가 아닌 `create()` 메소드를 이용할때는 `$fillable` 속성을 지정해줘야 한다. 왜 그런지와 그에 대한 자세한 설명은 공식 문서 참조

* [laravel 공식문서](https://laravel.com/docs/5.6/eloquent)
* [laravel 한글번역](https://laravel.kr/docs/5.6/eloquent)

  위 문서에서 `Mass Assignment`로 검색

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = ['name'];
}
```
