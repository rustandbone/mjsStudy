# 디스트럭처링 할당

디스트럭처링 할당 = 구조 분해 할당 은 구조화된 배열과 같은 이터러블 또는 객체를 비구조화하여 1개 이상의 변수에 개별적으로 할당하는 것이다.
배열과 같은 이터러블 또는 객체 리터럴에서 필요한 값만 추출하여 변수에 할당할 때 유용하다.

## 36.1 배열 디스트럭처링 할당

구조화된 배열을 디스트럭처링 ( 비구조화 )하여 1개 이상의 변수에 할당해보자.
##### 예제 36-01 : 구조 분해 할당 이전

```javascript
// ES5
var arr = [1, 2, 3];

var one   = arr[0];
var two   = arr[1];
var three = arr[2];

console.log(one, two, three); // 1 2 3
```

##### 예제 36-02 : 배열 구조 분해 할당

```javascript
const arr = [1, 2, 3];

// ES6 배열 디스트럭처링 할당
// 변수 one, two, three를 선언하고 배열 arr을 디스트럭처링하여 할당한다.
// 이때 할당 기준은 배열의 인덱스다.
const [one, two, three] = arr;

console.log(one, two, three); // 1 2 3
```

할당문의 우변에 있는 할당의 대상은 이터러블이어야한다.
할당 기준은 배열의 인덱스이다. = 순서대로 할당된다.

##### 예제 36-03

```javascript
const [x, y] = [1, 2];
```

연산자 왼쪽에 값을 할당받을 변수를 배열 리터럴 형태로 선언한다.

##### 예제 36-04

```javascript
const [x, y]; // SyntaxError: Missing initializer in destructuring declaration

const [a, b] = {}; // TypeError: {} is not iterable
```

우변에 이터러블을 할당하지 않으면 에러가 발생한다.

##### 예제 36-05 : 선언과 할당 분리

```javascript
let x, y;
[x, y] = [1, 2];
```
const 키워드로 변수를 선언할 수 없어서 권장하지 않는 방법이다.

##### 예제 36-06

```javascript
const [a, b] = [1, 2];
console.log(a, b); // 1 2

const [c, d] = [1];
console.log(c, d); // 1 undefined

const [e, f] = [1, 2, 3];
console.log(e, f); // 1 2

const [g, , h] = [1, 2, 3];
console.log(g, h); // 1 3
```
배열 구조 분해 할당은 배열의 순서대로 할당된다.
변수의 개수와 이터러블의 요소 개수가 반드시 일치하지 않아도 된다.

##### 예제 36-07 : 기본값 설정

```javascript
// 기본값
const [a, b, c = 3] = [1, 2];
console.log(a, b, c); // 1 2 3

// 기본값보다 할당된 값이 우선한다.
const [e, f = 10, g = 3] = [1, 2];
console.log(e, f, g); // 1 2 3
```
좌변의 변수에 기본값을 설정해주었다. `c`
할당된 값이 우선순위이다. `f`

##### 예제 36-08 : URL 파싱해 `protocol, host, path` 프로퍼티를 갖는 객체 생성해 반환

```javascript
// url을 파싱하여 protocol, host, path 프로퍼티를 갖는 객체를 생성해 반환한다.
function parseURL(url = '') {
  // '://' 앞의 문자열(protocol)과 '/' 이전의 '/'으로 시작하지 않는 문자열(host)과 '/' 이후의 문자열(path)을 검색한다.
  const parsedURL = url.match(/^(\w+):\/\/([^/]+)\/(.*)$/);
  console.log(parsedURL);
  /*
  [
    'https://developer.mozilla.org/ko/docs/Web/JavaScript',
    'https',
    'developer.mozilla.org',
    'ko/docs/Web/JavaScript',
    index: 0,
    input: 'https://developer.mozilla.org/ko/docs/Web/JavaScript',
    groups: undefined
  ]
  */

  if (!parsedURL) return {};

  // 배열 디스트럭처링 할당을 사용하여 이터러블에서 필요한 요소만 추출한다.
  const [, protocol, host, path] = parsedURL;
  return { protocol, host, path };
}

const parsedURL = parseURL('https://developer.mozilla.org/ko/docs/Web/JavaScript');
console.log(parsedURL);
/*
{
  protocol: 'https',
  host: 'developer.mozilla.org',
  path: 'ko/docs/Web/JavaScript'
}
*/
```
이터러블에서 필요한 요소만 추출하여 변수에 할당하고 싶을 때 유용하다.

##### 예제 36-09 : `Rest 요소` 사용

```javascript
// Rest 요소
const [x, ...y] = [1, 2, 3];
console.log(x, y); // 1 [ 2, 3 ]
```

Rest 파라미터와 유사하게 Rest Element 를 사용할 수 있다.
Rest 요소는 Rest 파라미터와 같게 반드시 마지막에 위치해야 한다.

<br/>
<br/>

## 36.2 객체 디스트럭처링 할당

객체의 각 프로퍼티를 객체로부터 디스트럭처링하여 변수에 할당해보자.

##### 예제 36-10 : ES5 프로퍼티 키 사용

```javascript
// ES5
var user = { firstName: 'Ungmo', lastName: 'Lee' };

var firstName = user.firstName;
var lastName  = user.lastName;

console.log(firstName, lastName); // Ungmo Lee
```

##### 예제 36-11 : ES6 디스트럭처링 할당

```javascript
const user = { firstName: 'Ungmo', lastName: 'Lee' };

// ES6 객체 디스트럭처링 할당
// 변수 lastName, firstName을 선언하고 user 객체를 디스트럭처링하여 할당한다.
// 이때 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다. 순서는 의미가 없다.
const { lastName, firstName } = user;

console.log(firstName, lastName); // Ungmo Lee
```

할당 기준이 `프로퍼티 키`이기 때문에 순서는 의미가 없고 선언된 변수 이름과 프로퍼티 키가 일치하면 할당된다.

##### 예제 36-12

```javascript
const { lastName, firstName } = { firstName: 'Ungmo', lastName: 'Lee' };
```

할당 연산자 왼쪽에 프로퍼티 값을 할당받을 변수 객체 리터럴 형태로 선언해야 한다.

##### 예제 36-13

```javascript
const { lastName, firstName };
// SyntaxError: Missing initializer in destructuring declaration

const { lastName, firstName } = null;
// TypeError: Cannot destructure property 'lastName' of 'null' as it is null.
```

우변에 객체를 할당하지 않으면 에러가 발생한다.
객체로 평가될 수 있는 표현식 ( 문자열, 숫자, 배열 등 )도 포함된다.

##### 예제 36-14 : 프로퍼티 축약 표현을 통해 선언

```javascript
const { lastName, firstName } = user;
// 위와 아래는 동치다.
const { lastName: lastName, firstName: firstName } = user;
```


##### 예제 36-15

```javascript
const user = { firstName: 'Ungmo', lastName: 'Lee' };

// 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다.
// 프로퍼티 키가 lastName인 프로퍼티 값을 ln에 할당하고,
// 프로퍼티 키가 firstName인 프로퍼티 값을 fn에 할당한다.
const { lastName: ln, firstName: fn } = user;

console.log(fn, ln); // Ungmo Lee
```

객체의 프로퍼티 키와 다른 변수 이름으로 할당받는다.

##### 예제 36-16 : 기본값 설정

```javascript
const { firstName = 'Ungmo', lastName } = { lastName: 'Lee' };
console.log(firstName, lastName); // Ungmo Lee

const { firstName: fn = 'Ungmo', lastName: ln } = { lastName: 'Lee' };
console.log(fn, ln); // Ungmo Lee
```

기본값을 설정해주었다. `firstname`
`fn`, `ln`

##### 예제 36-17

```javascript
const str = 'Hello';
// String 래퍼 객체로부터 length 프로퍼티만 추출한다.
const { length } = str;
console.log(length); // 5

const todo = { id: 1, content: 'HTML', completed: true };
// todo 객체로부터 id 프로퍼티만 추출한다.
const { id } = todo;
console.log(id); // 1
```

객체에서 프로퍼티 키로 필요한 프로퍼티 값만 추출하여 변수에 할당하고 싶을 때 유용하다.

##### 예제 36-18

```javascript
function printTodo(todo) {
  console.log(`할일 ${todo.content}은 ${todo.completed ? '완료' : '비완료'} 상태입니다.`);
}

printTodo({ id: 1, content: 'HTML', completed: true });
// 할일 HTML은 완료 상태입니다.
```

객체를 인수로 전달받는 함수의 매개변수에도 사용할 수 있다.

##### 예제 36-19

```javascript
function printTodo({ content, completed }) {
  console.log(`할일 ${content}은 ${completed ? '완료' : '비완료'} 상태입니다.`);
}

printTodo({ id: 1, content: 'HTML', completed: true });
// 할일 HTML은 완료 상태입니다.
```

객체를 인수로 전달받는 매개변수 `todo`에 객체 디스트럭처링 할당을 사용하면 더 간단하고 가독성이 좋게 표현할 수 있다.

##### 예제 36-20

```javascript
const todos = [
  { id: 1, content: 'HTML', completed: true },
  { id: 2, content: 'CSS', completed: false },
  { id: 3, content: 'JS', completed: false }
];

// todos 배열의 두 번째 요소인 객체로부터 id 프로퍼티만 추출한다.
const [, { id }] = todos;
console.log(id); // 2
```

배열의 요소가 객체일 때 배열 디스트럭처링 할당과 객체 디스트럭처링 할당을 혼용할 수 있다.

##### 예제 36-21 : 중첩 객체

```javascript
const user = {
  name: 'Lee',
  address: {
    zipCode: '03068',
    city: 'Seoul'
  }
};

// address 프로퍼티 키로 객체를 추출하고 이 객체의 city 프로퍼티 키로 값을 추출한다.
const { address: { city } } = user;
console.log(city); // 'Seoul'
```

##### 예제 36-22 : Rest 파라미터 사용

```javascript
// Rest 프로퍼티
const { x, ...rest } = { x: 1, y: 2, z: 3 };
console.log(x, rest); // 1 { y: 2, z: 3 }
```

`Rest 프로퍼티` `...` 를 사용할 수 있다.
`Rest 파라미터`나 `Rest 요소`같이 반드시 맨 마지막에 위치해야 한다.