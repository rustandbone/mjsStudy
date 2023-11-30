- [45장 프로미스](#45장-프로미스)
- [45.1 비동기 처리를 위한 콜백 패턴의 단점](#451-비동기-처리를-위한-콜백-패턴의-단점)
  - [45.1.1 콜백 헬](#4511-콜백-헬)
    - [45-01](#45-01)
    - [45-02](#45-02)
    - [45-03](#45-03)
    - [45-04](#45-04)
    - [45-05](#45-05)
    - [45-06](#45-06)
    - [45-07](#45-07)
    - [45-08](#45-08)
  - [45.1.2 에러 처리의 한계](#4512-에러-처리의-한계)
    - [45-09](#45-09)
- [45.2 프로미스의 생성](#452-프로미스의-생성)
  - [45-10](#45-10)
  - [45-11](#45-11)
  - [45-12](#45-12)
  - [45-13](#45-13)
- [45.3 프로미스의 후속 처리 메서드](#453-프로미스의-후속-처리-메서드)
  - [45.3.1 Promise.prototype.then](#4531-promiseprototypethen)
    - [45-14](#45-14)
  - [45.3.2 Promise.prototype.catch](#4532-promiseprototypecatch)
    - [45-15](#45-15)
    - [45-16](#45-16)
  - [45.3.3 Promise.prototype.finally](#4533-promiseprototypefinally)
    - [45-17](#45-17)
    - [45-18](#45-18)
- [45.4 프로미스의 에러 처리](#454-프로미스의-에러-처리)
  - [45-19](#45-19)
  - [45-20](#45-20)
  - [45-21](#45-21)
  - [45-22](#45-22)
  - [45-23](#45-23)
- [45.5 프로미스 체이닝](#455-프로미스-체이닝)
  - [45-24](#45-24)
  - [45-25](#45-25)
- [45.6 프로미스의 정적 메서드](#456-프로미스의-정적-메서드)
  - [45.6.1 Promise.resolve / Promise.reject](#4561-promiseresolve--promisereject)
    - [45-26](#45-26)
    - [45-27](#45-27)
    - [45-28](#45-28)
    - [45-29](#45-29)
  - [45.6.2 Promise.all](#4562-promiseall)
    - [45-30](#45-30)
    - [45-31](#45-31)
    - [45-32](#45-32)
    - [45-33](#45-33)
    - [45-34](#45-34)
  - [45.6.3 Promise.race](#4563-promiserace)
    - [45-35](#45-35)
    - [45-36](#45-36)
  - [45.6.4 Promise.allSettled](#4564-promiseallsettled)
    - [45-37](#45-37)
    - [45-38](#45-38)
- [45.7 마이크로태스크 큐](#457-마이크로태스크-큐)
  - [45-39](#45-39)
- [45.8 fetch](#458-fetch)
  - [45-40](#45-40)
  - [45-41](#45-41)
  - [45-42](#45-42)
  - [1. GET 요청](#1-get-요청)
  - [45-43](#45-43)
  - [2. POST 요청](#2-post-요청)
  - [45-44](#45-44)
  - [3. PATCH 요청](#3-patch-요청)
  - [45-45](#45-45)
  - [4. DELETE 요청](#4-delete-요청)
  - [45-46](#45-46)

# 45장 프로미스

자바스크립트는 비동기 처리를 위한 하나의 패턴으로 콜백 함수 사용. 그러나 가독성이 나쁘고 에러 처리 곤란하고, 여러 개의 비동기 처리를 한 번에 처리하는 데 한계가 있음

프로미스 : ES6에서도 도입된 비동기 처리를 위한 또 다른 패턴.  
전통적인 콜백 패턴의 단점 보완하며, 비동기 처리 시점을 명확하게 표현할 수 있음

# 45.1 비동기 처리를 위한 콜백 패턴의 단점

## 45.1.1 콜백 헬

### 45-01

```javascript
// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콘솔에 출력한다.
      console.log(JSON.parse(xhr.response));
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

// id가 1인 post를 취득
get('https://jsonplaceholder.typicode.com/posts/1');
/*
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere ...",
  "body": "quia et suscipit ..."
}
*/
```

비동기 함수 : 함수 내부에 비동기로 동작하는 코드를 포함하는 함수  
get 함수는 비동기 함수.  
비동기 함수를 호출하면 함수 내부의 비동기로 동작하는 코드가 완료되지 않았다 해도 기다리지 않고 즉시 종료됨.  
비동기로 동작하는 코드는 비동기 함수가 종료된 이후에 완료되는 것.  
=> 비동기로 동작하는 코드에서 처리 결과를 외부로 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않음

### 45-02

```javascript
let g = 0;

// 비동기 함수인 setTimeout 함수는 콜백 함수의 처리 결과를 외부로 반환하거나
// 상위 스코프의 변수에 할당하지 못한다.
setTimeout(() => {
  g = 100;
}, 0);
console.log(g); // 0
```

get 함수도 비동기 함수.  
이유 : get 함수 내부의 onload 이벤트 핸들러가 비동기로 동작하기 때문.

get 함수를 호출하면  
GET 요청을 전송하고  
onload 이벤트 핸들러를 등록한 다음  
undefined를 반환하고 즉시 종료됨

비동기 함수인 get 함수 내부의 onload 이벤트 핸들러는 get 함수가 종료된 이후에 실행됨  
get 함수의 onload 이벤트 핸들러에서 서버의 응답 결과를 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않음

### 45-03

```javascript
// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // ① 서버의 응답을 반환한다.
      return JSON.parse(xhr.response);
    }
    console.error(`${xhr.status} ${xhr.statusText}`);
  };
};

// ② id가 1인 post를 취득
const response = get('https://jsonplaceholder.typicode.com/posts/1');
console.log(response); // undefined
```

- get 함수가 호출되면 XMLHttpRequest 객체를 생성하고
- HTTP 요청을 초기화한 후
- HTTP 요청을 전송함
- xhr.onload 이벤트 핸들러 프로퍼티에 이벤트 핸들러를 바인딩하고 종료함
- get 함수에 명시적인 반환문이 없으므로 get 함수는 undefined를 반환함

xhr.onload 이벤트 핸들러 프로퍼티에 바인딩한 이벤트 핸들러의 반환문은 get 함수의 반환문이 아니다.

### 45-04

```html
<!DOCTYPE html>
<html>
  <body>
    <input type="text" />
    <script>
      document.querySelector('input').oninput = function () {
        console.log(this.value);
        // 이벤트 핸들러에서의 반환은 의미가 없다.
        return this.value;
      };
    </script>
  </body>
</html>
```

### 45-05

```javascript
let todos;

// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // ① 서버의 응답을 상위 스코프의 변수에 할당한다.
      todos = JSON.parse(xhr.response);
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

// id가 1인 post를 취득
get('https://jsonplaceholder.typicode.com/posts/1');
console.log(todos); // ② undefined
```

비동기 함수는 비동기 처리 결과를 외부에 반환할 수 없고  
상위 스코프의 변수에 할당할 수도 없음.  
비동기 함수의 처리 결과(서버 응답 등)에 대한 후속 처리는 비동기 함수 내부에서 수행해야 함

이때 비동기 함수를 범용적으로 사용하기 위해  
비동기 함수에 대한 비동기 처리 결과에 대한 후속 처리를 수행하는 콜백 함수를 전달하는 것이 일반적.  
필요에 따라 비동기 처리가 성공하면 호출될 콜백 함수와  
비동기 처리가 실패하면 호출될 콜백 함수를 전달할 수 있음

### 45-06

```javascript
// GET 요청을 위한 비동기 함수
const get = (url, successCallback, failureCallback) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콜백 함수에 인수로 전달하면서 호출하여 응답에 대한 후속 처리를 한다.
      successCallback(JSON.parse(xhr.response));
    } else {
      // 에러 정보를 콜백 함수에 인수로 전달하면서 호출하여 에러 처리를 한다.
      failureCallback(xhr.status);
    }
  };
};

// id가 1인 post를 취득
// 서버의 응답에 대한 후속 처리를 위한 콜백 함수를 비동기 함수인 get에 전달해야 한다.
get('https://jsonplaceholder.typicode.com/posts/1', console.log, console.error);
/*
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere ...",
  "body": "quia et suscipit ..."
}
*/
```

콜백 헬 : 콜백 함수 호출이 중첩되어 복잡도가 높아지는 현상

### 45-07

```javascript
// GET 요청을 위한 비동기 함수
const get = (url, callback) => {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콜백 함수에 전달하면서 호출하여 응답에 대한 후속 처리를 한다.
      callback(JSON.parse(xhr.response));
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

const url = 'https://jsonplaceholder.typicode.com';

// id가 1인 post의 userId를 취득
get(`${url}/posts/1`, ({ userId }) => {
  console.log(userId); // 1
  // post의 userId를 사용하여 user 정보를 취득
  get(`${url}/users/${userId}`, (userInfo) => {
    console.log(userInfo); // {id: 1, name: "Leanne Graham", username: "Bret",...}
  });
});
```

### 45-08

```javascript
get('/step1', (a) => {
  get(`/step2/${a}`, (b) => {
    get(`/step3/${b}`, (c) => {
      get(`/step4/${c}`, (d) => {
        console.log(d);
      });
    });
  });
});
```

## 45.1.2 에러 처리의 한계

문제점 중 가장 심각한 것은 에러 처리가 곤란하다는 것.

### 45-09

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

1. 비동기 함수 setTimeout 호출
2. setTimeout 함수의 실행 컨텍스트 생성되어 콜 스택에 푸시되어 실행
3. setTimeout은 비동기 함수이므로 콜백 함수가 호출되는 것을 기다리지 않고 즉시 종료되어 콜 스택에서 제거
4. 타이머가 만료되면 콜백 함수는 태스크 큐로 푸시됨
5. 콜 스택이 비어졌을 때 이벤트 루프에 의해 콜 스택으로 푸시되어 실행됨

- setTimeout 함수의 콜백 함수가 실행될 때 setTimeout 함수는 이미 콜 스택에서 제거된 상태.
- setTimeout 함수의 콜백 함수를 호출한 것이 setTimeout 함수가 아닌 것

에러는 호출자(caller) 방향으로 잔파됨. 즉, 콜 스택의 아래 방향(실행 중인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향)으로 전파됨. 그러나 setTimeout 함수의 콜백 함수를 호출한 것은 setTimeout 함수가 아니기에 에러가 catch 블록에서 캐치되지 않는 것

# 45.2 프로미스의 생성

Promise 생성자 함수를 new 연산자와 함께 호출하면 프로미스(Promise 객체)를 생성  
(Promise는 호스트 객체가 아닌 표준 빌트인 객체)

Promise 생성자 함수는 비동기 처리를 수행할 콜백 함수를 인수로 전달받음. 이 콜백 함수는 resolve와 reject 함수를 인수로 전달받음.

### 45-10

```javascript
// 프로미스 생성
const promise = new Promise((resolve, reject) => {
  // Promise 함수의 콜백 함수 내부에서 비동기 처리를 수행한다.
  if (/* 비동기 처리 성공 */) {
    resolve('result');
  } else { /* 비동기 처리 실패 */
    reject('failure reason');
  }
});
```

Promise 생성자 함수가 인수로 전달받은 콜백 함수 내부에서 비동기 처리 수행. 비동기 처리 성굥하면 resolve 함수 호출, 실패하면 reject 함수 호출

### 45-11

```javascript
// GET 요청을 위한 비동기 함수
const promiseGet = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        // 성공적으로 응답을 전달받으면 resolve 함수를 호출한다.
        resolve(JSON.parse(xhr.response));
      } else {
        // 에러 처리를 위해 reject 함수를 호출한다.
        reject(new Error(xhr.status));
      }
    };
  });
};

// promiseGet 함수는 프로미스를 반환한다.
promiseGet('https://jsonplaceholder.typicode.com/posts/1');
```

promiseGet 비동기 함수는 함수 내부에서 프로미스를 생성하고 반환함.  
프로미스는 비동기 처리가 어떻게 진행되고 있는지를 나타내는 상태(state) 정보를 가짐

- 상태 정보
  - pending
    - 비동기 처리가 아직 수행되지 않은 상태
    - 프로미스가 생성된 직후 기본 상태
  - fulfilled
    - 비동기 처리 수행 상태(성공)
    - resolve 함수 호출
  - rejected
    - 비동기 처리 수행 상태(실패)
    - reject 함수 호출

settled 상태 : fulfilled, rejected 상태 - pending이 아닌 상태로 비동기 처리가 수행된 상태  
일단 settled 상태가 되면 더는 다른 상태로 변화할 수 없음

### 45-12

pending => fulfilled  
비동기 처리 결과인 1을 값으로 가짐

```javascript
// fulfilled된 프로미스
const fulfilled = new Promise((resolve) => resolve(1));
```

### 45-13

pending => rejected
Error 객체를 값으로 가짐

```javascript
// rejected된 프로미스
const rejected = new Promise((_, reject) =>
  reject(new Error('error occurred'))
);
```

`프로미스는 비동기 처리 상태와 처리 결과를 관리하는 객체다.`

# 45.3 프로미스의 후속 처리 메서드

비동기 처리 상태가 변하면 후속 처리를 해야 함. 이를 위해 후속 메서드 then, catch, finally 제공.

프로미스의 비동기 처리 상태가 변화하면 후속 처리 메서드에 인수로 전달한 콜백 함수가 선택적으로 호출된다.  
이때 후속 처리 메서드의 콜백 함수에 프로미스의 처리 결과가 인수로 전달됨.

모든 후속 처리 메서드는 프로미스를 반환하고, 비동기로 동작함

## 45.3.1 Promise.prototype.then

두 개의 콜백 함수를 인수로 전달 받음

- 첫 번째 콜백 함수
  - 성공 처리 콜백 함수
  - 프로미스가 fulfilled 상태가 되면 호출됨. 프로미스의 비동기 처리 결과를 인수로 전달받음
- 두 번째 콜백 함수
  - 실패 처리 콜백 함수
  - 프로미스가 rejected 상태가 되면 호출됨. 프로미스의 에러를 인수로 전달받음

### 45-14

```javascript
// fulfilled
new Promise((resolve) => resolve('fulfilled')).then(
  (v) => console.log(v),
  (e) => console.error(e)
); // fulfilled

// rejected
new Promise((_, reject) => reject(new Error('rejected'))).then(
  (v) => console.log(v),
  (e) => console.error(e)
); // Error: rejected
```

then 메서드는 언제나 프로미스 반환.  
then 메서드의 콜백 함수가 프로미스 반환하면 그대로 반환,  
프로미스가 아닌 값을 반환하면 그 값을 암묵적으로 resolve 또는 reject하여 프로미스를 생성해 반환.

## 45.3.2 Promise.prototype.catch

한 개의 콜백 함수를 인수로 전달받음. 프로미스가 rejected 상태인 경우만 호출됨.

### 45-15

then(undefined, onRejected)와 동일하게 동작함.

```javascript
// rejected
new Promise((_, reject) => reject(new Error('rejected'))).catch((e) =>
  console.log(e)
); // Error: rejected
```

언제나 프로미스 반환함

### 45-16

```javascript
// rejected
new Promise((_, reject) => reject(new Error('rejected'))).then(undefined, (e) =>
  console.log(e)
); // Error: rejected
```

## 45.3.3 Promise.prototype.finally

한 개의 콜백 함수를 인수로 전달받음.  
프로미스의 성공, 실패 상관없이 무조건 한 번 호출됨.

프로미스의 상태와 상관없이 공통적으로 수행해야 할 처리 내용이 있을 때 유용함.  
언제나 프로미스 반환.

### 45-17

```javascript
new Promise(() => {}).finally(() => console.log('finally')); // finally
```

### 45-18

```javascript
const promiseGet = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        // 성공적으로 응답을 전달받으면 resolve 함수를 호출한다.
        resolve(JSON.parse(xhr.response));
      } else {
        // 에러 처리를 위해 reject 함수를 호출한다.
        reject(new Error(xhr.status));
      }
    };
  });
};

// promiseGet 함수는 프로미스를 반환한다.
promiseGet('https://jsonplaceholder.typicode.com/posts/1')
  .then((res) => console.log(res))
  .catch((err) => console.error(err))
  .finally(() => console.log('Bye!'));
```

# 45.4 프로미스의 에러 처리

콜백 패턴과 달리 프로미스는 에러를 문제없이 처리할 수 있음

비동기 처리 결과에 대한 후속 처리는 후속 처리 메서드를 사용하여 수행함  
발생한 에러는 then 메서드의 두 번째 콜백 함수로 처리할 수 있음

### 45-19

```javascript
const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1';

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl).then(
  (res) => console.log(res),
  (err) => console.error(err)
); // Error: 404
```

### 45-20

후속 처리 메서드 catch를 사용해 처리할 수도 있음

```javascript
const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1';

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl)
  .then((res) => console.log(res))
  .catch((err) => console.error(err)); // Error: 404
```

### 45-21

catch 메서드를 호출하면 내부적으로 then(undefined, onRejected)을 호출함

```javascript
const wrongUrl = 'https://jsonplaceholder.typicode.com/XXX/1';

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl)
  .then((res) => console.log(res))
  .then(undefined, (err) => console.error(err)); // Error: 404
```

### 45-22

then 메서드의 두 번째 콜백 함수는 첫 번째 콜백 함수에서 발생한 에러를 캐치하지 못하고, 코드가 복잡해져 가독성이 좋지 않음

```javascript
promiseGet('https://jsonplaceholder.typicode.com/todos/1').then(
  (res) => console.xxx(res),
  (err) => console.error(err)
); // 두 번째 콜백 함수는 첫 번째 콜백 함수에서 발생한 에러를 캐치하지 못한다.
```

### 45-23

catch 메서드를 모든 then 메서드를 호출한 이후에 호출하면  
비동기 처리에서 발생한 에러 뿐만 아니라 then 메서드 내부에서 발생한 에러까지 모두 캐치 가능
=> then의 두 번째 콜백 함수보다 catch 메서드 사용 권장

```javascript
promiseGet('https://jsonplaceholder.typicode.com/todos/1')
  .then((res) => console.xxx(res))
  .catch((err) => console.error(err)); // TypeError: console.xxx is not a function
```

# 45.5 프로미스 체이닝

프로미스는 then, catch, finally 후속 처리 메서드를 통해 콜백 헬을 해결함

### 45-24

```javascript
const url = 'https://jsonplaceholder.typicode.com';

// id가 1인 post의 userId를 취득
promiseGet(`${url}/posts/1`)
  // 취득한 post의 userId로 user 정보를 취득
  .then(({ userId }) => promiseGet(`${url}/users/${userId}`))
  .then((userInfo) => console.log(userInfo))
  .catch((err) => console.error(err));
```

=> then, catch, finally 후속 처리 메서드는 언제나 프로미스를 반환하므로 연속적으로 호출 가능 : 프로미스 체이닝

프로미스는 프로미스 체이닝을 통해 비동기 처리 결과를 전달받아 후속 처리를 하므로, 콜백 헬이 발생하지 않음. 콜백 패턴을 사용하므로 콜백 함수를 사용하지 않는 것은 아님

콜백 패턴은 가독성이 좋지 않음 => ES8의 `asyns/await`를 통해 해결 가능
프로미스의 후속 처리 메서드 없이 마치 동기 처리처럼 프로미스가 처리 결과를 반환하도록 구현 가능

### 45-25

async/await도 프로미스를 기반으로 동작

```javascript
const url = 'https://jsonplaceholder.typicode.com';

(async () => {
  // id가 1인 post의 userId를 취득
  const { userId } = await promiseGet(`${url}/posts/1`);

  // 취득한 post의 userId로 user 정보를 취득
  const userInfo = await promiseGet(`${url}/users/${userId}`);

  console.log(userInfo);
})();
```

# 45.6 프로미스의 정적 메서드

Promise는 주로 생성자 함수로 사용되지만, 함수도 객체이므로 메서드를 가질 수 있음  
5가지 정적 메서드 제공

## 45.6.1 Promise.resolve / Promise.reject

이미 존재하는 값을 래핑하여 프로미스를 생성하기 위해 사용

### 45-26

인수로 전달받은 값을 resolve하는 프로미스 생성

```javascript
// 배열을 resolve하는 프로미스를 생성
const resolvedPromise = Promise.resolve([1, 2, 3]);
resolvedPromise.then(console.log); // [1, 2, 3]
```

### 45-27

위 예제와 동일하게 동작함.

```javascript
const resolvedPromise = new Promise((resolve) => resolve([1, 2, 3]));
resolvedPromise.then(console.log); // [1, 2, 3]
```

### 45-28

인수로 전달받은 값을 reject하는 프로미스 생성

```javascript
// 에러 객체를 reject하는 프로미스를 생성
const rejectedPromise = Promise.reject(new Error('Error!'));
rejectedPromise.catch(console.log); // Error: Error!
```

### 45-29

위 예제와 동일 동작

```javascript
const rejectedPromise = new Promise((_, reject) => reject(new Error('Error!')));
rejectedPromise.catch(console.log); // Error: Error!
```

## 45.6.2 Promise.all

여러 개의 비동기 처리를 모두 병령 처리할 때 사용

### 45-30

세 개의 비동기 처리를 순차적으로 처리.

```javascript
const requestData1 = () =>
  new Promise((resolve) => setTimeout(() => resolve(1), 3000));
const requestData2 = () =>
  new Promise((resolve) => setTimeout(() => resolve(2), 2000));
const requestData3 = () =>
  new Promise((resolve) => setTimeout(() => resolve(3), 1000));

// 세 개의 비동기 처리를 순차적으로 처리
const res = [];
requestData1()
  .then((data) => {
    res.push(data);
    return requestData2();
  })
  .then((data) => {
    res.push(data);
    return requestData3();
  })
  .then((data) => {
    res.push(data);
    console.log(res); // [1, 2, 3] ⇒ 약 6초 소요
  })
  .catch(console.error);
```

=> 개별 수행 비동기 처리는 순차적으로 ㅓ리할 필요 없음

### 45-31

Promise.all 메서드로 여러 개의 비동기 처리를 모두 병렬 처리

```javascript
const requestData1 = () =>
  new Promise((resolve) => setTimeout(() => resolve(1), 3000));
const requestData2 = () =>
  new Promise((resolve) => setTimeout(() => resolve(2), 2000));
const requestData3 = () =>
  new Promise((resolve) => setTimeout(() => resolve(3), 1000));

Promise.all([requestData1(), requestData2(), requestData3()])
  .then(console.log) // [ 1, 2, 3 ] ⇒ 약 3초 소요
  .catch(console.error);
```

=> 프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받음. 모두 fulfilled 상태가 되면 처리 결과를 배열에 저장해 새로운 프로미스를 반환함

첫 번째 프로미스가 가장 나중에 fulfilled 되어도 차례대로 배열에 저장하기 때문에 `처리 순서가 보장됨`

하나라도 rejected 상태가 되면 즉시 종료

### 45-32

세 번째 프로미스가 가장 먼저 rejected 상태가 되므로 세 번째 프로미스가 reject한 에러가 catch 메서드로 전달됨

```javascript
Promise.all([
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Error 1')), 3000)
  ),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Error 2')), 2000)
  ),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Error 3')), 1000)
  ),
])
  .then(console.log)
  .catch(console.log); // Error: Error 3
```

### 45-33

인수의 이터러블 요소가 프로미스가 아닌 경우 Promise.resolve 메서드를 통해 프로미스로 래핑함

```javascript
Promise.all([
  1, // => Promise.resolve(1)
  2, // => Promise.resolve(2)
  3, // => Promise.resolve(3)
])
  .then(console.log) // [1, 2, 3]
  .catch(console.log);
```

### 45-34

```javascript
// GET 요청을 위한 비동기 함수
const promiseGet = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        // 성공적으로 응답을 전달받으면 resolve 함수를 호출한다.
        resolve(JSON.parse(xhr.response));
      } else {
        // 에러 처리를 위해 reject 함수를 호출한다.
        reject(new Error(xhr.status));
      }
    };
  });
};

const githubIds = ['jeresig', 'ahejlsberg', 'ungmo2'];

Promise.all(
  githubIds.map((id) => promiseGet(`https://api.github.com/users/${id}`))
)
  // [Promise, Promise, Promise] => Promise [userInfo, userInfo, userInfo]
  .then((users) => users.map((user) => user.name))
  // [userInfo, userInfo, userInfo] => Promise ['John Resig', 'Anders Hejlsberg', 'Ungmo Lee']
  .then(console.log)
  .catch(console.error);
```

promiseGet 함수가 반환한 3개의 프로미스로 이루어진 배열을 인수로 전달받고  
모두 fulfilled 상태가 되면 처리 결과를 배열에 저장해 새로운 프로미스를 반환함

## 45.6.3 Promise.race

프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받음. 가장 먼저 fulfilled 상태가 된 프로미스의 처리 결과를 resolve하는 새로운 프로미스를 반환함

### 45-35

```javascript
Promise.race([
  new Promise((resolve) => setTimeout(() => resolve(1), 3000)), // 1
  new Promise((resolve) => setTimeout(() => resolve(2), 2000)), // 2
  new Promise((resolve) => setTimeout(() => resolve(3), 1000)), // 3
])
  .then(console.log) // 3
  .catch(console.log);
```

### 45-36

프로미스가 하나라도 rejected 상태가 되면 에러를 reject하는 새로운 프로미스를 즉시 반환

```javascript
Promise.race([
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Error 1')), 3000)
  ),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Error 2')), 2000)
  ),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Error 3')), 1000)
  ),
])
  .then(console.log)
  .catch(console.log); // Error: Error 3
```

## 45.6.4 Promise.allSettled

프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받음. 모두 settled(fulfilled or rejected) 상태가 되면 처리 결과를 배열로 반환

### 45-37

```javascript
Promise.allSettled([
  new Promise((resolve) => setTimeout(() => resolve(1), 2000)),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Error!')), 1000)
  ),
]).then(console.log);
/*
[
  {status: "fulfilled", value: 1},
  {status: "rejected", reason: Error: Error! at <anonymous>:3:54}
]
*/
```

- 모든 프로미스들의 처리 결과가 모두 담겨 있음
  - fulfilled 상태인 경우 status 프로퍼티와 처리 결과를 나타내는 value 프로퍼티를 가짐
  - rejected 상태인 경우 status 프로퍼티와 에러를 나타내는 reason 프로퍼티를 가짐

### 45-38

```javascript
[
  // 프로미스가 fulfilled 상태인 경우
  {status: "fulfilled", value: 1},
  // 프로미스가 rejected 상태인 경우
  {status: "rejected", reason: Error: Error! at <anonymous>:3:60}
]
```

# 45.7 마이크로태스크 큐

### 45-39

```javascript
setTimeout(() => console.log(1), 0);

Promise.resolve()
  .then(() => console.log(2))
  .then(() => console.log(3));
```

=> 출력 순서 : 2 -> 3 -> 1

프로미스의 후속 처리 메서드의 콜백 함수는 마이크로태스크 큐에 저장됨

마이크로태스크 큐 : 태스크 큐와는 별도의 큐. 프로미스의 후속 처리 메서드의 콜백 함수가 일시 저장됨.

그 외의 비동기 함수의 콜백 함수, 이벤트 핸들러는 태스크 큐에 일시 저장됨.

마이크로태스크 큐의 우선 순위가 태스크 큐보다 높음.

콜 스택 => 마이크로태스크 큐 => 태스크 큐

이벤트 루프는 콜 스택이 비면 마이크로태스크 큐에서 대기하고 있는 함수를 가져와 실행함. 이후 마이크로태스크 큐가 비면 태스크 큐에 있는 함수를 가져와 실행함

# 45.8 fetch

fetch 함수 : HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web API.  
XMLHttpRequest 객체보다 사용법이 간단하고 프로미스를 지원하기 때문에 비동기 처리를 위한 콜백 패턴의 단점에서 자유로움.

fetch 함수는 HTTP 응답을 나타내는 Response 객체를 래핑한 Promise 객체를 반환함.

### 45-40

```javascript
fetch('https://jsonplaceholder.typicode.com/todos/1').then((response) =>
  console.log(response)
);
```

=> HTTP 응답을 나타내는 Response 객체를 래핑한 `프로미스를 반환하므로` 후속 처리 메서드 then을 통해 프로미스가 resolve한 Response 객체를 전달받을 수 있음

Response 객체는 HTTP 응답을 나타내는 다양한 프로퍼티를 제공함  
Response.prototype에는 Response 객체에 포함되어 있는 HTTP 응답 몸체를 위한 다양한 메서드를 제공함  
ex) Response.prototype.json - 역직렬화

### 45-41

```javascript
fetch('https://jsonplaceholder.typicode.com/todos/1')
  // response는 HTTP 응답을 나타내는 Response 객체이다.
  // json 메서드를 사용하여 Response 객체에서 HTTP 응답 몸체를 취득하여 역직렬화한다.
  .then((response) => response.json())
  // json은 역직렬화된 HTTP 응답 몸체이다.
  .then((json) => console.log(json));
// {userId: 1, id: 1, title: "delectus aut autem", completed: false}
```

fetch 함수 첫 번째 인수로 HTTP 요청을 전송할 URL과 두 번째 인수로 HTTP 요청 메서드, HTTP 요청 헤더, 페이로드 등을 설정한 객체를 전달함

### 45-42

```javascript
const request = {
  get(url) {
    return fetch(url);
  },
  post(url, payload) {
    return fetch(url, {
      method: 'POST',
      headers: { 'content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });
  },
  patch(url, payload) {
    return fetch(url, {
      method: 'PATCH',
      headers: { 'content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });
  },
  delete(url) {
    return fetch(url, { method: 'DELETE' });
  },
};
```

### 1. GET 요청

### 45-43

```javascript
request
  .get('https://jsonplaceholder.typicode.com/todos/1')
  .then((response) => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then((todos) => console.log(todos))
  .catch((err) => console.error(err));
// {userId: 1, id: 1, title: "delectus aut autem", completed: false}
```

### 2. POST 요청

### 45-44

```javascript
request
  .post('https://jsonplaceholder.typicode.com/todos', {
    userId: 1,
    title: 'JavaScript',
    completed: false,
  })
  .then((response) => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then((todos) => console.log(todos))
  .catch((err) => console.error(err));
// {userId: 1, title: "JavaScript", completed: false, id: 201}
```

### 3. PATCH 요청

### 45-45

```javascript
request
  .patch('https://jsonplaceholder.typicode.com/todos/1', {
    completed: true,
  })
  .then((response) => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then((todos) => console.log(todos))
  .catch((err) => console.error(err));
// {userId: 1, id: 1, title: "delectus aut autem", completed: true}
```

### 4. DELETE 요청

### 45-46

```javascript
request
  .delete('https://jsonplaceholder.typicode.com/todos/1')
  .then((response) => {
    if (!response.ok) throw new Error(response.statusText);
    return response.json();
  })
  .then((todos) => console.log(todos))
  .catch((err) => console.error(err));
// {}
```
