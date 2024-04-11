# 설정

## 환경 설정 파일

- 파일 경로 : /.env

운영 및 개발 등 서로 다른 환경에서도 소스 수정없이 동작하도록 하기 위하여 `.env` 환경 설정 파일을 지원합니다.
설치 파일에 포함된 `.env.example` 파일을 복사하여 `.env` 파일을 만들고, 필요에 따라 수정합니다.

```
APP_NAME="Nova"
APP_ENV=dev
APP_DEBUG=true
APP_KEY=

# dsn
DSN=mysql://sky:@sky@db/nova
```

- **APP_ENV**: 실행 환경 설정.
  - `live`: 실서버 운영 환경
  - `dev`: 로컬 개발 환경
  - `test`: 테스트 서버 환경
- **APP_DEBUG**: 디버깅 설정. `true` 또는 `false`
- **APP_KEY**: 암호화 로직에 사용되는 암호화 키
- **DEVELOPER**: 임시 디버깅을 사용할 수 있는 개발자 IP 지정 (PCRE 정규표현식)
- **DSN**: 디비 접속 정보. 필요에 따라 `DSN_SLAVE` `DSN_TEST` 등과 같이 여러 디비 접속 정보를 사용할 수 있습니다. 

`주의` `.env` 파일은 절대 `git`커밋 대상에 포함시켜서는 안됩니다.
 
## 설정 파일

- 파일 경로 : /config/app.php

프로그램에서 참조할 설정 사항을 모아 놓은 파일입니다.
기본 설정 이외에 필요한 설정을 추가하여 프로그램에서 사용할 수 있습니다.

### 프로그램에서 사용하는 방법

```php
// 조회
$is_debug = conf('app.debug');

// 변경
set_conf('app.debug', false);
```

### 주요 설정

#### 디렉토리 및 경로 설정

```php
'dir.root'       => APP_ROOT,                           // 루트 디렉토리
'dir.resources'  => APP_ROOT . '/resources',            // 리소스 루트 디렉토리
'dir.tpl'        => APP_ROOT . '/resources/views',      // 템플릿 파일 디렉토리
'dir.file'       => APP_ROOT,                           // 파일 루트 디렉토리
'dir.data'       => APP_ROOT . '/data',                 // 데이터 디렉토리 (업로드 파일, 사용자 파일 등)
'dir.tmp'        => APP_ROOT . '/tmp',                  // temp 디렉토리 (프로그램에서 생성하는 임시 파일용)
'dir.log'        => APP_ROOT . '/tmp/log',              // 로그 디렉토리
'dir.db_cache'   => APP_ROOT . '/tmp/_schema',          // DB 스키마 캐시 디렉토리
'dir.tpl_cache'  => APP_ROOT . '/tmp/_compile',         // 템플릿 캐시 디렉토리
'dir.file_cache' => APP_ROOT . '/tmp/_filecache',       // 파일 캐시 디렉토리
```

#### 디비 설정

```php
'db.dsn'          => env('DSN'),                        // 기본 디비 연결 정보
'db.dsn_slave'    => env('DSN_SLAVE'),                  // (선택) SLAVE 디비 연결 정보
'db.dsn_test'     => env('DSN_TEST'),                   // (선택) 테스트용 디비 연결 정보
'db.table_prefix' => '',                                // 테이블에 공통된 prefix를 사용하는 경우
```

#### 디버깅 설정

```php
'debug.db'        => true,                              // 디비 쿼리 디버깅 사용 여부
'debug.developer' => env('DEVELOPER'),                  // 개발자 아이피 지정
```

#### 템플릿 설정

```php
'view.version'          => 0,                               // 템플릿 버전. 템플릿 버전이 변경되면 템플릿 캐시 파일을 강제로 재생성
'view.resource_version' => env('APP_DEBUG') ? time() : 1,   // 리소스 버전. css, js 파일들의 캐시 방지를 위해 사용 
```
