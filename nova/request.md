# Request 사용하기

HTTP 요청(Request)은 `Nova\Http\Request` 클래스 또는 `Nova\Http\FormRequest` 를 통해 다룰 수 있습니다.
HTTP 요청은 클라이언트에서 서버로 전송된 모든 데이터를 포함하며, Nova의 `Request` 클래스를 사용하여 이러한 데이터에 액세스할 수 있습니다.

`참고` `Nova\Http\Request` 객체는 `ArrayObject`를 상속받기 때문에 배열 또는 속성 두가지 방법으로 데이터를 조회 및 수정할 수 있습니다.

## 사용 예시

```php
use Nova\Http\Request;

// 컨트롤러 메서드 내에서 요청 객체 생성
public function index(Request $request)
{
    // 요청에서 데이터 읽기
    $name = $request->name; // property 형식 사용
    $email = $request['email']; // 배열 key 형식 사용
    
    // 요청 메서드 가져오기
    $method = $request->method();
    
    // post 요청만 가져오기
    $allInputs = $request->post();

    ...
}
```

## request() 함수

Request 객체를 사용 편의를 위하여 `request()` 함수가 제공됩니다.

```php
// 요청에서 데이터 읽기
$name = request('name');

// 요청 싱글톤 가져오기
$request = request();
echo $request->name;
```

## FormRequest 생성

console 명령어를 사용하여 새로운 요청 클래스를 생성할 수 있습니다.

```bash
php console make:request post
```

`참고 ` `make:request` 대신 `mr` 를 사용할 수 있습니다.

위 명령어를 실행하면 `Nova\Http\FormRequest` 클래스를 상속한 `App/Requests/PostRequest.php` 파일이 생성됩니다.


## 주요 메소드

Nova의 `Nova\Http\Request` 클래스는 많은 유용한 메서드를 제공합니다. 몇 가지 주요 메서드는 다음과 같습니다.

- `method()`: 요청의 HTTP 메서드를 가져옵니다.
- `path()`: 요청의 경로를 가져옵니다.
- `get($key, $default = '')`: $_GET 요청 값만 가져옵니다.
- `post($key, $default = '')`: $_POST 요청 값을 가져옵니다.
- `request($key, $default = '')`: $_REQUEST 요청 값을 가져옵니다.
- `cookie($key, $default = '')`: $_COOKIE 요청 값을 가져옵니다.


## 요청 유효성 검사

유효성 검사는 `Nova\Http\FormRequest` 객체를 통하여 수행할 수 있습니다.

```php
use Nova\Http\FormRequest;

public function store(FormRequest $request)
{
    $data = $request->validate();

    // 유효성 검사를 통과한 경우, 처리 로직을 작성합니다.
}
```

유효성 검사를 통과하지 않은 경우 `Nova\Exceptions\ValidationException` 에러가 발생하며, 에러 핸들러에서 자동으로 에러처리를 하게 됩니다.
직접 에러처리를 하고자 하는 경우에는 `try-catch` 구문을 사용하여 처리하면 됩니다.

유효성 검사의 자세한 사항은 [Validation](validatation.md) 항목을 참고하시기 바랍니다.


## HTML Form 및 Javascript 연동

`sky.form.js` 와 연동하여 폼 데이터를 설정하거나 유효성 체크를 동일하게 수행할 수 있습니다.

`참고` `CSRF(Cross-Site Request Forgery)` 체크도 내부적으로 자동으로 처리됩니다.

```php
public function create(CreateRequest $request): View
{
    // default value
    $data = [
        'type' => UserType::NORMAL,
    ];

    return view('user.form')
        ->with($data)
        ->withForm($data, $request);
}

public function edit(User $user, EditRequest $request): View
{
    return view('user.form')
        ->with($user)
        ->withForm($user, $request);
}
```
