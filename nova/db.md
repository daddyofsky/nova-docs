# DB

`Nova\DB` 클래스 사용법을 정리한 문서입니다.  
디비 작업은 `Nova\DB` 클래스를 직접 사용하기 보다는 테이블별로 구분하여 `Nova\DB` 클래스의 모든 기능을 사용할 수 있는 `Nova\Model` 클래스를 사용하는 것을 추천합니다.

### 목차

- [WHERE 조건](#where)
    - [사용 가능한 연산자](#where_operator)
    - [= vs ==](#where_strict)
    - [IN, NOT IN](#where_in)
    - [LIKE](#where_like)
    - [BETWEEN](#where_between)
    - [SQL 직접 사용](#where_sql)
    - [? 사용](#where_question)
    - [orWhere() 사용](#where_or_whre)
    - [조건의 Lifecycle](#where_lifecycle)
- [SELECT](#select)
    - [JOIN](#select_join)
    - [WHERE 조건](#select_where)
    - [COLUMN](#select_column)
    - [ORDER](#select_order)
    - [GROUP](#select_group)
    - [LIMIT](#select_limit)
    - [갯수 조회](#select_findcount)
    - [여러 레코드 조회](#select_findall)
    - [하나의 레코드 조회](#select_find)
    - [하나의 칼럼 조회](#select_findcolumn)
    - [콜백(callback) 메소드 사용](#select_callback)
    - [QUERY (PDOStatement 직접 사용)](#select_query)
- [INSERT](#insert)
    - [단순 입력](#insert_simple)
    - [ON DUPLICATE KEY UPDATE 구문](#insert_update)
    - [Bulk Data](#insert_bulk)
- [UPDATE](#update)
    - [단순 수정](#update_simple)
    - [숫자 증감](#update_count)
- [DELETE](#delete)
- [RAW SQL 실행](#rawsql)
- [서브 쿼리](#subquery)
- [트랜잭션](#transaction)


<a id="where"></a>
## WHERE 조건

`where()` `orWhere()` 메소드를 사용하거나, `find()` 등의 메소드에 인수로 직접 전달한다.

```php
// where
public function where(string|array|null $key, mixed $type = '', mixed $value = ''): static

// orWhere
public function orWhere(string|array ...$wheres): static
```

- WHERE 조건을 지정하는 경우 `칼럼명`, `연산자`, `데이터`를 각각 분리하여 인수로 전달한다.
- **데이터는 자동으로 `escape` 처리된다.**
- 여러 조건을 전달하는 경우 배열을 사용한다.

`참고사항` 여러 조건들은 기본적으로 `AND`로 묶인다. (`OR`를 사용하는 방법은 [? 사용](#where_question) 또는 [orWhere() 사용](#where_or_whre) 부분을 참고)  
`주의사항` _기존에 문자열로 조건문을 조합하여 전달하는 방법은 사용을 하지 않도록 한다. 이 경우 escape 처리를 별도로 해주어야 하고,
문자열을 조합하는 과정에서 코드가 번잡해지는 경우가 많기 때문이다._

```php
// 기존 방법 (사용 금지)
$db->where("user_id = '" . $user_id . "'");

// 칼럼명, 연산자, 데이터를 분리하여 인수로 전달
$db->where('user_id', '=', $user_id);
$db->where('user_id', $user_id); // = 의 경우 생략 가능

// 배열 사용 (한 번에 여러 조건을 등록)
$db->where(['code', $code], ['flag', 1]);
$db->where([
    ['code', $code],
    ['flag', 1],
]);
// - -> code = '$code' AND flag = 1
```

<a id="where_operator"></a>
### 사용 가능한 연산자
`==` `!==`
`=` `EQ` `!=` `NE`
`IN` `NOT IN`
`LIKE` `NOT LIKE`
`BETWEEN`
`<` `LT` `<=` `LTE` `>` `GT` `>=` `GTE`
`&` `|` `^`

<a id="where_strict"></a>
### = vs ==
- `=`의 경우 데이터가 `null` 또는 `빈 문자열`인 경우 해당 조건을 무시한다. 검색어가 있는 경우에만 추가되는 where 구문등에 사용한다.
- `==`는 데이터와 상관 없이 무조건 추가된다. `null`이나 `빈 문자열` 인지 체크하는 경우 사용한다.
- `=`는 생략 가능하다.

```php
$db->where('status', 0); // - -> status = 0
$db->where('keyword', $keyword); // - -> keyword = '$keyword' (키워드가 있는 경우에만 추가됨) 
$db->where('user_id', '==', null); // - -> user_id IS NULL 
$db->where('user_id', '=', null); // 무시됨
```

<a id="where_in"></a>
### IN, NOT IN
- 문자열이 아닌 배열 데이터를 직접 지정하여 사용한다.
- `주의사항` _,(콤마) 및 따옴표를 사용하여 문자열로 조합하여 전달하지 않도록 한다._

```php
// IN (생략 가능), NOT IN
$db->where('id', [1, 2, 3]); // - -> id IN (1, 2, 3)
$db->where('id', 'IN', [1, 2, 3]);
$db->where('code', 'NOT IN', ['notice', 'qna']);
```

<a id="where_like"></a>
### LIKE, NOT LIKE

```php
// LIKE, NOT LIKE
$db->where('title', 'LIKE', $ss); // - -> title LIKE '%$ss%' ($ss 값이 없는 경우 무시)
$db->where('title', 'NOT LIKE', $ss . '%'); // - -> title LIKE '$ss%'
$db->where('cate', 'LIKE', $cate_code . '__'); // - -> title LIKE '$cate_code__'
```
`참고사항` 데이터가 없는 경우 해당 where 구문은 무시된다.

<a id="where_between"></a>
### BETWEEN, >, <, >=, <=

```php
// BETWEEN
$db->where('created_at', 'BETWEEN', $start_date, $end_date); // - -> created_at BETWEEN '$start_date' AND '$end_date'
$db->where('date', 'BETWEEN', '2024- 01- 01', ''); // - -> date >= '2024- 01- 01' (데이터가 없는 부분은 무시됨)

// >, <, >=, <=
$db->where('created_at', '>=', $start_date, '<=', $end_date); // - -> created_at >= '$start_date' AND created_at <= '$end_date'
$db->where('buy_status', '<=', 0, '>', 10); // - -> buy_status <= 0 OR buy_status > 10
$db->where('date', '>=', '', '<=', '2024- 01- 01'); // - -> date <= '2024- 01- 01' (데이터가 없는 부분은 무시됨)
```

`참고사항` 데이터가 없는 부분은 무시된다.

<a id="where_sql"></a>
### SQL 직접 사용
- SQL 구문을 직접 사용하는 경우 raw: 텍스트로 시작한다. (소문자로만 입력)

`주의사항` 불가피한 경우를 제외하고 사용하지 않도록 한다.

```php
// raw where
$db->where('buy_status & 16');
$db->where('LEFT(created_at, 10)', 'raw:LEFT(CURRENT_DATE, 10)'); // 값에 query를 직접 쓰는 경우는 raw: 를 붙여야 함
$db->where('LEFT(created_at, 10)', $today);
```

<a id="where_question"></a>
### ? 사용
- `OR`, `괄호` 등이 포함된 복잡한 조건을 지정할 때 사용한다.
- 조건문과 데이터는 분리해서 전달하고, ? 갯수와 데이터의 갯수가 같아야만 한다.
- `참고사항` 이 경우도 배열 데이터를 직접 사용할 수 있다.

```php
// raw where + ?
$db->where('(title LIKE ? OR content LIKE ?)', $keyword, $keyword);
$db->where('id IN (?)', [1, 2, 3]);
```

<a id="where_or_whre"></a>
### orWhere() 사용

- `OR`, `괄호` 등이 포함된 복잡한 조건을 지정할 때 사용한다.
- 각 인수에 전달한 조건들은 `(조건 OR 조건)` 형태로 묶인다.
- 하나의 인수에 여러개의 조건을 배열로 전달한 경우는 `(조건 AND 조건)` 형태로 묶인다. `OR`와 `AND`가 혼용된 경우에 사용한다.

```php
// (title LIKE '%keyword%' OR content LIKE '%keyword%')
$db->orWhere(['title', 'LIKE', $keyword], ['content', 'LIKE', $keyword]);

// ((sub_index = '' AND depth = 0) OR title LIKE '%keyword%' OR content LIKE '%keyword%')
$db->orWhere(
    [['sub_index', '==', ''], ['depth', 0]],
    ['title', 'LIKE', $keyword],
    ['content', 'LIKE', $keyword],
);
```

<a id="where_lifecycle"></a>
### 조건의 Lifecycle

- `where()` 메소드로 추가한 조건은 `init()` 또는 `table()` 메소드가 호출되기 전까지 유효하다.  
- `where()` 이외에 `find()` `findAll()` 등 다른 메소드에 직접 인수로 전달한 조건은 해당 메소드 호출이 끝나면 사라진다.
- `DB::factory()` 호출한 경우 기본적으로 `init()` 메소드가 호출된다.
- `예외사항` _**`updateCount()` `update()` `delete()` 메소드에 한하여 <u>직접 전달한 조건이 있는 경우</u> where() 메소드로 추가한 조건은 무시한다.**_

```php
// 조건의 Lifecycle
$db->where('code', 'notice');
$count1 = $db->findCount(['cate_code', '01']); // - -> code = 'notice' AND cate_code = '01'
$count2 = $db->findCount(['cate_code', '02']); // - -> code = 'notice' AND cate_code = '02'

// updateCount() update() delete() 메소드에 한하여 직접 전달한 조건이 있는 경우 where() 메소드로 추가한 조건은 무시
$db->updateCount(['id', $id], 'hit'); // - -> id = $id

// 직접 전달한 조건이 없는 경우는 where() 메소드로 추가한 조건 사용
$db->where('user_id', $user_id);
$db->update($data); // - -> code = 'notice' AND user_id = $user_id

// 초기화
$db->init();
$count01 = $db->findCount(['cate_code', '01']); // - -> cate_code = '01'
```





<a id="select"></a>
## SELECT

`get()` `find()` `findAll()` `findColumn()` `findCount()` 메소드를 사용하여 데이터를 조회할 수 있다.

```php
// 사용 예시
$db = DB::factory();
$db->where('code', $code);
$db->where('depth', 0);

if ($user_name) {
    $db->join('user U', 'LEFT', 'U.user_id = B.user_id');
    $db->where('U.user_name', 'LIKE', $user_name);
}

$db->orWhere(['title', 'LIKE', $ss], ['content', 'LIKE', $ss]);

$total = $db->findCount();
if ($total) {
    $db->join('category C', 'LEFT', 'C.cate_code = B.cate_code');
    $db->join('user U', 'LEFT', 'U.user_id = B.user_id'); // 중복 사용해도 한 번만 적용

    $db->column('B.*, C.cate_name, U.user_name');
    $db->orderBy('id DESC');
    $db->limit(10);
    $db->page($page);
    $data = $db->findAll();
}
```

### JOIN, WHERE, COLUMN, ORDER, GROUP, LIMIT
`join()` `where()` `column()` `orderBy()` `groupBy()` `limit()` `page()` 메소드를 사용하여 쿼리 세부 내용을 지정할 수 있다.

<a id="select_join"></a>
#### JOIN
- `join()` 메소드를 사용하여 테이블을 조인할 수 있다.

```php
public function join(string|array $table, string $joinType = '', string|array $on = '', string $column = ''): static
```

```php
$db->join('user_level L', 'LEFT', 'U.user_level = L.user_level', 'L.user_level_name');
```

<a id="select_where"></a>
#### WHERE 조건
위쪽 [WHERE 조건](#where) 참조

<a id="select_column"></a>
#### COLUMN
- `column()` 메소드를 사용하여 조회할 칼럼을 지정할 수 있다.
- 여러번 호출하여 필요한 칼럼을 계속 추가할 수 있다.

```php
public function column(string|array $column): static
```

```php
$db->column('U.*');
if ($join_level) {
    $db->column('L.user_level_name');
}
```

<a id="select_order"></a>
#### ORDER
- `orderBy()` 메소드를 사용하여 ORDER BY 쿼리를 지정할 수 있다.
- `주의사항` 여러번 호출하는 경우 마지막 값만 적용된다.

```php
public function orderBy(string|array $order): static
```

```php
$db->orderBy('main_index ASC, sub_index ASC');
```

<a id="select_group"></a>
#### GROUP
- `groupBy()` 메소드를 사용하여 GROUP BY 쿼리를 지정할 수 있다.
- `주의사항` 여러번 호출하는 경우 마지막 값만 적용된다.

```php
public function groupBy(string|array $group): static
```

```php
// groupBy($group)
$db->groupBy('user_id, user_level');
```

<a id="select_limit"></a>
#### LIMIT
- `limit()` `page()` 메소드를 사용하여 LIMIT 쿼리를 지정할 수 있다.
- `limit()` 메소드에서 offset을 지정할 수 있지만, offset을 따로 계산할 필요가 없고 페이지를 기준으로 조회하는 경우가 대부분이므로 `page()` 메소드 사용을 권장한다.


```php
// limit
public function limit(int|string $limit, int $offset = 0): static

// page
public function page(int $page): static
```

```php
$list_num = request('list_num', 10);
$page = request('page', 1);

// 예전 방법
$offset = max($page - 1, 0) - $list_num;
$db->limit($list_num, $offset);

// 권장 방법
$db->limit($list_num);
$db->page($page);
```

<a id="select_findcount"></a>
### 갯수 조회
- `findCount()` 메소드를 사용하여 레코드 갯수를 조회할 수 있다.

```php
public function findCount(true|string|array $where = self::AUTO_WHERE, string $column = '*', string|array $group = '', true|string|array $having = self::AUTO_HAVING): int
```

```php
$count = $db->findCount(1, 'DISTINCT(user_id)');

$count = $db->where('code', $code)->findCount();
```


<a id="select_findall"></a>
### 여러 레코드 조회
- `findAll()` 메소드를 사용하여 여러 레코드를 조회할 수 있다.
  
  ```php
  public function findAll(true|string|array $where = self::AUTO_WHERE, string $columns = '', string|array $order = '', int|string $limit = '', string|array $group = '', true|string|array $having = self::AUTO_HAVING, int $page = 0): array
  ```

- `참고사항1` 가독성 및 유지보수를 위해 아래 코드처럼 인수보다는 해당 메소드를 사용하는 것을 권장한다. (단, 조건문의 Lifecycle을 고려하여 의도한 경우는 예외로 한다.)
- `참고사항2` 조회 조건이 없는 경우 WHERE 1 조건(전체 데이터)으로 처리된다.

```php
// findAll($where = true, $columns = '', $order = '', $limit = '', $group = '', $page = 0)
$data = $db->findAll([['code', $code], ['depth', 0]], 'user_id, COUNT(*) AS user_write_count', 'id DESC', $list_num, 'user_id', $page);

// 권장 방법
$db->where('code', $code);
$db->where('depth', 0);
$db->column('user_id, COUNT(*) AS user_write_count');
$db->orderBy('id DESC');
$db->groupBy('user_id');
$db->limit(10);
$db->page(1);
$data = $db->findAll();
```

<a id="select_find"></a>
### 하나의 레코드 조회
- `find()` 메소드를 사용한다.

```php
public function find(true|string|array $where = self::AUTO_WHERE, string $columns = '', string|array $order = '', string|array $group = '', true|string|array $having = self::AUTO_HAVING): mixed
```

```php
/** @var \Nova\Models\User $db */
$db = User::make();
$data = $db->find($user_id, 'user_id, user_level, user_name');

$data = $db->find(['user_id', $user_id], 'user_id, user_level, user_name');
$data = $db->find(['code', $code], '*', 'id DESC');
$data = $db->find(1, 'user_id, COUNT(*) AS login_count', 'login_count DESC', 'user_id');
// - -> SELECT user_id, COUNT(*) AS login_count FROM rama_user WHERE 1 ORDER BY login_count DESC GROUP BY user_id
```

<a id="select_findcolumn"></a>
### 하나의 칼럼 조회

- `findColumn()` 메소드를 사용하여 특정 칼럼을 조회할 수 있다.

```php
public function findColumn(true|string|array $where = self::AUTO_WHERE, string $column = '', string|array $order = '', string|array $group = '', true|string|array $having = self::AUTO_HAVING): mixed
```

```php
$user_level = $db->findColumn(['user_id', $user_id], 'user_level');
$count = $db->findColumn(['code', $code], 'COUNT(*)');
```

<a id="select_callback"></a>
### 콜백(callback) 함수 사용

- `callback()` 또는 `iterate()` 메소드를 사용하여 콜백 함수를 적용할 수 있다.
  - `callback()` 쿼리 실행 전 메소드를 사용하여 콜백 함수를 지정한다. 쿼리 실행 후 `iterate()` 메소드를 자동으로 실행한다.
  - `iterate()` 쿼리 실행 후 직접 콜백 함수를 실행한다.

- 콜백 함수는 `array_walk()` 메소드를 사용하여 하나의 레코드에 한 번씩 호출된다.
- 콜백 함수에 전달되는 인수 : `레코드 데이터` `키값` `DB 객체`
- 레코드 데이터는 참조 전달된다.


```php
// callback
public function callback(callable $callback, mixed ...$args): static

// iterate
public function iterate(array|ArrayObject $data, callable $callback, mixed ...$args): array|ArrayObject
```

```php
// 콜백 함수 예시 
function format(&$data, $i)
{
  $data['title'] = Str::cut($data['title'], 40);
}  
```

`참고사항` 문자열 자르기, 날짜 형식 변환 등은 템플릿에서 `함수 호출`을 사용할 것을 권장한다. 

```html
{title|40}
{date|date_normal}
```

<a id="select_query"></a>
### QUERY (PDOStatement 직접 사용)

- `query()` 메소드를 사용하여 `PDOStatement` 객체를 받아온 후 처리해준다.
- 데이터 수가 너무 많아 메모리 문제가 발생는 경우와 같이 불가피한 경우에 사용한다.

```php
public function query(true|string|array $where = self::AUTO_WHERE, string $columns = '', string|array $order = '', int|string $limit = '', string|array $group = '', true|string|array $having = self::AUTO_HAVING, int $page = 0): bool|string|PDOStatement
```

```php
/** @var \Nova\Models\User $db */
$db = User::make();
$db->join('data B', 'LEFT', 'B.user_id = U.user_id');
$db->column('U.*, COUNT(B.id) AS user_count');
$db->groupBy('user_id');
$stmt = $db->query();
while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
    // 하나의 레코드씩 직접 처리
}
```


<a id="insert"></a>
## INSERT

<a id="insert_simple"></a>
### 단순 입력
- `insert()` 메소드를 사용하며, 칼럼명을 key로 사용하는 배열을 전달한다.
- 데이터 입력 후 ID 값을 가져오려는 경우는 `insertAndGetId()` 메소드를 사용한다.
- `참고사항1` 입력 데이터는 자동으로 escape 처리된다.
- `참고사항2` 입력 데이터에 SQL 구문을 직접 사용하고자 하는 경우 `raw:` 텍스트로 시작한다. (소문자로만 입력)

```php
// insert
public function insert(array|ArrayObject $data, array|bool $data_update = false): bool

// insertAndGetId
public function insertAndGetId(array|ArrayObject $data, array|bool $data_update = false): int|string|bool

// getInsertId
public function getInsertId(bool $new = false): int|string
```

```php
/** @var \App\Models\User $db */
$db = User::make();

$data = [
    'user_id' => 'test_id1',
    'user_name' => '테스트 유저1',
    ...
    'user_join_date' => 'raw: now()',
];
$db->insert($data);
```

<a id="insert_update"></a>
### ON DUPLICATE KEY UPDATE 구문
- 동일하게 `insert()` 메소드를 사용하되, 두번째 인수에 `true` 또는 업데이트할 데이터를 전달한다.

```php
// true
$db->insert($data, true);

// 데이터
$data_update = $data;
unset($data['user_id']);
$db->insert($data, $data_update);
```

<a id="insert_bulk"></a>
### Bulk Data

- `insertBulk()` 메소드를 사용하여 엑셀 데이터 일괄 입력 등 많은 데이터를 한 번의 쿼리로 입력할 수 있다.
- `insert()` 메소드를 통해서도 가능하지만, 데이터 양이 많을 경우
  `insertBulk()` 메소드를 사용한 경우 훨씬 속도가 빠르다. (메모리 등의 상황에 따라 한 번에 입력할 데이터 갯수 조정 필요)

```php
public function insertBulk(array $data = [], bool|int $count = 100): bool
```

```php
// insertBulk($data = [], $count = 100)
$count = 500;
foreach ($data as $v) {
    // $count 개의 데이터당 1번의 디비 입력
    $db->insertBulk($v, $count);
}
// 남은 데이터 디비 입력
$db->insertBulk();
```


<a id="update"></a>
## UPDATE

<a id="update_simple"></a>
### 단순 수정
- `update()` 메소드를 사용하여 지정한 Uniue ID의 데이터를 수정할 수 있다.
- 일반 WHERE 조건을 사용할 수도 있는데 이 경우에는 세번째 인수를 `true`로 전달해주어야 한다.
- 데이터는 `insert()` 메소드와 동일하게 배열을 사용한다.
- `참고사항` 조회 조건이 없는 경우는 쿼리를 실행하지 않고 `false`를 반환한다.

```php
public function update(array|ArrayObject $data, true|string|array $where = self::AUTO_WHERE, string|array $order = '', int $limit = 0): bool
```

```php
$data = [
    'title' => '수정된 게시글 제목',
    'content' => '수정된 게시글 내용',
];

// PK 사용
$db->update($data, $id);

// where 사용
$db->update($data, ['id', $id], true);

// where() 사용
$db->where('id', $id);
$db->update($data);
```

<a id="update_count"></a>
### 숫자 증감
- `updateCount()` 메소드는 칼럼의 숫자값을 더하거나 빼는 특수용도로 사용한다.

```php
public function updateCount(true|string|array $where, string|array $column, int $count = 1): bool
```

```php
$db->updateCount(['id', $id], 'hit', 1);

// update() 메소드로 처리한 예 (비추천)
$data = [
    'hit' => 'raw: hit + 1',
];
$db->update($data, $id);
```


<a id="delete"></a>
## DELETE

- `delete()` 메소드를 사용하여 지정한 Uniue ID의 데이터를 삭제할 수 있다.
- 일반 WHERE 조건을 사용할 수도 있는데 이 경우에는 두번째 인수를 `true`로 전달해주어야 한다.
- `참고사항` 조회 조건이 없는 경우는 쿼리를 실행하지 않고 `false`를 반환한다.

```php
public function delete(true|string|array $where = self::AUTO_WHERE, string|array $order = '', int $limit = 0): bool
```

```php
// PK 사용
$db->delete($id);

// where 사용
$db->delete(['id', $id], true);

// where() 사용
$db->where('id', $id);
$db->delete();
```


<a id="rawsql"></a>
## RAW SQL 실행
- `주의사항` RAW SQL 실행은 사용하지 않는 것을 원칙으로 한다. DB 클래스가 기능을 지원하지 않는 경우에 한해서만 사용하도록 한다.
- SQL 및 파라미터 지정은 `PDO`의 방법에 준한다.

```php
// execute
public function execute(string $sql, array $params = []): bool|string|PDOStatement

// fetchAll
public function fetchAll(string $sql, array $params = [], int $fetchStyle = PDO::FETCH_ASSOC): array

// fetch
public function fetch(string $sql, array $params = [], int $fetchStyle = PDO::FETCH_ASSOC): array

// fetchColumn
public function fetchColumn(string $sql, array $params = []): mixed
```


```php
$sql = 'SELECT * FROM data WHERE code = ? AND flag = ?';
$params = [$code, $flag];
$stmt = $db->execute($sql, $params);

$data = $db->fetchAll($sql, $params);

$sql = 'SELECT * FROM data WHERE code = :code AND flag = :flag';
$params = [':code' => $code, ':flag' => $flag];
$data = $db->fetch($sql, $params);

$column = $db->fetchColumn($sql, $params);
```


<a id="subquery"></a>
## 서브 쿼리

- `subQuery()` 메소드를 사용하여 서브쿼리를 생성할 수 있다.
- 서브쿼리의 세부적인 로직은 두번째 인수로 전달하는 콜백메소드 내에서 처리한다.
- `subQuery()` 메소드의 결과값은 raw sql 이며, 칼럼 데이터 및 조건문 등 필요한 곳에 사용할 수 있다.

```php
public function subQuery(string $table, callable $callback, string $alias = ''): string
```

```php
$db->table('category C');
$db->column('C.*');
$db->column($db->subQuery('data D', function($db) {
    $db->where('cate_code', 'raw:C.cate_code');
    $db->findCount();
}, 'cate_count'));
$db->where('code', $code);
// - -> SELECT C.*, (SELECT COUNT(*) FROM data D WHERE cate_code = C.cate_code) AS cate_count WHERE code = '$code' ... 
```


<a id="transaction"></a>
## 트랜잭션

- `beginTransaction()` `commit()` `rollback()` 메소드를 사용하여 트랜잭션 처리를 할 수 있다.


```php
try {
    // 트랜잭션 시작
    $db->beginTransaction();
    ...
    
    // commit
    $db->commit();
} catch (PDOException $e) {
    // rollback
    $db->rollback();
}
```
