# 서비스

서비스는 애플리케이션에서 특정한 작업을 수행하거나 기능을 제공하는 객체를 가리킵니다.
서비스는 종종 비즈니스 로직을 처리하거나 외부 API와 상호작용하거나 데이터베이스에 액세스하는 데 사용됩니다.

## 서비스 생성

console 명령어를 사용하여 새로운 서비스를 생성할 수 있습니다.

```bash
php console make:service post
```

`참고 ` `make:service` 대신 `ms` 를 사용할 수 있습니다.

위 명령어를 실행하면 `App/Services/PostService.php` 파일이 생성됩니다.


### 서비스 사용

서비스를 사용하려면 해당 클래스의 인스턴스를 생성하고 메소드를 호출합니다.

```php
use App\Services\MyService;

$myService = new MyService();
$myService->doSomething();
```

컨트롤러에서는 파라미터 자동 매칭을 사용하여 서비스를 사용할 수 있습니다.

```php
use App\Services\MyService;

public function index(MyService $myService)
{
    $myService->doSomething();
}
```
