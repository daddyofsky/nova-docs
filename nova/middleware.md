# 미들웨어

미들웨어는 요청과 응답 사이의 필터링 레이어로 작동하여, 요청을 처리하기 전이나 응답을 클라이언트에 보내기 전에 특정 로직을 실행할 수 있습니다. 
인증 검사, 로깅, CORS 설정 등과 같은 공통적인 작업을 처리하는 경우 미들웨어를 활욜 할 수 있습니다.

## 사용 예시

```php
namespace App\Middleware;

use App\Nova\Http\Request;

class AdminOnly
{
    public function boot(Request $request)
    {
        if (!auth('admin')) {
            error('권한이 없습니다.');
        }
    }
}
```


## 미들웨어 생성

console 명령어를 사용하여 새로운 미들웨어를 생성할 수 있습니다.

```bash
php console make:middleware auth
```
`참고 ` `make:middleware` 대신 `mw` 를 사용할 수 있습니다.

위 명령어를 실행하면 `App/Middleware/Auth.php` 파일이 생성됩니다.


## 미들웨어 작성 규칙

- 네임스페이스 : `App\Middleware`
- 클래스명, 파일명에 `Middleware` 접미사는 꼭 붙이지 않아도 됩니다.
- 디렉토리, 파일명은 [PSR-4](https://www.php-fig.org/psr/psr-4/)를 따른다.

### 주요 메소드

- **`boot`**: 컨트롤러 실행 전에 호출되는 메소드
- **`terminate`**: 컨트롤러 실행 이후에 호출되는 메소드


## 미들웨어 사용

미들웨어를 적용하기 위해, 라우트에 미들웨어를 등록해야 합니다.

```php
use App\Middleware\Admin;

Route::get('/admin', function () {
    // 관리자 페이지 로직
})->middleware(Admin::class);
```

### 미들웨어 그룹

Nova 프레임워크는 여러 미들웨어를 그룹화하여 한 번에 여러 라우트에 적용할 수 있는 기능을 제공합니다. 예를 들어, 웹 애플리케이션의 모든 HTTP 요청에 공통적으로 적용되어야 하는 미들웨어는 `web` 미들웨어 그룹에 포함될 수 있습니다.

```php
use 
Route::prefix('/admin')->group(function () {
    // 관리자 페이지 라우트
})->middleware(Admin::class);
```

위 예제는 `/admin` 으로 시작하는 모든 페이지에 `Admin` 미들웨어가 적용됩니다.


### 미들웨어 우선순위

Nova 프레임워크에서는 미들웨어의 실행 순서가 중요할 수 있습니다. 미들웨어의 우선순위는 라우트에 지정한 순서를 그대로 따릅니다.
