- [12장 함수](#12장-함수)
- [12.1 함수란?](#121-함수란)
    - [12-01](#12-01)
    - [12-02](#12-02)
    - [12-03](#12-03)
- [12.2 함수를 사용하는 이유](#122-함수를-사용하는-이유)
- [12.3 함수 리터럴](#123-함수-리터럴)
    - [12-04](#12-04)
- [12.4 함수 정의](#124-함수-정의)
  - [12.4.1 함수 선언문](#1241-함수-선언문)
    - [12-05](#12-05)
    - [12-06](#12-06)
    - [12-07](#12-07)
    - [12-08](#12-08)
    - [12-09](#12-09)
  - [12.4.2 함수 표현식(function expression)](#1242-함수-표현식function-expression)
    - [12-10](#12-10)
    - [12-11](#12-11)
  - [12.4.3 함수 생성 시점과 함수 호이스팅](#1243-함수-생성-시점과-함수-호이스팅)
    - [12-12](#12-12)
  - [12.4.4 Function 생성자 함수](#1244-function-생성자-함수)
    - [12-13](#12-13)
    - [12-14](#12-14)
  - [12.4.5 화살표 함수](#1245-화살표-함수)
    - [12-15](#12-15)
- [12.5 함수 호출](#125-함수-호출)
  - [12.5.1 매개변수와 인수](#1251-매개변수와-인수)
    - [12-16](#12-16)
    - [12-17](#12-17)
    - [12-18](#12-18)
    - [12-19](#12-19)
    - [12-20](#12-20)
  - [12.5.2 인수 확인](#1252-인수-확인)
    - [12-21](#12-21)
    - [12-22](#12-22)
    - [12-23](#12-23)
    - [12-24](#12-24)
    - [12-25](#12-25)
  - [12.5.3 매개변수의 최대 개수](#1253-매개변수의-최대-개수)
    - [12-26](#12-26)
  - [12.5.4 반환문](#1254-반환문)
    - [12-27](#12-27)
    - [12-28](#12-28)
    - [12-29](#12-29)
    - [12-30](#12-30)
    - [12-31](#12-31)
    - [12-32](#12-32)
- [12.6 참조에 의한 전달과 외부 상태의 변경](#126-참조에-의한-전달과-외부-상태의-변경)
    - [12-33](#12-33)
- [12-34](#12-34)
- [12-35](#12-35)
- [12-36](#12-36)
- [12-37](#12-37)
- [12-38](#12-38)
- [12-39](#12-39)
- [12-40](#12-40)
- [12-41](#12-41)
- [12-42](#12-42)
- [12-43](#12-43)
- [12-44](#12-44)
- [12-45](#12-45)
- [12-46](#12-46)
- [12-47](#12-47)
- [12-48](#12-48)
- [12-49](#12-49)
- [12-50](#12-50)
- [12-51](#12-51)
- [12-52](#12-52)
- [12-53](#12-53)
- [12-54](#12-54)
- [12-55](#12-55)
- [12-56](#12-56)
- [12-57](#12-57)

# 12장 함수

# 12.1 함수란?

함수는 일련의 과정을 문(statement)로 구현하고 코드 블록으로 감싸서 하나의 실행 단위로 정의한 것.

- 매개변수 : parameter => 함수 내부로 입력을 전달받는 변수
- 인수 : argument => 입력
- 반환값 : return value => 출력

함수는 `함수 정의(function definition)`를 통해 생성.

### 12-01

```javascript
// f(x, y) = x + y
function add(x, y) {
  return x + y;
}

// f(2, 5) = 7
add(2, 5); // 7
```

### 12-02

함수 선언문

```javascript
// 함수 정의
function add(x, y) {
  return x + y;
}
```

### 12-03

`함수 호출(function call/invoke)` : 함수의 실행을 명시적으로 지시해야 함.

```javascript
// 함수 호출
var result = add(2, 5);

// 함수 add에 인수 2, 5를 전달하면서 호출하면 반환값 7을 반환한다.
console.log(result); // 7
```

# 12.2 함수를 사용하는 이유

코드의 재사용 => 유지 보수의 편의성 높임 코드의 신뢰성 높임  
적절한 함수 이름 : 함수의 역할을 파악할 수 있게 도움. 코드의 가독성 향상.

# 12.3 함수 리터럴

함수는 객체 타입의 값.

### 12-04

```javascript
// 변수에 함수 리터럴을 할당
var f = function add(x, y) {
  return x + y;
};
```

- 함수 리터럴의 구성 요소
  - 함수 이름
    - 있을 시 기명 함수
    - 생략 시 익명 함수
  - 매개변수 목록
  - 함수 몸체

(리터럴 : 사람이 이해할 수 있는 문자 또는 약속된 기호를 사용해 값을 생성하는 표기 방식 => 값을 생성하기 위한 표기법)

`함수는 객체`지만 일반 객체와는 다름. 일반 객체는 호출할 수 없지만 함수는 호출할 수 있음. 함수 객체만의 고유한 프로퍼티가 있음. => 18장 참고

# 12.4 함수 정의

인수를 전달받을 매개변수, 실행할 문들, 반환할 값을 지정하는 것.

- 함수 정의 방식
  - 함수 선언문
  - 함수 표현식
  - Function 생성자 함수
  - 화살표 함수

## 12.4.1 함수 선언문

### 12-05

```javascript
// 함수 선언문
function add(x, y) {
  return x + y;
}

// 함수 참조
// console.dir은 console.log와는 달리 함수 객체의 프로퍼티까지 출력한다.
// 단, Node.js 환경에서는 console.log와 같은 결과가 출력된다.
console.dir(add); // ƒ add(x, y)

// 함수 호출
console.log(add(2, 5)); // 7
```

### 12-06

함수 이름 생략 불가

```javascript
// 함수 선언문은 함수 이름을 생략할 수 없다.
function (x, y) {
  return x + y;
}
// SyntaxError: Function statements require a function name
```

### 12-07

`함수 선언문은 표현식이 아닌 문`

```javascript
// 함수 선언문은 표현식이 아닌 문이므로 변수에 할당할 수 없다.
// 하지만 함수 선언문이 변수에 할당되는 것처럼 보인다.
var add = function add(x, y) {
  return x + y;
};

// 함수 호출
console.log(add(2, 5)); // 7
```

**하지만 ... 보인다** : 자바스크립트 엔진이 함수 선언문과 함수 표현식을 문맥에 따라 해석하기 때문. 기명 함수 리터럴은 함수 선언문 혹은 함수 리터럴 표현식으로 해석될 수 있음.  
기명 함수 리터럴은 중의적인 코드.

### 12-08

```javascript
// 기명 함수 리터럴을 단독으로 사용하면 함수 선언문으로 해석된다.
// 함수 선언문에서는 함수 이름을 생략할 수 없다.
function foo() {
  console.log("foo");
}
foo(); // foo

// 함수 리터럴을 피연산자로 사용하면 함수 선언문이 아니라 함수 리터럴 표현식으로 해석된다.
// 함수 리터럴에서는 함수 이름을 생략할 수 있다.
(function bar() {
  console.log("bar");
});
bar(); // ReferenceError: bar is not defined
```

함수는 식별자로 호출함.  
함수 표현식은 변수 이름을 식별자로 하여 호출하고
함수 선언문은 자바스크립트 엔진이 암묵적으로 함수 이름을 식별자를 생성하여 호출함.

### 12-09

```javascript
var add = function add(x, y) {
  return x + y;
};

console.log(add(2, 5)); // 7
```

## 12.4.2 함수 표현식(function expression)

함수는 일급 객체 => 함수를 값처럼 자유롭게 사용할 수 있음 => 함수 리터럴로 생성한 함수 객체를 변수에 할당할 수 있음.
(일급 객체 : 값처럼 변수에 할당할 수 있고 프로퍼티 값이 될 수 있고 배열의 요소가 될 수 있음)

### 12-10

이름 생략이 일반적

```javascript
// 함수 표현식
var add = function (x, y) {
  return x + y;
};

console.log(add(2, 5)); // 7
```

### 12-11

```javascript
// 기명 함수 표현식
var add = function foo(x, y) {
  return x + y;
};

// 함수 객체를 가리키는 식별자로 호출
console.log(add(2, 5)); // 7

// 함수 이름으로 호출하면 ReferenceError가 발생한다.
// 함수 이름은 함수 몸체 내부에서만 유효한 식별자다.
console.log(foo(2, 5)); // ReferenceError: foo is not defined
```

## 12.4.3 함수 생성 시점과 함수 호이스팅

함수 선언문으로 정의한 함수와  
함수 표현식으로 정의한 함수는  
생성 시점이 다름

### 12-12

```javascript
// 함수 참조
console.dir(add); // ƒ add(x, y)
console.dir(sub); // undefined

// 함수 호출
console.log(add(2, 5)); // 7
console.log(sub(2, 5)); // TypeError: sub is not a function

// 함수 선언문
function add(x, y) {
  return x + y;
}

// 함수 표현식
var sub = function (x, y) {
  return x - y;
};
```

함수 호이스팅 : `함수 선언문`이 코드의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 고유의 특징

(var 선언 변수는 undefined로 초기화, 함수 선언문을 통해 생성된 식별자는 함수 객체로 초기화)

함수 표현식으로 함수를 정의하면 함수 호이스팅이 발생하는 것이 아니라 변수 호이스팅이 발생.

## 12.4.4 Function 생성자 함수

### 12-13

일반적이지 않고 바람직하지 않음.

```javascript
var add = new Function("x", "y", "return x + y");

console.log(add(2, 5)); // 7
```

### 12-14

함수 선언문, 함수 표현식으로 생성한 함수와 다르게 동작.

```javascript
var add1 = (function () {
  var a = 10;
  return function (x, y) {
    return x + y + a;
  };
})();

console.log(add1(1, 2)); // 13

var add2 = (function () {
  var a = 10;
  return new Function("x", "y", "return x + y + a;");
})();

console.log(add2(1, 2)); // ReferenceError: a is not defined
```

## 12.4.5 화살표 함수

항상 익명 함수로 정의.  
기존 함수보다 내부 동작도 간략화.

### 12-15

```javascript
// 화살표 함수
const add = (x, y) => x + y;
console.log(add(2, 5)); // 7
```

- 차이점
  - 생성자 함수로 사용할 수 없음
  - 기존 함수와 this 바인딩 방식 다름
  - prototype 프로퍼티가 없음
  - arguments 객체 생성하지 않음
    => 26.3절 화살표 함수 참고

# 12.5 함수 호출

## 12.5.1 매개변수와 인수

함수 실행에 값이 필요할 경우 매개변수를 통해 인수를 전달. 개수와 타입에 제한이 없음.

### 12-16

```javascript
// 함수 선언문
function add(x, y) {
  return x + y;
}

// 함수 호출
// 인수 1과 2는 매개변수 x와 y에 순서대로 할당되고 함수 몸체의 문들이 실행된다.
var result = add(1, 2);
```

함수 호출 시  
매개변수 생성 => 일반 변수처럼 undefined로 초기화 후 인수가 순서대로 할당됨  
매개변수는 함수 몸체 내부에서만 참조 가능(스코프, 13장 참조).

### 12-17

```javascript
function add(x, y) {
  console.log(x, y); // 2 5
  return x + y;
}

add(2, 5);

// add 함수의 매개변수 x, y는 함수 몸체 내부에서만 참조할 수 있다.
console.log(x, y); // ReferenceError: x is not defined
```

### 12-18

매개변수, 인수 개수는 달라도 됨 인수가 할당되지 않은 경우 undefined가 됨.

```javascript
function add(x, y) {
  return x + y;
}

console.log(add(2)); // NaN
```

### 12-19

매개변수보다 인수가 더 많은 경우 초과된 인수는 무시됨.

```javascript
function add(x, y) {
  return x + y;
}

console.log(add(2, 5, 10)); // 7
```

### 12-20

```javascript
function add(x, y) {
  console.log(arguments);
  // Arguments(3) [2, 5, 10, callee: ƒ, Symbol(Symbol.iterator): ƒ]

  return x + y;
}

add(2, 5, 10);
```

## 12.5.2 인수 확인

자바스크립트의 경우 적절한 인수가 전달되었는지 확인할 필요가 있음. => 타입스크립트 사용

### 12-21

```javascript
function add(x, y) {
  return x + y;
}
```

### 12-22

```javascript
function add(x, y) {
  return x + y;
}

console.log(add(2)); // NaN
console.log(add("a", "b")); // 'ab'
```

### 12-23

```javascript
function add(x, y) {
  if (typeof x !== "number" || typeof y !== "number") {
    // 매개변수를 통해 전달된 인수의 타입이 부적절한 경우 에러를 발생시킨다.
    throw new TypeError("인수는 모두 숫자 값이어야 합니다.");
  }

  return x + y;
}

console.log(add(2)); // TypeError: 인수는 모두 숫자 값이어야 합니다.
console.log(add("a", "b")); // TypeError: 인수는 모두 숫자 값이어야 합니다.
```

### 12-24

```javascript
function add(a, b, c) {
  a = a || 0;
  b = b || 0;
  c = c || 0;
  return a + b + c;
}

console.log(add(1, 2, 3)); // 6
console.log(add(1, 2)); // 3
console.log(add(1)); // 1
console.log(add()); // 0
```

### 12-25

매개변수 기본값을 사용하면 인수 체크 및 초기화를 간소화할 수 있음

```javascript
function add(a = 0, b = 0, c = 0) {
  return a + b + c;
}

console.log(add(1, 2, 3)); // 6
console.log(add(1, 2)); // 3
console.log(add(1)); // 1
console.log(add()); // 0
```

## 12.5.3 매개변수의 최대 개수

- 최대 개수 명시적 제한 없음.
- 이상적인 매개변수는 0개. 적을수록 좋음.
- 매개변수가 많다 => 함수가 여러 가지 일을 한다
- 이상적인 함수는 한 가지 일만 해야 하며 가급적 작게 만들어야 함.
- 3개 이상 넘지 않는 것을 권장. 그 이상이 필요하다면 객체를 인수로 전달하는 것이 유리.

### 12-26

객체를 인수로 사용하면 순서 신경 쓰지 않아도 됨.

```javascript
$.ajax({
  method: "POST",
  url: "/user",
  data: { id: 1, name: "Lee" },
  cache: false,
});
```

## 12.5.4 반환문

1. 반환문은 함수의 실행을 중단하고 함수 몸체를 빠져나감
2. 반환문은 return 키워드 뒤에 오는 표현식을 평가해 반환한다. 명시적으로 지정하지 않으면 undefined 반환

### 12-27

return 키워드로 자바스크립트에서 사용 가능한 모든 값을 반환할 수 있음.

```javascript
function multiply(x, y) {
  return x * y; // 반환문
}

// 함수 호출은 반환값으로 평가된다.
var result = multiply(3, 5);
console.log(result); // 15
```

### 12-28

```javascript
function multiply(x, y) {
  return x * y; // 반환문
  // 반환문 이후에 다른 문이 존재하면 그 문은 실행되지 않고 무시된다.
  console.log("실행되지 않는다.");
}

console.log(multiply(3, 5)); // 15
```

### 12-29

```javascript
function foo() {
  return;
}

console.log(foo()); // undefined
```

### 12-30

```javascript
function foo() {
  // 반환문을 생략하면 암묵적으로 undefined가 반환된다.
}

console.log(foo()); // undefined
```

### 12-31

```javascript
function multiply(x, y) {
  // return 키워드와 반환값 사이에 줄바꿈이 있으면
  return; // 세미콜론 자동 삽입 기능(ASI)에 의해 세미콜론이 추가된다.
  x * y; // 무시된다.
}

console.log(multiply(3, 5)); // undefined
```

### 12-32

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      return; // SyntaxError: Illegal return statement
    </script>
  </body>
</html>
```

# 12.6 참조에 의한 전달과 외부 상태의 변경

### 12-33

```javascript
// 매개변수 primitive는 원시 값을 전달받고, 매개변수 obj는 객체를 전달받는다.
function changeVal(primitive, obj) {
  primitive += 100;
  obj.name = "Kim";
}

// 외부 상태
var num = 100;
var person = { name: "Lee" };

console.log(num); // 100
console.log(person); // {name: "Lee"}

// 원시 값은 값 자체가 복사되어 전달되고 객체는 참조 값이 복사되어 전달된다.
changeVal(num, person);

// 원시 값은 원본이 훼손되지 않는다.
console.log(num); // 100

// 객체는 원본이 훼손된다.
console.log(person); // {name: "Kim"}
```

# 12-34

```javascript
// 익명 즉시 실행 함수
(function () {
  var a = 3;
  var b = 5;
  return a * b;
})();
```

# 12-35

```javascript
// 기명 즉시 실행 함수
(function foo() {
  var a = 3;
  var b = 5;
  return a * b;
})();

foo(); // ReferenceError: foo is not defined
```

# 12-36

```javascript
function () { // SyntaxError: Function statements require a function name
  // ...
}();
```

# 12-37

```javascript
function foo() {
  // ...
}(); // SyntaxError: Unexpected token ')'
```

# 12-38

```javascript
function foo() {}(); // => function foo() {};();
```

# 12-39

```javascript
(); // SyntaxError: Unexpected token ')'
```

# 12-40

```javascript
console.log(typeof function f() {}); // function
console.log(typeof function () {}); // function
```

# 12-41

```javascript
(function () {
  // ...
})();

(function () {
  // ...
})();

!(function () {
  // ...
})();

+(function () {
  // ...
})();
```

# 12-42

```javascript
// 즉시 실행 함수도 일반 함수처럼 값을 반환할 수 있다.
var res = (function () {
  var a = 3;
  var b = 5;
  return a * b;
})();

console.log(res); // 15

// 즉시 실행 함수에도 일반 함수처럼 인수를 전달할 수 있다.
res = (function (a, b) {
  return a * b;
})(3, 5);

console.log(res); // 15
```

# 12-43

```javascript
function countdown(n) {
  for (var i = n; i >= 0; i--) console.log(i);
}

countdown(10);
```

# 12-44

```javascript
function countdown(n) {
  if (n < 0) return;
  console.log(n);
  countdown(n - 1); // 재귀 호출
}

countdown(10);
```

# 12-45

```javascript
// 팩토리얼(계승)은 1부터 자신까지의 모든 양의 정수의 곱이다.
// n! = 1 * 2 * ... * (n-1) * n
function factorial(n) {
  // 탈출 조건: n이 1 이하일 때 재귀 호출을 멈춘다.
  if (n <= 1) return 1;
  // 재귀 호출
  return n * factorial(n - 1);
}

console.log(factorial(0)); // 0! = 1
console.log(factorial(1)); // 1! = 1
console.log(factorial(2)); // 2! = 2 * 1 = 2
console.log(factorial(3)); // 3! = 3 * 2 * 1 = 6
console.log(factorial(4)); // 4! = 4 * 3 * 2 * 1 = 24
console.log(factorial(5)); // 5! = 5 * 4 * 3 * 2 * 1 = 120
```

# 12-46

```javascript
// 함수 표현식
var factorial = function foo(n) {
  // 탈출 조건: n이 1 이하일 때 재귀 호출을 멈춘다.
  if (n <= 1) return 1;
  // 함수를 가리키는 식별자로 자기 자신을 재귀 호출
  return n * factorial(n - 1);

  // 함수 이름으로 자기 자신을 재귀 호출할 수도 있다.
  // console.log(factorial === foo); // true
  // return n * foo(n - 1);
};

console.log(factorial(5)); // 5! = 5 * 4 * 3 * 2 * 1 = 120
```

# 12-47

```javascript
function factorial(n) {
  if (n <= 1) return 1;

  var res = n;
  while (--n) res *= n;
  return res;
}

console.log(factorial(0)); // 0! = 1
console.log(factorial(1)); // 1! = 1
console.log(factorial(2)); // 2! = 2 * 1 = 2
console.log(factorial(3)); // 3! = 3 * 2 * 1 = 6
console.log(factorial(4)); // 4! = 4 * 3 * 2 * 1 = 24
console.log(factorial(5)); // 5! = 5 * 4 * 3 * 2 * 1 = 120
```

# 12-48

```javascript
function outer() {
  var x = 1;

  // 중첩 함수
  function inner() {
    var y = 2;
    // 외부 함수의 변수를 참조할 수 있다.
    console.log(x + y); // 3
  }

  inner();
}

outer();
```

# 12-49

```javascript
// n만큼 어떤 일을 반복한다.
function repeat(n) {
  // i를 출력한다.
  for (var i = 0; i < n; i++) console.log(i);
}

repeat(5); // 0 1 2 3 4
```

# 12-50

```javascript
// n만큼 어떤 일을 반복한다.
function repeat1(n) {
  // i를 출력한다.
  for (var i = 0; i < n; i++) console.log(i);
}

repeat1(5); // 0 1 2 3 4

// n만큼 어떤 일을 반복한다.
function repeat2(n) {
  for (var i = 0; i < n; i++) {
    // i가 홀수일 때만 출력한다.
    if (i % 2) console.log(i);
  }
}

repeat2(5); // 1 3
```

# 12-51

```javascript
// 외부에서 전달받은 f를 n만큼 반복 호출한다.
function repeat(n, f) {
  for (var i = 0; i < n; i++) {
    f(i); // i를 전달하면서 f를 호출
  }
}

var logAll = function (i) {
  console.log(i);
};

// 반복 호출할 함수를 인수로 전달한다.
repeat(5, logAll); // 0 1 2 3 4

var logOdds = function (i) {
  if (i % 2) console.log(i);
};

// 반복 호출할 함수를 인수로 전달한다.
repeat(5, logOdds); // 1 3
```

# 12-52

```javascript
// 익명 함수 리터럴을 콜백 함수로 고차 함수에 전달한다.
// 익명 함수 리터럴은 repeat 함수를 호출할 때마다 평가되어 함수 객체를 생성한다.
repeat(5, function (i) {
  if (i % 2) console.log(i);
}); // 1 3
```

# 12-53

```javascript
// logOdds 함수는 단 한 번만 생성된다.
var logOdds = function (i) {
  if (i % 2) console.log(i);
};

// 고차 함수에 함수 참조를 전달한다.
repeat(5, logOdds); // 1 3
```

# 12-54

```javascript
// 콜백 함수를 사용한 이벤트 처리
// myButton 버튼을 클릭하면 콜백 함수를 실행한다.
document.getElementById("myButton").addEventListener("click", function () {
  console.log("button clicked!");
});

// 콜백 함수를 사용한 비동기 처리
// 1초 후에 메시지를 출력한다.
setTimeout(function () {
  console.log("1초 경과");
}, 1000);
```

# 12-55

```javascript
// 콜백 함수를 사용하는 고차 함수 map
var res = [1, 2, 3].map(function (item) {
  return item * 2;
});

console.log(res); // [2, 4, 6]

// 콜백 함수를 사용하는 고차 함수 filter
res = [1, 2, 3].filter(function (item) {
  return item % 2;
});

console.log(res); // [1, 3]

// 콜백 함수를 사용하는 고차 함수 reduce
res = [1, 2, 3].reduce(function (acc, cur) {
  return acc + cur;
}, 0);

console.log(res); // 6
```

# 12-56

```javascript
var count = 0; // 현재 카운트를 나타내는 상태

// 순수 함수 increase는 동일한 인수가 전달되면 언제나 동일한 값을 반환한다.
function increase(n) {
  return ++n;
}

// 순수 함수가 반환한 결과값을 변수에 재할당해서 상태를 변경
count = increase(count);
console.log(count); // 1

count = increase(count);
console.log(count); // 2
```

# 12-57

```javascript
var count = 0; // 현재 카운트를 나타내는 상태: increase 함수에 의해 변화한다.

// 비순수 함수
function increase() {
  return ++count; // 외부 상태에 의존하며 외부 상태를 변경한다.
}

// 비순수 함수는 외부 상태(count)를 변경하므로 상태 변화를 추적하기 어려워진다.
increase();
console.log(count); // 1

increase();
console.log(count); // 2
```
