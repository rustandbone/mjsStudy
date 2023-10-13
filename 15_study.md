# 15장 let, const 키워드와 블록 레벨 스코프
---

## 15.1 var 키워드로 선언한 변수의 문제점

#### 예제 15-01

```javascript
var x = 1;
var y = 1;

// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var x = 100;
// 초기화문이 없는 변수 선언문은 무시된다.
var y;

console.log(x); // 100
console.log(y); // 1
```

#### 예제 15-02

```javascript
var x = 1;

if (true) {
  // x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
  // 이는 의도치 않게 변수값이 변경되는 부작용을 발생시킨다.
  var x = 10;
}

console.log(x); // 10
```

#### 예제 15-03

```javascript
var i = 10;

// for문에서 선언한 i는 전역 변수이다. 이미 선언된 전역 변수 i가 있으므로 중복 선언된다.
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// 의도치 않게 i 변수의 값이 변경되었다.
console.log(i); // 5
```

#### 예제 15-04

```javascript
// 이 시점에는 변수 호이스팅에 의해 이미 foo 변수가 선언되었다(1. 선언 단계)
// 변수 foo는 undefined로 초기화된다. (2. 초기화 단계)
console.log(foo); // undefined

// 변수에 값을 할당(3. 할당 단계)
foo = 123;

console.log(foo); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행된다.
var foo;
```

## 15.2 let 키워드
var 키워드의 단점을 보완하기 위해 let과 const 를 사용한다.
<br/>


#### 예제 15-05

- ##### 변수 중복 선언 금지

```javascript
var foo = 123;
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var foo = 456;

let bar = 123;
// let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

같은 스코프 내 이름이 같은 변수를 중복선언시, 문법 에러 발생

#### 예제 15-06
- ##### 모든 코드 블록 ( 함수, if문, for문, while문, try/catch문 등 ) 을 지역스코프로 인정하는 블록 레벨 스코프를 따른다.

```javascript
let foo = 1; // 전역 변수

{
  let foo = 2; // 지역 변수
  let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

- 전역스코프 : 블록`{}` 바깥이나 함수 바깥에 선언 된 변수
전역 스코프에 변수를 선언하면 어떤 위치의 코드에서든 사용할 수 있는 전역변수가 됨
- 지역스코프 : 특정 부분에서만 사용할 수 있는 변수
  - 함수스코프 : `함수` 내에서 선언된 변수, 함수 내부에서만 접근 가능
  - 블록스코프 : 블록`{}` 내에서 선언된 변수, 블록 내부에서만 접근 가능

#### 예제 15-07
- ##### 변수 호이스팅이 발생하지 않는 것처럼 동작

변수의 생성과정
1. 선언 단계
2. 초기화 단계
3. 할당 단계

호이스팅 : 스코프 내부 어디서든 변수 선언은 최상위에 선언된 것처럼 행동

var 로 선언한 모든 변수는 선언은 호이스팅 되지만, 할당은 호이스팅 되지 않음 ( 변수의 존재는 알지만, 값은 모르는 상태 )

```javascript
console.log(foo); // ReferenceError: foo is not defined
let foo;
```


#### 예제 15-08

```javascript
// var 키워드로 선언한 변수는 런타임 이전에 선언 단계와 초기화 단계가 실행된다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 있다.
console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

let 키워드로 선언한 변수는 "선언 단계"와 "초기화 단계"가 분리되어 진행

let 과 const 도 호이스팅이 되지만, TDZ 때문에 에러가 남

-> 할당하기 전의 변수들은 TDZ ( 일시적 사각지대 ) 안에 보관되는데, 할당하기 전에는 사용할 수 없음

#### 예제 15-09

```javascript
// 런타임 이전에 선언 단계가 실행된다. 아직 변수가 초기화되지 않았다.
// 초기화 이전의 일시적 사각 지대에서는 변수를 참조할 수 없다.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```


#### 예제 15-10
let 도 호이스팅이 발생한다는 증거

```javascript
let foo = 1; // 전역 변수

{
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  let foo = 2; // 지역 변수
}
```
호이스팅이 발생하지 않는다면 전역 변수 foo 값을 출력해야 하지만, 호이스팅이 발생하기 때문에 참조 에러가 발생

#### 예제 15-11

##### 전역 객체 window 의 프로퍼티가 아니다.

```javascript
// 이 예제는 브라우저 환경에서 실행해야 한다.

// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
// 전역 함수
function foo() {}

// var 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.x); // 1
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

// 암묵적 전역은 전역 객체 window의 프로퍼티다.
console.log(window.y); // 2
console.log(y); // 2

// 함수 선언문으로 정의한 전역 함수는 전역 객체 window의 프로퍼티다.
console.log(window.foo); // ƒ foo() {}
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(foo); // ƒ foo() {}
```


#### 예제 15-12

```javascript
// 이 예제는 브라우저 환경에서 실행해야 한다.
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

`window.foo` 와 같이 접근 불가

## 15.3 const 키워드
상수 선언

let 키워드와 다른 점

#### 예제 15-13

```javascript
const foo = 1;
```
- 선언과 동시에 초기화 해야한다.

#### 예제 15-14

```javascript
const foo; // SyntaxError: Missing initializer in const declaration
```

#### 예제 15-15

```javascript
{
  // 변수 호이스팅이 발생하지 않는 것처럼 동작한다
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  const foo = 1;
  console.log(foo); // 1
}

// 블록 레벨 스코프를 갖는다.
console.log(foo); // ReferenceError: foo is not defined
```

#### 예제 15-16
- 재할당 금지

```javascript
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable.
```

#### 예제 15-17
- 상수 : 재할당이 금지 된 변수

```javascript
// 세전 가격
let preTaxPrice = 100;

// 세후 가격
// 0.1의 의미를 명확히 알기 어렵기 때문에 가독성이 좋지 않다.
let afterTaxPrice = preTaxPrice + (preTaxPrice * 0.1);

console.log(afterTaxPrice); // 110
```


#### 예제 15-18

```javascript
// 세율을 의미하는 0.1은 변경할 수 없는 상수로서 사용될 값이다.
// 변수 이름을 대문자로 선언해 상수임을 명확히 나타낸다.
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + (preTaxPrice * TAX_RATE);

console.log(afterTaxPrice); // 110
```

#### 예제 15-19

- const 키워드로 선언된 변수에 객체를 할당한 경우 값 변경 가능

```javascript
const person = {
  name: 'Lee'
};

// 객체는 변경 가능한 값이다. 따라서 재할당없이 변경이 가능하다.
person.name = 'Kim';

console.log(person); // {name: "Kim"}
```

const 키워드는 재할당을 금지할 뿐 "불변"을 의미하지 않음

- - -

## var, let, const 차이

||var|let|const|
|---|---|---|---|
|변수중복선언|O|X|X|
|재할당|O|필요한 경우 사용|X|
|전역 객체 window의<br/>프로퍼티|O|X|X|
|스코프|함수 레벨 스코프|블록 레벨 스코프|블록 레벨 스코프|
|변수의<br/>생성과정|1. 선언, 초기화 단계<br/>2. 할당 단계|1. 선언 단계<br/>2. 초기화 단계<br/>3. 할당 단계|1. 선언, 초기화, 할당 단계|