- [17장 생성자 함수에 의한 객체 생성](#17장-생성자-함수에-의한-객체-생성)
- [17.1 Obejct 생성자 함수](#171-obejct-생성자-함수)
  - [17-01](#17-01)
  - [17-02](#17-02)
- [17.2 생성자 함수](#172-생성자-함수)
  - [17.12.1 객체 리터럴에 의한 객체 생성 방식의 문제점](#17121-객체-리터럴에-의한-객체-생성-방식의-문제점)
    - [17-03](#17-03)
  - [17.2.2 생성자 함수에 의한 객체 생성 방식의 장점](#1722-생성자-함수에-의한-객체-생성-방식의-장점)
    - [17-04](#17-04)
    - [17-06](#17-06)
  - [17.2.3 생성자 함수의 인스턴스 생성 과정](#1723-생성자-함수의-인스턴스-생성-과정)
    - [1. 인스턴스 생성과 this 바인딩](#1-인스턴스-생성과-this-바인딩)
    - [2. 인스턴스 초기화](#2-인스턴스-초기화)
    - [3. 인스턴스 반환](#3-인스턴스-반환)
      - [17-12](#17-12)
  - [17.2.4 내부 메서드 `[[Call]]`과 `[[Construct]]`](#1724-내부-메서드-call과-construct)
    - [17-14](#17-14)
  - [17.2.5 constructor과 non-construcotr의 구분](#1725-constructor과-non-construcotr의-구분)
    - [17-15](#17-15)
    - [17-16](#17-16)
  - [17.2.6 new 연산자](#1726-new-연산자)
    - [17-17](#17-17)
    - [17-18](#17-18)
  - [new.target](#newtarget)
    - [17-19](#17-19)
    - [17-20](#17-20)
    - [17-22](#17-22)

# 17장 생성자 함수에 의한 객체 생성

1. Object 생성자 함수와 객체 리터럴의 차이를 설명할 수 있다.
2. 생성자 함수의 인스턴스 생성 과정에 대해 설명할 수 있다.
3. 함수 객체를 호출할 수 있는 이유를 설명할 수 있다.
4. `constructor`와 `non-constructor`에 대해 설명할 수 있다.
5. `new` 연산자와 `new.target`에 대해 설명할 수 있다.

# 17.1 Obejct 생성자 함수

### 17-01

- 빈 객체 = new 연산자 + Obejct 생성자 함수
- 프로퍼티 또는 메서드 추가

```javascript
// 빈 객체의 생성
const person = new Object();

// 프로퍼티 추가
person.name = "Lee";
person.sayHello = function () {
  console.log("Hi! My name is " + this.name);
};

console.log(person); // {name: "Lee", sayHello: ƒ}
person.sayHello(); // Hi! My name is Lee
```

`생성자 함수`

- new 연산자와 함께 호출하여 인스턴스(객체)를 생성하는 함수
- String, Number, Boolean, Function, Array, Date, RegExp, Promise 등 빌트인 생성자 함수

### 17-02

```javascript
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String("Lee");
console.log(typeof strObj); // object
console.log(strObj); // String {"Lee"}

// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(123);
console.log(typeof numObj); // object
console.log(numObj); // Number {123}

// Boolean 생성자 함수에 의한 Boolean 객체 생성
const boolObj = new Boolean(true);
console.log(typeof boolObj); // object
console.log(boolObj); // Boolean {true}

// Function 생성자 함수에 의한 Function 객체(함수) 생성
const func = new Function("x", "return x * x");
console.log(typeof func); // function
console.dir(func); // ƒ anonymous(x)

// Array 생성자 함수에 의한 Array 객체(배열) 생성
const arr = new Array(1, 2, 3);
console.log(typeof arr); // object
console.log(arr); // [1, 2, 3]

// RegExp 생성자 함수에 의한 RegExp 객체(정규 표현식) 생성
const regExp = new RegExp(/ab+c/i);
console.log(typeof regExp); // object
console.log(regExp); // /ab+c/i

// Date 생성자 함수에 의한 Date 객체 생성
const date = new Date();
console.log(typeof date); // object
console.log(date); // Mon May 04 2020 08:36:33 GMT+0900 (대한민국 표준시)
```

# 17.2 생성자 함수

## 17.12.1 객체 리터럴에 의한 객체 생성 방식의 문제점

객체 리터럴은 하나의 객체만 생성하기 때문에, 동일한 프로퍼티를 가질 경우 비효율적이다.

> **객체**: 프로퍼티를 통해 '객체 고유의 상태' 표현 <br> **메서드**: 프로퍼티를 참조하고 조작하는 '동작'을 표현

### 17-03

```javascript
const circle1 = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle1.getDiameter()); // 10

const circle2 = {
  radius: 10,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle2.getDiameter()); // 20
```

## 17.2.2 생성자 함수에 의한 객체 생성 방식의 장점

템플릿(클래스)처럼 프로퍼티 구조가 동일한 객체를 간편하게 생성할 수 있다.

### 17-04

```javascript
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 인스턴스의 생성
const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

### 17-06

new 연산자와 함께 호출할 시에만 생성자 함수로 동작한다.

```javascript
// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않는다.
// 즉, 일반 함수로서 호출된다.
const circle3 = Circle(15);

// 일반 함수로서 호출된 Circle은 반환문이 없으므로 암묵적으로 undefined를 반환한다.
console.log(circle3); // undefined

// 일반 함수로서 호출된 Circle내의 this는 전역 객체를 가리킨다.
console.log(radius); // 15
```

## 17.2.3 생성자 함수의 인스턴스 생성 과정

1. 인스턴스 생성
2. 생성된 인스턴스 초기화 = 인스턴스 프로퍼티 추가 및 초기값 할당

### 1. 인스턴스 생성과 this 바인딩

런타임 이전에 암묵적으로 빈 객체가 생성되며, this에 바인딩된다. (= 인스턴스의 this)

### 2. 인스턴스 초기화

this에 바인딩 된 인스턴스를 초기화한다.

- 프로퍼티나 메서드 추가
- 생성자 함수가 인수로 받은 초기값을 프로퍼티에 할당하여 초기화하거나 고정값을 할당

### 3. 인스턴스 반환

바인딩된 this를 암묵적으로 반환한다.
따라서, 명시적으로 반환하지 않으며, return 문은 생략한다.

#### 17-12

```javascript
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 암묵적으로 this를 반환한다.
  // 명시적으로 원시값을 반환하면 원시값 반환은 무시되고 암묵적으로 this가 반환된다.
  return 100;
}

// 인스턴스 생성. Circle 생성자 함수는 명시적으로 반환한 객체를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: ƒ}
```

## 17.2.4 내부 메서드 `[[Call]]`과 `[[Construct]]`

### 17-14

일반적인 함수로서는 내부 메서드 `[[Call]]`이, 생성자 함수로서는 내부 메서드 `[[Construct]]`가 호출된다.

```javascript
// 함수는 객체다.
function foo() {}

// 함수는 객체이므로 프로퍼티를 소유할 수 있다.
foo.prop = 10;

// 함수는 객체이므로 메서드를 소유할 수 있다.
foo.method = function () {
  console.log(this.prop);
};

foo.method(); // 10

// 일반적인 함수로서 호출: [[Call]]이 호출된다.
foo();

// 생성자 함수로서 호출: [[Construct]]가 호출된다.
new foo();
```

- callable: 내부 메서드 `[[Call]]`을 갖는 함수 객체로 호출할 수 있는 객체, 함수를 의미한다.
- constructor: 내부 메서드 `[[Construct]]`을 갖는 함수 객체, 생성자 함수로서 호출할 수 있는 함수를 의미한다.
- non-constructor: 내부 메서드 `[[Construct]]`을 갖지 않는 함수 객체, 생성자 함수로서 호출할 수 없는 함수를 의미한다.

**모든 함수 객체는 내부 메서드 `[[Call]]`을 갖고 있으므로 호출할 수 있다. <br> 모든 함수 객체는 호출할 수 있지만 모든 함수 객체를 생성자 함수로서 호출할 수 있는 것은 아니다.**

## 17.2.5 constructor과 non-construcotr의 구분

|      구분       |                   정의                    |
| :-------------: | :---------------------------------------: |
|   constructor   |     함수 선언문, 함수 표현식, 클래스      |
| non-constructor | 메서드(ES6 메서드 축약 표현), 화살표 함수 |

### 17-15

```javascript
// constructor: [일반 함수] 함수 선언문, 함수 표현식
function foo() {}
const bar = function () {};
// 프로퍼티 x의 값으로 할당된 것은 일반 함수로 정의된 함수다. 이는 메서드로 인정하지 않는다.
const baz = {
  x: function () {},
};

// 일반 함수로 정의된 함수만이 constructor이다.
new foo(); // -> foo {}
new bar(); // -> bar {}
new baz.x(); // -> x {}

// non-constructor: [화살표 함수 정의]
const arrow = () => {};

new arrow(); // TypeError: arrow is not a constructor

// non-constructor: [메서드 정의] ES6의 메서드 축약 표현
const obj = {
  x() {},
};

new obj.x(); // TypeError: obj.x is not a constructor
```

### 17-16

```javascript
function foo() {}

// 일반 함수로서 호출: [[Call]]이 호출된다. 모든 함수 객체에 구현
foo();

// 생성자 함수로서 호출: [[Construct]]가 호출된다.
// 이때 [[Construct]]를 갖지 않는다면 에러가 발생한다.
new foo();
```

> **주의** <br> 정의하지 않은 일반 함수(callable이면서 constructor)에 new 연산자 붙여 호출시 생성자 함수처럼 동작할 수 있다.

## 17.2.6 new 연산자

생성자 함수는 파스칼 케이스로 명명하여 일반 함수와 구별한다.

### 17-17

```javascript
// 생성자 함수로서 정의하지 않은 일반 함수
function add(x, y) {
  return x + y;
}

// 생성자 함수로서 정의하지 않은 일반 함수를 new 연산자와 함께 호출
let inst = new add();

// 함수가 객체를 반환하지 않았으므로 반환문이 무시된다. 따라서 빈 객체가 생성되어 반환된다.
console.log(inst); // {}

// 객체를 반환하는 일반 함수
function createUser(name, role) {
  return { name, role };
}

// 일반 함수를 new 연산자와 함께 호출
inst = new createUser("Lee", "admin"); // [[Constructor]]
// 함수가 생성한 객체를 반환한다.
console.log(inst); // {name: "Lee", role: "admin"}
```

### 17-18

```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수 호출하면 일반 함수로서 호출된다.
const circle = Circle(5); // [[Call]]
console.log(circle); // undefined

// 일반 함수 내부의 this는 전역 객체 window를 가리킨다.
console.log(radius); // 5
console.log(getDiameter()); // 10

circle.getDiameter();
// TypeError: Cannot read property 'getDiameter' of undefined
```

## new.target

메타 프로퍼티로, new 연산자와 함께 재귀 호출을 통해 생성자 함수로서 호출할 수 있다.

- new 연산자와 함께 생성자 함수로 호출시 **함수 내부의 new.target은 함수 자신을 가리킨다**.
- 일반 함수로서 호출된 함수 내부의 new.target은 undefined다.

### 17-19

```javascript
// 생성자 함수
function Circle(radius) {
  // 이 함수가 new 연산자와 함께 호출되지 않았다면 new.target은 undefined다.
  if (!new.target) {
    // new 연산자와 함께 생성자 함수를 재귀 호출하여 생성된 인스턴스를 반환한다.
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수를 호출하여도 new.target을 통해 생성자 함수로서 호출된다.
const circle = Circle(5);
console.log(circle.getDiameter()); // 10
```

### 17-20

단, IE에서는 `new.target`을 지원하지 않기 때문에, 스코프 세이프 생성자 패턴을 통해 사용한다.

```javascript
// Scope-Safe Constructor Pattern
function Circle(radius) {
  // 생성자 함수가 new 연산자와 함께 호출시 빈 객체 생성, this 바인딩.
  // 이 함수가 new 연산자와 함께 호출되지 않았다면 이 시점의 this는 전역 객체 window를 가리킨다.
  if (!(this instanceof Circle)) {
    // new 연산자와 함께 호출하여 생성된 인스턴스를 반환한다.
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수를 호출하여도 생성자 함수로서 호출된다.
const circle = Circle(5);
console.log(circle.getDiameter()); // 10
```

new 연산자 없이 호출시 Object와 Function 생성자 함수는 동일하게 동작하나
Stirng, Number, Boolean 생성자 함수는 문자열, 숫자, 불리언 값을 반환하기도 한다. 이를 이용하여 데이터 타입을 변환하기도 한다.

### 17-22

```javascript
const str = String(123);
console.log(str, typeof str); // 123 string

const num = Number("123");
console.log(num, typeof num); // 123 number

const bool = Boolean("true");
console.log(bool, typeof bool); // true boolean
```
