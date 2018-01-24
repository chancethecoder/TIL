# Strong parameters

[Strong parameters](http://api.rubyonrails.org/classes/ActionController/StrongParameters.html)는 루비 온 레일즈에서 매개변수를 검증하는 방법 중 하나다.

## 기본예제

```ruby
class UserController

  def create_user
    user = User.create(params)
    user.save
  end
end
```

위는 전달받은 파라미터를 모델에 넘겨 유저를 생성하는 기본적인 코드다.

여기서 파라미터에 이상이 있다면 모델에서 예외가 발생할 것이고, 이상이 없다면 의도대로 생성 될 테니 문제는 없어 보인다.

하지만, 특정 필드만 업데이트 하고싶을 때는 문제가 발생할 수 있다. 허용된 필드(whitelist)에 상관 없이 통째로 파라미터를 모델에 전달하기 때문이다.

그럼 컨트롤러에서 whitelist 처리를 하고 넘긴다면?

```ruby
class UserController

  def create_user
    whitelist = ['name', 'age', 'email', 'address']
    unpermitted = whitelist - params.keys
    if unpermitted.empty?
      user = User.create(params)
      user.save
    end
  end
end
```

단순히 파라미터를 일일이 확인하면서 검사를 수행하는 코드를 작성하면 된다. **하지만, 좋은 방법(패턴)은 아닐 수 있다.**

각각의 액션 마다 파라미터를 체크하는 코드가 지저분하게 들어간다면, whitelist를 변경하기 힘들고 재사용성도 좋지 않을 것이다.

이러한 것을 패턴화 한 것이 **strong parameter**다.

## Strong parameter 사용 예제

strong parameter는 **require**와 **permit**이라는 두 개의 조합으로 이루어진다.

### permit

```ruby
class UserController

  def create_user
    user = User.create(user_params)
    user.save
  end

  def user_params
    params.permit(:name, :age, :email, :address)
  end
end
```

위 예제에서는 permit을 통해 whitelist(name, age, email, address)에 해당하는 값만 허용하고 있다.

만약 whitelist에 없는 값이 있으면 **경고 로그를 남기고 whitelist에 해당하는 값만 반환한다.**

여기서 예외를 발생 시키고싶다면

```ruby
# environment 파일에서 다음 옵션 추가
config.action_controller.action_on_unpermitted_parameters = :raise # :log가 기본값

# 혹은 액션에서 지정
def user_params
  ActionController::Parameters.action_on_unpermitted_parameters = :raise
  params.permit(:name, :age, :email, :address)
end
```

이렇게 옵션을 변경할 수 있다. 이후 발생하는 [UnpermittedParameters](http://www.rubydoc.info/github/rails/rails/ActionController/UnpermittedParameters)에 대해서는 예외가 발생하며, 400에러(Bad request)를 반환하는 등의 대처를 할 수 있다.

### require

```ruby
class UserController

  def create_user
    user = User.create(user_params)
    user.save
  end

  def user_params
    params.require(:user).permit(:name, :age, :email, :address)
  end
end
```

require를 통해 필수적으로 포함할 값을 지정할 수도 있다. 위 코드에서 파라미터에 user라는 key가 없을 경우 [ParameterMissing](http://www.rubydoc.info/github/rails/rails/ActionController/ParameterMissing) 예외가 발생한다.

만약 정상적으로 들어있다면, 이어서 user의 value에 대해서 permit을 수행한다. 즉, user의 value에도 Hash가 들어있어야 한다. (Hash 타입이 아닐 경우 NoMethodError 발생)

필수적으로 포함할 값이 여러개일 경우 어떻게 할까?

```ruby
class UserController

  def create_user
    user = User.create(user_params)
    user.save
  end

  def user_params
    params.require(:foo)
    params.require(:bar)
    params.require(:user).permit(:name, :age, :email, :address)
  end
end
```

위와 같이 여러개의 값에 대해서도 체크할 수 있다. 다만, 마지막에 실행되는 줄이 최종으로 반환되는 parameter 값이 되니 주의해야 한다.

## Strong parameter 사용 예제 2

## 참조

- http://guides.rorlab.org/action_controller_overview.html
- https://github.com/rails/strong_parameters