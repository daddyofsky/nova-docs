# 컨트롤러


## 사용 예시

```php
<?php
namespace App\Controllers\Board;

use App\Controllers\Controller;
use App\Models\Board;
use App\Models\BoardSetup;
use App\Requests\Board\ViewRequest;
use Nova\Http\Redirect;
use Nova\Http\Request;

class BoardController extends Controller
{
    public function index(BoardSetup $setup, Request $request): View
    {
        $board = Board::where('code', $setup->code)
            ->orderBy()
            ->paginate()
            ->iterate();
        
        return view('board.list')
            ->layout($setup->layout)
            ->with('setup', $setup)
            ->with('board', $board);        
    }

	public function createAct(BoardSetup $setup, CreateRequest $request): Redirect
	{
		$data = $request->validated();
		$data = $request->format($data, $setup);

		if (Board::insert($data)) {
		    return redirect('..', 'LANG:게시글이 등록되었습니다.');
		}
        error('LANG:게시글 등록에 실패하였습니다.');
	}
    ...
}
```

## 컨트롤러 생성

console 명령어를 사용하여 새로운 컨트롤러를 생성할 수 있습니다.

```bash
php console make:controller front/post
```

`참고 ` `make:controller` 대신 `mc` 를 사용할 수 있습니다.

위 명령어를 실행하면 `App/Controllers/Front/PostController.php` 파일이 생성됩니다.


## 컨트롤러 작성 규칙

- 네임스페이스 : `App\Controllers`
- 클래스명, 파일명에 `Controller` 접미사를 꼭 붙인다.
- 디렉토리, 파일명은 [PSR-4](https://www.php-fig.org/psr/psr-4/)를 따른다. 


### 주요 메소드

- 라우팅과 연동하여 다음과 같은 메소드명 규칙을 따르도록 한다.

  | 메소드명               | 하는 일    | Route        | 요청 METHOD |
  |--------------------|---------|--------------|-----------|
  | **index**          | 목록(인덱스) | /            | GET       |
  | **view**           | 보기      | /{id}        | GET       |
  | **create**         | 등록 폼    | /create      | GET       |
  | **createAct**      | 등록 처리   | /create      | POST      |
  | **edit**           | 수정 폼    | /{id}/edit   | GET       |
  | **editAct**        | 수정 처리   | /{id}/edit   | POST      |
  | **delete**         | 삭제 폼    | /{id}/delete | GET       |
  | **deleteAct**      | 삭제 처리   | /{id}/delete | POST      |


## 파라미터 자동 매칭

라우터와 매칭된 메소드의 파라미터를 분석하여 연결 가능한 모델 및 Request 데이터가 자동으로 할당됩니다.

- **`request`**: 이름이 `$request` 이거나 타입이 `Request` 또는 `FormRequest`를 상속받은 경우

  ```php
  use Nova\Request;
  ...
  
  public function index(Request $request)
  {
      $keyword = $request->keyword;
      ....  
  }
  ```

  ```php
  use App\Requests\CreateRequest;
  
  public function createAct(CreateRequest $request)
  {
     $data = $request->validated();
     ...
  }
  ```
  
  `참고` `request` 클래스는 위치에 영향을 받지 않습니다.
  `참고` `FormRequest` 클래스를 활용하는 방법은 [Request](request.md) 내용을 참고


- **`service`**: 파라미터의 타입이 `App\Services` 네임스페이스에 해당하는 경우

  `참고` `request` 클래스는 위치에 영향을 받지 않습니다.


- **`model`**: 파라미터의 타입이 `Model` 클래스를 상속받은 경우. 라우트에 지정한 파라미터 순서와 같아야 함

  `주의` `model` 클래스는 위치에 라우팅에 지정한 파라미터 순서와 같아야 합니다. (`request`, `service` 파라미터 제외)


  ```php
  Route::get('/post/{id}', [PostController::class, 'view']);
  ```

  ```php
  public function view(Post $post, Request $request)
  {
      $title = $post->title;
      ....  
  }
  ```
  위 예제는 `/posts/1` 요청의 `1` 아이디값을 사용하여 다음과 같은 코드를 미리 실행한 결과를 `$post` 변수를 할당한 것과 같습니다. 
