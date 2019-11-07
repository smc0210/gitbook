# Snippet

## 1. route

`project/routes/web.php`이 기본 라우트

### 1-1. basic

```php
Route::get('/', function () {    return view('welcome');});
```

`return view('welcome');` 의 **welcome** 은 `resources/views/welcome.blade.php`를 의미한다

```php
Route::get('/', function () {    return view('errors.503');});// {file?}은 값이 없어도 에러가 나지 않도록 처리Route::get('docs/{file?}', 'IndexController@index),
```

`views` 하위 폴더의 블레이드 엔진은 `.` 또는 `/`로 구분한다.

### 1-2. data binding

```php
// with\(\)를 이용한 전달방법Route::get('/', function () {    return view('index')->with([        'greeting' => 'Good morning ^^/',        'name'     => 'Appkr'    ]);});// view()의 두번째 인자로 전달하는 방법Route::get('/', function () {    return view('index', [        'greeting' => 'Ola~',        'name'     => 'Laravelians'    ]);});
```

### 1-3. Controller routing

일반적으로 라우트에서 직접 뷰를 호출하거나 데이터를 직접 넘기지 않고 먼저 컨트롤러를 호출하게 된다 코드이그나이터의 클래스명@메소드와 동일한 방식

```php
# indexController 의 index()를 호출Route::get('/', 'IndexController@index');#Name 부여 Route::get('posts', [    'as'    =>  'posts.index',    'uses'  =>  'PostsController@index']);
```

{% hint style="info" %}
라우트에 이름을 부여할 경우 `route('posts.index');`와 같은 방식으로 view나 기타 다른곳에서도 편하게 재사용할 수 있다.
{% endhint %}

### 1-4. Resource Route

`RESTful` 원칙에 맞게 `@method`없이 컨트롤러에 연결시키는 방식

```php
# resource route의 경우 자동으로 name이 부여됨Route::resource('posts', 'PostsController');# 하위 resource route 생성 - Route::resource('posts.comments', 'PostCommentController');
```

{% hint style="success" %}
하위 리소스 라우트 생성 후 `php artisan route:list` 명령어로 뭐가 달라졌는지 확인해보자 설명보다는 한번만 보면 단번에 이해될듯
{% endhint %}

### 1-5. Route에서 DB query 조회

```php
DB::listen(function ($event) {    var_dump($event->sql);});
```

## 2. blade

`laravel`의 템플릿 엔진

### 2-1. `{{ }}`

`echo`와 동일

```php
<!-- <?= $hello; ?> -->{{ $hello }}
```

### 2-2. `{{-- --}}`

HTML 주석으로 컴파일

```php
{{-- count(range(1, 10)) --}} <!-- count() 자체가 실행안됨. 즉, 아무것도 출력되지 않음 --><!-- {{  count(range(1, 10)) }} --> <!-- 주석 안에 10이 표시됨 -->
```

### 2-3. `@foreach`

```php
<ul>  @foreach($items as $item)    <li>{{ $item }}</li>  @endforeach</ul>
```

### 2-4. `@if`

```php
@if($itemCount = count($items))  <p>There are {{ $itemCount }} items !</p>@else  <p>There is no item !</p>@endif
```

### 2-5. `@forelse`

`@foreach`와 `@if` 의 결합

```php
@forelse($items as $item)  <p>The item is {{ $item }}</p>@empty  <p>There is no item !</p>@endforelse
```

### 2-6. `@yield`, `@extends`, `@section`, `@include`

헤더, 푸터 등 공통된 부분을 사용할때 사용하는 레이아웃

> layout.blade.php

```markup
<!DOCTYPE html><html lang="en"><head>    <meta charset="UTF-8">    <meta name="viewport" content="width=device-width, initial-scale=1.0">    <meta http-equiv="X-UA-Compatible" content="ie=edge">    <title>Laravel Toy1 Essential</title>    @yield('style')</head><body>    @yield('content')    @yield('script')    @include('footer')</body></html>
```

> content.blade.php

```markup
@extends('master')@section('style')    <style>        body {background:#fefefe;}    </style>@stop@section('content')    Your content Here !!@stop@section('script')    <script>        console.log('Hello Blade!');    </script>@stop
```

> footer.blade.php

```markup
<footer>    <p>This is footer</p></footer>
```

### 2-7. Pagination

> `Route` 예시 - 실제로는 당연히 Controller에 작성한다

```php
$posts = App\Post::with('user')->paginate(10);``> `view` 사용예제 - blade - 부트스트랩과 호환된다.```php@if($posts)<div class="text-center">    {!! $posts->render() !!}</div>@endif
```

## 3. artisan

### 3-1. basic

```bash
# tinker 호출php artisan tinker
```

### 3-2. make

```bash
# model 생성php artisan make:model Post# Controller 생성php artisan make:controller IndexController# Route 목록 확인php artisan route:list# resource controller 생성php artisan make:controller PostsController --resource
```

### 3-3. DB migration

```bash
# migration 생성php artisan make:migration create_posts_table# migrationphp artisan migrate# rollbackphp artisan migrate:rollback# reset - 모든 마이그레이션을 롤백 한 후 데이터베이스 초기화php artisan migrate:reset# refresh - reset 실행 후 마이그레이션을 다시 시도php artisan migrate:refresh # field 추가 php artisan make:migration add_name_to_authors_table
```

### 3-4. Seed

```bash
# Seeder 클래스 생성$ php artisan make:seed UsersTableSeeder$ php artisan make:seed PostsTableSeeder# Seeding$ php artisan db:seed
```

## 4. tinker \( 계속 추가 \)

### 4-. seed

```bash
# instance 생성>>>> factory('App\User')->make();>>>> factory('App\User', 2)->make(); # Instance 2개 생성
```

## 5. Query

```bash
php artisan tinker# selectDB::select('select * from posts');# insertDB::insert('insert into posts(title, body) values(?, ?)', ['Second Title', 'Second Body']);#update DB::update('update posts set title="Modified Title" where id = ?', [2]);
```

## 6. Query builder

```bash
php artisan tinkerDB::table('posts')->get(); # SELECT * FROM postsDB::table('posts')->first();DB::table('posts')->find(2);DB::table('posts')->where('id', '=', 1)->get(); DB::table('posts')->where('id', 1)->get();DB::table('posts')->whereId(1)->get();DB::table('posts')->select('title')->get();
```

## 7. Eloquent ORM

간단하게는 `JPA`, `Django ORM` 등과 같은 모델간에 관계를 맺어주는 구현체로 사용방법만 다르지 다루는 내용은 거의 비슷하고 `Query builder`의 메소드는 거의 사용할 수 있다.

`artisan`을 이용해서 모델을 만들고 확인해보면 모델이 `Eloquent`를 하위에 존재하는걸 볼 수 있다.

```php
// 생성된 model 상단선언부use Illuminate\Database\Eloquent\Model;
```

{% hint style="info" %}
테이블 이름은 복수로 모델 이름은 단수로 한다. table : users model : user 만약 이 규칙과 다르다면 모델에 명시적으로 선언해줘야 한다. \(`protected $table = 'users';`\)
{% endhint %}

### 7-1. basic

```bash
# tinker 진입후php artisan tinker# 생성된 모델을 사용해서 조회App\Author::get(); # == DB::table('authors')->get();# instance 생성후 데이터베이스에 insert$author = new App\Author;$author->email = 'foo@bar.com';$author->password = 'password';$author->save(); # 메모리에만 존재하던 인스턴스를 데이터베이스에 저장한다.
```

### 7-2. basic config

`Eloquent ORM` 에는 정해져있는 몇가지 규칙들이 있는데 그중 하나가 `updated_at`과 `created_at`이며 \(Django 네이밍과 동일하니 이해가 빠를듯\) 만약 해당하는 컬럼이 없거나 이름이 다를경우 `model`에 명시적으로 표기해주거나 `timestaps`옵션을 꺼줘야 한다

> `project/app/User.php`

```php
class User extends Model{    public $timestamps = false;}
```

### 7-3. create

`save()`메소드가 아닌 `create()` 메소드를 이용할때는 `$fillable` 속성을 지정해줘야 한다. 왜 그런지와 그에 대한 자세한 설명은 공식 문서 참조

* [laravel 공식문서](https://laravel.com/docs/5.6/eloquent)
* [laravel 한글번역](https://laravel.kr/docs/5.6/eloquent)

  위 문서에서 `Mass Assignment`로 검색

```php
<?phpnamespace App;use Illuminate\Database\Eloquent\Model;class User extends Model{    /**     * The attributes that are mass assignable.     *     * @var array     */    protected $fillable = ['name'];}
```

### 7-4. Eagar 로딩

N+1 쿼리 문제를 해결

```php
// route 내에서 with 키워드로 eagar loaing 사용Route::get('posts', function() {    // with 키워드로 사용    $posts = App\Post::with('user')->get();    return view('posts.index', compact('posts'));});
```

{% hint style="danger" %}
with\(string\|array $relations\) 메소드는 항상 엘로퀀트 모델 바로 뒤, 다른 메소드를 체인하기 전에 써야 한다. 메소드의 인자는 테이블 이름이 아니라, 모델 클래스에서 정의한 관계를 나타내는 메소드 이름이다.
{% endhint %}

```php
// 엘로퀀트를 먼저 사용하고 그 후에 관계가 필요할경우 lazy eagar 로딩을 load키워드로 사용할 수 있다.Route::get('posts', function() {    $posts = App\Post::get();    $posts->load('user');    return view('posts.index', compact('posts'));});
```

## 8. Seeding

라라벨 5부터 `Seeding`을 편하기 하기위한 `Factory`기능이 제공된다.

{% hint style="info" %}
`database/factories/UserFactory.php`가 미리 생성되어 있으니 참고
{% endhint %}

### 8-1. Factory

> `database/factories/UserFactory.php` - Factory 정의 예시

```php
$factory->define(App\User::class, function (Faker $faker) {    return [        'name' => $faker->name,        'email' => $faker->unique()->safeEmail,        'password' => '$2y$10$TKh8H1.PfQx37YgCzwiKb.KjNyWgaHb9cbcoQgdIVFlYg7B77UdFm', // secret        'remember_token' => str_random(10),    ];});$factory->define(App\Post::class, function (Faker $faker) {    return [        'title'     =>  $faker->sentence,        'body'      =>  $faker->paragraph,        'user_id'   =>  App\User::all()->random()->id    ];});
```

{% hint style="info" %}
`php 5.5`이상부터 문자열 `'App\Post'`이 아닌 `App\Post::class`형태로 클래스로 지정하여 IDE에서 참조할수있도록 사용할 수 있다.
{% endhint %}

### 8-2. Seeder Class

`artisan make:seed {SeederClassName}` 명령어로 Seeder 클래스를 생성 후 작성

> `database/seeds/UsersTableSeeder`

```php
class UsersTableSeeder extends Seeder {    public function run()     {        App\User::truncate();        factory('App\User', 10)->create();    }}
```

Seeder 클래스 작성후 마스터 Seeder 클래스인 `DatabaseSeeder.php`에 등록한다.

> `database/seeds/DatabaseSeeder.php`

```php
class DatabaseSeeder extends Seeder{    public function run()    {        // FK 제약 무시 - 외래키 연관된 테이블일경우 선언해줌        DB::statement('SET FOREIGN_KEY_CHECKS=0');        // 모든 모델에 대해 대량할당(MassAssignment)을 허용한다        Model::unguard();        $this->call(UsersTableSeeder::class);        $this->command->info('users table seeded');        $this->call(PostsTableSeeder::class);        $this->command->info('posts table seeded');        Model::reguard();                DB::statement('SET FOREIGN_KEY_CHECKS=1');    }}
```

{% hint style="warning" %}
`Model::unguard()`를 사용하려면 `Model`을 import 해줘야 한다
{% endhint %}

## 9. Validation

사용자 입력값 유효성 검사로 어지간한 validate 체크는 이미 라라벨에 구현되어있다.

### 9-1. Sample

> `route` 예시

```php
Route::post('posts', function(\Illuminate\Http\Request $request) {    $rule = [        'title' => ['required'],        'body' => ['required', 'min:10']    ];    $validator = Validator::make($request->all(), $rule);    if ($validator->fails()) {        return redirect('posts/create')->withErrors($validator)->withInput();    }    return 'Valid & proceed to next job ~';});Route::get('posts/create', function() {    return view('posts.create');});
```

{% hint style="info" %}
라라벨에서 이미 제공하는 다양한 룰은 관련 문서를 참조 [관련 공식문서 참조](https://laravel.com/docs/5.6/validation#available-validation-rules) [관련 공식문서 참조 - 번역](https://laravel.kr/docs/5.6/validation#available-validation-rules)
{% endhint %}

> `view`예시 - blade

```php
@extends('master')@section('content')  <h1>New Post</h1>  <hr/>  <form action="/posts" method="POST">    <input type="hidden" name="_token" value="{{ csrf_token() }}"/>    <!--  old() 메소드는 helper 함수로 이전 입력값이 Session에 없으면 공백을 반환하고 값이 있을경우에만 반환한다.-->    <div>      <label for="title">Title : </label>      <input type="text" name="title" id="title" value="{{ old('title') }}"/>      {!! $errors->first('title', '<span>:message</span>') !!}    </div>    <div>      <label for="body">Body : </label>      <textarea name="body" id="body" cols="30" rows="10">{{ old('body') }}</textarea>      {!! $errors->first('body', '<span>:message</span>') !!}    </div>    <div>      <button type="submit">Create New Post</button>    </div>  </form>@stop
```

{% hint style="info" %}
모든 view에 $erros변수가 존재하기 때문에 `if(isset($errors))`같은 소스는 필요없다.
{% endhint %}

