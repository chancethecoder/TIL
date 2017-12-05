# ECMAScript

ECMAScript는 Ecma 인터내셔널에서 정한 표준 언어이다.  

Ecma 인터내셔널은 국제 정보/통신 시스템 표준화를 담당하는 조직이며, 다양한 표준을 다루고 있다.  

그 중 하나가 *ECMA-262*이며, 이를 ECMAScript(이하 ES)라고 부른다.  

## ECMAScript와 JavaScript

ES는 자바스크립트(이하 JS)의 명세 판이다.  

말자하면 ES는 JS가 지원해야 할 기능들의 요구사항일 뿐이다.  

개발자는 ES 명세를 보고 개발하는 것이 아니라, 본인이 구현하고자 하는 환경에서 지원하는 JS 문서를 따라야 한다.  

여기서 환경은 무엇을 말할까?  

일반적으로 웹 브라우저를 말한다.  

크롬, IE, 파이어폭스, 사파리 등 최신 웹 브라우저는 JS를 해석하는 엔진을 기본적으로 탑재한다.  

JS 엔진은 JS 코드를 인터프리트 혹은 JIT(Just In Time) 컴파일하여 실행하는 프로그램으로,  

특정 ES 명세를 준수하도록 만들어지며 **항상 최신 ES 명세를 따른다는 보장은 없다.**

결국 고려해야 할 것은  

1. 자신이 개발하는 웹 서비스가 어떤 브라우저의 어떤 버전까지 지원할 것인지
2. 해당 브라우저에서 지원하는 ES 스펙이 어떻게 되는지

이 두 가지다.  

## Polyfill? Transpile?

이제 개발자 입장으로 생각해보자.  

우리는 ES 스펙과는 별개로, 현재 릴리즈 되어 있는 브라우저의 스펙에 종속될 수 밖에 없다.  

그렇다면 브라우저가 이를 반영할 때까지 기다릴 수 밖에 없을까?  

아니다.  

모던 웹에서는 이런 문제점을 해결하기 위한 다양한 방법이 존재한다.  

대표적으로 다음과 같다.  

- polyfill
- transpile

두 방법 모두 미지원 기능을 사용하려는 목표는 같다. 하지만, 분명한 차이점이 존재한다.  

*polyfill*은 미지원 API를 모방하는 방식이다.  

즉, 상위 스펙의 API가 동작하는 방식을 낡은 브라우저가 해석 가능한 코드로 재현하여 제공하는 것이다.  

하지만, 새로운 **Syntex**를 기존 Syntex로 재현하는 것은 불가능할 수 있다.  

이 때는 *transpile* 방식을 사용해야 한다.  

transpile은 컴파일을 거쳐 현재 코드를 낡은 브라우저가 해석 가능한 코드로 변형하는 방법이다.  

transpile 도구로 가장 유명한 *babel*은 사실상 웹 표준처럼 쓰이고 있으며,  

다양한 플러그인을 조합해 개발 효율, 성능 향상 등을 가져올 수 있다.  

심지어, 현재 ES 표준에 명시되지 않은 실험적인 기능들도 babel을 통해 적용이 가능하다는 점에서 강력한 도구이다.  

## 초간단 정리

> A: 너 이거 어떻게 개발했어?  
> B: xxx 프레임워크로, 프론트는 그냥 자바스크립트 썼어  
> A: ES 몇인데?  
> B: ES5  
> A: 아 그래? Promise 좋던데, ES6 써봐  
> B: ES6 스펙을 IE9가 지원 하던가?  
> A: 아니, IE9 고려할거면 babel로 트랜스파일 해야지  

## 참조

- 위키피디아
    - [ECMA 스크립트](https://ko.wikipedia.org/wiki/ECMA%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8)
    - [Ecma 인터내셔널](https://ko.wikipedia.org/wiki/Ecma_%EC%9D%B8%ED%84%B0%EB%82%B4%EC%85%94%EB%84%90)
    - [자바스크립트 엔진](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8_%EC%97%94%EC%A7%84)

- MDN
    - [JavaScript_and_the_ECMAScript_Specification](https://developer.mozilla.org/ko/docs/Web/JavaScript/New_in_JavaScript)
    - [New_in_JavaScript](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/%EC%86%8C%EA%B0%9C)

- 기타
    - [Polyfill을 사용하는 보다 쉬운 방법](http://hacks.mozilla.or.kr/2014/12/an-easier-way-of-using-polyfills/)