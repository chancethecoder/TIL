자바스크립트를 다루다보면 [hoisting](https://developer.mozilla.org/ko/docs/Glossary/Hoisting)이라는 단어를 볼 때가 있습니다. hoisting이 무엇인지 YDKJS 예제를 통해 살펴보겠습니다.

```javascript
a = 2;
var a;
console.log(a); // 2
```

두 번째 줄에서 변수 a가 다시 선언됐기 때문에 undefined가 나와야 맞을 것 같지만 2가 출력됩니다. 그렇다면 변수 선언 위치는 상관이 없는걸까? 다음을 봅시다.

```javascript
console.log(a); // undefined
var a = 2;
```

???

### Compiling

자바스크립트 엔진은 코드를 인터프리팅하기 전에 JIT 컴파일 과정을 거칩니다.

&nbsp;

예를 들어, `var a = 2;`라는 코드가 있으면 선언부(`var a`)와 실행부(`a = 2`)로 나눕니다. 그 다음 선언부는 코드 블럭의 맨 위로 끌어올리는데, 이러한 동작을 **Hoisting**이라고 합니다. (**편의 상 맨 위로 끌어올리는 것으로 설명했지만, 실제로는 참조 가능하도록 만드는 것입니다**)

&nbsp;

이제 hoisting이 일어난 후의 예제를 다시 봅시다.

``` javascript
// compilation phase
var a;

// execution phase
a = 2;
console.log(a); // 2
```

첫 번째 코드를 이렇게 놓고 보면 말이 되는 것 같습니다. 두 번째를 봅시다.

```javascript
// compilation phase
var a;

// execution phase
console.log(a); // undefined
a = 2;
```

보이는 것처럼, 변수 선언 후 아무런 할당 없이 프린트를 시도하기 때문에 이제는 undefined가 나오는 코드로 보입니다.

### Per Scope

hoisting은 현재 scope의 맨 위로 끌어올려질 뿐이지, 프로그램의 첫 줄로 오는게 아닙니다.

```javascript
foo();

function foo() {
  console.log( a ); // undefined
  var a = 2;
}
```

위 코드에서 hoisting이 일어나면 다음과 같습니다.

```javascript
function foo() {
  var a;
  console.log( a ); // undefined
  a = 2;
}

foo();
```

바깥 scope에서는 foo 함수가 hoisting되고, 함수 내부 scope에서는 a 변수가 hoisting되고 있습니다.

### Function Expression

함수 표현식 or 화살표 함수 문법으로 변수에 함수를 할당할 수도 있습니다. 아래의 케이스를 살펴보겠습니다.

```javascript
foo(); // TypeError

var foo = function bar() {};
```

위 코드를 보면, 먼저 foo 변수에 bar 함수를 할당했습니다. 이제 hoisting이 일어나면 아래와 같이 foo의 변수 선언이 올라갈 것입니다.

```javascript
var foo;

foo(); // TypeError

foo = function bar() {};
```

이제 line by line으로 읽어보면, foo 변수가 선언은 됐지만 undefined 상태인데 함수 호출을 시도하는 코드가 돼버립니다.

&nbsp;

따라서, 변수 참조는 가능하기 때문에 ReferenceError가 아니라 TypeError가 발생하는 것을 알 수 있습니다.