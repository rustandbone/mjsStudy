# 14장 전역 변수의 문제점

- 전역 변수의 무분별한 사용은 위험하기 때문에 지역 변수를 사용하자
- 오늘 공부할 내용: 변수(지역변수, 전역변수)의 생명 주기, 전역 변수의 문제점, 전역 변수의 사용을 억제하는 방법

# 14.1 변수의 생명 주기

- 변수: 선언에 의해 생성. 할당 통해 값을 갖는다.
- 생명 주기: 변수가 가지는 생성되고 소멸되는 기간

## 14.1.1 지역 변수의 생명 주기

- 함수 내부에서 선언된 지역 변수: 함수 호출시 생성되어 종료시 소멸된다.
- 지역 변수의 생명 주기 = 함수의 생명 주기

### 14-01

```javascript
function foo() {
  var x = "local"; // 1. 자바스크립트 엔진: x 변수 선언, undefined 초기화  2. 런타임: "local" 값 할당
  console.log(x); // local
  return x;
}

foo(); // 함수 호출시 동작
console.log(x); // ReferenceError: x is not defined
```

- 지역 변수의 생명 주기 > 함수의 생명 주기
- 변수: 하나의 값을 저장하기 위해 _확보한 메모리 공간_ 자체 또는 그 메모리 공간을 식별하기 위해 붙인 이름
- 변수의 생명 주기: 메모리 공간이 확보된 시점부터 메모리 공간이 해제되어 가용 메모리 풀이 반환되는 시점

  > [메모리 풀](https://www.ibm.com/docs/ko/i/7.3?topic=concepts-memory-pools)  
  > 하나의 작업이나 작업 그룹을 처리하기 위해 예약되어 있는 주 메모리 또는 기억장치의 논리적인 영역.  
  > [필요 크기의 메모리를 미리 할당받고, 필요할 때마다 사용하고 반납하는 방법](https://internet-craft.tistory.com/13)

- 변수는 함수가 생성한 스코프에 등록(렉시컬 환경)되며 자신이 등록된 스코프가 소멸(메모리 해제)될 때까지 유효.
- 할당된 메모리 공간은 _더 이상 누구도 참조하지 않을 때_ 가비지 콜렉터에 의해 해제되어 가용 메모리 풀에 반환
- 스코프 참조 시 소멸하지 않고 생존
- 호이스팅: 스코프를 단위로 동작. 변수 선언이 스코프의 선두로 끌어 올려진 것처럼 동작하는 자바스크립트 특징

### 14-02

```javascript
var x = "global";

function foo() {
  console.log(x); // ①
  var x = "local";
}

foo();
console.log(x); // global
```

## 14.1.2 전역 변수의 생명 주기

- 명시적 호출이 없이 실행 = 특별한 진입점이 없으며, 곧바로 해석되고 실행.
- 마지막 문이 실행되어 더 이상 실행할 문이 없을 때 종료한다.
- var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 된다.
- var 키워드로 선언한 전역 변수 생명주기 = 전역 객체 생명주기

> 진입점  
> 프로그램이 시작되는 지점. 함수 호출 등
>
> ```js
> int main(void)
> {
>   ...
>   return 0;
> }
> ```

> 전역객체  
> 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체  
> 브라우저(클라이언트 사이드 환경)에서는 window, node.js(서버 사이드 환경)에서는 global 객체를 의미한다.  
> 표준 빌트인 객체, 호스트 객체, var 키워드로 선언한 전역 변수와 전역 함수

# 14.2 전역 변수의 문제점

1. 암묵적 결합

   - 암묵적 결합: 모든 코드가 전역 변수를 참조하고 변경할 수 있다.

2. 긴 생명주기

   - 메모리 리소스 오랜기간 소비 -> 상태 변경 시간이 길고 기회가 많다.
   - 중복 선언을 허용하여 의도치 않은 재할당 -> 오류 발생 확률이 크다.

3. 스코프 체인 상에서 종점에 존재

   - 전역 변수가 가장 마지막에 검색 = 검색 속도가 가장 느리다.

4. 네임스페이스 오염
   - 파일이 분리되어 있다 해도 하나의 전역 스코프를 공유

### 14-03

```javascript
var x = 1;

// ...

// 변수의 중복 선언. 기존 변수에 값을 재할당한다.
var x = 100;
console.log(x); // 100
```

# 14.3 전역 변수의 사용을 억제하는 방법

- 변수의 스코프는 좁을수록 좋다.

## 14.3.1 즉시 실행 함수

- [구성](https://developer.mozilla.org/ko/docs/Glossary/IIFE): 익명함수. 즉시 실행 함수를 생성하는 괄호 `()`

### 14-04

```javascript
(function () {
  var foo = 10; // 즉시 실행 함수의 지역 변수
  // ...
})();

console.log(foo); // ReferenceError: foo is not defined
```

## 14.3.2 네임스페이스 객체

- 네임스페이스 객체 자체가 전역 변수에 해당하므로 지양

### 14-05

```javascript
var MYAPP = {}; // 전역 네임스페이스 객체

MYAPP.name = "Lee";

console.log(MYAPP.name); // Lee
```

### 14-06

```javascript
var MYAPP = {}; // 전역 네임스페이스 객체

MYAPP.person = {
  name: "Lee",
  address: "Seoul",
};

console.log(MYAPP.person.name); // Lee
```

## 14.3.3 모듈 패턴

- 클로저를 기반으로 작동 (_렉시컬 스코프: 함수를 어디서 정의_)
- 장점: 전역 변수 방지. 캡슐화
- 캡슐화: 객체의 상태를 나타내는 '프로퍼티'와 프로퍼티를 참조하고 조작할 수 있는 동작인 '메서드'를 하나로 묶는 것
- 정보 은닉: 객체의 특정 프로퍼티나 메서드를 감출 목적. public, private 등과 같은 접근 제한자를 제공하지 않기 때문에 구현하기 위해 사용한다.

### 14-07

```javascript
var Counter = (function () {
  // private 멤버 (변수)
  var num = 0;

  // 퍼플릭 멤버: 외부로 공개할 데이터나 메서드를 프로퍼티로 추가한 객체를 반환한다.
  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
  };
})();

// private 변수는 외부로 노출되지 않는다.
console.log(Counter.num); // undefined

console.log(Counter.increase()); // 1
console.log(Counter.increase()); // 2
console.log(Counter.decrease()); // 1
console.log(Counter.decrease()); // 0
```

## 14.3.4 ES6 모듈

- [모듈 - Info](https://ko.javascript.info/modules-intro)
- 파일 자체의 독자적인 모듈 스코프를 제공
- script 태그에 `type="module"` 속성 추가시 로드된 자바스크립트 파일은 모듈로서 동작한다.
- 트랜스파일링이나 번들링 과정이 필요하므로 Webpack 등의 모듈 번들러를 사용하는 것이 일반적이다.

### 14-08

```html
<script type="module" src="lib.mjs"></script>
<script type="module" src="app.mjs"></script>
```
