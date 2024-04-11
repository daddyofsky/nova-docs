# Validation

## 사용 예시

- App\Requests\PostRequest.php
    ```php
    <?php
    namespace App\Requests;
    
    use Nova\Http\FormRequest;
    
    class PostRequest extends FormRequest
    {
        public function rules(): array
        {
            return [
                'title'   => 'required|min:10|max:100',
                'name'    => 'required|max:20',
                'content' => 'required',
            ];
        }
    
        public function messages(): array
        {
            return [
                'title'   => 'LANG:제목을 입력해주십시오.',
                'name'    => 'LANG:이름을 입력해주십시오.',
                'content' => 'LANG:내용을 입력해주십시오.',
            ];
        }
    }
    ```

- App\Controllers\PostController.php
    ```php
    <?php
    namespace App\Controllers;
    
    use App\Models\Post;
    use App\Requests\PostRequest;
    
    class PostController
    {
        public function edit(Post $post, PostRequest $request)
        {
            return view('post.form')
                ->with($post)
                ->withForm($request); // <-- js 입력폼 체크 
        }
    
        public function editAct(Post $post, PostRequest $request)
        {
            $data = $request->validated();
            
            // ??? $post->save($data);
            $post->update($data, $post->id);
        }
    }
    ```

## Validation 규칙 지정 방법

  ```php
  public function rules()
  {
      return [
          'name' => 'required|max:20',
          'user_id' => 'required|unique:users',
          'status' => [
            'required',
            'in' => [0, 1]
          ]
      ];
  ]
  ```

- 문자열 또는 배열로 지정 가능 `주의` 하나의 항목에 문자열과 배열을 혼합하지 않는다
- 문자열인 경우
  - 규칙이 여러 개인 경우 `|`로 구분
  - 규칙과 인수는 `:`로 구분하고, 인수가 여러개인 경우 `,`로 구분


## Validation 규칙

### 주요 규칙

필드가 비어 있지 않은지 확인합니다.

```php
public function rules()
{
    return [
        'name' => 'required',
    ];
]
```

### unique

값이 특정 데이터베이스 테이블의 필드에 대해 고유한지 확인합니다.

```html
key = 'unique:테이블명,칼럼명'
```

칼럼명이 생략된 경우 요청 키값이 사용됩니다.


```php
return [
    'user_id' => 'required|unique:users',
];
```

### min 및 max

값의 최소 및 최대 길이를 확인합니다.

```php
return [
    'password' => 'required|string|min:6|max:20',
];
```

### match

두 필드 값이 일치하는지 확인합니다. 대개 비밀번호 확인에 사용됩니다.

```php
return [
    'password' => 'required|string|match=password2',
];
```

### before 와 after

특정 날짜 전후 여부를 확인합니다.

```php
return [
    'start_date' => 'required|date|before:end_date',
    'end_date' => 'required|date|after:start_date',
];
```

### 데이터 타입 체크

- **string** : 문자열
- **bool** : 불린형 - **1**/0, Y/N, YES/NO, TRUE/FALSE (대소문자 구분 안함)
- **int** : 정수
- **float** : 소수
- **numeric** : 숫자형
- **array** : 배열

### 범위 체크

- **size** : 숫자, 크기 또는 갯수나 길이를 셀 수 있는 경우의 최대값
- **min** : 최소값 또는 최소 길이
- **max** : 최대값 또는 최대 길이
- **between** : 범위 이내. 최소값 생략 가능
- **range** : between 과 동일
- **length** : 문자열 길이 범위, 최소값 생략 가능
- **byte** : Byte 범위, 최소값 생략 가능
- **check** : 체크박스 선택 갯수 범위, 최소값 생략 가능
- **select** : Select 선택 갯수 범위, 최소값 생략 가능
- **in** : 지정 항목 목록에 있는지 체크
- **before** : 값 또는 날짜가 지정 항목의 값보다 작은지 체크
- **after** : 값 또는 날짜가 지정 항목의 값보다 큰지 체크

### 데이터 규칙 체크

- **alpha** : 영문 알파벳
- **word** : 영문 알파벳, 숫자, _(언더바)
- **email** : 이메일
- **domain** : 도메인
- **url** : URL
- **ip** : IP
- **ipv4** : IP v4
- **ipv6** : IP v6
- **mac_address** : Mac Address
- **file** : 파일 업로드
- **jumin** : 주민 등록 번호
- **bizno** : 사업자 번호
- **phone** : 전화 번호
- **mobile** : 휴대폰 번호
- **regex** : 정규표현식에 맞는지 체크
- **not_regex** : 정규표현식에 맞지 않는지 체크

### 기타
- **match** : 지정 항목의 값과 같은지 체크
