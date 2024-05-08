# 설치

## 시스템 요구 사항

Nova 프레임워크를 사용하기 위한 기본 시스템 요구 사항은 다음과 같습니다:

- PHP 8.2 이상
- PDO PHP Extension
- Composer: PHP 의존성 관리 도구

시스템이 이 요구 사항을 충족하는지 확인한 후, Nova 프레임워크 설치를 진행합니다.

## 설치하기

### Git을 이용한 설치

  ```bash
  git clone https://github.com/daddyofsky/nova.git <project-name>
  cd <project-name>
  ```

### 의존성 설치

  ```bash
  composer install
  ```

### 환경 설정

[설정](config.md) 참고

### 웹 서버 설정

웹 서버의 가상 호스트를 구성하고, 문서 루트를 프로젝트의 `public` 디렉토리로 설정합니다.
