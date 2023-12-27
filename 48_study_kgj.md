## 48.1 모듈의 일반적 의미

> 💡 **모듈** : 애플리케이션을 구성하는 개별적 요소로서 재사용 가능한 코드 조각

- 일반적으로 모듈은 기능을 기준으로 파일 단위로 분리, 자신만의 **파일 스코프(모듈 스코프)** 를 가져야만 모듈이 성립
- 파일 스코프를 갖는 모듈의 자산(모듈에 포함되어 있는 변수, 함수, 객체 등)은 기본적으로 비공개 상태 -> 모듈의 자산은 캡슐화되어 다른 모듈에서 접근 불가능
- 즉, <u>모듈은 개별적 존재로서 애플리케이션과 분리되어 존재</u>
- 모듈은 애플리케이션이나 다른 모듈에 의해 재사용되어야 의미가 있음

> 따라서, **모듈은 공개가 필요한 자산에 한정하여 명시적으로 선택적 공개가 가능** -> **`export`**

- 공개된 모듈의 자산을 사용하는 모듈 = **모듈 사용자(Module Consumer)**
- 모듈 사용자는 모듈이 공개한 자산 중 일부 또는 전체를 선택해 자신의 스코프 내로 불러들여 재사용 가능 -> **`import`**
- 코드의 단위를 명확히 분리하여 애플리케이션을 구성할 수 있고, 재사용성이 좋아서 개발 효율성과 유지보수성을 높일 수 있음

## 48.2 자바스크립트와 모듈

- 자바스크립트는 모듈 시스템을 지원하지 않음 -> 다시 말해, 자바스크립트는 모듈이 성립하기 위해 필요한 파일 스코프와 `import`, `export`를 지원하지 않음
- 자바스크립트는 `script`태그를 사용하여 외부의 자바스크립트 파일을 로드할 수 있지만 파일마다 독립적인 파일 스코프를 갖지 않음
- 다시 말해, 자바스크립트 파일을 여러 개의 파일로 분리하여 `script` 태그로 로드해도 분리된 자바스크립트 파일들은 결국 하나의 자바스크립트 파일 내에 있는 것처럼 동작
- 자바스크립트 파일은 하나의 전역을 공유 -> `CommonJS`,`AMD(Asynchronous Module Definition)`
- 자바스크립트 모듈 시스템은 브라우저 환경에서 `CommonJS`와`AMD` 를 구현한 모듈 로더 라이브러리를 사용해야 하는 상황이 됨

> 현재는 자바스크립트 런타임 환경인 `Node.js`는 모듈 시스템의 사실상 표준인 `CommonJS`를 사양으로 따르고 있음
> 즉, `Node.js`는 ECMAScript 펴준 사양은 아니지만 모듈 시스템을 지원

- 따라서 `Node.js`환경에서는 파일별로 독립적인 모듈 스코프를 가짐

## 48.3 ES6 모듈(ESM)

- ES6에서는 클라이언트 사이드 자바스크립트에서도 동작하는 모듈 기능을 추가 (IE를 제외한 대부분의 브라우저에서 ES6 모듈 사용 가능)

✅ ES6 모듈의 사용법

```html
<script type="module" src="app.mjs"></script>
```

- 일반적으로 ESM임을 명확히 하기 위해서 파일확장자는 mjs 권장

### 48.3.1 모듈 스코프

- ESM은 독자적인 모듈 스코프를 가짐

☑️ 자바스크립트 파일

```html
<!DOCTYPE html>
<html>
  <body>
    <script src="foo.js"></script>
    <script src="bar.js"></script>
  </body>
</html>
```

```javascript
// foo.js
// x 변수는 전역 변수다.
var x = 'foo';
console.log(window.x); // foo

----------


// bar.js
// x 변수는 전역 변수다. foo.js에서 선언한 전역 변수 x와 중복된 선언이다.
var x = 'bar';

// foo.js에서 선언한 전역 변수 x의 값이 재할당되었다.
console.log(window.x); // bar
```

- `script`태그로 분리해도 자바스크립트 파일은 하나의 파일 내에 있는 것처럼 동작

☑️ ESM 파일

```html
<!DOCTYPE html>
<html>
  <body>
    <script type="module" src="foo.mjs"></script>
    <script type="module" src="bar.mjs"></script>
  </body>
</html>
```

```javascript
// foo.mjs
// x 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다.
var x = 'foo';
console.log(x); // foo
console.log(window.x); // undefined

------

// bar.mjs
// x 변수는 전역 변수가 아니며 window 객체의 프로퍼티도 아니다.
// foo.mjs에서 선언한 x 변수와 스코프가 다른 변수다.
var x = 'bar';
console.log(x); // bar
console.log(window.x); // undefined
```

- 모듈 내에서 선언한 식별자는 모듈 외부에서 참조 불가능

```html
<!DOCTYPE html>
<html>
  <body>
    <script type="module" src="foo.mjs"></script>
    <script type="module" src="bar.mjs"></script>
  </body>
</html>
```

```javascript
// foo.mjs
const x = 'foo';
console.log(x); // foo

----

// bar.mjs
console.log(x); // ReferenceError: x is not defined
```

### 48.3.2 export 키워드

- 모듈은 독자적인 모듈 스코프를 가짐
- 모듈 내부에서 선언한 식별자를 외부에서 사용하려면 `export` 키워를 사용해야 함

☑️ 따로 `export` 선언한 경우

```javascript
// lib.mjs
// 변수의 공개
export const pi = Math.PI;

// 함수의 공개
export function square(x) {
  return x * x;
}

// 클래스의 공개
export class Person {
  constructor(name) {
    this.name = name;
  }
}
```

☑️ `export`할 대상을 하나의 객체로 구성하는 경우

```javascript
// lib.mjs
const pi = Math.PI;

function square(x) {
  return x * x;
}

class Person {
  constructor(name) {
    this.name = name;
  }
}

// 변수, 함수 클래스를 하나의 객체로 구성하여 공개
export { pi, square, Person };
```

### 48.3.3 import 키워드

- 다른 모듈에서 `export`한 식별자는 자신의 모듈 스코프로 로드하려면 `import` 키워드를 사용해야 함

```html
<!DOCTYPE html>
<html>
  <body>
    <script type="module" src="app.mjs"></script>
  </body>
</html>
```

```javascript
// app.mjs
// 같은 폴더 내의 lib.mjs 모듈이 export한 식별자 이름으로 import한다.
// ESM의 경우 파일 확장자를 생략할 수 없다.
import { pi, square, Person } from './lib.mjs';

console.log(pi); // 3.141592653589793
console.log(square(10)); // 100
console.log(new Person('Lee')); // Person { name: 'Lee' }
```

☑️ 모듈을 하나의 이름으로 `import` 하는 경우

```javascript
// app.mjs
// lib.mjs 모듈이 export한 모든 식별자를 lib 객체의 프로퍼티로 모아 import한다.
import * as lib from './lib.mjs';

console.log(lib.pi); // 3.141592653589793
console.log(lib.square(10)); // 100
console.log(new lib.Person('Lee')); // Person { name: 'Lee' }
```

☑️ `export`한 식별자 이름을 변경하는 경우

```javascript
// app.mjs
// lib.mjs 모듈이 export한 식별자 이름을 변경하여 import한다.
import { pi as PI, square as sq, Person as P } from './lib.mjs';

console.log(PI); // 3.141592653589793
console.log(sq(2)); // 4
console.log(new P('Kim')); // Person { name: 'Kim' }
```

☑️ 하나의 값만 `export`하는 경우 (`default` 사용)

```javascript
// lib.mjs
export default (x) => x * x;
```

- `default` 키워드 사용시 `var`, `let`, `const` 사용 불가

```javascript
// lib.mjs
export default const foo = () => {};
// => SyntaxError: Unexpected token 'const'
// export default () => {};
```

- `default` 키워드 사용시 `{}`없이 임의의 이름으로 `import` 해야함

```javascript
// app.mjs
import square from './lib.mjs';

console.log(square(3)); // 9
```
