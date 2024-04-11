# 디버깅

## 디버깅 모드

### 환경 설정

`.env` 파일에서 `APP_DEBUG` 값을 `true`로 설정하면 디버깅 모드를 사용할 수 있습니다.

`주의` 실서버 환경에서는 반드시 디버깅 모드를 `false`로 설정해야 합니다.

### set_conf() 사용

```php
// 디버깅 ON
set_conf('app.debug', true);

// 디버깅 OFF
set_conf('app.debug', false);
```

## 임시 디버깅

실서버 환경에서 임시로 디버깅 모드를 사용하고자 하는 경우에 사용합니다.
임시 디버깅 모드를 사용하기 위해서는 다음과 같은 과정이 필요합니다.

1. `.env` 파일의 `DEVELOPER` 항목에 접속 IP 추가 (정규표현식)
2. `debug`파라미터를 사용하여 웹페이지 접속 
    ```shell
    # 한 번만 디버깅 사용 : debug=1
    https://my-domain.com?debug=1
    
    # 디버깅 계속 사용 (브라우저 닫을 때까지 유지) : debug=on
    https://my-domain.com?debug=on
    
    # 디버깅 사용 종료 : debug=off
    https://my-domain.com?debug=off
    ```
3. 웹페이지 좌측 하단의 `DEBUG` 버튼을 클릭하여 디버깅 정보 확인 


## 디버깅 정보 출력

- `debug()` 헬퍼 함수를 사용하여 디버깅 정보를 출력할 수 있습니다. `app.debug` 설정값이 `true` 인 경우만 페이지 하단에 해당 디버깅 정보 출력합니다.
- `dump()` `dd()` 헬퍼 함수를 사용하여 디버깅 정보를 출력할 수도 있습니다. 단, 이 함수들은 실서버 환경에서는 절대 사용하여서는 안됩니다. 
