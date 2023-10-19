- [26장 ES6 함수의 추가 기능](#26장-es6-함수의-추가-기능)
- [26.1 함수의 구분](#261-함수의-구분)
    - [26-01](#26-01)
    - [26-02](#26-02)
    - [26-03](#26-03)
    - [26-04](#26-04)
- [26.2 메서드](#262-메서드)
    - [26-05](#26-05)
    - [26-06](#26-06)
    - [26-07](#26-07)
    - [26-08](#26-08)
    - [26-09](#26-09)
    - [26-10](#26-10)
- [26.3 화살표 함수](#263-화살표-함수)
  - [26.3.1 화살표 함수 정의](#2631-화살표-함수-정의)
    - [26-11](#26-11)
    - [26-12](#26-12)
    - [26-13](#26-13)
    - [26-14](#26-14)
    - [26-15](#26-15)
    - [26-16](#26-16)
    - [26-17](#26-17)
    - [26-18](#26-18)
    - [26-19](#26-19)
    - [26-20](#26-20)
    - [26-21](#26-21)
    - [26-22](#26-22)
  - [26.3.2 화살표 함수와 일반 함수의 차이](#2632-화살표-함수와-일반-함수의-차이)
    - [26-23](#26-23)
    - [26-24](#26-24)
    - [26-25](#26-25)
    - [26-26](#26-26)
    - [26-27](#26-27)
  - [26.3.3 this](#2633-this)
    - [26-28](#26-28)
    - [26-29](#26-29)
    - [26-30](#26-30)
    - [26-31](#26-31)
    - [26-32](#26-32)
    - [26-33](#26-33)
    - [26-34](#26-34)
    - [26-35](#26-35)
    - [26-36](#26-36)
    - [26-37](#26-37)
    - [26-38](#26-38)
    - [26-39](#26-39)
    - [26-40](#26-40)
    - [26-41](#26-41)
    - [26-42](#26-42)
    - [26-43](#26-43)
    - [26-44](#26-44)
    - [26-45](#26-45)
    - [26-46](#26-46)
  - [26.3.4 super](#2634-super)
    - [26-47](#26-47)
  - [26.3.5 arguments](#2635-arguments)
    - [26-48](#26-48)
- [26.4 Rest 파라미터](#264-rest-파라미터)
  - [26.4.1. 기본 문법](#2641-기본-문법)
    - [26-49](#26-49)
    - [26-50](#26-50)
    - [26-51](#26-51)
    - [26-52](#26-52)
    - [26-53](#26-53)
  - [26.4.2 Rest 파라미터와 arguments 객체](#2642-rest-파라미터와-arguments-객체)
    - [26-54](#26-54)
    - [26-55](#26-55)
    - [26-56](#26-56)
- [26.5 매개변수 기본값](#265-매개변수-기본값)
    - [26-57](#26-57)
    - [26-58](#26-58)
    - [26-59](#26-59)
    - [26-60](#26-60)
    - [26-61](#26-61)
    - [26-62](#26-62)

# 26장 ES6 함수의 추가 기능

# 26.1 함수의 구분

- ES6 이전의 함수
  - 일반적인 함수
  - 인스턴스를 생성할 수 있는 생성자 함수
  - 메서드(객체 바인딩)

### 26-01

```javascript
var foo = function () {
  return 1;
};

// 일반적인 함수로서 호출
foo(); // -> 1

// 생성자 함수로서 호출
new foo(); // -> foo {}

// 메서드로서 호출
var obj = { foo: foo };
obj.foo(); // -> 1
```

### 26-02

```javascript
var foo = function () {};

// ES6 이전의 모든 함수는 callable이면서 constructor다.
foo(); // -> undefined
new foo(); // -> foo {}
```

=> 모든 함수가 callable(호출할 수 있는 함수 객체)이면서 constructor(인스턴스를 생성할 수 있는 함수 객체)임.  
메서드(객체에 바인딩된 함수)도 callable이며 constructor인 것.

### 26-03

```javascript
// 프로퍼티 f에 바인딩된 함수는 callable이며 constructor다.
var obj = {
  x: 10,
  f: function () {
    return this.x;
  },
};

// 프로퍼티 f에 바인딩된 함수를 메서드로서 호출
console.log(obj.f()); // 10

// 프로퍼티 f에 바인딩된 함수를 일반 함수로서 호출
var bar = obj.f;
console.log(bar()); // undefined

// 프로퍼티 f에 바인딩된 함수를 생성자 함수로서 호출
console.log(new obj.f()); // f {}
```

메서드가 constructor라는 것은 prototype 프로퍼티를 가지며, 프로토타입 객체도 생성한다는 뜻 => `성능 문제`

### 26-04

함수에 전달되어 보조 함수의 역할을 하는 콜백 함수도 constructor이기 때문에 `불필요한` 프로토타입 객체를 생성.  
생성자 함수로 호출되지 않아도 프로토타입을 생성하는 것.

```javascript
// 콜백 함수를 사용하는 고차 함수 map. 콜백 함수도 constructor이며 프로토타입을 생성한다.
[1, 2, 3].map(function (item) {
  return item * 2;
}); // -> [ 2, 4, 6 ]
```

- ES6에서 구분한 함수
  - 일반 함수 : constructor, prototype, arguments
  - 메서드 : super, arguments - non-constuctor
  - 화살표 함수 - non-constructor

# 26.2 메서드

ES6 사양에서 정의된 메서드 : 메서드 축약 표현으로 정의된 함수

### 26-05

```javascript
const obj = {
  x: 1,
  // foo는 메서드이다.
  foo() {
    return this.x;
  },
  // bar에 바인딩된 함수는 메서드가 아닌 일반 함수이다.
  bar: function () {
    return this.x;
  },
};

console.log(obj.foo()); // 1
console.log(obj.bar()); // 1
```

non-constructor => 생성자 함수로 호출할 수 없음 => 인스턴스 생성 못하므로 prototype 프로퍼티가 없고, 프로토타입도 생성하지 않음

### 26-06

```javascript
new obj.foo(); // -> TypeError: obj.foo is not a constructor
new obj.bar(); // -> bar {}
```

### 26-07

```javascript
// obj.foo는 constructor가 아닌 ES6 메서드이므로 prototype 프로퍼티가 없다.
obj.foo.hasOwnProperty("prototype"); // -> false

// obj.bar는 constructor인 일반 함수이므로 prototype 프로퍼티가 있다.
obj.bar.hasOwnProperty("prototype"); // -> true
```

### 26-08

표준 빌트인 객체가 제공하는 프로토타입 메서드, 정적 메서드 모두 non-constructor.

```javascript
String.prototype.toUpperCase.prototype; // -> undefined
String.fromCharCode.prototype; // -> undefined

Number.prototype.toFixed.prototype; // -> undefined
Number.isFinite.prototype; // -> undefined

Array.prototype.map.prototype; // -> undefined
Array.from.prototype; // -> undefined
```

ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 가짐. super 참조는 내부 슬롯을 사용해 수퍼클래스의 메서드를 참조하므로 super 키워드 사용할 수 있음.

### 26-09

```javascript
const base = {
  name: "Lee",
  sayHi() {
    return `Hi! ${this.name}`;
  },
};

const derived = {
  __proto__: base,
  // sayHi는 ES6 메서드다. ES6 메서드는 [[HomeObject]]를 갖는다.
  // sayHi의 [[HomeObject]]는 sayHi가 바인딩된 객체인 derived를 가리키고
  // super는 sayHi의 [[HomeObject]]의 프로토타입인 base를 가리킨다.
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  },
};

console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

ES6 메서드가 아닌 함수는 super 키워드 사용할 수 없음. 내부 슬롯 [[HomeObject]]를 갖지 않기 때문.

### 26-10

```javascript
const derived = {
  __proto__: base,
  // sayHi는 ES6 메서드가 아니다.
  // 따라서 sayHi는 [[HomeObject]]를 갖지 않으므로 super 키워드를 사용할 수 없다.
  sayHi: function () {
    // SyntaxError: 'super' keyword unexpected here
    return `${super.sayHi()}. how are you doing?`;
  },
};
```

=> 메서드는 본연의 기능(super)을 추가, 의미적으로 맞지 않는 기능(constructor) 제거.

# 26.3 화살표 함수

## 26.3.1 화살표 함수 정의

### 26-11

함수 정의 : 함수 표현식으로 해야 함

```javascript
const multiply = (x, y) => x * y;
multiply(2, 3); // -> 6
```

### 26-12

매개변수가 여러 개일 경우 소괄호 안에 선언

```javascript
const arrow = (x, y) => { ... };
```

### 26-13

매개변수가 한 개라면 소괄호 생략 가능

```javascript
const arrow = x => { ... };
```

### 26-14

매개변수가 없는 경우 소괄호 생략할 수 없음

```javascript
const arrow = () => { ... };
```

### 26-15

함수 몸체가 하나의 문이라면 중괄호 생략 가능. 하나의 문이 값으로 표현될 수 있다면 암묵적으로 반환함(return)

```javascript
// concise body
const power = (x) => x ** 2;
power(2); // -> 4

// 위 표현은 다음과 동일하다.
// block body
const power = (x) => {
  return x ** 2;
};
```

### 26-16

함수 몸체 내부의 문이 표현식이 아닌 문이라면 에러 발생. 반환할 수 없기 때문

```javascript
const arrow = () => const x = 1; // SyntaxError: Unexpected token 'const'

// 위 표현은 다음과 같이 해석된다.
const arrow = () => { return const x = 1; };
```

### 26-17

하나의 문이라도 표현식이 아닌 문이라면 중괄호를 생략하면 안됨.

```javascript
const arrow = () => {
  const x = 1;
};
```

### 26-18

객체 리터럴은 소괄호로 감싸 주어야 함.

```javascript
const create = (id, content) => ({ id, content });
create(1, "JavaScript"); // -> {id: 1, content: "JavaScript"}

// 위 표현은 다음과 동일하다.
const create = (id, content) => {
  return { id, content };
};
```

### 26-19

객체 리터럴의 중괄호를 함수 몸체를 감싸는 중괄호로 잘못 해석하기 때문(중의적 해석)

```javascript
// { id, content }를 함수 몸체 내의 쉼표 연산자문으로 해석한다.
const create = (id, content) => {
  id, content;
};
create(1, "JavaScript"); // -> undefined
```

### 26-20

함수 몸체가 여러 개의 문이라면 중괄호 생략할 수 없고, 반환값 명시해야 함

```javascript
const sum = (a, b) => {
  const result = a + b;
  return result;
};
```

### 26-21

즉시 실행 함수로 사용 가능

```javascript
const person = ((name) => ({
  sayHi() {
    return `Hi? My name is ${name}.`;
  },
}))("Lee");

console.log(person.sayHi()); // Hi? My name is Lee.
```

### 26-22

화살표 함수도 일급 객체이므로 고차 함수에 인수로 전달 가능. 일반적인 함수 표현식보다 표현 간결, 가독성 좋음

```javascript
// ES5
[1, 2, 3].map(function (v) {
  return v * 2;
});

// ES6
[1, 2, 3].map((v) => v * 2); // -> [ 2, 4, 6 ]
```

## 26.3.2 화살표 함수와 일반 함수의 차이

### 26-23

1. 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor이다.

```javascript
const Foo = () => {};
// 화살표 함수는 생성자 함수로서 호출할 수 없다.
new Foo(); // TypeError: Foo is not a constructor
```

### 26-24

- prototype 프로퍼티가 없고 프로토타입도 생성하지 않음.

```javascript
const Foo = () => {};
// 화살표 함수는 prototype 프로퍼티가 없다.
Foo.hasOwnProperty("prototype"); // -> false
```

2. 중복된 매개변수 선언할 수 없음

### 26-25

(일반 함수는 중복된 매개변수 이름 선언해도 에러 발생하지 않음)

```javascript
function normal(a, a) {
  return a + a;
}
console.log(normal(1, 2)); // 4
```

### 26-26

(strict mode에서는 에러 발생)

```javascript
"use strict";

function normal(a, a) {
  return a + a;
}
// SyntaxError: Duplicate parameter name not allowed in this context
```

### 26-27

화살표 함수에는 중복된 매개변수 이름 선언 시 에러 발생

```javascript
const arrow = (a, a) => a + a;
// SyntaxError: Duplicate parameter name not allowed in this context
```

3. 화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 갖지 않음 => 함수 내부에서 참조 시 상위 스코프의 것을 참조. 화살표 함수끼리 중첩되어 있다면 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 것을 참조.

## 26.3.3 this

`콜백 함수 내부의 this 문제` : 콜백 함수의 this와 외부 함수의 this가 서로 다른 값을 가리키기 때문에 TypeError 발생. => 해결 방법(예제 29~31)

### 26-28

```javascript
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    // add 메서드는 인수로 전달된 배열 arr을 순회하며 배열의 모든 요소에 prefix를 추가한다.
    // ①
    return arr.map(function (item) {
      return this.prefix + item; // ②
      // -> TypeError: Cannot read property 'prefix' of undefined
    });
  }
}

const prefixer = new Prefixer("-webkit-");
console.log(prefixer.add(["transition", "user-select"]));
```

### 26-29

```javascript
...
add(arr) {
  // this를 일단 회피시킨다.
  const that = this;
  return arr.map(function (item) {
    // this 대신 that을 참조한다.
    return that.prefix + ' ' + item;
  });
}
...
```

### 26-30

```javascript
...
add(arr) {
  return arr.map(function (item) {
    return this.prefix + ' ' + item;
  }, this); // this에 바인딩된 값이 콜백 함수 내부의 this에 바인딩된다.
}
...
```

### 26-31

```javascript
...
add(arr) {
  return arr.map(function (item) {
    return this.prefix + ' ' + item;
  }.bind(this)); // this에 바인딩된 값이 콜백 함수 내부의 this에 바인딩된다.
}
...
```

### 26-32

화살표 함수는 함수 자체의 this 바인딩을 갖지 않음. 화살표 함수 내부에서 this를 사용하면 상위 스코프의 this를 그대로 참조 => lexical this.

```javascript
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    return arr.map((item) => this.prefix + item);
  }
}

const prefixer = new Prefixer("-webkit-");
console.log(prefixer.add(["transition", "user-select"]));
// ['-webkit-transition', '-webkit-user-select']
```

### 26-33

```javascript
// 화살표 함수는 상위 스코프의 this를 참조한다.
() => this.x;

// 익명 함수에 상위 스코프의 this를 주입한다. 위 화살표 함수와 동일하게 동작한다.
(function () {
  return this.x;
}).bind(this);
```

### 26-34

```javascript
// 중첩 함수 foo의 상위 스코프는 즉시 실행 함수다.
// 따라서 화살표 함수 foo의 this는 상위 스코프인 즉시 실행 함수의 this를 가리킨다.
(function () {
  const foo = () => console.log(this);
  foo();
}).call({ a: 1 }); // { a: 1 }

// bar 함수는 화살표 함수를 반환한다.
// bar 함수가 반환한 화살표 함수의 상위 스코프는 화살표 함수 bar다.
// 하지만 화살표 함수는 함수 자체의 this 바인딩을 갖지 않으므로 bar 함수가 반환한
// 화살표 함수 내부에서 참조하는 this는 화살표 함수가 아닌 즉시 실행 함수의 this를 가리킨다.
(function () {
  const bar = () => () => console.log(this);
  bar()();
}).call({ a: 1 }); // { a: 1 }
```

### 26-35

전역 함수라면 this는 전역 객체 가리킴. 상위 스코프가 전역이기 때문.

```javascript
// 전역 함수 foo의 상위 스코프는 전역이므로 화살표 함수 foo의 this는 전역 객체를 가리킨다.
const foo = () => console.log(this);
foo(); // window
```

### 26-36

```javascript
// increase 프로퍼티에 할당한 화살표 함수의 상위 스코프는 전역이다.
// 따라서 increase 프로퍼티에 할당한 화살표 함수의 this는 전역 객체를 가리킨다.
const counter = {
  num: 1,
  increase: () => ++this.num,
};

console.log(counter.increase()); // NaN
```

### 26-37

```javascript
window.x = 1;

const normal = function () {
  return this.x;
};
const arrow = () => this.x;

console.log(normal.call({ x: 10 })); // 10
console.log(arrow.call({ x: 10 })); // 1
```

### 26-38

```javascript
const add = (a, b) => a + b;

console.log(add.call(null, 1, 2)); // 3
console.log(add.apply(null, [1, 2])); // 3
console.log(add.bind(null, 1, 2)()); // 3
```

### 26-39

메서드를 화살표 함수로 정의하는 것은 피해야 함. this가 person이 아닌 상위 스코프인 전역 객체를 가리키기 때문.

```javascript
// Bad
const person = {
  name: "Lee",
  sayHi: () => console.log(`Hi ${this.name}`),
};

// sayHi 프로퍼티에 할당된 화살표 함수 내부의 this는 상위 스코프인 전역의 this가 가리키는
// 전역 객체를 가리키므로 이 예제를 브라우저에서 실행하면 this.name은 빈 문자열을 갖는
// window.name과 같다. 전역 객체 window에는 빌트인 프로퍼티 name이 존재한다.
person.sayHi(); // Hi
```

### 26-40

메서드를 정의할 때는, 메서드 축약 표현으로 정의한 ES6 메서드를 사용하는 것이 좋음

```javascript
// Good
const person = {
  name: "Lee",
  sayHi() {
    console.log(`Hi ${this.name}`);
  },
};

person.sayHi(); // Hi Lee
```

### 26-41

```javascript
// Bad
function Person(name) {
  this.name = name;
}

Person.prototype.sayHi = () => console.log(`Hi ${this.name}`);

const person = new Person("Lee");
// 이 예제를 브라우저에서 실행하면 this.name은 빈 문자열을 갖는 window.name과 같다.
person.sayHi(); // Hi
```

### 26-42

```javascript
// Good
function Person(name) {
  this.name = name;
}

Person.prototype.sayHi = function () {
  console.log(`Hi ${this.name}`);
};

const person = new Person("Lee");
person.sayHi(); // Hi Lee
```

### 26-43

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype = {
  // constructor 프로퍼티와 생성자 함수 간의 연결을 재설정
  constructor: Person,
  sayHi() {
    console.log(`Hi ${this.name}`);
  },
};

const person = new Person("Lee");
person.sayHi(); // Hi Lee
```

### 26-44

```javascript
// Bad
class Person {
  // 클래스 필드 정의 제안
  name = "Lee";
  sayHi = () => console.log(`Hi ${this.name}`);
}

const person = new Person();
person.sayHi(); // Hi Lee
```

### 26-45

```javascript
class Person {
  constructor() {
    this.name = "Lee";
    // 클래스가 생성한 인스턴스(this)의 sayHi 프로퍼티에 화살표 함수를 할당한다.
    // sayHi 프로퍼티는 인스턴스 프로퍼티이다.
    this.sayHi = () => console.log(`Hi ${this.name}`);
  }
}
```

### 26-46

```javascript
// Good
class Person {
  // 클래스 필드 정의
  name = "Lee";

  sayHi() {
    console.log(`Hi ${this.name}`);
  }
}
const person = new Person();
person.sayHi(); // Hi Lee
```

## 26.3.4 super

화살표 함수는 함수 자체의 super 바인딩을 갖지 않음. this와 마찬가지로 상위 스코프의 super 참조.

### 26-47

```javascript
class Base {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    return `Hi! ${this.name}`;
  }
}

class Derived extends Base {
  // 화살표 함수의 super는 상위 스코프인 constructor의 super를 가리킨다.
  sayHi = () => `${super.sayHi()} how are you doing?`;
}

const derived = new Derived("Lee");
console.log(derived.sayHi()); // Hi! Lee how are you doing?
```

## 26.3.5 arguments

화살표 함수는 함수 자체의 arguments 바인딩을 갖지 않음. this와 마찬가지로 상위 스코프의 arguments 참조.

### 26-48

```javascript
(function () {
  // 화살표 함수 foo의 arguments는 상위 스코프인 즉시 실행 함수의 arguments를 가리킨다.
  const foo = () => console.log(arguments); // [Arguments] { '0': 1, '1': 2 }
  foo(3, 4);
})(1, 2);

// 화살표 함수 foo의 arguments는 상위 스코프인 전역의 arguments를 가리킨다.
// 하지만 전역에는 arguments 객체가 존재하지 않는다. arguments 객체는 함수 내부에서만 유효하다.
const foo = () => console.log(arguments);
foo(1, 2); // ReferenceError: arguments is not defined
```

# 26.4 Rest 파라미터

## 26.4.1. 기본 문법

매개변수 이름 앞에 ...을 붙여서 정의한 매개변수 : 함수에 전달된 인수들의 목록을 배열로 전달 받음.

### 26-49

```javascript
function foo(...rest) {
  // 매개변수 rest는 인수들의 목록을 배열로 전달받는 Rest 파라미터다.
  console.log(rest); // [ 1, 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);
```

### 26-50

일반 매개변수와 Rest 파라미터는 함께 사용할 수 있음. 순차적으로 할당됨.

```javascript
function foo(param, ...rest) {
  console.log(param); // 1
  console.log(rest); // [ 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);

function bar(param1, param2, ...rest) {
  console.log(param1); // 1
  console.log(param2); // 2
  console.log(rest); // [ 3, 4, 5 ]
}

bar(1, 2, 3, 4, 5);
```

### 26-51

먼저 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이 할당됨. 반드시 마지막 파라미터이어야 함.

```javascript
function foo(...rest, param1, param2) { }

foo(1, 2, 3, 4, 5);
// SyntaxError: Rest parameter must be last formal parameter
```

### 26-52

단 하나만 선언할 수 있음

```javascript
function foo(...rest1, ...rest2) { }

foo(1, 2, 3, 4, 5);
// SyntaxError: Rest parameter must be last formal parameter
```

### 26-53

함수 객체의 length 프로퍼티(매개변수 개수)에 영향을 주지 않음

```javascript
function foo(...rest) {}
console.log(foo.length); // 0

function bar(x, ...rest) {}
console.log(bar.length); // 1

function baz(x, y, ...rest) {}
console.log(baz.length); // 2
```

## 26.4.2 Rest 파라미터와 arguments 객체

ES5까지, 가변 인자 함수의 경우 arguments 객체를 활용해 인자를 유사 배열 객체 모양으로 전달 받아 사용함

### 26-54

```javascript
// 매개변수의 개수를 사전에 알 수 없는 가변 인자 함수
function sum() {
  // 가변 인자 함수는 arguments 객체를 통해 인수를 전달받는다.
  console.log(arguments);
}

sum(1, 2); // {length: 2, '0': 1, '1': 2}
```

### 26-55

배열 메서드를 사용하려면 Function.prototype.call, Function.prototype.apply 메서드를 사용해 배열로 변환해야 했음

```javascript
function sum() {
  // 유사 배열 객체인 arguments 객체를 배열로 변환한다.
  var array = Array.prototype.slice.call(arguments);

  return array.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```

### 26-56

Rest 파라미터로 가변 인자 함수의 인수 목록을 배열로 직접 전달받을 수 있음.

```javascript
function sum(...args) {
  // Rest 파라미터 args에는 배열 [1, 2, 3, 4, 5]가 할당된다.
  return args.reduce((pre, cur) => pre + cur, 0);
}
console.log(sum(1, 2, 3, 4, 5)); // 15
```

- 함수
- ES6 메서드
  - Rest 파라미터, arguments 객체를 모두 사용할 수 있음
- 화살표 함수
  - arguments 객체를 갖지 않아 가반 인자 함수 구현 시 반드시 Rest 파라미터 사용해야 함.

# 26.5 매개변수 기본값

자바스크립트 엔진은 매개변수 개수, 인수 개수를 체크하지 않아 달라도 에러가 발생하지 않음.  
인수가 전달되지 않은 매개변수의 값은 undefined임. => 의도치 않은 문제가 생길 수 있음

### 26-57

```javascript
function sum(x, y) {
  return x + y;
}

console.log(sum(1)); // NaN
```

### 26-58

인수가 전달되지 않은 경우 매개변수에 기본값을 할당할 필요가 있음(방어 코드).

```javascript
function sum(x, y) {
  // 인수가 전달되지 않아 매개변수의 값이 undefined인 경우 기본값을 할당한다.
  x = x || 0;
  y = y || 0;

  return x + y;
}

console.log(sum(1, 2)); // 3
console.log(sum(1)); // 1
```

### 26-59

ES6의 매개변수 기본값 사용하면 인수 체크 및 초기화를 간소화할 수 있음

```javascript
function sum(x = 0, y = 0) {
  return x + y;
}

console.log(sum(1, 2)); // 3
console.log(sum(1)); // 1
```

### 26-60

매개변수 기본값은 인수를 전달하지 않은 경우나 undefined를 전달한 경우에만 유효함

```javascript
function logName(name = "Lee") {
  console.log(name);
}

logName(); // Lee
logName(undefined); // Lee
logName(null); // null
```

### 26-61

Rest 파라미터에는 기본값 지정할 수 없음

```javascript
function foo(...rest = []) {
  console.log(rest);
}
// SyntaxError: Rest parameter may not have a default initializer
```

### 26-62

매개변수 기본값은 함수 정의 시 선언한 매개변수 개수를 나타내는 함수 객체의 length 프로퍼티와 arguments 객체에 아무런 영향을 주지 않음

```javascript
function sum(x, y = 0) {
  console.log(arguments);
}

console.log(sum.length); // 1

sum(1); // Arguments { '0': 1 }
sum(1, 2); // Arguments { '0': 1, '1': 2 }
```
