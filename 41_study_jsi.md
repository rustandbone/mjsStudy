# 41장 타이머

- [41장 타이머](#41장-타이머)
- [41.1 호출 스케일링](#411-호출-스케일링)
- [41.2 타이머 함수](#412-타이머-함수)
  - [41.2.1 setTimeout / claerTimeout](#4121-settimeout--claertimeout)
    - [41-01](#41-01)
    - [41-02](#41-02)
  - [41.2.1 setInterval / claerInterval](#4121-setinterval--claerinterval)
    - [41-03](#41-03)
- [41.3 디바운스와 스로틀](#413-디바운스와-스로틀)
  - [41-04 실행결과](#41-04-실행결과)
  - [41.3.1 디바운스](#4131-디바운스)
    - [41-05](#41-05)
  - [41.3.2 스로틀](#4132-스로틀)
    - [41-06](#41-06)

# 41.1 호출 스케일링

🤔 **호출 스케일링**이란?

일반 함수처럼 명시적으로 호출을 하지 않고, **일정 시간이 경과된 후 호출되도록 함수 호출을 예약하는 경우**를 의미합니다.

> [출처: javascript.info](https://ko.javascript.info/settimeout-setinterval)
> 일정 시간이 지난 후에 원하는 함수를 예약 실행(호출)할 수 있게 하는 것

✅ **타이머 함수**

|            생성             |              제거               |
| :-------------------------: | :-----------------------------: |
| `setTimeout`, `setInterval` | `clearTimeout`, `clearInterval` |

- 브라우저 환경과 Node.js 환경에서 모두 전역 객체 메서드로 제공됩니다. 😀 **호스트 객체** !
- 자바스크립트 엔진은 싱글 스레드로 동작하기 때문에 (= 하나의 실행 컨텍스트), **비동기 처리 방식**으로 동작합니다.

# 41.2 타이머 함수

## 41.2.1 setTimeout / claerTimeout

🤔 **setTimeout**

```js
const timsoutId = setTimeout(func|code[, delay, param1, parma2, ...]);
```

|        매개변수         |                     설명                     |
| :---------------------: | :------------------------------------------: |
|         `func`          |     타이머가 만료된 뒤 호출될 콜백 함수      |
|         `delay`         |   타이머 만료 시간(ms, 1/1000초). 기본값 0   |
| `param1`, `param2`, ... | 콜백 함수에 전달해야 할 인수가 존재하는 경우 |

- `delay` 변수로 단 한 번 동작하는 타이머를 생성합니다.
  😀 즉, 콜백 함수는 두 번째 인수로 전달받은 시간 이후 **단 한 번 실행되도록 호출 스케줄링**됩니다.

### 41-01

```javascript
// 1초(1000ms) 후 타이머가 만료되면 콜백 함수가 호출된다.
setTimeout(() => console.log("Hi!"), 1000);

// 콜백 함수 호출시 'Lee'가 인수로 전달된다.
setTimeout((name) => console.log(`Hi! ${name}.`), 1000, "Lee");
```

🤔 **clearTimeout**

- `setTimeout` 함수는 브라우저 환경인 경우 숫자, Node.js 환경인 경우 객체인 **고유한 타이머 id**를 반환합니다.
- 이를 `clearTimeout` 함수의 인수로 전달하여 타이머를 취소합니다.
  😀 즉, **호출 스케줄링을 취소**합니다.

### 41-02

```javascript
// setTimeout 함수는 고유한 타이머 id를 반환한다.
const timerId = setTimeout(() => console.log("Hi!"), 1000);

// 반환한 타이머 id를 clearTimeout 함수의 인수로 전달하여 타이머를 취소한다.
// 콜백 함수가 실행되지 않는다.
clearTimeout(timerId);
```

![41-02 실행결과](/assets/41-02.gif)

## 41.2.1 setInterval / claerInterval

🤔 **setInterval**

```js
const timsoutId = setInterval(func|code[, delay, param1, parma2, ...]);
```

- `delay` 변수로 반복 동작하는 타이머를 생성합니다.
  😀 즉, 콜백 함수는 두 번째 인수로 전달받은 시간이 경과할 때마다 **반복 실행되도록 호출 스케줄링**됩니다.

🤔 **clearInterval**

- `setInterval` 함수 또한 **고유한 타이머 id**를 반환합니다.
- 이를 `clearInterval` 함수의 인수로 전달하여 타이머, 즉 **호출 스케줄링을 취소**합니다.

### 41-03

```javascript
let count = 1;

// 1초 후 타이머가 만료될 때마다 콜백 함수가 호출된다.
const timeoutId = setInterval(() => {
  console.log(count);
  // count가 5이면 setInterval 함수가 반환한 타이머 id를 clearInterval 함수의
  // 인수로 전달하여 타이머를 취소한다. setInterval 함수의 콜백 함수가 실행되지 않는다.
  if (count++ === 5) clearInterval(timeoutId);
}, 1000);
```

![41-03 실행 결과](/assets/41-03.gif)

# 41.3 디바운스와 스로틀

🤔 **짧은 시간 연속해서 발생하는 이벤트 문제**
`scroll`, `resize`, `input`, `mousemove` 같은 이벤트는 과도한 호출로 성능에 문제를 일으킬 수 있습니다.

😀 디바운스와 스로틀은 이러한 이벤트를 그룹화하여 **과도한 이벤트 핸들러의 호출을 방지**합니다.

### 41-04 실행결과

![41-04 실행결과](/assets/41-04.gif)

## 41.3.1 디바운스

✅ 짧은 시간 간격으로 발생하는 이벤트를 그룹화하여, 일정 시간이 경과한 이후 **한번만 호출**합니다.

### 41-05

```html
<!DOCTYPE html>
<html>
  <body>
    <input type="text" />
    <div class="msg"></div>
    <script>
      const $input = document.querySelector("input");
      const $msg = document.querySelector(".msg");

      const debounce = (callback, delay) => {
        let timerId;
        // timerId를 기억하는 클로저를 반환한다.
        return (...args) => {
          // delay 경과 전 이벤트 발생시 이전 타이머를 취소하고 새로운 타이머를 재설정한다.
          // 따라서 delay보다 짧은 간격으로 이벤트 발생시 callback이 호출되지 않는다.
          if (timerId) clearTimeout(timerId);
          timerId = setTimeout(callback, delay, ...args);
        };
      };

      // debounce 함수가 반환하는 클로저가 이벤트 핸들러로 등록된다.
      // 500ms보다 짧은 간격으로 input 이벤트가 발생하면 debounce 함수의 콜백 함수는
      // 호출되지 않다가 500ms 동안 input 이벤트가 더 이상 발생하지 않으면 한 번만 호출된다.
      $input.oninput = debounce((e) => {
        $msg.textContent = e.target.value;
      }, 500);
    </script>
  </body>
</html>
```

![41-05 호출결과](/assets/41-05.gif)

- `resize` 이벤트 처리, 입력 필드 자동완성 UI 구현, 버튼 중복 클릭 방지
- 실무에서는 [Underscore](https://underscorejs.org/#debounce)나 [Lodash](https://lodash.com/docs/4.17.15#debounce)의 `debounce 함수` 사용을 권장한다.

> 1. Underscore의 debounce 함수 ![Underscore debounce 함수](/assets/41-underscore.png)
> 2. Lodash의 debounce 함수 ![Lodash debounce 함수](/assets/41-lodash.png)

## 41.3.2 스로틀

✅ 짧은 시간 간격으로 발생하는 이벤트를 그룹화하여, **일정 시간 단위로 호출**되도록 **호출 주기**를 만듭니다.

### 41-06

```html
<!DOCTYPE html>
<html>
  <head>
    <style>
      /* ... */
    </style>
  </head>
  <body>
    <div class="container">
      <div class="content"></div>
    </div>
    <div>
      일반 이벤트 핸들러가 scroll 이벤트를 처리한 횟수:
      <span class="normal-count">0</span>
    </div>
    <div>
      스로틀 이벤트 핸들러가 scroll 이벤트를 처리한 횟수:
      <span class="throttle-count">0</span>
    </div>

    <script>
      const $container = document.querySelector(".container");
      const $normalCount = document.querySelector(".normal-count");
      const $throttleCount = document.querySelector(".throttle-count");

      const throttle = (callback, delay) => {
        let timerId;
        // timerId를 기억하는 클로저를 반환한다.
        return (...args) => {
          // delay 경과 전 이벤트가 발생시 아무것도 반환하지 않고, 경과 후 이벤트 발생시 새로운 타이머를 재설정한다.
          // 따라서 delay 간격으로 callback이 호출된다.
          if (timerId) return;
          timerId = setTimeout(() => {
            callback(...args);
            timerId = null;
          }, delay);
        };
      };

      let normalCount = 0;
      $container.addEventListener("scroll", () => {
        $normalCount.textContent = ++normalCount;
      });

      let throttleCount = 0;
      // throttle 함수가 반환하는 클로저가 이벤트 핸들러로 등록된다.
      $container.addEventListener(
        "scroll",
        throttle(() => {
          $throttleCount.textContent = ++throttleCount;
        }, 100)
      );
    </script>
  </body>
</html>
```

![41-06 호출결과](/assets/41-06.gif)

- `scroll` 이벤트 처리, 무한 스크롤 UI 구현
- 실무에서는 [Underscore](https://underscorejs.org/#throttle)나 [Lodash](https://lodash.com/docs/4.17.15#throttle)의 `throttle 함수` 사용을 권장한다.
