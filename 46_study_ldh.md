# 제너레이터와 async/await

- [제너레이터와 async/await](#제너레이터와-asyncawait)
- [46.1 제너레이터란?](#461-제너레이터란)
- [46.2 제너레이터 함수의 정의](#462-제너레이터-함수의-정의)
  - [46-01](#46-01)
  - [46-02](#46-02)
  - [46-03](#46-03)
  - [46-04](#46-04)
- [46.3 제너레이터 객체](#463-제너레이터-객체)
  - [46-05](#46-05)
  - [46-06](#46-06)
  - [46-07](#46-07)
- [46.4 제너레이터의 일시 중지와 재개](#464-제너레이터의-일시-중지와-재개)
  - [46-08](#46-08)
  - [46-09](#46-09)
- [46.5 제너레이터의 활용](#465-제너레이터의-활용)
  - [46.5.1 이터러블의 구현](#4651-이터러블의-구현)
    - [46-10](#46-10)
    - [46-11](#46-11)
    - [46-12](#46-12)
    - [46-13](#46-13)
    - [46-14](#46-14)
    - [46-15](#46-15)
    - [46-16](#46-16)
    - [46-17](#46-17)
    - [46-18](#46-18)
    - [46-19](#46-19)
    - [46-20](#46-20)
    - [46-21](#46-21)
    - [46-22](#46-22)
    - [46-23](#46-23)

# 46.1 제너레이터란?

ES6에서 도입된 제너레이터. 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수.

- 제너레이터와 일반 함수의 차이
  - 제너레이터 함수는 함수 호출자에게 함수 실행의 제어권을 양도할 수 있다
    일반 함수를 호출하면 제어권이 함수에게 넘어가고 함수 코드 일괄 실행. 함수 호출자는 함수를 호출한 이후 함수 실행 제어 불가.
    제너레이터 함수는 함수 실행을 함수 호출자가 제어할 수 있음. 일시 중지시키거나 재개시킬 수 있음. 함수 제어권을 함수가 독점하는 것이 아니라 함수 호출자에게 양도(yield)할 수 있다는 것
  - 제너레이터 함수는 함수 호출자와 함수의 상태를 주고받을 수 있다
    일반 함수를 호출하면 매개변수를 통해 함수 외부에서 값을 주입받고 함수 코드를 일괄 실행하여 결과값을 함수 외부로 반환. 함수가 실행되는 동안 함수 외부에서 내부로 값을 전달하여 상태 변경 불가.
    제너레이터 함수는 함수 호출자와 양방향으로 함수의 상태를 주고받을 수 있음. 제너레이터 함수는 함수 호출자에게 상태를 전달할 수 있고, 함수 호출자로부터 상태를 전달받을 수도 있음
  - 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다
    일반 함수를 호출하면 함수 코드를 일괄 실행하고 값을 반환. 제너레이터 함수를 호출하면 함수 코드를 실행하는 것이 아니라 이터러블이면서 동시에 이터레이터인 제너레이터 객체를 반환

# 46.2 제너레이터 함수의 정의

function\* 키워드로 선언하고 하나 이상의 yield 표현식 포함

### 46-01

```javascript
// 제너레이터 함수 선언문
function* genDecFunc() {
  yield 1;
}

// 제너레이터 함수 표현식
const genExpFunc = function* () {
  yield 1;
};

// 제너레이터 메서드
const obj = {
  *genObjMethod() {
    yield 1;
  },
};

// 제너레이터 클래스 메서드
class MyClass {
  *genClsMethod() {
    yield 1;
  }
}
```

\*의 위치는 function 키워드와 함수 이름 사이라면 상관없음. 바로 뒤에 붙이는 것 권장.

### 46-02

```javascript
function* genFunc() {
  yield 1;
}

function* genFunc() {
  yield 1;
}

function* genFunc() {
  yield 1;
}

function* genFunc() {
  yield 1;
}
```

### 46-03

제너레이터 함수는 화살표 함수로 정의할 수 없음

```javascript
const genArrowFunc = * () => {
  yield 1;
}; // SyntaxError: Unexpected token '*'
```

### 46-04

제너레이터 함수는 new 연산자와 함께 생성자 함수로 호출할 수 없음

```javascript
function* genFunc() {
  yield 1;
}

new genFunc(); // TypeError: genFunc is not a constructor
```

# 46.3 제너레이터 객체

제너레이터 함수를 호출하면 일반 함수처럼 함수 코드 블록을 실행하는 것이 아니라, 제너레이터 객체를 생성해 반환함

제너레이터 객체는 이터러블이면서 동시에 이터레이터이다.  
=> 제너레이터 객체는 Symbol.iterator 메서드를 상속받는 이터러블이면서  
value, done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는  
next 메서드를 소유하는 이터레이터인 것.

### 46-05

```javascript
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
const generator = genFunc();

// 제너레이터 객체는 이터러블이면서 동시에 이터레이터다.
// 이터러블은 Symbol.iterator 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 객체다.
console.log(Symbol.iterator in generator); // true
// 이터레이터는 next 메서드를 갖는다.
console.log('next' in generator); // true
```

제너레이터 객체는 next 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 return, throw 메서드를 가짐

next 메서드 : 호출하면 제너레이터 함수의 yield 표현식까지 코드 블록 실행. yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환함

return 메서드 : 호출하면 인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 갖는 이터레이터 리절트 객체를 반환함

### 46-06

```javascript
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.return('End!')); // {value: "End!", done: true}
```

throw 객체 : 호출하면 인수로 전달받은 에러 발생시키고 undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 갖는 이터레이터 리절트 객체를 반환함

### 46-07

```javascript
function* genFunc() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } catch (e) {
    console.error(e);
  }
}

const generator = genFunc();

console.log(generator.next()); // {value: 1, done: false}
console.log(generator.throw('Error!')); // {value: undefined, done: true}
```

# 46.4 제너레이터의 일시 중지와 재개

yield 키워드와 next 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 다시 재개 가능.  
제너레이터는 함수 호출자에게 제어권을 양도하여 필요한 시점에 함수 실행 재개 가능.

제너레이터 함수 호출 => 제너레이터 객체 반환  
제너레이터 객체는 next 메서드를 가짐  
제너레이터 객체의 next 메서드 호출하면 제너레이터 함수의 코드 블록 실행함

=> yield 표현식까지만 실행  
yield 키워드는 제너레이터 함수의 실행을 일시 중지시키거나  
yield 키워드 뒤에 오는 표현식의 평가 결과를 제너레이터 함수 호출자에게 반환함

### 46-08

```javascript
// 제너레이터 함수
function* genFunc() {
  yield 1;
  yield 2;
  yield 3;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이면서 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc();

// 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 첫 번째 yield 표현식에서 yield된 값 1이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 1, done: false}

// 다시 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 두 번째 yield 표현식에서 yield된 값 2가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 2, done: false}

// 다시 next 메서드를 호출하면 세 번째 yield 표현식까지 실행되고 일시 중지된다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 세 번째 yield 표현식에서 yield된 값 3이 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 false가 할당된다.
console.log(generator.next()); // {value: 3, done: false}

// 다시 next 메서드를 호출하면 남은 yield 표현식이 없으므로 제너레이터 함수의 마지막까지 실행한다.
// next 메서드는 이터레이터 리절트 객체({value, done})를 반환한다.
// value 프로퍼티에는 제너레이터 함수의 반환값 undefined가 할당된다.
// done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었음을 나타내는 true가 할당된다.
console.log(generator.next()); // {value: undefined, done: true}
```

제너레이터 객체의 next 메서드 호출하면  
yield 표현식까지 실행되고 일시 중지(suspend)  
제어권이 호출자로 양도(yield)됨  
이후 호출자가 또다시 next 메서드 호출하면  
일시중지된 코드부터 실행 재개(resume)  
다음 yield 표현식까지 실행되고 또 다시 일시 중지

next 메서드는 value, done 프로퍼티를 갖는 이터레이터 리절트 객체 반환  
value 프로퍼티에는 yield 표현식에서 yield된 값  
done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언 값 할당

이터레이터 next 메서드와 달리  
제너레이터 객체의 next 메서드에는 인수 전달 가능.

제너레이터 객체의 next 메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당됨  
(yield 표현식을 할당받는 변수에 yield 표현식의 평가 결과가 할당되지 않음)

### 46-09

```javascript
function* genFunc() {
  // 처음 next 메서드를 호출하면 첫 번째 yield 표현식까지 실행되고 일시 중지된다.
  // 이때 yield된 값 1은 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  // x 변수에는 아직 아무것도 할당되지 않았다. x 변수의 값은 next 메서드가 두 번째 호출될 때 결정된다.
  const x = yield 1;

  // 두 번째 next 메서드를 호출할 때 전달한 인수 10은 첫 번째 yield 표현식을 할당받는 x 변수에 할당된다.
  // 즉, const x = yield 1;은 두 번째 next 메서드를 호출했을 때 완료된다.
  // 두 번째 next 메서드를 호출하면 두 번째 yield 표현식까지 실행되고 일시 중지된다.
  // 이때 yield된 값 x + 10은 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  const y = yield x + 10;

  // 세 번째 next 메서드를 호출할 때 전달한 인수 20은 두 번째 yield 표현식을 할당받는 y 변수에 할당된다.
  // 즉, const y = yield (x + 10);는 세 번째 next 메서드를 호출했을 때 완료된다.
  // 세 번째 next 메서드를 호출하면 함수 끝까지 실행된다.
  // 이때 제너레이터 함수의 반환값 x + y는 next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에 할당된다.
  // 일반적으로 제너레이터의 반환값은 의미가 없다.
  // 따라서 제너레이터에서는 값을 반환할 필요가 없고 return은 종료의 의미로만 사용해야 한다.
  return x + y;
}

// 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.
// 이터러블이며 동시에 이터레이터인 제너레이터 객체는 next 메서드를 갖는다.
const generator = genFunc(0);

// 처음 호출하는 next 메서드에는 인수를 전달하지 않는다.
// 만약 처음 호출하는 next 메서드에 인수를 전달하면 무시된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 첫 번째 yield된 값 1이 할당된다.
let res = generator.next();
console.log(res); // {value: 1, done: false}

// next 메서드에 인수로 전달한 10은 genFunc 함수의 x 변수에 할당된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 두 번째 yield된 값 20이 할당된다.
res = generator.next(10);
console.log(res); // {value: 20, done: false}

// next 메서드에 인수로 전달한 20은 genFunc 함수의 y 변수에 할당된다.
// next 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티에는 제너레이터 함수의 반환값 30이 할당된다.
res = generator.next(20);
console.log(res); // {value: 30, done: true}
```

제너레이터 함수는 next 메서드와 yield 표현식으로 함수 호출자와 함수의 상태를 주고받을 수 있음.

함수 호출자는 next 메서드를 통해 yield 표현식까지 함수를 실행시켜 제너레이터 객체가 관리하는 상태(yield된 값)를 꺼내올 수 있고  
next 메서드에 인수를 전달해서 제너레이터 객체에 상태(yield 표현식을 할당받는 변수)를 밀어넣을 수 있음  
=> 이러한 제너레이터의 특성을 활용해 비동기 처리를 동기 처리처럼 구현 가능

# 46.5 제너레이터의 활용

## 46.5.1 이터러블의 구현

### 46-10

```javascript
// 무한 이터러블을 생성하는 함수
const infiniteFibonacci = (function () {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() {
      return this;
    },
    next() {
      [pre, cur] = [cur, pre + cur];
      // 무한 이터러블이므로 done 프로퍼티를 생략한다.
      return { value: cur };
    },
  };
})();

// infiniteFibonacci는 무한 이터러블이다.
for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...2584 4181 6765
}
```

### 46-11

```javascript
// 무한 이터러블을 생성하는 제너레이터 함수
const infiniteFibonacci = (function* () {
  let [pre, cur] = [0, 1];

  while (true) {
    [pre, cur] = [cur, pre + cur];
    yield cur;
  }
})();

// infiniteFibonacci는 무한 이터러블이다.
for (const num of infiniteFibonacci) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...2584 4181 6765
}
```

### 46-12

```javascript
// node-fetch는 node.js 환경에서 window.fetch 함수를 사용하기 위한 패키지다.
// 브라우저 환경에서 이 예제를 실행한다면 아래 코드는 필요 없다.
// https://github.com/node-fetch/node-fetch
const fetch = require('node-fetch');

// 제너레이터 실행기
const async = (generatorFunc) => {
  const generator = generatorFunc(); // ②

  const onResolved = (arg) => {
    const result = generator.next(arg); // ⑤

    return result.done
      ? result.value // ⑨
      : result.value.then((res) => onResolved(res)); // ⑦
  };

  return onResolved; // ③
};

async(function* fetchTodo() {
  // ①
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = yield fetch(url); // ⑥
  const todo = yield response.json(); // ⑧
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
})(); // ④
```

### 46-13

```javascript
const fetch = require('node-fetch');
// https://github.com/tj/co
const co = require('co');

co(function* fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = yield fetch(url);
  const todo = yield response.json();
  console.log(todo);
  // { userId: 1, id: 1, title: 'delectus aut autem', completed: false }
});
```

### 46-14

```javascript
const fetch = require('node-fetch');

async function fetchTodo() {
  const url = 'https://jsonplaceholder.typicode.com/todos/1';

  const response = await fetch(url);
  const todo = await response.json();
  console.log(todo);
  // {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
}

fetchTodo();
```

### 46-15

```javascript
// async 함수 선언문
async function foo(n) {
  return n;
}
foo(1).then((v) => console.log(v)); // 1

// async 함수 표현식
const bar = async function (n) {
  return n;
};
bar(2).then((v) => console.log(v)); // 2

// async 화살표 함수
const baz = async (n) => n;
baz(3).then((v) => console.log(v)); // 3

// async 메서드
const obj = {
  async foo(n) {
    return n;
  },
};
obj.foo(4).then((v) => console.log(v)); // 4

// async 클래스 메서드
class MyClass {
  async bar(n) {
    return n;
  }
}
const myClass = new MyClass();
myClass.bar(5).then((v) => console.log(v)); // 5
```

### 46-16

```javascript
class MyClass {
  async constructor() { }
  // SyntaxError: Class constructor may not be an async method
}

const myClass = new MyClass();
```

### 46-17

```javascript
const fetch = require('node-fetch');

const getGithubUserName = async (id) => {
  const res = await fetch(`https://api.github.com/users/${id}`); // ①
  const { name } = await res.json(); // ②
  console.log(name); // Ungmo Lee
};

getGithubUserName('ungmo2');
```

### 46-18

```javascript
async function foo() {
  const a = await new Promise((resolve) => setTimeout(() => resolve(1), 3000));
  const b = await new Promise((resolve) => setTimeout(() => resolve(2), 2000));
  const c = await new Promise((resolve) => setTimeout(() => resolve(3), 1000));

  console.log([a, b, c]); // [1, 2, 3]
}

foo(); // 약 6초 소요된다.
```

### 46-19

```javascript
async function foo() {
  const res = await Promise.all([
    new Promise((resolve) => setTimeout(() => resolve(1), 3000)),
    new Promise((resolve) => setTimeout(() => resolve(2), 2000)),
    new Promise((resolve) => setTimeout(() => resolve(3), 1000)),
  ]);

  console.log(res); // [1, 2, 3]
}

foo(); // 약 3초 소요된다.
```

### 46-20

```javascript
async function bar(n) {
  const a = await new Promise((resolve) => setTimeout(() => resolve(n), 3000));
  // 두 번째 비동기 처리를 수행하려면 첫 번째 비동기 처리 결과가 필요하다.
  const b = await new Promise((resolve) =>
    setTimeout(() => resolve(a + 1), 2000)
  );
  // 세 번째 비동기 처리를 수행하려면 두 번째 비동기 처리 결과가 필요하다.
  const c = await new Promise((resolve) =>
    setTimeout(() => resolve(b + 1), 1000)
  );

  console.log([a, b, c]); // [1, 2, 3]
}

bar(1); // 약 6초 소요된다.
```

### 46-21

```javascript
try {
  setTimeout(() => {
    throw new Error('Error!');
  }, 1000);
} catch (e) {
  // 에러를 캐치하지 못한다
  console.error('캐치한 에러', e);
}
```

### 46-22

```javascript
const fetch = require('node-fetch');

const foo = async () => {
  try {
    const wrongUrl = 'https://wrong.url';

    const response = await fetch(wrongUrl);
    const data = await response.json();
    console.log(data);
  } catch (err) {
    console.error(err); // TypeError: Failed to fetch
  }
};

foo();
```

### 46-23

```javascript
const fetch = require('node-fetch');

const foo = async () => {
  const wrongUrl = 'https://wrong.url';

  const response = await fetch(wrongUrl);
  const data = await response.json();
  return data;
};

foo().then(console.log).catch(console.error); // TypeError: Failed to fetch
```
