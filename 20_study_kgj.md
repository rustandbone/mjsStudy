**👉🏻 목차**

- [20.1 strict Mode란?](#201-strict-mode란)
- [strict mode의 적용](#strict-mode의-적용)
- [20.3 전역에 strict mode를 적용하는 것은 피하자](#203-전역에-strict-mode를-적용하는-것은-피하자)
- [20.4 함수 단위로 strict mode를 적용하는 것도 피하자](#204-함수-단위로-strict-mode를-적용하는-것도-피하자)
- [20.5 strict mode가 발생시키는 에러](#205-strict-mode가-발생시키는-에러)
  - [20.5.1 암묵적 전역](#2051-암묵적-전역)
  - [20.5.2 변수, 함수, 매개변수의 삭제](#2052-변수-함수-매개변수의-삭제)
  - [20.5.3 매개변수 이름의 중복](#2053-매개변수-이름의-중복)
  - [20.5.4 with문의 사용](#2054-with문의-사용)
- [20.6 strict mode 적용에 의한 변화](#206-strict-mode-적용에-의한-변화)
  - [20.6.1 일반 함수의 this](#2061-일반-함수의-this)
  - [20.6.2 arguments 객체](#2062-arguments-객체)

## 20.1 strict Mode란?

```javascript
function foo() {
  x = 10;
}
foo();

console.log(x); // ?
```

1. x 변수를 찾아야 x에 값을 할당할 수 있기 때문에 자바스크립트 엔진은 x변수가 어디에서 선언되었는지 스코프 체인을 통해 검색
2. foo함수의 스코프에서 x 변수 선언을 검색
   -> 함수 내부에 선언이 없으므로 검색 실패
3. foo 함수 컨텍스트의 상위 스코프에서 x 변수 선언 검색

<details>
<summary>결과는?</summary>
<div markdown="1">
  암묵적으로 전역 객체에 x 프로퍼티를 동적 생성
  -> <b>암묵적 전역</b>
</div>
</details>  
<br/>

- 암묵적 전역은 오류를 발생시키는 원인
  -> `var` , `let`, `const` 변수 사용

> 💡 strict Mode(엄격모드)
> 잠재적인 오류 발생을 막기 위한 환경
> 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생
> ESLint같은 린트 도구도 유사한 효과를 얻을 수 있음

## strict mode의 적용

```javascript
---전역의 선두
'use strict';

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();

---함수 맨 앞

function foo() {
  'use strict';

  x = 10; // ReferenceError: x is not defined
}
foo();
```

- 엄격모드를 적용하려면 전역의 맨 앞 또는 함수 맨앞에 `use strict`을 추가
- 추가하면 스크립트 전체에 엄격모드가 적용

```javascript
function foo() {
  x = 10; // 에러를 발생시키지 않는다.
  ('use strict');
}
foo();
```

- 코드 맨 앞에 `use strict`을 위치시키기 않으면 엄격모드가 제대로 동작하지 않음

## 20.3 전역에 strict mode를 적용하는 것은 피하자

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      'use strict';
    </script>
    <script>
      x = 1; // 에러가 발생하지 않는다.
      console.log(x); // 1
    </script>
    <script>
      'use strict';

      y = 1; // ReferenceError: y is not defined
      console.log(y);
    </script>
  </body>
</html>
```

- 전역에 적용한 엄격모드는 스크립트 단위로 적용
- 다른 스크립트에 영향을 주지 않고 해당 스크립트에 한정되어 적용
- 이처럼 strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있음

```javascript
// 즉시 실행 함수의 선두에 strict mode 적용
(function () {
  'use strict';

  // Do something...
})();
```

- 위와 같은 경우는 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용

## 20.4 함수 단위로 strict mode를 적용하는 것도 피하자

```javascript
(function () {
  // non-strict mode
  var lеt = 10; // 에러가 발생하지 않는다.

  function foo() {
    'use strict';

    let = 20; // SyntaxError: Unexpected strict mode reserved word
  }
  foo();
})();
```

- 함수 단위로 엄격모드를 적용할 수 있음
- 따라서, 엄격모드는 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직

## 20.5 strict mode가 발생시키는 에러

### 20.5.1 암묵적 전역

```javascript
(function () {
  'use strict';

  x = 1;
  console.log(x); // ReferenceError: x is not defined
})();
```

- 선언하지 않은 변수를 참조하면 ReferenceError가 발생

### 20.5.2 변수, 함수, 매개변수의 삭제

```javascript
(function () {
  'use strict';

  var x = 1;
  delete x;
  // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo;
  // SyntaxError: Delete of an unqualified identifier in strict mode.
})();
```

- `delete` 연산자로 변수, 함수, 매개변수를 삭제하면 SyntaxError가 발생

### 20.5.3 매개변수 이름의 중복

```javascript
(function () {
  'use strict';

  //SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

- 중복된 매개변수 이름을 사용하면 SyntaxError가 발생

### 20.5.4 with문의 사용

```javascript
(function () {
  'use strict';

  // SyntaxError: Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
})();
```

- `with`문을 사용하면 SyntaxError가 발생

## 20.6 strict mode 적용에 의한 변화

### 20.6.1 일반 함수의 this

```javascript
(function () {
  'use strict';

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
})();
```

- 엄격 모드에서 함수를 일반함수로 호출하면 `this`에 `undefined`가 바인딩

### 20.6.2 arguments 객체

```javascript
(function (a) {
  'use strict';
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0: 1, length: 1 }
})(1);
```

- 엄격모드에서는 매개변수에 전달된 인수를 재할당하여 변경해도 `arguments` 객체에 반영되지 않음
