# 24장 클로저
---
클로저는 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어에서 사용되는 중요한 특성이다.

>MDN
"클로저는 함수와 그 **함수가 선언된 렉시컬 환경**과의 조합이다."

제일 먼저 이해해야 할 핵심 키워드는 "함수가 선언된 렉시컬 환경"이다.


##### 예제 24-01

```javascript
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }

  innerFunc();
}

outerFunc();
```

7번째 줄 콘솔에서 10이 나온 이유 : 자신이 정의된 환경( 상위스코프 )를 기억한다.

함수에서 내부는 외부 변수를 사용할 수 있지만, 외부에서 내부 값으로는 접근할 수 없다.

`outerFunc` 함수 내부에서 중첩 함수 `innerFunc`가 정의, 호출되었다.
중첩 함수 `innerFunc`의 상위 스코프는 `outerFunc`이다.
중첩 함수 `innerFunc` 내부에서 자신을 포함하고 있는 외부 함수 `outerFunc`의 `x` 변수에 접근할 수 있다.

##### 예제 24-02

```javascript
const x = 1;

function outerFunc() {
  const x = 10;
  innerFunc();
}

function innerFunc() {
  console.log(x); // 1
}

outerFunc();
```
`outerFunc` 내부에 있는 것이 아니라 따로 있다. `innerFunc`의 상위 스코프는 전역이기 때문에 9번 줄 콘솔의 결과값은 1이다.

------
## 24.1 렉시컬 스코프

자바스크립트 엔진은 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 결정하는데, 이를 렉시컬 스코프 ( 정적 스코프 ) 라고 한다.
##### 예제 24-03

```javascript
const x = 1;

function foo() {
  const x = 10;
  bar();
} // 전역 함수

function bar() {
  console.log(x);
} // 전역 함수

foo(); // ?
bar(); // ?
```

결과 값은 무엇일까?

이유는 무엇일까?

`bar` 함수의 상위 스코프는? => `전역`
`foo` 함수는 `bar` 함수를 실행시키는 값이기 때문에 `bar` 함수를 실행시킨 값이 나온다.

상위 스코프에 대한 참조는 함수가 정의되는 시점에서의 환경(위치)에 의해 결정된다. 이것이 바로 렉시컬 스코프이다.


>호랑이가 우주로 납치되어서도 기억 할 호랑이의 고향은
호랑이가 태어난 시점에서의 위치에 의해 결정된다.<br/>
= 호랑이는 지구에서 태어났기 때문에 호랑이의 고향은 지구이다.

------
## 24.2 함수 객체의 내부 슬롯
함수가 정의된 환경과 호출되는 환경은 다를 수 있다. 함수가 호출되는 환경에 관계 없이 자신이 정의된 환경, 상위 스코프를 기억해야 한다. 이를 위해 함수는 자신의 내부 슬롯에 자신이 정의된 환경, 상위 스코프의 참조를 저장한다.

> 호랑이는 어떤 행성에 가더라도 항상 자신의 고향인 지구(상위스코프) 를 기억한다.

###### 예제 24-04

```javascript
const x = 1;

function foo() {
  const x = 10;

  // 상위 스코프는 함수 정의 환경(위치)에 따라 결정된다.
  // 함수 호출 위치와 상위 스코프는 아무런 관계가 없다.
  bar();
}

// 함수 bar는 자신의 상위 스코프, 즉 전역 렉시컬 환경을 [[Environment]]에 저장하여 기억한다.
function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```
------
## 24.3 클로저와 렉시컬 환경

###### 예제 24-05

```javascript
const x = 1;

// ①
function outer() {
  const x = 10;
  const inner = function () { console.log(x); }; // ②
  return inner;
}

// outer 함수를 호출하면 중첩 함수 inner를 반환한다.
// 그리고 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 팝되어 제거된다.
const innerFunc = outer(); // ③
innerFunc(); // ④ 10
```

`innerFunc` 의 실행 값이 10이 나오는 과정
: `outer` 함수를 호출 (3) => 중첩 함수 `inner`를 반환 => 생명 주기 마감 => `outer` 함수 실행 종료 => 실행 컨텍스트에서 제거 => `outer` 함수의 지역변수 `x`(10) 의 생명 주기 마감
생명 주기가 마감되어 변수 `x`(10)에 접근할 수 있는 방법이 없어 보이지만, 실행 결과는 10이다.

외부 함수보다 중첩 함수가 더 오래 유지되는 경우,
중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참고할 수 있다.
이러한 중첩 함수를 클로저(closure)라고 부른다.

`outer`함수의 실행 컨텍스트가 실행 컨텍스트 스택에서 제거되지만 `outer` 함수의 렉시컬 환경까지 소멸하는 것은 아니다.

가비지 컬렉터는 누군가가 참조하고 있는 메모리 공간을 함부러 해제하지 않는다.

>월 E ( 가비지컬렉터 )가 호랑이를 납치해 지구에서 호랑이가 없어지더라도 = 실행 컨텍스트 스택에서 실행 컨텍스트가 제거되더라도<br/>
호랑이는 자신의 고향인 지구(상위스코프)의 환경을 기억한다.

더 오래 생존한 중첩 함수는 외부 함수의 생존 여부(실행 컨텍스트의 생존 여부)와 상관없이 자신이 정의된 위치에 의해 결정된 상위 스코프를 기억한다. 중첩 함수 `inner` 내부에서는 상위 스코프를 참조할 수 있으므로 상위 스코프의 식별자를 참조할 수 있고 식별자의 값을 변경할 수도 있다.

------

자바스크립트의 모든 함수는 상위 스코프를 기억하므로 이론적으로 모든 함수는 클로저다.

하지만 일반적으로 모든 함수를 클로저라고 하지 않는다.
###### 예제 24-06

```html
<!DOCTYPE html>
<html>
<body>
  <script>
    function foo() {
      const x = 1;
      const y = 2;

      // 일반적으로 클로저라고 하지 않는다.
      function bar() {
        const z = 3;

        debugger;
        // 상위 스코프의 식별자를 참조하지 않는다.
        console.log(z);
      }

      return bar;
    }

    const bar = foo();
    bar();
  </script>
</body>
</html>
```

`bar` 함수는 클로저일까?

`bar` 함수가 클로저가 아닌 이유는 뭘까?
=> 상위 스코프의 식별자를 참조하지 않기 때문에
<br/>

###### 예제 24-07

```html
<!DOCTYPE html>
<html>
<body>
  <script>
    function foo() {
      const x = 1;

      // 일반적으로 클로저라고 하지 않는다.
      // bar 함수는 클로저였지만 곧바로 소멸한다.
      function bar() {
        debugger;
        // 상위 스코프의 식별자를 참조한다.
        console.log(x);
      }
      bar();
    }

    foo();
  </script>
</body>
</html>
```

`bar` 함수는 클로저일까?
<p color='#fff'>네</p>

`bar` 함수가 클로저인 이유는?
상위 스코프의 식별자를 참조하기 때문에

중첩 함수 `bar`는 클로저였지만 외부 함수보다 일찍 소멸되기 때문에 **생명 주기가 종료된 외부 함수의 식별자를 참조할 수 있다**는 클로저의 본질에 부합하지 않는다.

중첩 함수 `bar`는 일반적으로 클로저라고 하지 않는다.
<br/>

###### 예제 24-08

```html
<!DOCTYPE html>
<html>
<body>
  <script>
    function foo() {
      const x = 1;
      const y = 2;

      // 클로저
      // 중첩 함수 bar는 외부 함수보다 더 오래 유지되며 상위 스코프의 식별자를 참조한다.
      function bar() {
        debugger;
        console.log(x);
      }
      return bar;
    }

    const bar = foo();
    bar();
  </script>
</body>
</html>
```

`bar` 함수는 클로저일까?
<p color='#fff'>네</p>

`bar` 함수가 클로저인 이유는?
1. 중첩 함수가 상위 스코프의 식별자를 참조하고 있다.
2. 중첩 함수가 외부 함수보다 더 오래 유지된다.

자유변수 : 클로저에 의해 참조되는 상위 스코프의 변수
      = `foo` 함수의 `x` 변수

**clousre** 란 **함수가 자유 변수에 대해 닫혀있다** (closed) 라는 의미이다.

자유 변수에 묶여있는 함수

> 호랑이가 지구를 기억하고 기억한 내용을 써먹을때가 진정한 클로저 !
![Alt text](image.png)

---


## 24.4 클로저의 활용

클로저 사용 이유 : **상태 state 를 안전하게 변경하고 유지**하기 위해서 =
상태가 의도치 않게 변경되지 않도록 **상태를 안전하게 은닉**하고 **특정 함수에게만 상태 변경을 허용**하기 위해

`num` 변수는 안전하게 변경하고 유지해야 할 상태라는 것을 기억하고 아래의 예제를 살펴봅시다.

###### 예제 24-09

```javascript
// 카운트 상태 변수
let num = 0;

// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태를 1만큼 증가 시킨다.
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 코드가 바르게 동작하기 위해 지켜져야 하는 조건
1. 카운트 상태 (`num` 변수의 값)은 `increase` 함수가 호출되기 전까지 변경되지 않고 유지되어야 한다.
2. 카운트 상태 (`num` 변수의 값)은 `increase` 함수만이 변경할 수 있어야 한다.

하지만 카운트 상태는 전역 변수 이기 때문에 누구나 접근, 변경 가능

🤯 의도치 않게 상태가 변경될 수 있음

###### 예제 24-10

```javascript
// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태 변수
  let num = 0;

  // 카운트 상태를 1만큼 증가 시킨다.
  return ++num;
};

// 이전 상태를 유지하지 못한다.
console.log(increase()); // 1
console.log(increase()); // 1
console.log(increase()); // 1
```

위 예제에서는 전역 변수 `num`을 `increase` 함수의 지역 변수로 바꾸어 의도치 않은 상태 변경을 방지해보았다.

`num`변수의 상태는 `increase` 함수만이 변경할 수 있다.

console.log 의 결과는 무엇일까?
: 모두 1

console.log 의 결과값이 모두 1인 이유는 무엇일까?
: `increase` 함수가 호출될 때마다 지역 변수 `num`이 다시 선언, 0으로 초기화되기 때문

🤯 이전 상태를 유지하지 못함


###### 예제 24-11

```javascript
// 카운트 상태 변경 함수
const increase = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저
  return function () {
    // 카운트 상태를 1만큼 증가 시킨다.
    return ++num;
  };
}());

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 예제에서는 이전 상태를 유지할 수 있도록 클로저를 사용해보았다.

즉시 실행 함수가 반환한 클로저는 카운트 상태를 유지하기 위한 자유 변수 `num` 을 언제 어디서 호출하든지 참조하고 변경 할 수 있다.

##### 클로저는 상태 state 가 의도치 않게 변경되지 않도록 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하여 상태를 안전하게 유지하기 위해 사용한다.

###### 예제 24-12

```javascript
const counter = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저인 메서드를 갖는 객체를 반환한다.
  // 객체 리터럴은 스코프를 만들지 않는다.
  // 따라서 아래 메서드들의 상위 스코프는 즉시 실행 함수의 렉시컬 환경이다.
  return {
    // num: 0, // 프로퍼티는 public하므로 은닉되지 않는다.
    increase() {
      return ++num;
    },
    decrease() {
      return num > 0 ? --num : 0;
    }
  };
}());

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

카운트 상태를 감소시킬 수 있도록 발전시킨 코드이다.

`increase`, `decrease` 메서드들의 상위 스코프는 즉시 실행 함수의 렉시컬 환경이다.

`increase`, `decrease` 메서드가 언제 어디서 호출되든 상관없이 `increase`, `decrease` 함수는 즉시 실행 함수의 스코프의 식별자를 참조할 수 있다.

###### 예제 24-13

```javascript
const Counter = (function () {
  // ① 카운트 상태 변수
  let num = 0;

  function Counter() {
    // this.num = 0; // ② 프로퍼티는 public하므로 은닉되지 않는다.
  }

  Counter.prototype.increase = function () {
    return ++num;
  };

  Counter.prototype.decrease = function () {
    return num > 0 ? --num : 0;
  };

  return Counter;
}());

const counter = new Counter();

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

생성자 함수로 표현한 코드이다.

생성자 함수 : 객체를 생성하기 위해 사용되는 특수한 함수

`increase`, `decrease` 는 모두 즉시 실행 함수 실행 컨텍스트의 렉시컬 환경을 기억하는 클로저이다.

------
외부 상태 변경이나 가변 데이터를 피하고 불변성을 지향하는 함수형 프로그래밍에서 부수 효과를 최대한 억제하여 오류를 피하고 프로그램의 안정성을 높이기 위해 클로저는 적극적으로 사용된다.

함수형 프로그래밍에서 클로저를 사용하는 예시를 살펴보도록 한다.

###### 예제 24-14

```javascript
// makeCounter : 함수를 인수로 전달받고 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한
// 자유 변수 counter를 기억하는 클로저를 반환한다.
function makeCounter(aux) {
  // 카운트 상태를 유지하기 위한 자유 변수
  let counter = 0;

  // 클로저를 반환 : 자신이 생성됐을 때의 렉시컬 환경인
  // makeCounter 함수의스코프에 속한 counter 변수를 기억한다.
  return function () {
    // 인수로 전달 받은 보조 함수에 상태 변경을 위임한다.
    counter = aux(counter);
    return counter;
  };
}

// 보조 함수
function increase(n) {
  return ++n;
}

// 보조 함수
function decrease(n) {
  return --n;
}

// 함수로 함수를 생성한다.
// makeCounter 함수는 보조 함수를 인수로 전달받아 함수를 반환한다
const increaser = makeCounter(increase); // ①
console.log(increaser()); // 1
console.log(increaser()); // 2

// increaser 함수와는 별개의 독립된 렉시컬 환경을 갖기 때문에 카운터 상태가 연동하지 않는다.
const decreaser = makeCounter(decrease); // ②
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

`makeCounter` 함수를 호출해 함수를 반환할 때 반환된 함수는 **자신만의 독립된 렉시컬 환경**을 가진다. 함수를 호출하면 그때마다 새로운 `makeCounter` 함수 실행 컨텍스트의 렉시컬 환경이 생성되기 때문이다.

① `makeCounter` 함수 호출 - `makeCounter` 함수의 실행 컨텍스트 생성 - 함수 객체를 생성하여 반환 - 소멸

`makeCounter` 함수의 실행 컨켁스트는 소멸되지만 렉시컬 환경은 참조되고 있기 때문에 소멸되지 않는다.

② 도 마찬가지로 실행되고, 참조된 환경은 소멸되지 않는다.

이 예시에서 전역 변수 `increaser`과 `decreaser`에 할당된 함수는 각각 자신만의 독립된 렉시컬 환경을 가지기 때문에 카운트를 유지하기 위한 `counter`를 공유하지 않아 숫자의 증가 감소가 연동되지 않는다.


###### 예제 24-15

```javascript
// 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
const counter = (function () {
  // 카운트 상태를 유지하기 위한 자유 변수
  let counter = 0;

  // 함수를 인수로 전달받는 클로저를 반환
  return function (aux) {
    // 인수로 전달 받은 보조 함수에 상태 변경을 위임한다.
    counter = aux(counter);
    return counter;
  };
}());

// 보조 함수
function increase(n) {
  return ++n;
}

// 보조 함수
function decrease(n) {
  return --n;
}

// 보조 함수를 전달하여 호출
console.log(counter(increase)); // 1
console.log(counter(increase)); // 2

// 자유 변수를 공유한다.
console.log(counter(decrease)); // 1
console.log(counter(decrease)); // 0
```

렉시컬 환경을 공유하는 클로저를 만들기 위해 `makeCounter` 함수를 두 번 호출하지 않는 코드이다.

즉시 실행 함수 내부에서 `counter` 라는 클로저를 정의하고, 해당 클로저를 반환한다. 이렇게 반환된 클로저는 인수로 전달된 보조 함수에 상태 변경을 위임하며, 자유 변수인 `counter`을 공유한다.

즉시 실행 함수 내부에서 생성된 단일한 클로저만을 사용하여 카운터 상태를 유지한다. 같은 클로저에 대해 여러 번 호출하는 경우, 보조 함수에 의해 변경된 카운터 값이 계속해서 유지된다.

------
## 24.5 캡슐화와 정보 은닉

캡슐화 : 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것

정보 은닉 : 객체의 특정 프로퍼티나 메서드를 감추는 것

캡슐화 하는 이유
: 1. 객체 간의 상호 의존성 = 결합도를 낮춘다.
: 2. 외부에 구현의 일부를 공개되지 않도록 감추어 적절치 못한 접근으로부터 객체의 상태가 변경되는 것을 방지해 정보를 보호한다.

객체의 모든 프로퍼티와 메서드는 기본적으로 public 하다.

###### 예제 24-16

```javascript
function Person(name, age) {
  this.name = name; // public
  let _age = age;   // private

  // 인스턴스 메서드
  this.sayHi = function () {
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };
}

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined
```

`name` 프로퍼티는 생성자 함수 내부에서 인스턴스 객체에 동적으로 속성을 추가하는 방식이고, public 해서 자유롭게 참조, 변경 가능하다.

`_age` 변수는 `Person` 생성자 함수의 지역 변수여서 `Person` 생성자 함수 외부에서 참조, 변경할 수 없다. = private 하다.


###### 예제 24-17

```javascript
function Person(name, age) {
  this.name = name; // public
  let _age = age;   // private
}

// 프로토타입 메서드
Person.prototype.sayHi = function () {
  // Person 생성자 함수의 지역 변수 _age를 참조할 수 없다
  console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
};
```

`sayHi` 메서드를 프로토타입 메서드로 변경하여 `sayHi` 메서드의 중복 생성을 방지한 코드이다.

`Person.prototype.sayHi` 메서드 내에서 `Person` 생성자 함수의 지역 변수 `_age`를 참조할 수 없는 문제가 발생한다.

###### 예제 24-18

```javascript
const Person = (function () {
  let _age = 0; // private

  // 생성자 함수
  function Person(name, age) {
    this.name = name; // public
    _age = age;
  }

  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };

  // 생성자 함수를 반환
  return Person;
}());

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined
```

즉시 실행 함수를 사용하여 `Person` 생성자 함수와 `Person.prototype.sayHi` 메서드를 하나의 함수 내에 모은 코드이다.

`Person.prototype.sayHi`는 즉시 실행 함수가 종료된 이후 호출된다.

`Person` 생성자 함수와 `sayHi` 메서드는 이미 종료되어 소멸한 즉시 실행 함수의 지역 변수 `_age`를 참조할 수 있는 **클로저**이다.

`Person` 생성자 함수가 여러 개의 인스턴스를 생성할 경우 `_age`변수의 상태가 유지되지 않는다.

###### 예제 24-19

```javascript
const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.

// _age 변수 값이 변경된다!
me.sayHi(); // Hi! My name is Lee. I am 30.
```

새로운 `Person` 인스턴스를 생성할 때마다 `_age` 값이 변경된다. 마지막으로 생성된 인스턴스의 나이 (`_age`)가 이전 모든 인스턴스의 나이로 설정된다.

`_age`변수가 모든 인스턴스 간에 공유되어서 마지막으로 생성된 `you` 객체의 나이(30)가 이전에 만들어진 `me` 객체의 나이(20)를 덮어쓴 것이다.


자바스크립트는 정보 은닉을 완전하게 지원하지 않는다. 인스턴스 메서드를 사용하면 자유 변수를 통해 흉내 낼 수은 있지만 프로토타입 메서드를 사용하면 불가능하다.

클래스에 private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어 있다. 25.7.4절에서 자세히 볼 수 있다.

------
## 24.6 자주 발생하는 실수

###### 예제 24-20

```javascript
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () { return i; }; // ①
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // ②
}
```

어떤 값이 출력될까요? `3, 3, 3`

우리가 예상한 결과는?   `0, 1, 2`

문제의 원인이 무엇일까요? `클로저와 변수 스코프`

① 에서 익명 함수가 생성될 때 그 함수는 `i` 에 대한 참조를 유지한다. for 루프가 끝난 후의 `i` 값은 `3`이다. `funcs` 배열 안의 모든 함수들이 동일한 i ( 3 )를 참조하게 되므로 모든 함수 호출 결과가 `3`이 된다.

###### 예제 24-21

```javascript
var funcs = [];

for (var i = 0; i < 3; i++){
  funcs[i] = (function (id) { // ①
    return function () {
      return id;
    };
  }(i));
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

클로저를 사용해 바르게 동작하는 코드로 만들어보았다.

클로저와 즉시 실행 함수 표현식을 사용하여 각 함수가 자신만의 고유한 `i` 값을 가지도록 한다.

1. ①에서 각 반복마다 즉시 실행 함수 표현식이 호출된다.
2. 이 함수는 다시 내부에 있는 익명함수를 반환한다.
3. 반환된 함수가 각각의 `funcs[i]` 에 저장된다.
+ 반환된 익명함수가 즉시 실행 함수의 스코프에서 생성되었기 때문에, 해당 스코프 내부의 변수인 `id`를 계속 참조하게 된다. = 클로저가 형성된다.
4. `funcs[i]`에 저장된 함수는 자신만의 고유한 `id`를 가지게 된다.
5. 원래 기대한 결과인 `'0', '1', '2'` 출력

`let` 키워드를 사용할 수 없는 환경에서 for 루프와 클로저를 함께 사용할 때 발생하는 문제를 해결하는 방법이다.


###### 예제 24-22

```javascript
const funcs = [];

for (let i = 0; i < 3; i++) { ①
  funcs[i] = function () { return i; };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]()); // 0 1 2
}
```

그냥 `let` 키워드를 쓰면 간단하게 해결 가능하다.

1. ① 에서, 각 반복마다 `let i = 0;` 구문에 의해 새로운 `i` 변수가 선언되고 초기화된다. 이때 `let` 키워드는 블록 스코프(block scope)를 가지므로, 각 반복마다 새로운 스코프가 형성됩니다.
2. 익명 함수 `function () { return i; }`가 생성되어 `funcs[i]` 위치에 저장된다. 이 함수는 자신을 생성한 스코프의 변수인 `i`에 대한 참조를 유지한다. = 클로저가 형성된다.
3. 각 반복마다 새롭게 선언된 `i` 때문에 각각의 클로저(익명함수)는 서로 다른 `i`를 참조하게 된다.
4. 원래 기대한 결과인 `'0', '1', '2'` 출력


`let`이나 `const` 키워드를 사용하는 반복문은 코드 블록을 반복 실행할 때마다 새로운 렉시컬 환경을 생성하여 반복할 당시의 상태를 마치 스냅샷처럼 저장한다. 반복문의 코드 블록 **내부**에서 함수를 정의할 때 의미가 있다.
###### 예제 24-23

```javascript
// 요소가 3개인 배열을 생성하고 배열의 인덱스를 반환하는 함수를 요소로 추가한다.
// 배열의 요소로 추가된 함수들은 모두 클로저다.
const funcs = Array.from(new Array(3), (_, i) => () => i); // (3) [ƒ, ƒ, ƒ]

// 배열의 요소로 추가된 함수 들을 순차적으로 호출한다.
funcs.forEach(f => console.log(f())); // 0 1 2
```
함수형 프로그래밍 기법인 고차 함수를 사용하는 방법도 있다.
아직 살펴보지 않은 내용이니 알아만 두는게 좋겠다.!!


------
