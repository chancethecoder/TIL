# Strong parameters

Strong parameters는 루비 온 레일즈에서 매개변수를 검증하는 방법 중 하나다. 컨트롤러에서 받은 정보를 모델에 업데이트 하기 전에 화이트리스트로 한 번 체크하여 조건을 충족하는지 확인할 수 있다.

## Basic

```ruby
class A

  def create_user
    person = Person.create(foo_params)
    person.save
  end

  def foo_params
    params.permit(:name, :age, :email, :address)
  end

end
```


## 참조

- http://guides.rorlab.org/action_controller_overview.html
- https://github.com/rails/strong_parameters