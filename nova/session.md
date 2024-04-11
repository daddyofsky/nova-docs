# 세션

## 세션 시작

세션은 `Nova\Http\Session` 객체를 사용하는 경우 자동으로 시작됩니다.

## 세션에 데이터 저장

```php
Session::set('key', 'value');
```

위의 예제에서는 'key'라는 이름으로 'value' 값을 세션에 저장합니다.

## 세션에서 데이터 가져오기

```php
$value = Session::get('key');
```

## 세션에서 데이터 삭제

```php
Session::remove('key');
```

## 플래시 데이터

플래시 데이터는 현재 요청과 다음 요청 사이에만 유효한 데이터입니다. 일반적으로 리디렉션 후에 메시지를 전달하는 데 사용됩니다.

```php
// 플래시 데이터 저장
Session::setFlash('status', 'Task was successful!');

// 플래시 데이터 조회
Session::flash('status');
```

`Response::with()` 메소드를 사용하는 경우 자동으로 플래시 데이터에 저장됩니다.

```php
return response()
    ->with('status', 'Task was successful!');

```
