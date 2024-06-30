# 로그

## 로그 파일

로그 파일들은 `tmp/log` 디렉토리에 저장되며, 로그 메세지와 함께 trace 정보가 저장됩니다.

로그 파일 경로는 다음 규칙에 따라 생성됩니다.
```
// 기본
tmp/log/종류/YYYYMM/YYYYMMDD_HH_일련번호.log
// ex) tmp/log/error/202404/20240409_23_00.log

// prefix를 지정한 경우
tmp/log/종류/YYYYMM/prefix_YYYYMMDD_HH_일련번호.log
// ex) tmp/log/error/202404/pg_20240409_12_00.log
```

로그 종류는 `에러` `디버그` `쿼리` 이렇게 3가지가 있으며 종류별로 각각 `error`, `debug`, `query` 디렉토리에 저장됩니다.


## 로그 기록

```php
use App\Nova\Log;

Log::error('에러 로그입니다.');
Log::debug('디버깅 로그입니다.');
Log::query('쿼리 로그입니다.');

// prefix 지정
Log::error('PG 에러 로그입니다.', 'pg');
```
