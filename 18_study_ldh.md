- [18장 함수와 일급 객체](#18장-함수와-일급-객체)
- [18.1 일급 객체](#181-일급-객체)
    - [18-01](#18-01)
- [18.2 함수 객체의 프로퍼티](#182-함수-객체의-프로퍼티)
    - [18-02](#18-02)
    - [18-03](#18-03)
  - [18.2.1 arguments 프로퍼티](#1821-arguments-프로퍼티)
    - [18-04](#18-04)
    - [18-05](#18-05)
    - [18-06](#18-06)
    - [18-07](#18-07)
    - [18-08](#18-08)
  - [18.2.2 caller 프로퍼티](#1822-caller-프로퍼티)
    - [18-09](#18-09)
  - [18.2.3 length 프로퍼티](#1823-length-프로퍼티)
    - [18-10](#18-10)
  - [18.2.4 name 프로퍼티](#1824-name-프로퍼티)
    - [18-11](#18-11)
  - [18.2.5 `__proto__` 접근자 프로퍼티](#1825-__proto__-접근자-프로퍼티)
    - [18-12](#18-12)
  - [18.2.6 prototype 프로퍼티](#1826-prototype-프로퍼티)
    - [18-13](#18-13)

# 18장 함수와 일급 객체

# 18.1 일급 객체

- `일급 객체`
  - 무명의 리터럴로 생성할 수 있다. 런타임에 생성 가능
  - 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
  - 함수의 매개변수에 전달할 수 있다.
  - 함수의 반환값으로 사용할 수 있다.

=> 자바스크립트의 함수는 일급 객체다.

### 18-01

```javascript
// 1. 함수는 무명의 리터럴로 생성할 수 있다.
// 2. 함수는 변수에 저장할 수 있다.
// 런타임(할당 단계)에 함수 리터럴이 평가되어 함수 객체가 생성되고 변수에 할당된다.
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};

// 2. 함수는 객체에 저장할 수 있다.
const auxs = { increase, decrease };

// 3. 함수의 매개변수에게 전달할 수 있다.
// 4. 함수의 반환값으로 사용할 수 있다.
function makeCounter(aux) {
  let num = 0;

  return function () {
    num = aux(num);
    return num;
  };
}

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const increaser = makeCounter(auxs.increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const decreaser = makeCounter(auxs.decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

함수가 일급 객체라는 것은 함수를 객체와 동일하게 사용할 수 있다는 것. 값을 사용할 수 있는 곳이라면 어디서든지 리터럴로 정의할 수 있으며 런타임에 함수 객체로 평가됨.

매개변수에 전달할 수 있고, 반환값으로 사용할 수도 있음 => 자바스크립트의 함수형 프로그래밍을 가능케 함

# 18.2 함수 객체의 프로퍼티

함수는 객체이므로 프로퍼티를 가지고 있음.

### 18-02

```javascript
function square(number) {
  return number * number;
}

console.dir(square);
```

### 18-03

```javascript
function square(number) {
  return number * number;
}

console.log(Object.getOwnPropertyDescriptors(square));
/*
{
  length: {value: 1, writable: false, enumerable: false, configurable: true},
  name: {value: "square", writable: false, enumerable: false, configurable: true},
  arguments: {value: null, writable: false, enumerable: false, configurable: false},
  caller: {value: null, writable: false, enumerable: false, configurable: false},
  prototype: {value: {...}, writable: true, enumerable: false, configurable: false}
}
*/

// __proto__는 square 함수의 프로퍼티가 아니다.
console.log(Object.getOwnPropertyDescriptor(square, "__proto__")); // undefined

// __proto__는 Object.prototype 객체의 접근자 프로퍼티다.
// square 함수는 Object.prototype 객체로부터 __proto__ 접근자 프로퍼티를 상속받는다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "__proto__"));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}
```

- 함수 객체 고유의 프로퍼티
  - arguments
  - caller
  - length
  - name
  - prototype

`__proto__`는 접근자 프로퍼티. Object.prototype 객체의 프로퍼티를 상속받은 것 => 19장 프로토타입 참고

## 18.2.1 arguments 프로퍼티

arguments 프로퍼티 값은 arguments 객체.

arguments 객체 : 함수 호출 시 전달된 인수들의 정보를 담고 있는 유사 배열 객체. 함수 내부에서 지역 변수처럼 사용됨

매개변수는 함수 몸체 내부에서 변수와 동일하게 취급됨.  
호출 => 선언, undefined로 초기화, 인수 할당  
적게 전달한 경우 : undefined로 초기화 상태 유지  
많이 전달한 경우 : 무시(arguments 객체의 프로퍼티로 보관)

### 18-04

```javascript
function multiply(x, y) {
  console.log(arguments);
  return x * y;
}

console.log(multiply()); // NaN
console.log(multiply(1)); // NaN
console.log(multiply(1, 2)); // 2
console.log(multiply(1, 2, 3)); // 2
```

### 18-05

```javascript
function multiply(x, y) {
  // 이터레이터
  const iterator = arguments[Symbol.iterator]();

  // 이터레이터의 next 메서드를 호출하여 이터러블 객체 arguments를 순회
  console.log(iterator.next()); // {value: 1, done: false}
  console.log(iterator.next()); // {value: 2, done: false}
  console.log(iterator.next()); // {value: 3, done: false}
  console.log(iterator.next()); // {value: undefined, done: true}

  return x * y;
}

multiply(1, 2, 3);
```

### 18-06

매개변수 개수를 확정할 수 없는 `가변 인자 함수`를 구현할 떄 유용.

```javascript
function sum() {
  let res = 0;

  // arguments 객체는 length 프로퍼티가 있는 유사 배열 객체이므로 for 문으로 순회할 수 있다.
  for (let i = 0; i < arguments.length; i++) {
    res += arguments[i];
  }

  return res;
}

console.log(sum()); // 0
console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3)); // 6
```

유사 배열 객체 : length 프로퍼티를 가진 객체로 for문으로 순회할 수 있는 객체.

### 18-07

유사 배열 객체는 배열이 아니므로 배열 메서드를 사용하기 위해서 Function.prototype.call, Function.prototype.apply를 사용해 건접 호출해야 함.

```javascript
function sum() {
  // arguments 객체를 배열로 변환
  const array = Array.prototype.slice.call(arguments);
  return array.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3, 4, 5)); // 15
```

### 18-08

번거로움 해결 위해 Rest 파라미터 도입.

```javascript
// ES6 Rest parameter
function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2)); // 3
console.log(sum(1, 2, 3, 4, 5)); // 15
```

## 18.2.2 caller 프로퍼티

비표준 프로퍼티, 함수 자신을 호출한 함수를 가리킴.

### 18-09

```javascript
function foo(func) {
  return func();
}

function bar() {
  return "caller : " + bar.caller;
}

// 브라우저에서의 실행한 결과
console.log(foo(bar)); // caller : function foo(func) {...}
console.log(bar()); // caller : null
```

## 18.2.3 length 프로퍼티

함수를 정의할 때 선언한 매개변수 개수 가리킴.  
arguments 객체의 length 프로퍼티(인자의 개수 가리킴)와  
함수 객체의 length 프로퍼티의 값(매개변수의 개수 가리킴)은 다를 수 있음.

### 18-10

```javascript
function foo() {}
console.log(foo.length); // 0

function bar(x) {
  return x;
}
console.log(bar.length); // 1

function baz(x, y) {
  return x * y;
}
console.log(baz.length); // 2
```

## 18.2.4 name 프로퍼티

name 프로퍼티 : 함수 이름을 나타냄  
ES6에서 정식 표준이 됨.

- 익명 함수의 경우
  - ES5는 빈 문자열을 값으로
  - ES6에서 함수 객체를 가리키는 식별자를 값으로 가짐.

### 18-11

```javascript
// 기명 함수 표현식
var namedFunc = function foo() {};
console.log(namedFunc.name); // foo

// 익명 함수 표현식
var anonymousFunc = function () {};
// ES5: name 프로퍼티는 빈 문자열을 값으로 갖는다.
// ES6: name 프로퍼티는 함수 객체를 가리키는 변수 이름을 값으로 갖는다.
console.log(anonymousFunc.name); // anonymousFunc

// 함수 선언문(Function declaration)
function bar() {}
console.log(bar.name); // bar
```

## 18.2.5 `__proto__` 접근자 프로퍼티

모든 객체는 [[prototype]]이라는 내부 슬롯 가짐. 객체지향 프로그래밍의 상속을 구현하는 프로토타입 객체 가리킴.  
프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티.
내부 슬롯에는 간접적인 접근 방법을 제공하는 경우에 한하여 접근할 수 있음.
[[prototype]] 내부 슬롯에도 `__proto__` 접근자 프로퍼티를 통해 간접적으로 프로토타입 객체에 접근할 수 있음.

### 18-12

```javascript
const obj = { a: 1 };

// 객체 리터럴 방식으로 생성한 객체의 프로토타입 객체는 Object.prototype이다.
console.log(obj.__proto__ === Object.prototype); // true

// 객체 리터럴 방식으로 생성한 객체는 프로토타입 객체인 Object.prototype의 프로퍼티를 상속받는다.
// hasOwnProperty 메서드는 Object.prototype의 메서드다.
console.log(obj.hasOwnProperty("a")); // true
console.log(obj.hasOwnProperty("__proto__")); // false
```

## 18.2.6 prototype 프로퍼티

생성자 함수로 호출할 수 있는 함수 객체, 즉 constructor만이 소유하는 프로퍼티  
일반 객체와 생성자 함수로 호출할 수 없는 non-constructor에는 prototype 프로퍼티가 없음

### 18-13

```javascript
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty("prototype"); // -> true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty("prototype"); // -> false
```

prototype 프로퍼티는 함수가 객체를 생성하는 생성자 함수로 호출될 때 생성자 함수가 생성할 인스턴스의 프로토타입 객체 가리킴.
