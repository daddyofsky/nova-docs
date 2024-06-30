# Response

`Nova\Http\Response` 클래스를 사용하여 뷰(View), JSON 데이터, 리디렉션 등을 처리할 수 있습니다.


## 기본 응답 생성

가장 간단한 형태의 응답은 문자열을 반환하는 것입니다.

```php
use App\Nova\Http\Response;

public function index()
{
    return 'Hello, World!';
}
```

## 템플릿 사용하기

템플릿을 사용하는 경우는 `view()` 함수를 사용합니다.

```php
use App\Models\Post;
use App\Nova\Http\Response;

public function index()
{
    $posts = Post()::where('status', 1)
        ->paginate()
        ->iterate();        

    return view('post.list')
        ->with('posts', $posts);
}
```

`참고` 자세한 사항은 [모델](model.md) 과 [View](view.md) 항목을 참고.

## JSON 응답 생성

배열 데이터를 반환하는 경우 자동으로 JSON 으로 변경하여 클라이언트에 반환됩니다.

```php
public function index()
{
    return [
        'name' => '이름',
        'email' => 'test@example.com',
    ];
}
```

`불린(Boolean)` 데이터를 반환하는 경우 자동으로 다음과 같이 JSON 응답을 클라이언트에 반환됩니다.

```js
// true
{
    success: true,
    message: 'OK',    
}

// false
{
    success: false, 
    message: 'ERROR',
}
```

Ajax 요청 처리중 에러가 발생하는 경우 다음과 같은 JSON 응답을 클라이언트에 반환합니다.

```php
error('에러가 발생하였습니다.');
```

```js
{
    error: true,
    message: "에러가 발생하였습니다."
}
```
`참고` 다음과 같은 경우 Ajax 로 판단합니다. 

  - HTTP 헤더의 `X-Requested-With` 값이 `XmlHttpRequest` 인 경우 (jQuery ajax 사용시 자동으로 추가됨)
  - 요청 파라미터에 `ajax` 값이 참인 경우 (예: /board/comment?ajax=true)
  - Request::setAjax(true) 호출하여 강제로 설정한 경우

## 페이지 이동

- 파라미터를 포함하는 경우 `Default`

    ```php
    public function redirectToList()
    {
        // ex) /post/1?page=1 --> /post?page=1
        return redirect('/post');
    }
    ```

- 상대 경로를 사용하는 경우

    ```php
    public function redirectToList()
    {
        // ex) /post/1?page=1 --> /post?page=1
        return redirect('..');
    }
    ```

- 파라미터를 포함하지 않는 경우

    ```php
    public function redirectToList()
    {
        // ex) /post/1?page=1 --> /post
        return redirect('/post', false);
    }
    ```

- 메세지를 포함하는 경우

    ```php
    public function redirectToList()
    {
        // ex) /post/1?page=1 --> /post?page=1
        return redirect('..', '등록되었습니다.');
    }
    ```

## 주요 메서드

- `header($key, $value)`: 응답 헤더를 설정합니다.
- `status($status)`: 응답의 HTTP 상태 코드를 설정합니다.
- `cookie($name, $value, $minutes)`: 응답에 쿠키를 추가합니다.
- `with($key, $value)`: 응답에 1회성 세션 데이터를 포함합니다.
