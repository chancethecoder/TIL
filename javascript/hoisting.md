# Hoisting

JS를 하다보면 hoisting이라는 단어를 볼 때가 있다. hoisting은 과연 무엇일까?  

YDKJS의 예제를 살펴보자.

```javascript
a = 2;
var a;
console.log(a); // 2
```

두 번째 줄에서 a가 다시 선언됐기 때문에 `undefined`가 나와야 맞을 것 같지만 2가 출력된다.  

그렇다면 a가 위로 올라가는 걸까?  

다음을 보자  

```javascript
console.log(a); // undefined
var a = 2;
```

???

## Compiling

JS 엔진은 코드를 인터프리팅하기 전에 컴파일 과정을 거친다.  

예를 들어, `var a = 2;`라는 코드가 있으면,  

1. `var a`와 `a = 2`로 쪼갠다
    * 선언부: `var a`
    * 실행부: `a = 2`
2. 실행부는 제자리에, 선언부는 코드 블럭의 맨 위로 끌어올린다

이러한 동작을 **Hoisting**이라고 한다.  

(개념적으로는 맨 위로 끌어올리는 것이지만, 실제로는 참조 가능하도록 만드는 것 뿐이다)  

예제를 다시 보자.  

```javascript
// compilation phase
var a;

// execution phase
a = 2;
console.log(a);
```

첫 번째를 이렇게 놓고 보면 말이 되는 것 같다.

이번엔 두 번째를 보자.  

```javascript
// compilation phase
var a;

// execution phase
console.log(a);
a = 2;
```

보이는 것처럼, 당연히 `undefined`가 나오는 코드다.  

## Per Scope

hoisting은 현재 scope의 맨 위로 끌어올려질 뿐이지, 프로그램의 첫 줄로 오는게 아니다.  

```javascript
foo();

function foo() {
    console.log( a ); // undefined
    var a = 2;
}
```

이 코드를 엔진에서는 아래와 같이 해석한다.  

```javascript
function foo() {
    var a;
    console.log( a ); // undefined
    a = 2;
}

foo();
```

global scope에서는 `function foo(){...}`가 hoisting되고  

블럭 scope에서는 `var a`가 hoisting되고 있다.  

## Function Expression

함수 표현식 or 화살표 함수 문법으로 변수에 함수를 할당할 수도 있다.  

```javascript
foo(); // TypeError

var foo = function bar() {
    // ...
};
```

위 코드에서 무슨 일이 벌어졌는지 알아보자.  

먼저 `foo` 변수에 `bar()` 함수를 할당했다.  

이게 컴파일 되면 함수 표현식을 선언부, 실행부로 쪼개어 `foo`가 hoisting될 것이다.  

최종 형태는 다음과 같다.  

```javascript
var foo;
foo(); // TypeError

foo = function bar() {
    // ...
};
```

이제 line by line으로 읽어보면,  

`foo`가 선언은 됐지만 `undefined` 상태인데 함수 호출을 시도하는 코드가 돼버렸다.  

그리고 `ReferenceError`가 아니라 `TypeError`가 발생한다. (변수 참조는 가능하기 때문에)  

위 예제를 좀 더 디테일하게 파고들어보자.  

```javascript
foo(); // TypeError
bar(); // ReferenceError

var foo = function bar() {
    // ...
};
```

이 코드는 다음과 같이 해석된다.  

```javascript
var foo;
foo(); // TypeError
bar(); // ReferenceError

foo = function() {
    var bar = ...self...
    // ...
}
```

유명 함수 표현식이 자기 자신을 외부 scope에서 호출한 경우다.  

`bar`가 블럭 scope에 있기 때문에 global scope에선 `ReferenceError`가 발생한다.  

## 참조

- You-Don't-Kown-JS