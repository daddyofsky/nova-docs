# 공용 함수

## env()

```php
function env(string $key, mixed $default = null): mixed
```

`.env` 파일에 정의된 환경 설정 값을 가져오는 함수

## conf()

```php
function conf(string $key, mixed $default = null): mixed
```

`config/app.php` 파일에 정의된 설정 값을 가져오는 함수

## set_conf()

```php
function set_conf(string $key, mixed $value)
```

`conf()` 함수로 조회할 수 있는 설정 값을 일시적으로 변경하는 함수.
`config/app.php` 파일의 설정 값은 변경되지 않음.

## auth()

```php
function auth(string $key = '')
```

사용자 인증 정보를 가져오는 함수

```php
$user_level = auth('level');
```

## request()

```php
function request(string|array|true $key = true, $default = '')
```

지정한 키가 있는 경우 해당하는 요청 값, 없는 경우 Request 객체 인스턴스를 가져오는 함수

```php
// Request 객체 인스턴스를 가져오는 경우
$uri = request()->uri();

// 요청 값을 가져오는 경우
$code = request('code');
```

## response()

```php
function response($content = null, $status = 200, array $headers = []): Response
```

Response 객체 인스턴스를 가져오는 함수

```php
response()->cookie('name', $value);
```

## view()

```php
function view(string|array|null $tpl = null, ?string $layout = null): View
```

View 객체 인스턴스를 가져오는 함수

```php
view('main.index')->with('title', 'MAIN 페이지');
```

## route()

```php
function route(string|array|ArrayData|bool $path = '', string|array|ArrayData|true $params = true): string
```

현재 경로를 기준으로 라우팅 경로를 생성해주는 함수. 기본적으로 쿼리값을 함께 반환한다.

```php
// 경로를 지정하지 않으면 referer 값을 반환
$referer = route();

// 절대 경로 지정
$url_login = route('/login');

// 상대 경로 지정
// ex) /board/basic/1?page=1 --> /board/basic/1/edit?page=1
$url_create = route('edit');

// ex) /board/basic/1?page=1 --> /board/basic?page=1
$url_list = route('..');
```

## redirect()

```php
/**
 * @throws \Nova\Exceptions\RedirectException
 */
function redirect(string $url = '', string $message = '')
```

페이지 이동시 사용

## lang()

```php
function lang(): string
```

현재 표시 언어를 반환. 기본값은 `ko`

## _T()

```php
function _T(string $text, ...$args): string
```

표시 언어에 해당하는 텍스트를 반환. 텍스트에 포함된 변수값을 변환해주는 역할도 한다.

```php
$msg = _T('LANG:최대값은 {max} 입니다.', 3);
echo $msg; // ex) Max value is 3.
```

## blank()

```php
function blank($value): bool
```

입력값이 빈 문자열인지를 판별한다.

```php
blank(0); // true
blank(0.0); // true
blank(' '); // true
blank(true); // true
blank(false); // false
blank(null); // false
```

## debug()

```php
function debug(mixed $msg, string $label = '', mixed $extra = null): void
```

디버깅 정보를 출력한다.
`conf('app.debug')` 값이 `true`인 경우에만 디버깅 정보가 페이지 하단에 출력된다. 

## error()

```php
/**
 * @throws \Nova\Exceptions\ErrorException
 */
function error(mixed $message, string $label = ''): void
```

에러 메세지를 출력한다.
`$label` 값은 에러 메세지 레이어의 제목 영역에 표시된다. (사용 템플릿이 지원하는 경우에 한함)

```php
error('장바구니 페이지로 이동하시겠습니까?', '안내');
```

## dump()

```php
function dump(...$vars): void
```

디버깅 정보를 출력한다.
`env('APP_DEBUG)` 값이 `true`인 경우에만 해당 위치에 바로 디버깅 정보를 출력한다.
`env('APP_ENV')` 값이 `dev`인 경우에는 [Symfony VarDumper](https://symfony.com/doc/current/components/var_dumper.html)를 이용해서 출력한다.
그 이외의 경우에는 `var_dump` 함수를 사용해서 출력한다.

## dd()

```php
function dd(...$vars): void
```

`dump` 함수를 이용하여 디버깅 정보를 출력하고 실행을 종료한다.
