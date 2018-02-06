# Custom exception handling

기본적으로 레일즈의 Exception 처리는 begin, rescue 구문에서 사용되며 클래스 계층구조로 이뤄져있다.

## Exception

최상위 예외 클래스. StandardError 및 시스템 예외 클래스들의 부모다.

## StandardError

프로그램 수준의 최상위 예외 클래스. `rescue => ex`에서 기본 예외 클래스로 사용된다.

```ruby
# 아래 두 코드는 동일
begin
  raise "Error!"
rescue => ex
  # Error handling
end

begin
  raise "Error!"
rescue StandardError => ex
end
```

## RuntimeError

런타임 예외 클래스. `raise` 구문의 기본 예외 클래스로 사용된다.

```ruby
# 아래 두 코드는 동일
begin
  raise "Error!"
rescue => ex
  # Error handling
end

begin
  raise RuntimeError.new("Error!")
rescue => ex
end
```

## Custom Exception Class

프로그램 상 필요한 예외처리 핸들링을 위해 추가 정의하는 클래스.

```ruby
# Definition
class MyCustomError < StandardError
  def initialize(msg="Default message here")
    super
  end
end

# Inline style definition
class  MyCustomError < StandardError;

# Use
begin
  raise MyCustomError
rescue MyCustomError => ex
  # Error handling
end
```