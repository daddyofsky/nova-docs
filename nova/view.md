# View


## SkyTemplate

Nova 프레임워크에서는 `SkyTemplate` 템플릿 엔진을 사용합니다.

- **조건문**:
  ```html
  {?name == 'Nova'}
      <p>Hello, Nova!</p>
  {/}
  ```

- **반복문**:
  ```html
  {@:users}
      <p>This is user {.id}</p>
  {/}
  ```

`SkyTemplate`에 대한 자세한 내용은 [SkyTemplate](sky_template.md) 항목을 참고하시기 바랍니다.


## 뷰 생성

- 뷰 파일은 `resources/views` 디렉토리 내에 저장됩니다.
- `dir.tpl` 설정값을 사용하여 디렉토리 위치를 변경할 수 있습니다. 
- `.html` 확장자를 사용하여 SkyTemplate 템플릿 엔진을 활용할 수 있습니다.

## 뷰 파일 지정

- `view()` 헬퍼 함수의 첫 번째 인수로 뷰 파일을 지정할 수 있습니다.
- 뷰 파일의 절대 경로를 직접 지정하거나 `.(dot)`로 구분하여 상대 경로를 지정할 수 있습니다.

```php
// 절대 경로 지정
view('/path/of/view/file.html');

// resources/views 기준 상대 경로 지정
view('main.index'); // --> resources/views/main/index.html

// dir.tpl 설정 값이 resources/views/admin 인 경우
set_conf('dir.tpl', APP_ROOT . '/resources/views/admin');
view('main.index'); // --> resources/views/admin/main/index.html
```

`참고` `view()` 헬퍼 함수는 `Nova\View` 클래스의 인스턴스를 반환합니다.
`참고` 상대 경로로 지정하는 경우 `dir.tpl` 설정값의 영향을 받습니다.


## 데이터 전달

컨트롤러에서 뷰로 데이터를 전달할 때는 다음과 같이 배열을 사용합니다:

```php
return view('user.view')
    ->with('name', '꽃미남')
    ->with('age', '18');
```

```php
return view('user.view')
    ->with([
        'name' => '꽃미남',
        'age' => '18',
    ]);
```

이 예제에서는 `resources/vies/user/view.html` 템플릿 파일을 사용하여 전달한 데이터를 출력합니다.

```html
<h1>회원 정보</h1>
<p>
  이름 : {name}<br />
  나이 : {age}
</p>
```

## 레이아웃

공통적인 웹 페이지 구조(예: 헤더, 푸터)를 재사용하기 위해 레이아웃을 사용할 수 있습니다.

```php
return view('info.view')
    ->layout('sub')
    ->with($info);
```

```php
return view('info.view', 'sub')
    ->with($info);
```
위 예제에서는 `resources/views/layout/sub.html` 파일을 레이아웃으로 사용합니다.  
레이아웃 파일에는 `{&BODY}` 태그가 반드시 포함되어야 합니다.

`참고` 레이아웃 파일은 `dir.tpl` 설정 디렉토리 하위의 `layout` 디렉토리에 위치하여야 합니다.


### 레이아웃 파일 예시

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="utf-8" />
    <title>레이아웃 샘플</title>
<body>
    <div id="wrap" class="layout-A">
        <header>
            {{M_LOGO}}
        </header>
        <nav>
            {{M_MENU}}
        </nav>
        <main>
            {&BODY}
        </main>
        <footer>
            {{M_FOOTER}}
        </footer>
    </div>
</body>
</html>
```

## 뷰 모듈

재사용 가능한 UI 모듈을 정의하여, 애플리케이션 전반에 걸쳐 사용할 수 있습니다.

`{{M_모듈코드}}` 형태로 템플릿 파일에서 사용합니다.

모듈에 대한 로직은 `App\Service\View\ModuleHandler` 클래스에 `module_M_모듈코드` 이름을 가진 메소드가 있는 경우 자동으로 호출됩니다.
