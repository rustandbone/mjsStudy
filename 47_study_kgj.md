# 에러 처리

🔖 목차

- [에러 처리](#에러-처리)
  - [47.1 에러 처리의 필요성](#471-에러-처리의-필요성)
  - [47.2 try...catch...finally문](#472-trycatchfinally문)
  - [Error 객체](#error-객체)
    - [에러 객체 생성 가능한 `Error` 생성자 함수](#에러-객체-생성-가능한-error-생성자-함수)
  - [47.4 throw문](#474-throw문)
  - [47.5 에러의 전파 \*](#475-에러의-전파-)

## 47.1 에러 처리의 필요성

- 에러가 발생하지 않는 코드를 작성하는 것은 불가능

🔽 발생한 에러에 대해 대처하지 않고 방치하면 프로그램은 강제 종료됨

```javascript
console.log('[Start]');

foo(); // ReferenceError: foo is not defined
// 발생한 에러를 방치하면 프로그램은 강제 종료된다.

// 에러에 의해 프로그램이 강제 종료되어 아래 코드는 실행되지 않는다.
console.log('[End]');
```

🔽 `try...catch문`을 사용하면 발생한 에러에 적절하게 대응하면서 강제 종료 없이 계속 코드를 진행시킬 수 있음

```javascript
console.log('[Start]');

try {
  foo();
} catch (error) {
  console.error('[에러 발생]', error);
  // [에러 발생] ReferenceError: foo is not defined
}

// 발생한 에러에 적절한 대응을 하면 프로그램이 강제 종료되지 않는다.
console.log('[End]');
```

🔽 직접적으로 에러를 발생하지는 않는 예외적인 상황 발생 가능

- 해당 상황에 적절하게 대응하지 않으면 에러로 이어질 가능성이 큼 \*

```javascript
// DOM에 button 요소가 존재하지 않으면 querySelector 메서드는 에러를 발생시키지 않고 null을 반환한다.
const $button = document.querySelector('button'); // null

$button.classList.add('disabled');
// TypeError: Cannot read property 'classList' of null

-------
$button?.classList.add('disabled');
```

> - 에러나 예외적인 상황은 너무나 다양하기 때문에 아무런 조치없이 프로그램이 강제 종료된다면 원인을 파악하여 대응하기 어려움
> - 에러 상황이 발생할 수 있다는 전제하고 이에 대응하는 코드를 작성하는 것이 중요!

## 47.2 try...catch...finally문

- 일반적으로 이 방법을 에러 처리라고 함
- `try...catch...finally문`으로 에러 처리하면 프로그램이 강제 종료되지 않음
- `finally문`은 불필요하다면 생략 가능
- `catch문`도 생략 가능하지만 `catch문`이 없는 `try문`은 의미가 없으므로 생략하지 않음
- 코드 블럭
  ```js
  try {
    // 실행할 코드(에러가 발생할 가능성이 있는 코드)
  } catch (err) {
    //try 코드 블록에서 에러가 발생하면 이 코드 블록의 코드가 실행된다.
    // err에는 try 코드 블록에서 발생한 Error 객체가 전달된다.
  } finally {
    // 에러 발생과 상관없이 반드시 한 번 실행된다.
  }
  ```
- 가장 먼저 `try 코드블록` 실행
- `try 코드 블록`에서 에러 발생 시 `catch 코드블록` 실행
- `finally 코드 블록`은 에러 발생과 상관없이 반드시 한번 실행

## Error 객체

- `Error` 생성자 함수는 에러 객체 생성
- 에러를 상세히 설명하는 에러 메세지를 인수로 전달할 수 있음

```javascript
const error = new Error('invalid');
```

- `Error` 생성자 함수가 생성한 에러 객체는 `message` 프로퍼티와 `stack` 프로퍼티를 가짐
- `message` 프로퍼티 : 인수로 전달한 에러메세지
- `stack` 프로퍼티 : 에러를 발생시킨 콜스택의 호출 정보를 나타내는 문자열, 디버깅 목적으로 사용

#### 에러 객체 생성 가능한 `Error` 생성자 함수

<img src="https://velog.velcdn.com/images/cline1103/post/a95cb541-eabb-4186-9a36-5312ceb05d39/image.png" width="500">

```javascript
1 @ 1;    // SyntaxError: Invalid or unexpected token
foo();    // ReferenceError: foo is not defined
null.foo; // TypeError: Cannot read property 'foo' of null
new Array(-1); // RangeError: Invalid array length
decodeURIComponent('%'); // URIError: URI malformed
```

## 47.4 throw문

- `Error` 생성자 함수로 에러 객체를 생성한다고 에러가 발생하는 것이 아님
- 에러 객체 생성과 에러 발생의 의미는 다름

```javascript
try {
  // 에러 객체를 생성한다고 에러가 발생하는 것은 아니다.
  new Error('something wrong');
} catch (error) {
  console.log(error);
}
```

- 에러를 발생시키려면 `try` 코드 블록에서 `throw`문으로 에러 객체를 던져야 함
- `throw 표현식` \*

[예제 1]

```javascript
try {
  // 에러 객체를 던지면 catch 코드 블록이 실행되기 시작한다.
  throw new Error('something wrong');
} catch (error) {
  console.log(error);
}
```

[예제 2]

```javascript
// 외부에서 전달받은 콜백 함수를 n번만큼 반복 호출한다
const repeat = (n, f) => {
  // 매개변수 f에 전달된 인수가 함수가 아니면 TypeError를 발생시킨다.
  if (typeof f !== 'function') throw new TypeError('f must be a function');

  for (var i = 0; i < n; i++) {
    f(i); // i를 전달하면서 f를 호출
  }
};

try {
  repeat(2, 1); // 두 번째 인수가 함수가 아니므로 TypeError가 발생(throw)한다.
} catch (err) {
  console.error(err); // TypeError: f must be a function
}
```

## 47.5 에러의 전파 \*

- 에러는 콜 스택의 <u>실행중인 실행컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향</u>으로 전파됨

```javascript
const foo = () => {
  throw Error('foo에서 발생한 에러'); // ④
};

const bar = () => {
  foo(); // ③
};

const baz = () => {
  bar(); // ②
};

try {
  baz(); // ①
} catch (err) {
  console.error(err);
}
```

<img src="https://velog.velcdn.com/images/dev-redo/post/42b0893c-f334-446d-80c8-84454fe36631/image.png" width="200"/>

- `throw`된 에러를 캐치하지 않으면 호출자 방향으로 전파됨 \*
- 비동기 함수인 `setTimeout`이나 프로미스 후속 처리 메서드의 콜백함수는 호출자가 없다는 것을 주의
