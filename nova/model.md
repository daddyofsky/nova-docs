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
   
   use Nova\Model;
   
   class Post extends Model
   {
       protected string $table = 'posts';
       protected string $primaryKey = 'id';
       ...
   } 
   ```

## 데이터베이스 작업

### 조회

- 단일 데이터 조회 

  ```php
  $post = new Post(1);
  echo $post->title;
  echo $post['content'];
  ```

  이 코드는 id 값이 1인 게시글을 반환합니다.

 `참고` `Nova\Model` 객체는 `ArrayObject`를 상속받기 때문에 배열 또는 속성 두가지 방법으로 데이터를 조회 및 수정할 수 있습니다.

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

  ```php
  use App\Models\Post;

  $data = [
    'title' => '제목입니다.',
    'content' => '내용입니다.',
  ];
  Post::insert($data);
  ```
 
### 업데이트

  ```php
  use App\Models\Post;

  $data = [
    'title' => '제목이 수정되었습니다.',
  ];
  Post::update($data, 1);
  ```
  이 코드는 ID가 1인 게시물의 제목을 업데이트합니다.

### 삭제

  ```php
  Post::delete(1);
  ```
  이 코드는 ID가 1인 게시물을 삭제합니다.



`참고` 디비 쿼리에 관한 자세한 사항은 [DB](db.md) 참고


#### 관계


```php
class User extends Model
{
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}
```

이를 통해 사용자의 모든 게시물을 쉽게 조회할 수 있습니다:

```php
$user = User::find(1);
$posts = $user->posts;
```
