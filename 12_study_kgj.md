- [12.6 참조에 의한 전달과 외부 상태의 변경](#126-참조에-의한-전달과-외부-상태의-변경)
- [12.7 다양한 함수의 형태](#127-다양한-함수의-형태)
  - [12.7.1 즉시 실행 함수](#1271-즉시-실행-함수)
  - [12.7.2 재귀 함수](#1272-재귀-함수)
  - [12.7.3 중첩 함수 (내부 함수)](#1273-중첩-함수-내부-함수)
  - [12.7.4 콜백 함수](#1274-콜백-함수)
  - [12.7.5 순수 함수와 비순수 함수](#1275-순수-함수와-비순수-함수)
    - [11장 원시값과 객체의 비교](#11장-원시값과-객체의-비교)
    - [옵저버 패턴](#옵저버-패턴)



## 12.6 참조에 의한 전달과 외부 상태의 변경
[참조 11장](#11장-원시값과-객체의-비교)

```javascript
// 매개변수 primitive는 원시 값을 전달받고, 매개변수 obj는 객체를 전달받는다.
function changeVal(primitive, obj) {
  primitive += 100;
  obj.name = 'Kim';
}

// 외부 상태
var num = 100;
var person = { name: 'Lee' };

console.log(num); // 100
console.log(person); // {name: "Lee"}

// 원시 값은 값 자체가 복사되어 전달되고 객체는 참조 값이 복사되어 전달된다.
changeVal(num, person);

// 원시 값은 원본이 훼손되지 않는다.
console.log(num); // 100

// 객체는 원본이 훼손된다.
console.log(person); // {name: "Kim"}
```
- 매개변수는 타입에 따라 값에 의한 전달, 참조에 의한 전달 방식을 따른다.
    
![deepdive 12-33 (1)](https://github.com/KIMGEUNDU/CodingTest/assets/126174401/455dc612-c086-4bb3-8e7e-7dd44722c7a9)
    

- 이처럼 외부 상태 person에 의해 원본이 파괴가 되는 상태 변화가 일어나면 추적하기가 어려워진다. → 코드의 복잡성 증가, 가독성을 해친다 / 논리가 간단해야 버그가 일어나지않고 찾기가 쉽다.
- 객체의 변경을 추적하려면 [옵저버 패턴](#옵저버-패턴) 등을 통해 변경 사실을 통지하고 이에 대처하는 추가 대응이 필요하다.
- 또 다른 방법 불변 객체로 만들어 사용하는 것 → 객체를 마치 원시값처럼 변경 불가능한 값으로 동작하게 만드는 것 예) 스프레드문법, 원본은 그대로 새로운 객체 만들기


## 12.7 다양한 함수의 형태
### 12.7.1 즉시 실행 함수

- 함수 정의와 동시에 즉시 호출되는 함수
- 단 한번만 호출되며 재호출할 수 없다


```javascript
// 익명 즉시 실행 함수
(function () {
  var a = 3;
  var b = 5;
  return a * b;
}());
```

```javascript
// 기명 즉시 실행 함수
(function foo() {
  var a = 3;
  var b = 5;
  return a * b;
}());

foo(); // ReferenceError: foo is not defined
```
- 익명 함수를 사용하는 것이 일반적, 기명 즉시 실행 함수도 사용 가능
- 그룹 연산자 `( )` 내의 기명 함수는 함수 선언문이 아닌 함수 리터럴로 평가
- 함수 이름은 **함수 몸체에서만 참조할 수 있는 식별자**이므로 즉시 실행 함수를 다시 호출할 수 없다.


```javascript
function () { // SyntaxError: Function statements require a function name
  // ...
}();
```
* 즉시 실행 함수는 반드시 그룹 연산자 ( ) 로 감싸야한다. 안그러면 에러가 난다. → 함수 선언문의 형식에 맞지 않기 때문이다.

```javascript
function foo() {
  // ...
}(); // SyntaxError: Unexpected token ')'
```
* 위 처럼 그룹 연산자 없이 기명 함수를 정의해도 오류가 난다. → 자바스크립트 엔진이 암묵적으로 수행하는 세미콜론 자동 삽입 기능에 의해 함수 선언문이 끝나는 위치, 함수 코드 블록의 닫는 중괄호 뒤에 “;” 이 암묵적으로 추가가 되기 때문이다.


```javascript
function foo() {}(); // => function foo() {};();
```
* 따라서 함수 선언문 뒤에 ( ) 는 함수 호출하는 것이 아닌 그룹 연산자로 해석되기 때문에 에러가 발생한다.


```javascript
console.log(typeof (function f(){})); // function
console.log(typeof (function (){}));  // function
```
- 그룹 연산자로 묶은 이유는 먼저 함수 리터럴로 평가해서 함수 객체를 생성하기 위해서이다.
- 보통 첫번째 방식을 많이 사용한다.

```javascript
// 즉시 실행 함수도 일반 함수처럼 값을 반환할 수 있다.
var res = (function () {
  var a = 3;
  var b = 5;
  return a * b;
}());

console.log(res); // 15

// 즉시 실행 함수에도 일반 함수처럼 인수를 전달할 수 있다.
res = (function (a, b) {
  return a * b;
}(3, 5));

console.log(res); // 15
```
* 즉시 실행 함수도 일반 함수처럼 값을 반환할 수 있고 인수를 전달할 수 있다.

### 12.7.2 재귀 함수
- 함수가 자기 자신을 호출하는 것을 재귀 호출이라고 한다.
- 자기 자신을 호출하는 행위, 즉 재귀 호출을 수행하는 함수
- 재귀 함수는 **반복되는 처리**를 위해 사용한다.
<br>
- 10부터 0까지 출력하는 함수

```javascript
function countdown(n) {
  for (var i = n; i >= 0; i--) console.log(i);
}

countdown(10);
```

* 반복문없이 구현할 수 있는 방법이 바로 “재귀 함수”
```javascript
function countdown(n) {
  if (n < 0) return;
  console.log(n);
  countdown(n - 1); // 재귀 호출
}

countdown(10);
```

- 팩토리얼 함수
    - 1부터 자기 자신까지의 모든 양의 정수의 곱을 하는 함수

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
- 재귀 함수는 자신을 무한 재귀 호출한다. 그래서 함수 내에는 재귀 호출을 멈출 수 있는 **탈출 조건**을 반드시 만들어야 한다. → 탈출 조건이 없으면 함수가 무한 호출, ⚠️스택 오버플로 에러 발생
- 재귀 함수는for문이나 while 문으로 구현 가능하다.
- 반복문 없이 구현할 수 있다는 장점
- 무한 반복에 빠질 위험, 스택 오버플로 에러 발생할 수 있는 단점이 있어 주의해서 사용
- 즉, 반복문보다는 재귀함수를 사용하는 것이 더 직관적으로 이해하기 쉬울 때만 한정적으로 사용하는 것이 바람직

### 12.7.3 중첩 함수 (내부 함수)
- 함수 내부에 정의된 함수
- 중첩 함수는 외부 함수 내부에서만 호출할 수 있다.
- 자신을 포함하는 외부 함수를 돕는 헬퍼 함수의 역할

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

### 12.7.4 콜백 함수

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
- repeat1함수와 repeat2의 함수는 **공통적인 로직**이 존재한다.
- 함수 합성 → 변하지 않는 공통 로직은 미리 정의해두고 경우에 따라 변경되는 로직은 추상화해서 함수 외부에서 내부로 전달하는 것


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
- 이처럼 함수의 매개변수를 통해 다른 함수의 내부에 전달되는 함수 → **콜백 함수**
- 매개 변수를 통해 함수의 외부에서 콜백 함수를 전달받은 함수 → **고차 함수** repeat
- 중첩 함수는 외부 함수를 돕는 헬퍼 함수의 역할, 콜백 함수는 고차 함수를 돕는 헬퍼 함수의 역할
- 단, 중첩 함수는 고정되어 있어서 교체하기 곤란하지만 콜백 함수는 함수 외부에서 고차 함수 내부로 주입하기 때문에 자유롭게 교체가능하다. → 고차 함수는 콜백 함수를 자신의 일부분으로 합성
- 고차 함수는 매개 변수를 통해 전달받은 **콜백 함수의 호출 시점을 결정**해서 호출한다.


```javascript
// 익명 함수 리터럴을 콜백 함수로 고차 함수에 전달한다.
// 익명 함수 리터럴은 repeat 함수를 호출할 때마다 평가되어 함수 객체를 생성한다.
repeat(5, function (i) {
  if (i % 2) console.log(i);
}); // 1 3
```
- 콜백 함수가 고차 함수 내부에만 호출된다면 콜백 함수는 익명 함수 리터럴로 정의하면서 곧바로 전달하는 것이 일반적
- 이때 콜백 함수는 고차 함수가 호출될때마다 평가되어 함수 객체를 생성 → 그래서 재사용될 가능성이 있다면 외부에서 정의하는 것이 효율적

```javascript
// 콜백 함수를 사용한 이벤트 처리
// myButton 버튼을 클릭하면 콜백 함수를 실행한다.
document.getElementById('myButton').addEventListener('click', function () {
  console.log('button clicked!');
});

// 콜백 함수를 사용한 비동기 처리
// 1초 후에 메시지를 출력한다.
setTimeout(function () {
  console.log('1초 경과');
}, 1000);
```
- 콜백 함수는 비동기 처리에 활용되는 중요한 패턴


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
- 콜백 함수는 배열 고차 함수에서도 사용

### 12.7.5 순수 함수와 비순수 함수

- **순수 함수**
    - 어떤 외부 상태에 의존하지 않고 변경하지도 않는, 즉 부수 효과가 없는 함수
    - **동일한 인수가 전달되면 언제나 동일한 값을 반환하는 함수**

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

- **비순수 함수**
    - 외부 상태에 의존하거나 외부 상태를 변경하는, 즉 부수 효과가 있는 함수
    - 외부 상태에 의존하거나 외부 상태를 변경하는 함수

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

> "💡 함수가 외부 상태를 변경하면 상태 변화를 추적하기 어려워진다. 따라서 함수 외부 상태의 변경을 지양하는 **순수함수를 사용**하는 것이 좋다."


- **함수형 프로그래밍**은 **순수 함수와 보조 함수의 조합**을 통해 외부 상태를 변경하는 부수 효과를 최소화해서 불변성을 지향하는 프로그래밍 패러다임이다.  결국 **순수 함수를 통해 부수 효과를 최대한 억제해 오류를 피하고 프로그램의 안전성을 높이려는 노력의 일환**이다.

---
#### 11장 원시값과 객체의 비교

- 자바스크립트에서 제공하는 7가지 데이터 타입(숫자, 문자열, 불리언, null, undefined, 심벌, 객체 타입)은 크게 **원시 타입**과 **객체 타입**으로 구분 가능

| 원시 타입 | 객체 타입 |
| --- | --- |
| 변경 불가능한 값 | 변경 가능한 값 |
| 변수에는 실제값 저장 | 변수에는 참조값 저장 |
| 다른 변수에 할당하면 원본의 원시값이 복사되어 전달 | 다른 변수에 할당하면 원본의 참조 값이 복사되어 전달 |
| 값에 의한 전달 | 참조에 의한 전달 |

#### 옵저버 패턴

- **옵저버 패턴(observer pattern)**은 객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴이다.
