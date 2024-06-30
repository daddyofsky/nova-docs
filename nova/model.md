# 모델 사용하기

## 모델 생성

console 명령어를 사용하여 새로운 컨트롤러를 생성할 수 있습니다.

```bash
php console make:model front/post
```

`참고 ` `make:model` 대신 `mm` 를 사용할 수 있습니다.

위 명령어를 실행하면 `App/Models/Post.php` 파일이 생성됩니다.

## 예시

   ```php
   namespace App\Models;
   
   use App\Nova\Model;
   
   class Post extends Model
   {
       protected string $table = 'posts';
       protected string $primaryKey = 'id';
       ...
   } 
   ```

## 데이터베이스 작업

`참고` 디비 쿼리에 관한 자세한 사항은 [DB](db.md) 참고

### 조회

- 단일 데이터 조회 

  ```php
  $post = new Post(1);
  echo $post->title;
  echo $post['content'];
  ```

  이 코드는 id 값이 1인 게시글을 반환합니다.

 `참고` `get()` `gets()` `paginate()` 메소드를 통해 조회한 데이터는 `ArrayObject` 상속 객체를 반환하며, 배열 또는 속성 두가지 방법으로 내부 데이터를 조회 및 수정할 수 있습니다.

- 목록 데이터 조회

    ```php
    public function index(BoardSetup $setup, Request $request)
    {
        $board = Board::where('code', $setup->code)
            ->orderBy()
            ->page($request->page ?? 1)
            ->paginate(20)
            ->iterate();
    }
    ```
  
    `참고` `paginate()` 메소드를 사용한 경우 페이징 데이터를 내부적으로 함께 처리되며, 템플릿 등에서 `links()` 메소드를 호출하여 출력할 수 있습니다.  
    

### 입력

- `insert()` 사용
  ```php
  use App\Models\Post;

  $data = [
    'title' => '제목입니다.',
    'content' => '내용입니다.',
  ];
  Post::insert($data);
  ```

- `save()` 사용
  ```php
  use App\Models\Post;

  $post = new Post();
  $post->title = '제목입니다.';
  $post->content = '내용입니다.';
  
  $id = $post->save();
  ```
 
### 업데이트

- `update()` 사용

  ```php
  use App\Models\Post;

  $data = [
    'title' => '제목이 수정되었습니다.',
  ];
  Post::update($data, 1);
  ```

- `save()` 사용

  ```php
  use App\Models\Post;

  $post = new Post(1);
  $post->title = '제목이 수정되었습니다.';
  
  $post->save();
  ```

### 삭제

  ```php
  Post::delete(1);
  ```

## 관계

### 관계와 조인

- 관계는 검색 조건에 포함되지 않는 단순 연결 데이터를 조회할 때 사용합니다.
- 검색 조건에 포함되는 경우에는 `join()` 메소드를 사용합니다.

### 관계 관련 주요 메소드

- **belongsTo** : 1:1 또는 1:N 의 관계에서 자식 테이블이 부모 테이블의 데이터를 조회하는 경우 (예: 게시글의 `user_id` 칼럼 기준으로 `user` 테이블의 데이터 조회)
- **hasOne** : 1:1 관계에서 부모 테이블에서 자식 테이블의 데이터를 조회하는 경우
- **hasMany** : 1:N 관계에서 부모 테이블에서 자식 테이블의 데이터를 조회하는 경우 (예: 게시글의 댓글 데이터 조회)
- **withOne** : 1:1 관계 데이터를 조회하는 경우. (연결 칼럼 및 관계를 직접 지정하는 경우)
- **withMany** : 1:N 관계 데이터를 조회하는 경우. (연결 칼럼 및 관계를 직접 지정하는 경우)
- **withCount** : 1:N 관계 데이터의 갯수를 조회하는 경우. (연결 칼럼 및 관계를 직접 지정하는 경우)

`참고` 모델 클래스의 `getPrimaryKey()` `getForeignKey()` 메소드를 사용하여 연결 칼럼을 추정합니다.
예외적인 경우 `withOne()` `withMany()` 메소드를 사용하여 칼럼을 직접 지정하여 사용하시기 바랍니다. 

```php
class Post extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

이를 통해 사용자의 모든 게시물을 쉽게 조회할 수 있습니다:

```php
$post = Post::get(1)->user();
$title = $post->title;
$user = $post->user;
```

`주의` 관계 데이터 조회는 `get()` `gets()` `paginate()` 메소드를 통해 데이터를 조회한 이후에 사용 가능합니다.
