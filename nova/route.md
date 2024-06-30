# 라우팅

Nova 프레임워크에서 라우팅은 클라이언트 요청을 애플리케이션의 다양한 처리 로직에 연결하는 중요한 역할을 합니다. 이 섹션에서는 Nova 프레임워크의 라우팅 기능을 설정하고 사용하는 방법에 대해 설명합니다.

## 라우트 정의

라우트는 `routes` 디렉토리 내의 파일들에 정의됩니다. 가장 일반적으로 사용되는 파일은 `web.php`와 `api.php`입니다. `web.php`는 웹 인터페이스를 통한 요청을 처리하고, `api.php`는 API 요청을 처리합니다.

- **기본 라우트 예제**:
  ```php
  Route::get('/', function () {
      return 'Hello World!';
  });
  ```
  이 코드는 루트 URL (`/`)에 접속했을 때, "Welcome to Nova!"를 출력하도록 설정합니다.

- **컨트롤러로의 라우팅**:
  ```php
  use App\Controllers\PostController;
  
  Route::get('/posts', [PostController::class, 'index']);
  ```
  `/posts` 경로에 대한 GET 요청이 `PostController`의 `index` 메서드로 라우팅됩니다.

- **요청 METHOD**:
    - **get**: GET, HEAD 요청인 경우만 처리
    - **post**: POST 요청인 경우만 처리
    - **put**: PUT 요청인 경우만 처리
    - **patch**: PATCH 요청인 경우만 처리
    - **delete**: DELETE 요청인 경우만 처리
    - **options**: OPTIONS 요청인 경우만 처리
    - **any**: 요청 METHOD를 구분하지 않고 처리

  `주의 사항` 브라우저는 GET, POST 두 가지의 METHOD만 사용할 수 있습니다. 그 이외의 METHOD는 서브밋 폼에 _method 항목을 추가하여 METHOD를 지정해주어야만 합니다.
  ```html
  <form name="dataForm" method="POST">
  <input type="hidden" name="_method" value="PUT">
  ...
  </form>
  ```

## 라우트 파라미터

라우트는 동적 파라미터도 지원합니다. 예를 들어, 특정 게시물의 ID를 URL 경로에 포함시키는 경우, 라우트 파라미터를 사용하여 해당 값을 캡처할 수 있습니다.

- **라우트 파라미터 예제**:
  ```php
  Route::get('/posts/{id}', [PostController::class, 'view']);
  ```
  이 라우트는 `/posts/1`, `/posts/2` 등의 요청을 `PostController`의 `view` 메서드로 라우팅하며, `{id}`는 해당 게시물의 ID로 대체됩니다.


- **파라미터 필터링**:

  ```php
  Route::get('/posts/{id=\d+}', [PostController::class, 'view']);
  ```
  이 라우트는 정규표현식을 사용하여 id 값이 숫자인 경우로 제한하고 있습니다.


- **파라미터 직접 지정**

  필요한 파라미터 값이 URL에 포함되지 않은 경우도 있습니다. 이 경우 직접 파라미터 값을 지정해줄 수 있습니다.

  ```php
  Route::resource('/board/{code=qna}', PostController::class);
  Route::resource('/ask', PostController::class, ['code' => 'qna']);
  ```

## 라우트 그룹

- **prefix**: 공통의 접두어를 공유하는 라우트 집합에 대해 사용됩니다. 해당 그룹 내에서 매칭된 라우트를 찾지 못한 경우 404 에러를 출력합니다.

  ```php
  Route::prefix('/admin', function () {
      Route::get('/', [MainController::class, 'index']);
      Route::get('/setup', [SetupController::class, 'index']);
  })->middleware(AdminRegion::class);
  ```
  이 예제에서는 `/admin`와 `/admin/setup` 경로에 대한 요청이 `AdminRegion` 미들웨어를 거치도록 설정됩니다.

- **group**: 여러 라우트를 묶어 동일한 파라미터, 미들웨어 또는 요청값을 적용할 때 사용됩니다.

  ```php
  Route::group(function() {
     Route::resource('/mypage', MypageCrontroller::class);
     Route::resource('/user', [UserCrontroller::class, 'edit']);
  })->middleware(MemberOnly::class)->with('menu', 'mypage');
  ```

## 자동 라우트

- **resource**: 컨트롤러 클래스만 지정하면 자주 사용되는 요청을 자동으로 매칭해줄 수 있습니다.
  ```php
  Route::resource('/posts', PostController::class);
  ```
  이것은 다음과 코드와 같은 효과가 있습니다.
  ```php
  Route::prefix('/posts', static function(...$args) use ($class) {
      Route::get('/', [$class, 'index'], $args);
      Route::prefix('/{id_or_mode}', static function(...$args) use ($class) {
          if (!(int)($mode = end($args)) && ($mode = Str::camel($mode))
              && (method_exists($class, $mode) || method_exists($class, $mode . 'Act'))) {
              Route::get('/', [$class, $mode], $args);
              Route::post('/', [$class, $mode . 'Act'], $args);
              return;
          }

          Route::get('/', [$class, 'view'], $args);
          Route::prefix('/{mode}', static function(...$args) use ($class) {
              $mode = Str::camel(end($args));
              Route::get('/', [$class, $mode], $args);
              Route::post('/', [$class, $mode . 'Act'], $args);
          }, $args);
          Route::put('/', [$class, 'editAct'], $args);
          Route::delete('/', [$class, 'deleteAct'], $args);
      }, $args);
  }, $args);
  ```
- **legacy**: 요청에 따라 모드별로 연결하는 컨트롤러를 나누는 것만 다르고 나머지는 **`resource`** 동일합니다.
  ```php
  Route::legacy('/posts', PostController::class);
  ```

- **auto**:
  ```php
  Route::auto('App\Controllers\Admin');
  ```
  이 라우트는 `/{pkg}` 요청을 지정한 네임스페이스의 `{Pkg}Controller`에 매칭해줍니다. 예를 들어 `/user` 요청인 경우 `App\Controllers\Admin\UserController`에 매칭해줍니다.  
  매칭해주는 요청은 **`resource`** 와 동일합니다.

- **autoLegacy**: 요청에 따라 모드별로 연결하는 컨트롤러를 나누는 것만 다르고 나머지는 **`auto`**와 동일합니다.


## 별칭(alias)

URL이 서로 다르지만 동일한 컨트롤러를 호출하는 경우 사용합니다.

  ```php
  Route::get(['/', '/main', '/main/index'], [MainController::class, 'index']);
  ```
