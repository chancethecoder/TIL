# rack-cors

[rack-cors](https://github.com/cyu/rack-cors)는 루비 온 레일즈에서 Rack 인터페이스와 호환 가능하며 CORS 기능을 지원하는 라이브러리다.  

## Rack

[Rack](http://rack.github.io/)은 Rails 프레임워크에서 기본으로 사용하는 웹서버 인터페이스다. Rack은 RESTful API를 쉽게 작성할 수 있도록 도와주며, 각 API를 하나의 메소드 호출 형태로 감싸주는 역할을 한다.  

Rack은 범용적으로 구현되어 Rails 뿐만 아니라 다양한 프레임워크에서 사용되지만, Rails에 의해서 가장 크게 사용되고 있다.  

## CORS

[CORS(Cross-Origin Resource Sharing)](https://developer.mozilla.org/ko/docs/Web/HTTP/Access_control_CORS)는 W3C에서 제안한 것으로, HTTP 호출과 보안에 관한 문제로 인해 생긴 개념이다.  

웹 어플리케이션에서 HTTP를 통해 리소스를 요청하는 경우, 요청을 하는 쪽과 받는 쪽의 도메인이 같을 수도 있지만 다를 수도 있다. 예를 들어, 이미지를 제공하는 REST API가 있다고 가정하자. ajax 콜을 통해 자신과 같은 도메인의 API를 요청할 경우, http://*client.com* 에서 http://*client.com/image/dog* 로 요청을 보낼 것이다. 이런 경우 개발자의 의도에 맞기 때문에 아무런 문제가 없다. 하지만, 다른 도메인의 경우, http://*client.com* 에서 http://*another_server.com/image/dog* 로 요청을 보내기 때문에 API 서버 입장에서 원하지 않는 응답을 해야 할 수도 있다.  

이처럼 같은 도메인의 HTTP 요청을 **same-origin HTTP** 요청, 다른 도메인의 HTTP 요청을 **cross-origin HTTP** 요청이라고 하며, 위의 문제를 막기 위해 브라우저의 스크립트 내에서는 기본으로 cross-origin HTTP 요청을 제한한다.  

하지만, 단일 서비스라도 여러 개의 다른 도메인을 얼마든지 가질 수 있으며, cross-origin HTTP 요청을 처리해야 하는 경우가 발생한다. 이를 해결하기 위해 HTTP 헤더에 추가적인 정보를 제공하여 cross-origin의 요청을 허가하는 방법이 CORS다.  

## rack-cors 사용법

Gemfile에서 `gem 'rack-cors'` 혹은 콘솔에서 `gem install rack-cors`를 통해 설치한다.  

Rails에서는 config 디렉토리의 application.rb 혹은 initializer에서 설정해줄 수 있다. origin을 통해 요청 url을, resource를 통해 요청 헤더를 제한할 수 있다.  

```ruby
Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins '*'
    resource '*', header: :any, methods: [:get,:post,:options]
  end
end
```

## 참고자료

- https://en.wikipedia.org/wiki/Rack_(web_server_interface)
- http://homoefficio.github.io/2015/07/21/Cross-Origin-Resource-Sharing/
- https://developer.mozilla.org/ko/docs/Web/HTTP/Access_control_CORS
- https://til.hashrocket.com/posts/4d7f12b213-rails-5-api-and-cors