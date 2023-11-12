## 34.1 이터레이션 프로토콜

>❗ **이터레이션 프로토콜**은 순회 가능한 (iterable) 데이터 컬렉션(자료구조)을 만들기 위해 ECMAScript 사양에 정의하여 미리 약속한 규칙


- ES6에서는 순회 가능한 데이터들을 이터레이션 프로토콜을 준수하는 이터러블로 통일했다.

🌿 이터레이션 프로토콜

![](https://velog.velcdn.com/images/pjh1011409/post/70278c70-5858-4287-beea-195ae0ae3a2e/image.png)

- 이터러블 프로토콜
    - 이터러블 프로토콜을 준수한 객체를 이터러블이라 한다. 이터러블은 `for…of문` 으로 순회할 수 있으며 스프레드 문법과 배열 디스트럭처링 할당의 대상으로 사용할 수 있다.
- 이터레이터 프로토콜
    - 이터레이터 프로토콜을 준수한 객체를 이터레이터라 한다. 이터레이터는 **이터러블의 요소를 탐색하기 위한 포인터 역할**을 한다.

### 34.1.1 이터러블

- `Symbol.iterator`를 프로퍼티 키로 사용한 메서드를 직접 구현하거나 프로토타입 체인을 통해 상속받은 객체

```javascript
const isIterable = v => v !== null && typeof v[Symbol.iterator] === 'function';

// 배열, 문자열, Map, Set 등은 이터러블이다.
isIterable([]);        // -> true
isIterable('');        // -> true
isIterable(new Map()); // -> true
isIterable(new Set()); // -> true
isIterable({});        // -> false
```

- `isIterable`함수는 v가 이터러블 객체인지 확인하는 함수
- 첫번째 False를 찾는 `&&연산자`를 사용하여서 v가 null값이 아니고 `Symbol.iterator` 프로퍼티가 함수인 경우를 찾는것


```javascript
const array = [1, 2, 3];

// 배열은 Array.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in array); // true

// 이터러블인 배열은 for...of 문으로 순회 가능하다.
for (const item of array) {
  console.log(item); // 1 2 3
}

// 이터러블인 배열은 스프레드 문법의 대상으로 사용할 수 있다.
console.log([...array]); // [1, 2, 3]

// 이터러블인 배열은 배열 디스트럭처링 할당의 대상으로 사용할 수 있다.
const [a, ...rest] = array;
console.log(a, rest); // 1, [2, 3]
```
- `Symbol.iterator` 메서드를 직접 구현하지 않거나 상속받지 않은 일반 객체는 이터러블 프로토콜을 준수한 이터러블이 아님 → 일반객체는 `for..of`문으로 순회 X, 스프레드 문법과 배열 디스트럭처링 할당의 대상 X

### 34.1.2 이터레이터

- 이터러블의 `Symbol.iterator` 메서드가 반환한 이터레이터는  `next` 메서드를 갖는다.


```javascript
// 배열은 이터러블 프로토콜을 준수한 이터러블이다.
const array = [1, 2, 3];

// Symbol.iterator 메서드는 이터레이터를 반환한다.
const iterator = array[Symbol.iterator]();

// Symbol.iterator 메서드가 반환한 이터레이터는 next 메서드를 갖는다.
console.log('next' in iterator); // true
```
* next 메서드는 이터러블의 각 요소를 순회하기 위한 포인터 역할

* 이터러블을 순차적으로 한 단계 씩 순회하며 순회 결과를 나타내는 이터레이터 리절트 객체(iterator result object)를 반환

```javascript
// 배열은 이터러블 프로토콜을 준수한 이터러블이다.
const array = [1, 2, 3];

// Symbol.iterator 메서드는 이터레이터를 반환한다. 이터레이터는 next 메서드를 갖는다.
const iterator = array[Symbol.iterator]();

// next 메서드를 호출하면 이터러블을 순회하며 순회 결과를 나타내는 이터레이터 리절트 객체를
// 반환한다. 이터레이터 리절트 객체는 value와 done 프로퍼티를 갖는 객체다.
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

## 34.2 빌트인 이터러블

- 표준 빌트인 객체의 빌트인 이터러블

| 빌트인 이터러블 | Symbol.iterator 메서드 |
| --- | --- |
| Array | Array.prototype[Symbol.iterator] |
| String | String.prototype[Symbol.iterator] |
| Map | Map.prototype[Symbol.iterator] |
| Set | Set.prototype[Symbol.iterator] |
| TypedArray | TypedArray.prototype[Symbol.iterator] |
| arguments | arguments[Symbol.iterator] |
| DOM 컬렉션 | NodeList.protytype[Symbol.iterator]
HTMLCollection.protytype[Symbol.iterator] |

## 34.3 for…of문

- `for...of`문은 이러터블을 순회하면서 이터러블의 요소를 변수에 할당한다.
- `for...in`문 의 형식과 비슷하다

`for 변수선언문  of 이터러블){...}`

`for 변수선언문 in 객체 {...}`

- `for...in` 문은 객체를 순회하며 프로퍼티 키가 심벌인 프로퍼티는 열거하지 않는다.
- `for...of`문은 내부적으로 이터레이터의 `next` 메서드를 호출하여 이터러블을 순회하며 `next` 메서드가 반환한 이터레이터 리절트 객체의 value 프로퍼티 값을 변수에 할당한다. 리절트 객체의 done 프로퍼티 값이 false 이면 이터러블의 순회를 계속하고 아니면 순회를 중단한다.

```javascript
// 이터러블
const iterable = [1, 2, 3];

// 이터러블의 Symbol.iterator 메서드를 호출하여 이터레이터를 생성한다.
const iterator = iterable[Symbol.iterator]();

for (;;) {
  // 이터레이터의 next 메서드를 호출하여 이터러블을 순회한다. 이때 next 메서드는 이터레이터 리절트 객체를 반환한다.
  const res = iterator.next();

  // next 메서드가 반환한 이터레이터 리절트 객체의 done 프로퍼티 값이 true이면 이터러블의 순회를 중단한다.
  if (res.done) break;

  // 이터레이터 리절트 객체의 value 프로퍼티 값을 item 변수에 할당한다.
  const item = res.value;
  console.log(item); // 1 2 3
}
```

## 34.4 이터러블과 유사 배열 객체

- 유사 배열 객체 = 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있고 length 프로퍼티를 갖는 객체
- length 프로퍼티를 갖기 때문에 for문으로 순회 가능
- 인덱스를 나타내는 숫자 형식의 문자열을 프로퍼티 키로 가지므로 배열처럼 인덱스로 프로퍼티 값에 접근 가능
- 이터러블이 아닌 일반 객체 → `Symbol.iterator` 메서드가 없기 때문에 `for...of`문으로 순회 X

```javascript
// 유사 배열 객체
const arrayLike = {
  0: 1,
  1: 2,
  2: 3,
  length: 3
};

// 유사 배열 객체는 length 프로퍼티를 갖기 때문에 for 문으로 순회할 수 있다.
for (let i = 0; i < arrayLike.length; i++) {
  // 유사 배열 객체는 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있다.
  console.log(arrayLike[i]); // 1 2 3
}
```
- 단, `arguments` , `NodeList` , `HTMLCollection` 은 유사 배열 객체이면서 이터러블이다. ES6부터 이터러블이 도입되면서 위의 3가지가 이터러블이 되었다.
- 하지만 이터러블이 된 이후에도 LENGTH 프로퍼티를 가지며 인덱스로 접근할 수 있는 것에는 변함이 없으므로 유사배열 객체이면서 이터러블 인 것


## 4.5 이터레이션 프로토콜의 필요성

![Untitled](https://velog.velcdn.com/images/dltkdals224/post/9829b845-574e-4001-a8b7-78d1e7ac5f4b/image.jpeg)

- 이터러블은 데이터 소비자에 의해 사용되므로 데이터 공급자의 역할을 한다.
- 다양한 데이터 공급자가 하나의 순회 방식을 갖도록 규정하며 데이터 소비자가 효율적으로 다양한 데이터 공급자를 사용할 수 있도록 **데이터 소비자와 데이터 공급자를 연결하는 인터페이스의 역할**을 한다.

## 34.6 사용자 정의 이터러블

### 34.6.1 사용자 정의 이터러블 구현

일반 객체를 이터레이션 프로토콜을 준수하도록 구현하면 사용자 정의 이터러블이 된다.

```javascript
// 피보나치 수열을 구현한 사용자 정의 이터러블
const fibonacci = {
  // Symbol.iterator 메서드를 구현하여 이터러블 프로토콜을 준수한다.
  [Symbol.iterator]() {
    let [pre, cur] = [0, 1]; // "36.1. 배열 디스트럭처링 할당" 참고
    const max = 10; // 수열의 최대값

    // Symbol.iterator 메서드는 next 메서드를 소유한 이터레이터를 반환해야 하고
    // next 메서드는 이터레이터 리절트 객체를 반환해야 한다.
    return {
      next() {
        [pre, cur] = [cur, pre + cur]; // "36.1. 배열 디스트럭처링 할당" 참고
        // 이터레이터 리절트 객체를 반환한다.
        return { value: cur, done: cur >= max };
      }
    };
  }
};

// 이터러블인 fibonacci 객체를 순회할 때마다 next 메서드가 호출된다.
for (const num of fibonacci) {
  console.log(num); // 1 2 3 5 8
}
```

### 34.6.2 이터러블을 생성하는 함수

이터러블을 반환하는 함수 만들기

```javascript
// 피보나치 수열을 구현한 사용자 정의 이터러블을 반환하는 함수. 수열의 최대값을 인수로 전달받는다.
const fibonacciFunc = function (max) {
  let [pre, cur] = [0, 1];

  // Symbol.iterator 메서드를 구현한 이터러블을 반환한다.
  return {
    [Symbol.iterator]() {
      return {
        next() {
          [pre, cur] = [cur, pre + cur];
          return { value: cur, done: cur >= max };
        }
      };
    }
  };
};

// 이터러블을 반환하는 함수에 수열의 최대값을 인수로 전달하면서 호출한다.
for (const num of fibonacciFunc(10)) {
  console.log(num); // 1 2 3 5 8
}
```

### 34.6.3 이터러블이면서 이터레이터인 객체를 생성하는 함수

- 이터러블을 생성하려면 이터러블의 `Symbol.iterator` 메서드를 호출해야한다.
- `Symbol.iterator` 메서드는 this를 반환하므로 next 메서드를 갖는 이터레이터를 반환한다.

```javascript
// 이터러블이면서 이터레이터인 객체. 이터레이터를 반환하는 Symbol.iterator 메서드와
// 이터레이션 리절트 객체를 반환하는 next 메서드를 소유한다.
{
  [Symbol.iterator]() { return this; },
  next() {
    return { value: any, done: boolean };
  }
}
```

### 34.6.5 무한 이터러블과 지연 평가

- 무한 이터러블 생성하는 함수

```javascript
// 무한 이터러블을 생성하는 함수
const fibonacciFunc = function () {
  let [pre, cur] = [0, 1];

  return {
    [Symbol.iterator]() { return this; },
    next() {
      [pre, cur] = [cur, pre + cur];
      // 무한을 구현해야 하므로 done 프로퍼티를 생략한다.
      return { value: cur };
    }
  };
};

// fibonacciFunc 함수는 무한 이터러블을 생성한다.
for (const num of fibonacciFunc()) {
  if (num > 10000) break;
  console.log(num); // 1 2 3 5 8...4181 6765
}

// 배열 디스트럭처링 할당을 통해 무한 이터러블에서 3개의 요소만 취득한다.
const [f1, f2, f3] = fibonacciFunc();
console.log(f1, f2, f3); // 1 2 3
```
- 이터러블은 **지연 평가**를 통해 데이터를 생성
- 지연 평가 ⇒ 평가 결과가 필요할 때까지 평가를 늦추는 기법
