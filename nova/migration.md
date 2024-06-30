# DB 마이그레이션

## 마이그레이션 생성

console 명령어를 사용하여 새로운 마이그레이션 스크립트를 생성할 수 있습니다.

```bash
php console make:migration add_post_table
```

`참고 ` `make:migration` 대신 `mg` 를 사용할 수 있습니다.

위 명령어를 실행하면 `data/migrations/YYYYMMDD_HHIISS_add_post_table.php` 파일이 생성됩니다.


## 마이그레이션 파일 작성

생성된 마이그레이션 파일 내에서 `up` 메서드와 `down` 메서드를 구현합니다. `up` 메서드는 마이그레이션을 적용할 때 실행되며, `down` 메서드는 마이그레이션을 롤백할 때 실행됩니다.

예를 들어, 사용자 테이블을 생성하는 마이그레이션은 다음과 같이 작성할 수 있습니다:

```php
use App\Nova\Database\Migration;

return new class extends Migration
{
    public function up(): void
    {
        $sql = <<<SQL
            CREATE TABLE post (
                id INT NOT NULL AUTO_INCREMENT,
                title VARCHAR(100) NOT NULL,
                name VARCHAR(50) NOT NULL,
                content text,
                PRIMARY KEY (id)
            )
            SQL;

        $this->createTable('test', $sql);
    }

    public function down(): void
    {
        $this->dropTable('test');
    }
};
```

## 주요 메소드

- **createTable** : 테이블 생성, 지정한 테이블이 없는 경우에만 쿼리를 실행합니다.
- **dropTable** : 테이블 삭제
- **addColumn** : 컬럼 추가, 지정한 칼럼이 없는 경우에만 쿼리를 실행합니다.
- **dropColumn** : 컬럼 삭제
- **changeColumn** : 칼럼 변경
- **addIndex** : 인덱스 생성, 지정한 인덱스가 없는 경우에만 쿼리를 실행합니다.
- **dropIndex** : 인덱스 삭제

## 마이그레이션 실행

모든 마이그레이션 파일이 준비되면, 다음 명령어로 모든 마이그레이션을 실행할 수 있습니다:

```bash
php console db:up
```

`참고 ` `db:up` 대신 `db:migrate` `du` `dm` 를 사용할 수 있습니다.

이 명령은 `_migrations` 테이블을 확인하여 아직 실행되지 않은 모든 마이그레이션을 실행합니다.


```bash
php console db:up 20240412_133814_add_post_table 
```

이 명령처럼 마이그레이션 스크립트 이름을 지정하여 해당 마이그레이션만을 실행할 수도 있습니다.

`--step` 옵션을 사용하여 특정 수의 마이그레이션만 실행할 수도 있습니다.

## 마이그레이션 롤백

마이그레이션을 롤백하려면, 다음 명령어를 사용합니다:

```bash
php console db:down 
```

`참고 ` `db:down` 대신 `db:rollback` `dd` `dr` 를 사용할 수 있습니다.

이 명령은 마지막으로 실행된 배치의 마이그레이션을 롤백합니다.


```bash
php console db:down 20240412_133814_add_post_table 
```

이 명령처럼 마이그레이션 스크립트 이름을 지정하여 해당 마이그레이션만을 롤백할 수도 있습니다.

`--batch` 옵션을 사용하여 롤백할 배치를 지정할 수도 있습니다.
`--step` 옵션을 사용하여 특정 수의 마이그레이션을 롤백할 수도 있습니다.


## 마이그레이션 목록 확인

```bash
php console db:status
```
`참고 ` `db:status` 대신 `ds` 를 사용할 수 있습니다.

이 명령은 모든 마이그레이션 스크립트의 목록을 실행 여부와 함께 보여줍니다. 
