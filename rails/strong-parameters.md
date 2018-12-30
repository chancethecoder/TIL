루비 온 레일즈는 매개변수 검증 방법으로 [Strong Parameters](https://guides.rorlab.org/action_controller_overview.html#strong-parameters)를 제공합니다.

&nbsp;

Strong Parameters를 왜, 어떻게 사용하는지 알아봅시다.

## Why use?

유효성 검사는 API(특히 Public API)를 개발할 때 기본으로 생각해야 할 부분입니다. 사용자가 문서에 맞게 잘 사용할 수도 있지만, 그렇지 않을 경우를 대비해 방어적으로 만들지 않으면 시스템이 쉽게 무너지기 때문입니다. (사례가 궁금하다면 맨 아래 백그라운드 참고)

&nbsp;

루비 온 레일즈에서 API를 작성할 시 아래와 같은 문제가 있습니다.

### 문제점1

```ruby
class UserController

  def create_user
    user = User.create(params)
    user.save
  end
end
```
위의 코드는 전달받은 파라미터를 모델에 넘겨 유저를 생성하는 기본적인 코드입니다. 여기서 파라미터에 이상이 있다면 모델에서 예외가 발생할 것이고, 이상이 없다면 의도대로 생성 될 테니 문제는 없어 보입니다. 하지만, 특정 필드만 선택적으로 업데이트 할 수는 없습니다. 통째로 파라미터를 모델에 전달하기 때문입니다.

&nbsp;

그럼 컨트롤러에서 whitelist 처리를 하고 넘긴다면?

```ruby
class UserController

  def create_user
    whitelist = ['name', 'age', 'email', 'address']
    unpermitted = whitelist - params.keys
    if unpermitted.empty?
      user = User.create(params)
      user.save
    else
      raise "Unpermitted parameter passed"
    end
  end
end
```

파라미터를 필터링하는 코드를 작성하면 됩니다. 하지만 액션마다 필터링하는 코드가 따로있다면 whitelist를 관리하기 힘들고 재사용성도 좋지 않을 것 같습니다.

### 문제점2

위에서는 API 컨트롤러에서 모델을 통하여 흐름이 진행되고 있습니다. 즉, 모델에서 타입이나 Nullable 등의 예외처리를 수행할 수 있습니다. 그렇다면 모델을 사용하지 않는 경우, 예를 들어 서비스 클래스에 비즈니스 로직이 있는 경우는 어떨까요? API 컨트롤러 혹은 서비스 클래스에 타입 추론 등의 유효성 검사 코드가 덕지덕지 붙게 될 것입니다.

&nbsp;

위의 문제점들을 종합해보면 다음과 같습니다.

1. whitelist
2. 재사용성
3. 타입추론, Null check

루비 온 레일즈에서는 위와같은 일반적인 문제들을 해결하기 위해 Strong Parameters라는 기능을 제공하고 있습니다.

## 사용 예제

strong parameters는 **require**와 **permit**이라는 두 개의 조합으로 이루어집니다.

### permit

permit은 whitelist를 지정할 수 있습니다. 사용은 아래와 같습니다.

```ruby
class UserController

  def create_user
    user = User.create(user_params)
    user.save
  end

  private

  def user_params
    params.permit(:name, :age, :email, :address)
  end
end
```

만약 whitelist에 없는 값이 있으면 **경고 로그를 남기고 whitelist에 해당하는 값만 반환**합니다. 여기서 예외를 발생 시키고싶다면 environment.rb 파일에 다음 옵션을 추가하면 됩니다.

```ruby
config.action_controller.action_on_unpermitted_parameters = :raise
```

혹은 액션에서 다음과 같이 지정해줄 수도 있습니다.

```ruby
def user_params
  ActionController::Parameters.action_on_unpermitted_parameters = :raise
  params.permit(:name, :age, :email, :address)
end
```

이후 발생하는 UnpermittedParameters에 대해서는 예외가 발생하며, 400에러(Bad request)를 반환하는 등의 대처를 할 수 있습니다.

### require

require는 필수적으로 포함할 값을 지정할 수 있습니다. 사용은 아래와 같습니다.

```ruby
class UserController

  def create_user
    user = User.create(user_params)
    user.save
  end

  private
  
  def user_params
    params.require(:user).permit(:name, :age, :email, :address)
  end
end
```

params에 user라는 필드가 없을 경우 ParameterMissing 예외가 발생합니다. 정상적으로 들어있다면, 이어서 user에 대해서 permit을 수행합니다. 즉, **user에도 Hash가 들어있어야 합니다**. (Hash 타입이 아닐 경우 NoMethodError 발생)

&nbsp;

필수적으로 포함할 값이 여러개일 경우 어떻게 할까요?

```ruby
class UserController

  def create_user
    user = User.create(user_params)
    user.save
  end
  
  private
  
  def user_params
    params.require(:token)
    params.require(:item).permit(:name, :price)
    params.require(:user).permit(:name, :age, :email, :address)
    params
  end
end
```

위와 같이 여러개의 값에 대해서도 체크할 수 있습니다. 다만, 마지막에 실행되는 줄이 최종으로 반환되는 parameter 값이 되니 주의해야 합니다.

### nested parameter

중첩 매개 변수에 대해서도 검증할 수 있습니다.

```ruby
class UserController

  def create_user
    user = User.create(user_params)
    user.save
  end

  private
  
  def user_params
    params.require(:user).permit(:name, :age, { emails: [] }, :address, sites: [ :name, :url ])
  end
end
```

위 코드에서는 user라는 필드에 name, age, emails, address, sites라는 값들이 whitelist에 포함됩니다. 또한 emails는 배열로, sites는 name과 url을 속성으로 가지는 객체의 배열로 검증할 수 있습니다. 따라서, 아래와 같은 요청에 대해서 예외 처리가 가능합니다.

```javascript
{
  "user": {
    "name": "youngkyun",
    "age": 20,
    "emails": [
      "chancethecoder@gmail.com"
    ],
    "address": "somewhere",
    "sites": [{
      "name": "personal site",
      "url": "https://chancethecoder.me"
    },{
      "name": "another site",
      "url": "https://anothersite.com",
      "foo": "bar"        // UnpermittedParameters 발생
    }]
  }
}
```

## 백그라운드

Strong Parameters라는 기능이 루비 온 레일즈에 기본 모듈로 제공되며 사용을 권장하게된 것에는 배경이 있습니다.

&nbsp;

한 때 레일즈 개발자 사이에 떠들썩한 이슈가 있었는데, 바로 Egor Homakov라는 사람이 [Github의 보안취약점](https://www.extremetech.com/computing/120981-github-hacked-millions-of-projects-at-risk-of-being-modified-or-deleted)을 지적한 일입니다.(다행히 cracker는 아니었던 모양입니다)

&nbsp;

이슈가 된 이유는 간단합니다. 해킹 방법이 너무 간단하고 치명적이었기 때문입니다. 그의 [포스트](http://homakov.blogspot.com/2012/03/how-to.html) 내용은 아래와 같습니다.

> I simply added a <input value=USER_ID name=public_key[user_id]> field to Public key update form, where USER_ID = 4223 (from https://api.github.com/users/rails).

> Backend didn't whitelist accessible attributes and had something like this:
@key = PublicKey.find(params[:id])
@key.update_attributes(params[:public_key]) #Oh no! We passed public_key[user_id] of our victim!

> Now our victim (Rails) has our public key associated with their account. You can read/write in any public/private repo on github.

이로 인해 레일즈를 사용하는 모든 개발자들이 whitelist에 대해 심각성을 느끼게 되었고, Rails4에서부터 Strong Parameters를 기본 기능으로 제공하기 시작했습니다. 재밌는 점은, [일부 개발자](https://medium.com/@apneadiving/why-i-do-not-use-strong-parameters-in-rails-e3bd07fcda1d)들은 Strong Parameters의 필요성이 과대포장된 것이라고 생각한다는 것입이다. API 성능이 매우 중요하여 overhead에 대한 최적화가 필요하다면, 이러한 개발자들과 같은 접근 방법도 고민해볼만합니다.