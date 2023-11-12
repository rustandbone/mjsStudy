- [25장 클래스](#25장-클래스)
- [25.1 클래스는 프로토타입의 문법적 설탕인가?](#251-클래스는-프로토타입의-문법적-설탕인가)
  - [25-01](#25-01)

# 25장 클래스

# 25.1 클래스는 프로토타입의 문법적 설탕인가?

`프로토타입`

- **자바스크립트는 프로토타입 기반 객체지향 언어**
- 클래스 없이도 생성자 함수와 프로토타입만으로 객체지향 언어와 상속을 구현 가능

  - `생성자 함수`: new 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수
  - `상속`: 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 받아 그대로 사용할 수 있는 것
  - 자바스크립트는 **프로토타입을 기반으로 상속을 구현**
    ![Alt text](/assets/Prototype_object.png)

### 25-01

```javascript
// ES5 생성자 함수
var Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    console.log("Hi! My name is " + this.name);
  };

  // 생성자 함수 반환
  return Person;
})();

// 인스턴스(객체) 생성
var me = new Person("Lee"); // this = me
me.sayHi(); // Hi! My name is Lee
```

- **클래스는 함수**로서 작동. 견고하고 명료
- 문법적 설탕 = 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용가능한 **새로운 객체 생성 매커니즘**

|                  | 클래스                                     | 생성자 함수                          |
| ---------------- | ------------------------------------------ | ------------------------------------ |
| new 연산자       | 에러                                       | 일반 함수                            |
| 상속             | extends, super                             | X                                    |
| 호이스팅         | 발생하지 않는 것처럼 동작                  | 함수선언문/표현식: 함수/변수호이스팅 |
| strictmode       | 암묵적                                     | X                                    |
| `[[Enumerable]]` | constructor, 프로토타입/정적 메서드: false |                                      |

# 25.2 클래스 정의

### 25-02

```javascript
// 클래스 선언문: class 키워드. 파스칼 케이스
class Person {}
```

### 25-03

```javascript
// 익명 클래스 표현식
const Person = class {};

// 기명 클래스 표현식
const Person = class MyClass {};
```

- 클래스 = 함수 = 값으로 사용할 수 있는 일급 객체
  - 무명의 리터럴로 생성할 수 있다. 즉, 런타임 생성이 가능하다.
  - 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
  - 함수의 매개변수에게 전달할 수 있다.
  - 함수의 반환값으로 사용할 수 있다.

### 25-04

```javascript
// 클래스 선언문
class Person {
  // 1. 생성자
  constructor(name) {
    // 인스턴스(객체) 생성 (this 바인딩) 및 초기화
    this.name = name; // name 프로퍼티는 public하다.
    // 암묵적 this 반환
  }

  // 2. 프로토타입 메서드
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }

  // 3. 정적 메서드
  static sayHello() {
    console.log("Hello!");
  }
}

// 인스턴스 생성
const me = new Person("Lee");

// 인스턴스의 프로퍼티 참조
console.log(me.name); // Lee
// 프로토타입 메서드 호출
me.sayHi(); // Hi! My name is Lee
// 정적 메서드 호출
Person.sayHello(); // Hello!
```

# 25.3 클래스의 호이스팅

클래스는 함수로 평가된다.

### 25-05

```javascript
// 클래스 선언문
class Person {}

console.log(typeof Person); // function
```

- 런타임(소스코드 평가 과정) 이전에 먼저 평가되어 함수 객체 생성
- **constructor: 생성자 함수로서 호출할 수 있는 함수** = 클래스가 평가되어 생성된 함수 객체

### 25-06

```javascript
console.log(Person);
// ReferenceError: Cannot access 'Person' before initialization

// 클래스 선언문
class Person {}
```

### 25-07

```javascript
const Person = "";

{
  // 호이스팅이 발생하지 않는다면 ''이 출력되어야 한다.
  console.log(Person);
  // ReferenceError: Cannot access 'Person' before initialization

  // 클래스 선언문
  class Person {}
}
```

- 마치 let, const 키워드로 선언한 변수처럼 호이스팅 -> TDZ(일시적 사각지대) = 변수가 선언은 되었지만 어떤값도 할당이 되지 않는 상태

# 25.4 인스턴스 생성

클래스는 반드시 new 연산자와 함께 호출한다.

### 25-08

```javascript
class Person {}

// 인스턴스 생성
const me = new Person();
console.log(me); // Person {}
```

### 25-09

```javascript
class Person {}

const me = Person();
// TypeError: Class constructor Person cannot be invoked without 'new'
```

`표현식으로 정의된 경우`

- 클래스를 가리키는 식별자가 아닌 기명 클래스 표현식의 클래스 이름을 사용해 인스턴스를 생성시
  => 외부 코드에서 접근 불가능하여 에러

### 25-10

```javascript
const Person = class MyClass {};

// 함수 표현식과 마찬가지로 클래스를 가리키는 식별자로 인스턴스를 생성해야 한다.
const me = new Person();

console.log(MyClass); // ReferenceError: MyClass is not defined

const you = new MyClass(); // ReferenceError: MyClass is not defined
```

# 25.5 메서드

`constructor(생성자), 프로토타입 메서드, 정적 메서드`

## 25.5.1 constructor

인스턴스를 생성하고 초기화

### 25-11, 25-12

```javascript
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}

// 클래스는 함수다.
console.log(typeof Person); // function
console.dir(Person);
```

- constructor 프로퍼티 = 클래스 자기자신 = 클래스는 인스턴스를 생성하는 생성자 함수

### 25-13

```javascript
// 인스턴스 생성
const me = new Person("Lee");
console.log(me);
```

- name 프로퍼티: 클래스가 생성한 인스턴스 프로퍼티
- this: 클래스가 생성한 인스턴스 (<- me)

### 25-14

```javascript
// 클래스
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}

// 생성자 함수
function Person(name) {
  // 인스턴스 생성 및 초기화
  this.name = name;
}
```

`constructor`는 최대 1개

### 25-15

```javascript
class Person {
  constructor() {}
  constructor() {}
}
// SyntaxError: A class may only have one constructor
```

`constructor` 생략시

- 빈 constructor 암묵적으로 정의
- 빈 객체가 생성

### 25-16

```javascript
class Person {}
```

### 25-17

```javascript
class Person {
  constructor() {}
}

const me = new Person();
console.log(me); // Person {}
```

`프로퍼티 추가되어 초기화된 인스턴스`

- this 프로퍼티 추가

### 25-18

```javascript
class Person {
  constructor() {
    // 고정값으로 인스턴스 초기화
    this.name = "Lee";
    this.address = "Seoul";
  }
}

const me = new Person();
console.log(me); // Person {name: "Lee", address: "Seoul"}
```

### 25-19

```javascript
class Person {
  constructor(name, address) {
    // 매개변수: 인수로 인스턴스 초기화
    this.name = name;
    this.address = address;
  }
}

// 인수로 초기값을 전달한다. 초기값은 constructor에 전달된다.
const me = new Person("Lee", "Seoul");
console.log(me); // Person {name: "Lee", address: "Seoul"}
```

`constructor`

- 암묵적으로 this = 인스턴스 반환

### 25-20

```javascript
class Person {
  constructor(name) {
    this.name = name;

    // 명시적으로 객체를 반환하면 암묵적인 this 반환이 무시된다.
    return {};
  }
}

const me = new Person("Lee");
console.log(me); // {}
```

### 25-21

```javascript
class Person {
  constructor(name) {
    this.name = name;

    // 명시적으로 원시값을 반환하면 원시값 반환은 무시되고 암묵적으로 this가 반환된다.
    return 100;
  }
}

const me = new Person("Lee");
console.log(me); // Person { name: "Lee" }
```

## 25.5.2 프로토타입 메서드

### 25-22

```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHi = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person("Lee");
me.sayHi(); // Hi! My name is Lee
```

아래와 같이 클래스의 prototype 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 된다.

### 25-23

```javascript
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 프로토타입 메서드
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }
}

const me = new Person("Lee");
me.sayHi(); // Hi! My name is Lee
```

- 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다.

  1. `getPrototypeOf`: 지정된 객체의 프로토타입(가령 내부 `[[Prototype]]` 속성값)을 반환

- **상속**: 클래스 몸체에서 정의한 메서드는 인스턴스의 프로토타입에 존재하는 프로토타입 메서드가 된다.

- 클래스 = 인스턴스 생성하는 생성자 함수 = 프로토타입 기반의 객체 생성 매커니즘

### 25-24

```javascript
// me 객체의 프로토타입은 Person.prototype이다.
Object.getPrototypeOf(me) === Person.prototype; // -> true
me instanceof Person; // -> true

//? Person.prototype의 프로토타입은 Object.prototype이다.
Object.getPrototypeOf(Person.prototype) === Object.prototype; // -> true
me instanceof Object; // -> true

// me 객체의 constructor는 Person 클래스다.
me.constructor === Person; // -> true
```

## 25.5.3 정적 메서드

= 인스턴스를 생성하지 않아도 호출할 수 있는 메서드

### 25-25

```javascript
// 생성자 함수의 경우
function Person(name) {
  this.name = name;
}

// 정적 메서드
Person.sayHi = function () {
  console.log("Hi!");
};

// 정적 메서드 호출
Person.sayHi(); // Hi!
```

`static`

- 메서드에 static 키워드를 붙이면 정적 메서드(클래스 메서드)

### 25-26

```javascript
// 클래스의 경우
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 프로토타입 메서드
  sayHello() {
    console.log(`Hello! My name is ${this.name}`);
  }

  // 정적 메서드
  static sayHi() {
    console.log("Hi!");
  }
}

// const me = new Person('Lee');
```

- 정적 메서드: 클래스(자기자신)에 바인딩된 메서드
- 클래스는 함수 객체로 평가되므로 자신의 프로퍼티/메서드 소유 => 인스턴스 없이도 호출!
  ![정적 메서드](/assets/25_정적%20메서드.png)

### 25-27

```javascript
// 정적 메서드는 클래스로 호출한다.
// 정적 메서드는 인스턴스 없이도 호출할 수 있다.
Person.sayHi(); // Hi!
```

정적 메서드가 바인딩된 클래스는 인스턴스의 프로토타입 체인상에 존재하지 않음 =>상속 불가능

### 25-28

```javascript
// 인스턴스 생성
const me = new Person("Lee");
me.sayHi(); // TypeError: me.sayHi is not a function
```

## 25.5.4 정적 메서드와 프로토타입 메서드의 차이

1. 자신이 속해 있는 프로토타입 체인이 다르다.
2. 정적 메서드는 클래스로, 프로토타입 메서드는 인스턴스로 호출한다.
3. 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만, 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.

### 25-29

```javascript
class Square {
  // 정적 메서드
  static area(width, height) {
    return width * height;
  }
}

console.log(Square.area(10, 10)); // 100
```

### 25-30

```javascript
class Square {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  // 프로토타입 메서드
  area() {
    return this.width * this.height;
  }
}

const square = new Square(10, 10);
console.log(square.area()); // 100
```

- 프로토타입 메서드와 정적 메서드 내부의 this 바인딩이 다르다.

`표준 빌트인 객체`

- Math, Number, JSON, Object, Reflect 등 다양한 정적 메서드 = 전역에서 사용할 유틸리티 함수

### 25-31

```javascript
// 표준 빌트인 객체의 정적 메서드
Math.max(1, 2, 3); // -> 3
Number.isNaN(NaN); // -> true
JSON.stringify({ a: 1 }); // -> "{"a":1}"
Object.is({}, {}); // -> false
Reflect.has({ a: 1 }, "a"); // -> true
```

## 25.5.5 클래스에서 정의한 메서드의 특징

1. function 키워드를 생략한 메서드 축약 표현을 사용한다. - 프로토타입 메서드 등
2. 객체 리터럴과는 다르게 클래스에 메서드를 정의할 때는 콤마가 필요 없다.
3. 암묵적으로 strict mode로 실행된다.
4. 열거불가능하다. => 프로퍼티 어트리뷰트 [[Enumverable]]의 값이 false다.
5. 내부 메서드 [[Construct]]를 갖지 않는 non-constructor다. 따라서, new 연산자와 함께 호출할 수 없다.

# 25.6 클래스의 인스턴스 생성 과정

1. 인스턴스 생성과 this 바인딩
   암묵적으로 빈 객체 생성 = 인스턴스
   -> this에 바인딩: 클래스의 prototype 프로퍼티가 가리키는 객체 설정
   <br/>
2. 인스턴스 초기화
   constructor 내부 코드 실행
   -> this에 바인딩 되어 있는 인스턴스 초기화
   <br/>
3. 인스턴스 반환
   this 암묵적 반환

### 25-32

```javascript
class Person {
  // 생성자
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // Person {}
    console.log(Object.getPrototypeOf(this) === Person.prototype); // true

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.name = name;

    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
  }
}
```

# 25.7 프로퍼티

## 25.7.1 인스턴스 프로퍼티

> **constructor 내부에서 정의**  
> **this: 빈객체 바인딩** = 클래스가 암묵적으로 생성 <br> **인스턴스 프로퍼티 추가**: 인스턴스 초기화

### 25-34

```javascript
class Person {
  constructor(name) {
    // this에 추가한 프로퍼티 = 인스턴스 프로퍼티
    this.name = name; // name 프로퍼티는 public하다.
  }
}

const me = new Person("Lee");
console.log(me); // Person {name: "Lee"}

// name은 public하다.
console.log(me.name); // Lee
```

## 25.7.2 접근자 프로퍼티

> **접근자 함수로 구성된 프로퍼티**: 자체적으로 값(`[[Value]]` 내부슬롯)을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장 <br> getter 함수와 setter 함수로 구성

### 25-35

```javascript
const person = {
  // 데이터 프로퍼티
  firstName: "Ungmo",
  lastName: "Lee",

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set fullName(name) {
    // 배열 디스트럭처링 할당: "36.1. 배열 디스트럭처링 할당" 참고
    [this.firstName, this.lastName] = name.split(" ");
  },
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(`${person.firstName} ${person.lastName}`); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
person.fullName = "Heegun Lee";
console.log(person); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(person.fullName); // Heegun Lee

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다.
console.log(Object.getOwnPropertyDescriptor(person, "fullName"));
// {get: ƒ, set: ƒ, enumerable: true, configurable: true}
```

> **클래스에 사용되는 접근자 프로퍼티**

### 25-36

```javascript
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // setter 함수
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(" ");
  }
}

// 인스턴스 생성
const me = new Person("Ungmo", "Lee");

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(`${me.firstName} ${me.lastName}`); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
me.fullName = "Heegun Lee";
console.log(me); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(me.fullName); // Heegun Lee

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다.
console.log(Object.getOwnPropertyDescriptor(Person.prototype, "fullName"));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}
```

1. getter 함수: 인스턴스 프로퍼티에 접근할 때
   <br>
2. setter 함수: 인스턴스 프로퍼티에 값을 할당할 때
   <br>
3. 인스턴스 프로퍼티처럼 사용: 호출이 아닌 참조하는 형식으로 사용 -> 반드시 매개변수 필요 (setter는 하나)
   <br>
4. 프로토타입의 프로퍼티: 클래스의 메서드, 접근자 프로퍼티 등
   ![접근자 프로퍼티는 프로토타입 프로퍼티](/assets/25_접근자%20프로퍼티.png)

### 25-37

```javascript
// Object.getOwnPropertyNames는 비열거형(non-enumerable)을 포함한 모든 프로퍼티의 이름을 반환한다.(상속 제외)
Object.getOwnPropertyNames(me); // -> ["firstName", "lastName"]
Object.getOwnPropertyNames(Object.getPrototypeOf(me)); // -> ["constructor", "fullName"]
```

## 25.7.3 클래스 필드 정의 제안

- **클래스 필드(또는 맴버)**란?
  (클래스 기반 객체지향 언어) 클래스가 생성할 인스턴스의 프로퍼티를 가리키는 용어

### 25-38

```java
// 자바의 클래스 정의
public class Person {
  // ① 클래스 필드 정의
  // 클래스 필드는 클래스 몸체에 this 없이 선언해야 한다. (constructor 사용 X)
  private String firstName = "";  // 접근제한자 private
  private String lastName = "";

  // 생성자
  Person(String firstName, String lastName) {
    // ③ this는 언제나 클래스가 생성할 인스턴스를 가리킨다.
    this.firstName = firstName;
    this.lastName = lastName;
  }

  public String getFullName() {
    // ② 클래스 필드 참조
    // this 없이도 클래스 필드를 참조할 수 있다.
    return firstName + " " + lastName;
  }
}
```

- **자바스크립트의 경우**
  - 메서드만 선언 가능
  - 최신 브라우저(Chrome 72 이상) 또는 최신 Node.js(버전 12 이상)에서 실행시 문법 에러가 발생하지 않는다.

> **문법 에러가 발생하지 않는 이유?**
> "Class field declarations" <br> 2021.01, TC39 프로세스의 stage3(candidate)에 제안 <br> 인스턴스 프로퍼티를 클래스 기반 객체지향 언어의 클래스 필드처럼 정의할 수 있다 <br> 정식 표준 사양으로 승급되지 않았지만 선제적으로 미리 구현하여 에러가 나지 않는다. <br/><br/> _\* TC39: ECMA-262 사양의 관리를 담당하는 위원회_ <br/> _\* TC39 프로세스:ECMA-262 사양에 새로운 표준 사양을 추가하기 위해 공식적으로 명문화해 놓은 과정_

### 25-40

```javascript
class Person {
  // 클래스 필드 정의(this 사용 X)
  name = "Lee";
}

const me = new Person();
console.log(me); // Person {name: "Lee"}
```

### 25-41

this에 클래스 필드를 바인딩해서는 안된다.

```javascript
class Person {
  this.name = ''; // SyntaxError: Unexpected token '.'
}
```

### 25-42

클래스 필드 참조시 this를 반드시 사용해야 한다. (클래스 기반 객체지향 언어는 생략 가능)

```javascript
class Person {
  // 클래스 필드
  name = "Lee";

  constructor() {
    console.log(name); // ReferenceError: name is not defined
  }
}

new Person();
```

### 25-43

클래스 필드를 초기화하지 않으면 undefined를 갖는다.

```javascript
class Person {
  name;
}

const me = new Person();
console.log(me); // Person {name: undefined}
```

### 25-44

인스턴스 생성시 외부 초기값으로 클래스 필드를 초기화해야 한다면 constructor 클래스 필드를 초기화해야 한다.

```javascript
class Person {
  name;

  constructor(name) {
    // 클래스 필드 초기화.
    this.name = name;
  }
}

const me = new Person("Lee");
console.log(me); // Person {name: "Lee"}
```

### 25-45

인스턴스에 클래스 필드에 해당하는 프로퍼티가 없다면 자동 추가된다.
따라서, constructor 밖에서 클래스 필드를 정의할 필요가 없다.

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
}

const me = new Person("Lee");
console.log(me); // Person {name: "Lee"}
```

### 25-46

함수는 일급객체이기 때문에 할당할 수 있다.
따라서, 클래스 필드를 통해 메서드를 정의할 수 있다.
다만, 인스턴스 메서드가 되기 때문에 권장하지 않는다. (모든 클래스 필드는 인스턴스 프로퍼티)

```javascript
class Person {
  // 클래스 필드에 문자열을 할당
  name = "Lee";

  // 클래스 필드에 함수를 할당
  getName = function () {
    return this.name;
  };
  // 화살표 함수로 정의할 수도 있다.
  // getName = () => this.name;
}

const me = new Person();
console.log(me); // Person {name: "Lee", getName: ƒ}
console.log(me.getName()); // Lee
```

### 25-47

클래스 필드에 화살표 함수를 할당하여 화살표 내부 함수의 this가 인스턴스를 가리키게 한다.

```html
<!DOCTYPE html>
<html>
  <body>
    <button class="btn">0</button>
    <script>
      class App {
        constructor() {
          this.$button = document.querySelector(".btn");
          this.count = 0;

          // increase 메서드를 이벤트 핸들러로 등록
          // 이벤트 핸들러 increase 내부의 this는 DOM 요소(this.$button)를 가리킨다.
          // 하지만 increase는 화살표 함수로 정의되어 있으므로
          // increase 내부의 this는 인스턴스를 가리킨다.
          this.$button.onclick = this.increase;

          // 만약 increase가 화살표 함수가 아니라면 bind 메서드를 사용해야 한다.
          // $button.onclick = this.increase.bind(this);
        }

        // 인스턴스 메서드
        // 화살표 함수 내부의 this는 언제나 상위 컨텍스트의 this를 가리킨다.
        increase = () => (this.$button.textContent = ++this.count);
      }
      new App();
    </script>
  </body>
</html>
```

## 25.7.4 private 필드 정의 제안

private, public, protected와 같은 접근 제한자를 지원하지 않아 언제나 public이다.

### 25-48

```javascript
class Person {
  constructor(name) {
    this.name = name; // 인스턴스 프로퍼티는 기본적으로 public하다.
  }
}

// 인스턴스 생성
const me = new Person("Lee");
console.log(me.name); // Lee
```

### 25-49

클래스 필드도 기본적으로 public하다.

```javascript
class Person {
  name = "Lee";
}

// 인스턴스 생성
const me = new Person();
console.log(me.name); // Lee
```

> TC39 프로세스의 Stage 3(candidate)에 **private 필드를 정의할 수 있는 표준 사양**이 제안되었다.

### 25-50

```javascript
class Person {
  // private 필드 정의
  #name = "";

  constructor(name) {
    // private 필드 참조 (# 붙여줘야한다.)
    this.#name = name;
  }
}

const me = new Person("Lee");

// private 필드 #name은 클래스 외부에서 참조할 수 없다.
console.log(me.#name);
// SyntaxError: Private field '#name' must be declared in an enclosing class
```

|         접근 가능성         | public | private |
| :-------------------------: | :----: | :-----: |
|         클래스 내부         |   O    |    O    |
|      자식 클래스 내부       |   O    |    X    |
| 클래스 인스턴스를 통한 접근 |   O    |    X    |

### 25-51

접근자 프로퍼티(getter, setter)를 이용한 접근은 가능하다.

```javascript
class Person {
  // private 필드 정의
  #name = "";

  constructor(name) {
    this.#name = name;
  }

  // name은 접근자 프로퍼티다.
  get name() {
    // private 필드를 참조하여 trim한 다음 반환한다.
    return this.#name.trim();
  }
}

const me = new Person(" Lee ");
console.log(me.name); // Lee
```

### 25-52

private 필드는 반드시 클래스 몸체에서 정의해야 한다.

```javascript
class Person {
  constructor(name) {
    this.#name = name;
    // SyntaxError: Private field '#name' must be declared in an enclosing class
  }
}
```

# 25.7.5 static 필드 정의 제안

- static 키워드를 사용하여 정적 메서드 정의 가능. 단, 정적 필드는 정의할 수 없었다.
- TC39 프로세스의 stage 3에서 제안한 static public/private 필드 최신 브라우저와 최신 Node.js에 구현

### 25-53

```javascript
class MyMath {
  // static public 필드 정의
  static PI = 22 / 7;

  // static private 필드 정의
  static #num = 10;

  // static 메서드
  static increment() {
    return ++MyMath.#num;
  }
}

console.log(MyMath.PI); // 3.142857142857143
console.log(MyMath.increment()); // 11
```

# 25.8 상속에 의한 클래스 확장

## 25.8.1 클래스 상속과 생성자 함수 상속

**기존 클래스를 상속받아 새로운 클래스를 확장**
![클래스 상속](/assets/25_7.png)

### 25-54

상속을 통해 다른 클래스를 확장할 수 있는 문법인 extends 키워드를 제공한다.
자신만의 고유한 속성만 추가하여 확장할 수 있다.

```javascript
class Animal {
  constructor(age, weight) {
    this.age = age;
    this.weight = weight;
  }

  eat() {
    return "eat";
  }

  move() {
    return "move";
  }
}

// 상속을 통해 Animal 클래스를 확장한 Bird 클래스
class Bird extends Animal {
  fly() {
    return "fly";
  }
}

const bird = new Bird(1, 5);

console.log(bird); // Bird {age: 1, weight: 5}
console.log(bird instanceof Bird); // true
console.log(bird instanceof Animal); // true

console.log(bird.eat()); // eat
console.log(bird.move()); // move
console.log(bird.fly()); // fly
```

![상속에 의해 확장된 클래스 Bird에 의해 생성된 인스턴스의 프로토타입 체인](/assets/25_9.png)

### 25-55

의사 클래스 상속 패턴(pseudo classical inheritance) 참고만 하기

```javascript
var Animal = (function () {
  function Animal(age, weight) {
    this.age = age;
    this.weight = weight;
  }

  Animal.prototype.eat = function () {
    return "eat";
  };

  Animal.prototype.move = function () {
    return "move";
  };

  return Animal;
})();

// Animal 생성자 함수를 상속하여 확장한 Bird 생성자 함수
var Bird = (function () {
  function Bird() {
    // Animal 생성자 함수에게 this와 인수를 전달하면서 호출
    Animal.apply(this, arguments);
  }

  // Bird.prototype을 Animal.prototype을 프로토타입으로 갖는 객체로 교체
  Bird.prototype = Object.create(Animal.prototype);
  // Bird.prototype.constructor을 Animal에서 Bird로 교체
  Bird.prototype.constructor = Bird;

  Bird.prototype.fly = function () {
    return "fly";
  };

  return Bird;
})();

var bird = new Bird(1, 5);

console.log(bird); // Bird {age: 1, weight: 5}
console.log(bird.eat()); // eat
console.log(bird.move()); // move
console.log(bird.fly()); // fly
```

## 25.8.2 extends 키워드

### 25-56

```javascript
// 수퍼(베이스/부모)클래스
class Base {}

// 서브(파생/자식)클래스
class Derived extends Base {}
```

- 서브클래스: 상속을 통해 확장된 클래스. 파생 클래스. 자식 클래스
- 수퍼클래스: 서브클래스에 상속된 클래스. 베이스 클래스. 부모 클래스

![extends 키워드](/assets/25_10.png)

## 25.8.3 동적 상속

extends 키워드는 생성자 함수를 상속받아 클래스를 확장할 수 있다.

### 25-57

```javascript
// 생성자 함수
function Base(a) {
  this.a = a;
}

// 생성자 함수를 상속받는 서브클래스
class Derived extends Base {}

const derived = new Derived(1);
console.log(derived); // Derived {a: 1}
```

### 25-58

`[[Construct]]` 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수도 있다.
따라서, 동적으로 상속받을 대상을 결정할 수 있다.

```javascript
function Base1() {}

class Base2 {}

let condition = true;

// 조건에 따라 동적으로 상속 대상을 결정하는 서브클래스
class Derived extends (condition ? Base1 : Base2) {}

const derived = new Derived();
console.log(derived); // Derived {}

console.log(derived instanceof Base1); // true
console.log(derived instanceof Base2); // false
```

## 25.8.4 서브클래스의 constructor

constructor 생략시 비어있는 constructor가 암묵적으로 정의된다.

### 25-59

```javascript
constructor() {}
```

### 25-60

서브 클래스에서 constructor 생략시 다음과 같은 constructor가 암묵적으로 정의된다.
super()는 수퍼 클래스의 constructor을 호출하여 인스턴스를 생성한다.

```javascript
constructor(...args) { super(...args); }
```

> 다음 예제를 살펴보자!

### 25-61

```javascript
// 수퍼클래스
class Base {}

// 서브클래스
class Derived extends Base {}
```

### 25-62

위의 예제는 다음과 같이 암묵적으로 constructor가 정의된다.

```javascript
// 수퍼클래스
class Base {
  constructor() {} // 암묵적으로 constructor 정의
}

// 서브클래스
class Derived extends Base {
  constructor() {
    super(); // 수퍼 클래스의 constructor을 호출하여 인스턴스를 생성
  }
}

const derived = new Derived();
console.log(derived); // Derived {}
```

## 25.8.5 super 키워드

1. 함수처럼 호출할 수 있다.
   <br>
2. this와 같이 식별자처럼 참조할 수 있다.
   <br>
3. 동작
   - **super를 호출하면 수퍼클래스의 constructor(super-constructor)를 호출한다.**
   - **super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.**

### super 호출

super를 호출하면 수퍼클래스의 constructor를 호출한다.

- 서브 클래스의 constructor를 생략할 수 있다.
- 이때 new 연산자와 함께 서브클래스를 호출하며 전달한 인수는 모두 서브클래스에 암묵적으로 정의된 constructor의 super 호출을 통해 수퍼클래스의 constructor에 전달된다.

#### 25-63

```javascript
// 수퍼클래스
class Base {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브클래스
class Derived extends Base {
  // 다음과 같이 암묵적으로 constructor가 정의된다.
  // constructor(...args) { super(...args); }
}

const derived = new Derived(1, 2);
console.log(derived); // Derived {a: 1, b: 2}
```

#### 25-64

서브클래스에서 추가한 프로퍼티를 갖는 인스턴스를 생성한다면 서브클래스의 constructor를 생략할 수 없다.

```javascript
// 수퍼클래스
class Base {
  constructor(a, b) {
    // ④
    this.a = a;
    this.b = b;
  }
}

// 서브클래스 - 프로퍼티 추가
class Derived extends Base {
  constructor(a, b, c) {
    // ②
    super(a, b); // ③
    this.c = c;
  }
}

const derived = new Derived(1, 2, 3); // ①
console.log(derived); // Derived {a: 1, b: 2, c: 3}
```

### **super 호출할 때 주의사항**

1. 서브 클래스에서 constructor를 생략하지 않는 경우 서브클래스의 constructor에서는 반드시 super를 호출해야 한다.

#### 25-65

```javascript
class Base {}

class Derived extends Base {
  constructor() {
    // ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    console.log("constructor call");
  }
}

const derived = new Derived();
```

2. 서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없다.

#### 25-66

```javascript
class Base {}

class Derived extends Base {
  constructor() {
    // ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    this.a = 1;
    super();
  }
}

const derived = new Derived(1);
```

3. super는 반드시 서브클래스의 constructor에서만 호출한다.

#### 25-67

```javascript
class Base {
  constructor() {
    super(); // SyntaxError: 'super' keyword unexpected here
  }
}

function Foo() {
  super(); // SyntaxError: 'super' keyword unexpected here
}
```

### super 참조

super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

#### 25-68

1. super.sayHi는 수퍼클래스의 프로토타입 메서드를 가리킨다.

```javascript
// 수퍼클래스
class Base {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    return `Hi! ${this.name}`;
  }
}

// 서브클래스
class Derived extends Base {
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  }
}

const derived = new Derived("Lee");
console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

#### 25-69

수퍼클래스의 prototype 프로퍼티에 바인딩된 프로토타입을 참조할 수 있다.

```javascript
// 수퍼클래스
class Base {
  constructor(name) {
    this.name = name; // 인스턴스 프로퍼티
  }

  sayHi() {
    return `Hi! ${this.name}`;
  }
}

class Derived extends Base {
  sayHi() {
    // __super는 Base.prototype을 가리킨다.
    const __super = Object.getPrototypeOf(Derived.prototype);
    return `${__super.sayHi.call(this)} how are you doing?`;
  }
}
```

- super는 자신을 참조하고 있는 메서드(Drived의 sayHi)가 바인딩되어 있는 객체(Derived.prototype)의 프로토타입(Base의 sayHi)를 가리킨다.
- super.sayHi -> Derived.prototype.sayHi
- call 메서드를 사용해 this를 전달
  <br>
- this를 전달하지 않을 경우 Base.prototype이 나와 인스턴스에 존재하는 name 프로퍼티를 찾을 수 없다.
- 따라서, super 참조가 동작하기 위해서는 super가 참조하고 있는 메서드가 바인딩되어 있는 객체의 프로토타입을 찾을 수 있어야 한다.
- 메서드는 내부 슬롯 `[[HomeObject]]`을 통해 자신을 바인딩하고 있는 객체를 가리킨다.

#### 25-70

```javascript
/*
[[HomeObject]]는 메서드 자신을 바인딩하고 있는 객체를 가리킨다.
[[HomeObject]]를 통해 메서드 자신을 바인딩하고 있는 객체의 프로토타입을 찾을 수 있다.
예를 들어, Derived 클래스의 sayHi 메서드는 Derived.prototype에 바인딩되어 있다.
따라서 Derived 클래스의 sayHi 메서드의 [[HomeObject]]는 Derived.prototype이고
이를 통해 Derived 클래스의 sayHi 메서드 내부의 super 참조가 Base.prototype으로 결정된다.
따라서 super.sayHi는 Base.prototype.sayHi를 가리키게 된다.
*/
super = Object.getPrototypeOf([[HomeObject]])
```

#### 25-71

단, ES6의 메서드 축약 표현으로 정의한 메서드만 `[[HomeObject]]`를 가지므로 주의한다.

```javascript
const obj = {
  // foo는 ES6의 메서드 축약 표현으로 정의한 메서드다. 따라서 [[HomeObject]]를 갖는다.
  foo() {},
  // bar는 ES6의 메서드 축약 표현으로 정의한 메서드가 아니라 일반 함수다.
  // 따라서 [[HomeObject]]를 갖지 않는다.
  bar: function () {},
};
```

#### 25-72

클래스 외에 객체 리터럴에서도 가질 수 있다.

```javascript
const base = {
  name: "Lee",
  sayHi() {
    return `Hi! ${this.name}`;
  },
};

const derived = {
  __proto__: base,
  // ES6 메서드 축약 표현으로 정의한 메서드다. 따라서 [[HomeObject]]를 갖는다.
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  },
};

console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

2. 서브클래스의 정적 메서드 내에서 super.sayHi는 수퍼클래스의 정적 메서드 sayHi를 가리킨다.

#### 25-73

```javascript
// 수퍼클래스
class Base {
  static sayHi() {
    return "Hi!";
  }
}

// 서브클래스
class Derived extends Base {
  static sayHi() {
    // super.sayHi는 수퍼클래스의 정적 메서드를 가리킨다.
    return `${super.sayHi()} how are you doing?`;
  }
}

console.log(Derived.sayHi()); // Hi! how are you doing?
```

## 25.8.6 상속 클래스의 인스턴스 생성 과정

### 25-74

```javascript
// 수퍼클래스
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }

  toString() {
    return `width = ${this.width}, height = ${this.height}`;
  }
}

// 서브클래스
class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);
    this.color = color;
  }

  // 메서드 오버라이딩
  toString() {
    return super.toString() + `, color = ${this.color}`;
  }
}

const colorRectangle = new ColorRectangle(2, 4, "red");
console.log(colorRectangle); // ColorRectangle {width: 2, height: 4, color: "red"}

// 상속을 통해 getArea 메서드를 호출
console.log(colorRectangle.getArea()); // 8
// 오버라이딩된 toString 메서드를 호출
console.log(colorRectangle.toString()); // width = 2, height = 4, color = red
```

![ColorRectangle 클래스에 의해 생성된 인스턴스의 프로토타입 체인](/assets/25_11.png)

### 서브 클래스 ColorRectangle이 new 연산자와 함께 호출시 진행되는 인스턴스 생성 과정

**1. 서브 클래스의 super 호출**

- 자바스크립트 엔진에 의해 내부 슬롯 `[[ConstructorKind]]`를 가져 수퍼 클래스와 서브 클래스의 동작이 구분된다. (기본 'base'. 서브클래스는 'derived')
- **자신이 직접 인스턴스를 생성하지 않고 수퍼클래스에게 위임한다.** <br> 따라서, 서브 클래스의 constructor에서 반드시 super를 호출해야 한다.

**2. 수퍼클래스의 인스턴스 생성과 this 바인딩**

- 인스턴스는 수퍼클래스가 생성한다.
- new 연산자와 함께 호출된 클래스는 서브클래스이다.
- 따라서, **인스턴스는 new.target이 가리키는 서브클래스가 생성한 것으로 처리**된다. <br> -> 생성된 인스턴스의 프로토타입 = 서브클래스의 prototype 프로퍼티가 가리키는 객체

#### 25-76

```javascript
// 수퍼클래스
class Rectangle {
  constructor(width, height) {
    // 암묵적으로 빈 객체, 즉 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // ColorRectangle {}
    // new 연산자와 함께 호출된 함수, 즉 new.target은 ColorRectangle이다.
    console.log(new.target); // ColorRectangle

    // 생성된 인스턴스의 프로토타입으로 ColorRectangle.prototype이 설정된다.
    console.log(Object.getPrototypeOf(this) === ColorRectangle.prototype); // true
    console.log(this instanceof ColorRectangle); // true
    console.log(this instanceof Rectangle); // true
...
```

**3. 수퍼클래스의 인스턴스 초기화**

### 25-77

```javascript
// 수퍼클래스
class Rectangle {
  constructor(width, height) {
    // 암묵적으로 빈 객체, 즉 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // ColorRectangle {}
    // new 연산자와 함께 호출된 함수, 즉 new.target은 ColorRectangle이다.
    console.log(new.target); // ColorRectangle

    // 생성된 인스턴스의 프로토타입으로 ColorRectangle.prototype이 설정된다.
    console.log(Object.getPrototypeOf(this) === ColorRectangle.prototype); // true
    console.log(this instanceof ColorRectangle); // true
    console.log(this instanceof Rectangle); // true

    // 인스턴스 초기화
    this.width = width;
    this.height = height;

    console.log(this); // ColorRectangle {width: 2, height: 4}
  }
...
```

**4. 서브클래스 constructor로의 복귀와 this 바인딩**

super가 반환한 인스턴스가 this에 바인딩되고 서브클래스가 이를 그대로 사용한다.

#### 25-78

```javascript
// 서브클래스
class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);

    // super가 반환한 인스턴스가 this에 바인딩된다.
    console.log(this); // ColorRectangle {width: 2, height: 4}
...
```

- 이처럼, super가 호출되지 않으면 인스턴스가 생성되지 않고, this 바인딩도 할 수 없다.
- 서브클래스의 constructor에서 super를 호출하기 전에 this를 참조할 수 없는 이유도 동일하다.

**5. 서브클래스의 인스턴스 초기화**

인스턴스에 프로퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티를 초기화한다.

**6. 인스턴스 반환**

#### 25-79

```javascript
// 서브클래스
class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);

    // super가 반환한 인스턴스가 this에 바인딩된다.
    console.log(this); // ColorRectangle {width: 2, height: 4}

    // 인스턴스 초기화
    this.color = color;

    // 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
    console.log(this); // ColorRectangle {width: 2, height: 4, color: "red"}
  }
...
```

## 25.8.7 표준 빌트인 생성자 함수 확장

`[[Construct]]` 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다.

### 25-80

```javascript
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
  // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 1, 2, 3);
console.log(myArray); // MyArray(4) [1, 1, 2, 3]

// MyArray.prototype.uniq 호출
console.log(myArray.uniq()); // MyArray(3) [1, 2, 3]
// MyArray.prototype.average 호출
console.log(myArray.average()); // 1.75
```

> **map, filter와 같이 새로운 배열을 반환하는 메서드가 MyArray 클래스의 인스턴스를 반환한다.**

### 25-81

```javascript
console.log(myArray.filter((v) => v % 2) instanceof MyArray); // true
```

### 25-82

MyArray의 인스턴스를 반환하지 않고 Array의 인스턴스를 반환하면 MyArray 클래스의 메서드와 메서드 체이닝이 불가능하다.

```javascript
// 메서드 체이닝
// [1, 1, 2, 3] => [ 1, 1, 3 ] => [ 1, 3 ] => 2
console.log(
  myArray
    .filter((v) => v % 2)
    .uniq()
    .average()
); // 2
```

### 25-83

Array가 생성한 인스턴스를 반환하게 하려면 `Symbol.species`를 사용하여 정적 접근자 프로퍼티를 추가한다.

```javascript
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
  // 모든 메서드가 Array 타입의 인스턴스를 반환하도록 한다.
  static get [Symbol.species]() {
    return Array;
  }

  // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 1, 2, 3);

console.log(myArray.uniq() instanceof MyArray); // false
console.log(myArray.uniq() instanceof Array); // true

// 메서드 체이닝
// uniq 메서드는 Array 인스턴스를 반환하므로 average 메서드를 호출할 수 없다.
console.log(myArray.uniq().average());
// TypeError: myArray.uniq(...).average is not a function
```
