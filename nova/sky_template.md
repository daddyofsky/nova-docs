# SkyTemplate

## 템플릿이란?

- 프로그램과 디자인을 분리하여 동적데이타 처리를 위한 간결한 인터페이스를 제공합니다.

## 템플릿 태그

- 템플릿 태그는 템플릿 파일내에서 템플릿엔진이 해석할 위치를 표시합니다.  
  `{` `}` 를 사용합니다.

- 필요에 따라 `<!--{ }-->`, `<!--{ }`, `{ }-->` 처럼 HTML 주석과 함께 사용 가능합니다.
    - 템플릿 태그로 인하여 웹에디터나 브라우저에서 모양이 깨지는 것을 막기 위해 주로 사용합니다.
    - HTML 주석과 템플릿 태그 사이의 공백은 허용되지 않습니다.

- 공백, 줄바꿈
    - 탬플릿 시작태그 다음에는 공백을 사용할 수 없습니다.
    - 태그 내에 줄바꿈 문자를 사용할 수 없습니다.

- `{` `}` 를 그대로 출력하려는 경우
  - 템플릿 작성 규칙에 맞지 않는 경우는 그대로 출력됩니다.
  - `{\name}` 와 같이 사용하면 `{name}` 과 같이 그대로 출력됩니다.


## 주석

- 컴파일, 실행에서 제외되는 설명을 위한 것입니다.
  - `{*`, `*}` 또는 `<!--{*`, `*}-->` 감싸진 부분은 주석으로 컴파일에서 제외됩니다.
  - `{*` 와 `*}` 는 다른 템플릿 태그와 달리 `*` 과 `{`, `}` 사이에 공백이 허용되지 않습니다.
  - 중첩이 가능합니다.


## 템플릿 기호

| 기호 | 코드      | 의미                   | 사용법                  |
|:--:|---------|----------------------|----------------------|
| @  | LOOP    | 루프의 시작               | {LOOP 블럭_아이디}        |
| ?  | IF      | 조건문의 시작(if)          | {IF 표현식}             |
| :  | ELSE    | 루프 없음, else, else if | {ELSE} 또는 {ELSE 표현식} |
| /  | END     | 루프 또는 조건문의 끝         | {END}                |
| #  | INCLUDE | 템플릿 파일 include       | {INCLUDE "파일경로"}     |

- 템플릿 태그의 앞부분에 위치하여 특별한 동작을 하도록 미리 정의된 것입니다.
    - 하나의 동작에 기호 또는 코드를 사용할 수 있습니다.
    - 기호의 경우에 한하여 공백없이 사용 가능합니다.
    - 템플릿 기호는 대소문자를 구별하지 않습니다.

## 변수 출력

`참고` 변수는 내부적으로 `htmlspecialchars` 함수를 사용하여 `escape` 처리됩니다.
html 코드와 같이 `escape` 처리하지 말아야 하는 경우는 `{=content}`와 같이 `표현식`을 사용하여 출력하여야 합니다.

- 일반 변수
  ```html
  {name}
  {\Foo\Bar::staticVar}
  ```
- 상수
  ```html
  {c.SITE_NAME}
  {c.\Foo\Bar::STATUS_CODE}
  ```
- 배열
  ```html
  {data.name}
  ```
- 루프 변수
  ```html
  {@LIST}
    {.name}
  {/}
  ```

### 예제

- php
    ```php
    <?php
    public function index()
    {
        return view('index')
            ->with([
                'title' => '템플릿 변수',
                'content' => '<b>변수</b>에 대한 설명입니다.'
            ]);
    }
    ```

- html
  ```html
  <h1>{title}</h1>
  <p>
    {content}
  </p>
  ```

- output
  ```html
  <h1>템플릿 변수</h1>
  <p>
    &lt;b&gt;변수&lt;/b&gt;에 대한 설명입니다.
  </p>
  ```

## 표현식

| 기호  | 의미           | 사용법     |
|:---:|--------------|---------|
|  =  | 템플릿변수의 값을 출력 | {=name} |

### 예제

- html
  ```html
  <h1>{title}</h1>
  <p>
    {=content}
  </p>
  ```

- output
  ```html
  <h1>템플릿 변수</h1>
  <p>
    <b>변수</b>에 대한 설명입니다.
  </p>
  ```

## 함수 호출

- 변수 출력에서 함수 호출
  ```html
  <!-- 첫번째 인수가 해당 변수인 경우 -->
  {point|number_format}
  {name|substr=0,20}

  <!-- 첫번째 인수가 아닌 경우 : 인수 위치에 ## 을 사용 -->
  {today|date="Y.m.d",##}

  <!-- 여러 함수 호출 -->
  {user_id|strtolower|substr=0,20}
  ```

- 표현식에서 함수 호출
  ```html
  {=number_format(point)}

  {=date("Y.m.d",today)}

  {=substr(strtolower(user_id),0,20)}
  ```

`참고사항` 표현식에서도 변수 출력시 함수 호출 방법을 사용할 수 있습니다.

`주의사항` 변수 출력에서 함수 호출시 인수가 많거나 복잡한 경우 오류 가능성이 있습니다. 이 경우는 표현식을 사용하시기 바랍니다.  


## 루프

| 기호 | 약어   | 의미                   | 사용법           |
|:--:|------|----------------------|---------------|
| @  | LOOP | 루프의 시작               | {@ loop_id}   |
| :  | ELSE | 루프 데이터가 없을 때 출력 (옵션) | {:}           |
| /  | END  | 루프의 끝                | {/}           |

- 데이터 지정
    - 데이터의 지정은 `assign()`를 사용하거나 템플릿파일에 직접 명시할 수 있습니다.
- 변수
    - `{.name}`와 같이 변수명 앞에 `.`를 붙입니다.
    - 예약어
      - `_index` : 0부터 시작하는 루프의 index 값
      - `_number` : 1부터 시작하는 루프의 number 값
      - `_key` : 현재 루프의 키값
      - `_value` : 현재 루프의 데이터


### 기본 루프 예제

- php
    ```php
    <?php
    public function index()
    {
        $data = [];
        for ($i = 1; $i <= 3; $i++) {
            $data[] = [
                'name' => '홍길동',
                'title' => $i . ' 번째 글입니다',
                'hit' => $i * 5,
            ];
        }
    
        return view('index')
            ->with('title', '기본 루프 예제')
            ->with('LIST', $data);
    }
    ```

- html
  ```html
  <h1>{title}</h1>
  <table>
      <tr>
          <th>제목</th>
          <th>글쓴이</th>
          <th>조회수</th>
      </tr>
      {@LIST}
      <tr>
          <td>{.title}</td>
          <td>{.name}</td>
          <td>{.hit}</td>
      </tr>
      {:}
      <tr>
          <td colspan='3'>데이터가 없습니다.</td>
      </tr>
      {/}
  </table>
  ```

- output
  ```html
  <h1>기본 루프 예제</h1>
  <table>
      <tr>
          <th>제목</th>
          <th>글쓴이</th>
          <th>조회수</th>
      </tr>
      <tr>
          <td>1 번째 글입니다</td>
          <td>홍길동</td>
          <td>5</td>
      </tr>
      <tr>
          <td>2 번째 글입니다</td>
          <td>홍길동</td>
          <td>10</td>
      </tr>
      <tr>
          <td>3 번째 글입니다</td>
          <td>홍길동</td>
          <td>15</td>
      </tr>
  </table>
  ```

### 중첩 루프 예제

- php
    ```php
    <?php
    public function index()
    {
        $data = [];
        for ($i = 1; $i <= 9; $i++) {
            $data[$i] = [
                'dan' => $i . ' 단',
                'LIST2' => [],
            ];
                
            for ($j = 1; $j <= 9; $j++) {
                $data[$i]['LIST2'][] = [
                    'i' => $i,
                    'j' => $j,
                ];
            }
        }
    
        return view('index')
            ->with('title', '중첩 루프 예제')
            ->with('LIST', $data);
    }
    ```

- html
  ```html
  <h1>{title}</h1>
  
  <table>
      {@LIST}
      <tr>
          <td>
              {.dan}
              <table>
                  {@LIST2}
                  <tr>
                      <td>{.i} * {.j} = {=.i*.j}</td>
                  </tr>
                  {/}
              </table>
          </td>
      </tr>
      {/}
  </table>
  ```

- output
  ```html
  <h1>중첩 루프 예제</h1>
  
  <table>
      <tr>
          <td>
              1 단
              <table>
                  <tr>
                      <td>1 * 1 = 1</td>
                  </tr>
                  <tr>
                      <td>1 * 2 = 2</td>
                  </tr>
                
                  ...
                
                  <tr>
                      <td>1 * 8 = 8</td>
                  </tr>
                  <tr>
                      <td>1 * 9 = 9</td>
                  </tr>
              </table>
          </td>
      </tr>
      <tr>
          <td>
              2 단
              <table>
                  <tr>
                      <td>2 * 1 = 2</td>
                  </tr>
                  <tr>
                      <td>2 * 2 = 4</td>
                  </tr>
  
                ...
  
                <tr>
                      <td>2 * 8 = 16</td>
                  </tr>
                  <tr>
                      <td>2 * 9 = 18</td>
                  </tr>
              </table>
          </td>
      </tr>
  
      ...
  
  </table>
  ```

### 예약어 예제

- php
    ```php
    <?php
    public function index()
    {
        $current = 'blue';
  
        $data = [
            'red' => '빨강',
            'orange' => '주황',
            'yellow' => '노랑',
            'green' => '초록',
            'blue' => '파랑',
            'darkblue' => '남색',
            'purple' => '보라',
        ];
    
        return view('index')
            ->with('title', '예약어 예제')
            ->with('current', $current);
            ->with('COLOR', $data);
    }
    ```

- html

  ```html
  <h1>{title}</h1>
  
  {@COLOR}
  <label>{_number}. <input type="checkbox" name="color" value="{_key}" {?_key==current}checked{/}> {_value}</label>
  {/}
  ```

- output
  ```html
  <h1>예약어 예제</h1>
  
  <label>1. <input type="checkbox" name="color2" value="red" > 빨강</label>
  <label>2. <input type="checkbox" name="color2" value="orange" > 주황</label>
  <label>3. <input type="checkbox" name="color2" value="yellow" > 노랑</label>
  <label>4. <input type="checkbox" name="color2" value="green" > 초록</label>
  <label>5. <input type="checkbox" name="color2" value="blue" checked> 파랑</label>
  <label>6. <input type="checkbox" name="color2" value="darkblue" > 남색</label>
  <label>7. <input type="checkbox" name="color2" value="purple" > 보라</label>
  ```

## 분기 - IF

| 기호  | 약어   | 의미                   | 사용법                  |
|:---:|------|----------------------|----------------------|
|  ?  | IF   | 조건문의 시작(if)          | {IF 표현식}             |
|  :  | ELSE | 루프 없음, else, else if | {ELSE} 또는 {ELSE 표현식} |
|  /  | END  | 루프 또는 조건문의 끝         | {END}                |

`참고사항` 문맥에 의해, `:` 은 분기의 `else` 로, `/` 은 루프나 분기의 종결문으로 해석됩니다.

### 분기 예제

- php
    ```php
    <?php
    public function index()
    {
        return view('index')
            ->with([
                'level' => 1,
                'name' => '홍길동',
            ]);
    }
    ```

- html
  ```html
  <h1>{title}</h1>
  
  {?level>0}
  <div><b>{name}</b>님 환영합니다.</div>
  {:}
  <div><a href="/login">로그인</a></div>
  {/}
  ```

- output
  ```html
  <h1>분기 예제</h1>

  <div><b>홍길동</b>님 환영합니다.</div>
  ```

## 템플릿 파일 인클루드

| 기호  | 약어      | 의미          | 사용법              |
|:---:|---------|-------------|------------------|
|  #  | INCLUDE | 템플릿 파일 인클루드 | {INCLUDE "파일경로"} |

### 예제

- test.html
  ```html
  아래 부분은 test_include.html 템플릿 파일을 include 한 것입니다.
  <hr />
  {#"test_include.html"}
  ```
- test_include.html
  ```html
  test_include.html 파일입니다.
  ```

- output
  ```html
    아래 부분은 test_include.html 템플릿 파일을 include 한 것입니다.
    <hr />
    test_include.html 파일입니다.
  ```
