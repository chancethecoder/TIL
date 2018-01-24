
# Strong parameters

[Strong parameters](http://api.rubyonrails.org/classes/ActionController/StrongParameters.html)는 루비 온 레일즈에서 매개변수를 검증하는 방법 중 하나다. 컨트롤러에서 받은 정보를 모델에 업데이트 하기 전에 화이트리스트로 한 번 체크하여 조건을 충족하는지 확인할 수 있다.

## 기본예제

```ruby
class UserController

  def create_user
    user = User.create(params)
    user.save
  end
end
```

위 코드는 전달받은 파라미터를 모델에 넘겨 유저를 생성하는 코드다.

여기서는 파라미터에 이상이 있거나, 모델의 필드 타입과 다른 데이터가 들어오더라도 아무런 체크를 하지 않는다. 즉, 데이터 검증을 모델에서 할 수 밖에 없는 구조가 된다.

그럼 컨트롤러에서 처리하고싶다면? 

## Strong parameter 사용 예제

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

## 참조

- http://guides.rorlab.org/action_controller_overview.html
- https://github.com/rails/strong_parameters