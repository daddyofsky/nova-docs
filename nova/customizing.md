# 커스터마이징

Nova 프레임워크 기본 라이브러리에 기능을 추가하거나 일부를 수정해야할 필요가 있을 수 있습니다.
Nova 프레임워크는 다음과 같은 방법으로 Nova 프레임워크 라이브러리를 수정하거나 대체할 수 있는 방법을 제공합니다.

## App\Nova 네임스페이스

Nova 프레임워크 라이브러리를 사용할 때 `Nova` 네임스페이스를 직접 사용하지 않고 `App\Nova` 네임스페이스를 사용합니다.

`Model` 클래스를 예로 들어보겠습니다.
`App\Nova\Model` 클래스를 호출한 경우 `/App/Nova/Model.php` 파일이 존재하지 않는 경우 자동으로 `Nova\Model` 클래스가 사용됩니다.
사용자가 `/App/Nova/Model.php` 파일을 추가한 경우 해당 클래스 파일이 사용됩니다.

이 경우 `Nova\Model` 클래스를 상속하여 사용할지, 전혀 새로운 `Model` 클래스를 사용할지는 사용자가 필요에 따라 결정할 수 있습니다.

- **상속 예제**

  ```php
  <?php
  namespace App\Nova;

  class Model extends \Nova\Model
  {
      // your own code 
  }
  ```

- **대체 예제**

  ```php
  <?php
  namespace App\Nova;

  use Illuminate\Database\Eloquent\Model as ORM;
  
  class Model extends ORM
  {
      // your own code 
  }
  ```
