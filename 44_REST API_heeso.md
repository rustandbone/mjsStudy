# REST API

**REST API 의 탄생**
<br>
REST 는 2000년 발표 당시의 웹이 HTTP 를 제대로 사용하지 못하고 있는 상황을 보고 HTTP 의 장점을 최대한 활용할 수 있는 아키텍처로서 소개되었다.<br>
REST 는 HTTP 프로토콜을 의도에 맞게 디자인하도록 유도하고 있다.<br>
REST 의 기본 원칙을 성실히 지킨 서비스 디자인을 "RESTful😴" 이라고 표현한다.

**REST** 는 REpresentational State Transfer (상태 전이 표현) 의 약자로 웹 서비스 간의 통신을 위한 소프트웨어 아키텍처 스타일 중 하나이다. 자원을 표현하고 상태 전이를 통해 데이터를 주고받는 방식을 의미한다. 다양한 클라이언트 애플리케이션과 서버 간의 데이터 통신을 가능하게 한다.

**API** 는 Application Programming Interface 의 약자로, 응용 프로그램 간의 상호작용을 위한 인터페이스를 의미한다. 다른 응용 프로그램이나 서비스의 기능과 데이터에 접근할 수 있도록 정의된 메서드, 규약, 도구 등의 집합이다. API 는 다양한 형태로 제공될 수 있는데, REST API 는 웹 서비스에서 사용되는 것 중 하나로, 상태 전이 표현 ( REST ) 의 원칙을 따르는 API 이다.

📢💁 **REST** 는 HTTP 를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처이고, **REST API** 는 REST 를 기반으로 서비스 API 를 구현한 것을 의미한다.

---
## 44.1 REST API 의 구성
REST API 는 자원 resource, 행위 verb, 표현 representations 의 3가지 요소로 구성된다.
자체 표현 구조로 구성되어 REST API 만으로 HTTP 요청의 내용을 이해할 수 있다.

|구성 요소|내용|표현 방법|예시|
|--|--|--|--|
|자원<br>Resource|자원|URI(엔드포인트)|사용자 정보를 나타내는 자원에 접근하기 위함<br>`/users`: 사용자 정보에 접근<br>`/users/1`: ID가 1인 사용자 정보에 접근
|행위<br>Verb|자원에 대한 행위|HTTP 요청 메서드|`GET`,`POST`,`PUT`,`DELETE`|
|표현<br>Representation|자원에 대한 행위의 구체적 내용|페이로드|자원에 대한 행위의 구체적인 내용을 담는 데이터|

<br>

## 44.2 REST API 의 설계 원칙

RESTful😴 하기 위한 규칙 💁

1. **URI 는 리소스를 표현**하는데 집중하고 <br>
2. **행위에 대한 정의는 HTTP 요청 메서드를 통해** 한다.

<br>

📌 **1. URI 는 리소스를 표현해야 한다.**

리소스를 식별할 수 있는 이름은 동사보다는 명사를 사용한다.
이름에 `get` 같은 행위에 대한 표현이 들어가서는 안 된다.

```
# BAD 😞👎
GET / getTodos/1

# GOOD 😊👏
GET / todos/1
```

📌 **2. 리소스에 대한 행위는 HTTP 요청 메서드로 표현한다.**

HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적을 알리는 방법이다.
주로 5가지 요청 메서드를 사용하여 CRUD 를 구현한다.

|HTTP 요청 메서드|종류|목적|페이로드|
|:--:|--|--|:--:|
|`GET`|index/retrieve|모든/특정 리소스 취득|X|
|`POST`|create|리소스 생성|O|
|`PUT`|replace|리소스의 전체 교체|O|
|`PATCH`|modify|리소스의 일부 수정|O|
|`DELETE`|delete|모든/특정 리소스 삭제|X|

retrieve : 특정 리소스를 가져오는 것
페이로드 : 요청의 본문에 담기는 데이터

리소스에 대한 행위는 HTTP 요청 메서드를 통해 표현하며 URI 에 표현하지 않는다.

```
# BAD 😞👎
GET /todos/delete/1

# GOOD 😊👏
DELETE /todos/1
```

<br>

## 44.3 JSON Server 를 이용한 REST API 실습

JSON Server를 사용해 가상 REST API 서버를 구축하여 HTTP 요청을 전송하고 응답을 받는 실습을 진행해보자!

<br>

### 44.3.1 JSON Server 설치

`mksir json-server-exam && json-server-exam`<br>
`pnpm init-y`<br>
`pnpm install json-server--save-dev`

<br>

### 44.3.2 db.json 파일 생성

프로젝트 루트 폴더에 `db.json` 파일을 생성한다.
리소스를 제공하는 데이터베이스 역할을 한다.

~44-01~

```json
{
  "todos": [
    {
      "id": 1,
      "content": "HTML",
      "completed": true
    },
    {
      "id": 2,
      "content": "CSS",
      "completed": false
    },
    {
      "id": 3,
      "content": "Javascript",
      "completed": true
    }
  ]
}
```

<br>

### 44.3.3 JSON Server 실행

`$ json-server--watch db.json`<br>
db.json 파일의 변경을 감지하기 위해 watch 옵션을 추가한다.

`$ json-server--watch db.json --port 5000`<br>
기본 포트 (3000) 변경을 위해 port 옵션을 추가했다.

매번 명령어를 입력하는 번거로움을 덜기 위해 `package.json` 파일의 `scripts` 를 아래와 같이 수정한다.

~44-02~

```json
{
  "name": "json-server-exam",
  "version": "1.0.0",
  "scripts": {
    "start": "json-server --watch db.json"
  },
  "devDependencies": {
    "json-server": "^0.16.1"
  }
}
```

그 후 터미널에서 `pnpm start` 명령어를 입력해 JSON Server 를 실행한다.

<br>

### 44.3.4 `GET` 요청

todos 리소스에서 모든 todo 를 취득(index) 한다.

JSON Server 의 루트 폴저에 `public` 폴더를 생성하고 JSON Server 를 중단한 후 재실행한다. 그리고 `public` 폴더에 아래 `get_index.html` 을 추가하고 브라우저에서 `http:/localhost:3000/get_index.html` 로 접속한다.

~44-03~

```html
<!DOCTYPE html>
<html>
<body>
  <pre></pre>
  <script>
    // XMLHttpRequest 객체 생성
    const xhr = new XMLHttpRequest();

    // HTTP 요청 초기화
    // todos 리소스에서 모든 todo를 취득(index)
    xhr.open('GET', '/todos');

    // HTTP 요청 전송
    xhr.send();

    // load 이벤트는 요청이 성공적으로 완료된 경우 발생한다.
    xhr.onload = () => {
      // status 프로퍼티 값이 200이면 정상적으로 응답된 상태다.
      if (xhr.status === 200) {
        document.querySelector('pre').textContent = xhr.response;
      } else {
        console.error('Error', xhr.status, xhr.statusText);
      }
    };
  </script>
</body>
</html>
```

todos 리소스에서 id 를 사용하여 특정 todo 를 취득(retrieve)한다. `public` 폴더에 아래 `get_retrieve.html` 을 추가하고 브라우저에서 `http://localhost:3000?get_retrieve.html` 로 접속한다.

~44-04~

```html
<!DOCTYPE html>
<html>
<body>
  <pre></pre>
  <script>
    // XMLHttpRequest 객체 생성
    const xhr = new XMLHttpRequest();

    // HTTP 요청 초기화
    // todos 리소스에서 id를 사용하여 특정 todo를 취득(retrieve)
    xhr.open('GET', '/todos/1');

    // HTTP 요청 전송
    xhr.send();

    // load 이벤트는 요청이 성공적으로 완료된 경우 발생한다.
    xhr.onload = () => {
      // status 프로퍼티 값이 200이면 정상적으로 응답된 상태다.
      if (xhr.status === 200) {
        document.querySelector('pre').textContent = xhr.response;
      } else {
        console.error('Error', xhr.status, xhr.statusText);
      }
    };
  </script>
</body>
</html>
```

```json
{
  "id": 1,
  "content": "HTML",
  "completed": true
}
```
위의 정보가 보이게 된다.

<br>

### 44.3.5 `POST` 요청

todos 리스트에 새로운 todo 를 생성한다.
`setRequestHeader` 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 한다.

`public` 폴더에 아래 `post.html` 을 추가하고 브라우저에서 `http://localhost:3000/post.html` 로 접속한다.

~44-05~ : `post.html`

```html
<!DOCTYPE html>
<html>
<body>
  <pre></pre>
  <script>
    // XMLHttpRequest 객체 생성
    const xhr = new XMLHttpRequest();

    // HTTP 요청 초기화
    // todos 리소스에 새로운 todo를 생성
    xhr.open('POST', '/todos');

    // 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정
    xhr.setRequestHeader('content-type', 'application/json');

    // HTTP 요청 전송
    // 새로운 todo를 생성하기 위해 페이로드를 서버에 전송해야 한다.
    xhr.send(JSON.stringify({ id: 4, content: 'Angular', completed: false }));

    // load 이벤트는 요청이 성공적으로 완료된 경우 발생한다.
    xhr.onload = () => {
      // status 프로퍼티 값이 200(OK) 또는 201(Created)이면 정상적으로 응답된 상태다.
      if (xhr.status === 200 || xhr.status === 201) {
        document.querySelector('pre').textContent = xhr.response;
      } else {
        console.error('Error', xhr.status, xhr.statusText);
      }
    };
  </script>
</body>
</html>
```

```json
{
  "id": 4,
  "content": "Angular",
  "completed": false
}
```
위와 같이 정보가 들어가게 된다.

<br>

### 44.3.6 `PUT` 요청

PUT 은 특정 리소스 전체를 교체할 때 사용한다. todos 리소스에서 id 로 todo 를 특정하여 id 를 제외한 리소스 전체를 교체한다. PUT 요청시에는 `setRequestHeader` 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 한다.

`public` 폴더에 아래 `put.html` 을 추가하고 브라우저에서 `http://localhost:3000/put.html`로 접속한다.

~44-06~

```html
<!DOCTYPE html>
<html>
<body>
  <pre></pre>
  <script>
    // XMLHttpRequest 객체 생성
    const xhr = new XMLHttpRequest();

    // HTTP 요청 초기화
    // todos 리소스에서 id로 todo를 특정하여 id를 제외한 리소스 전체를 교체
    xhr.open('PUT', '/todos/4');

    // 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정
    xhr.setRequestHeader('content-type', 'application/json');

    // HTTP 요청 전송
    // 리소스 전체를 교체하기 위해 페이로드를 서버에 전송해야 한다.
    xhr.send(JSON.stringify({ id: 4, content: 'React', completed: true }));

    // load 이벤트는 요청이 성공적으로 완료된 경우 발생한다.
    xhr.onload = () => {
      // status 프로퍼티 값이 200이면 정상적으로 응답된 상태다.
      if (xhr.status === 200) {
        document.querySelector('pre').textContent = xhr.response;
      } else {
        console.error('Error', xhr.status, xhr.statusText);
      }
    };
  </script>
</body>
</html>
```

```json
{
  "id": 4,
  "content": "React",
  "completed": true
}
```
위와 같은 정보를 확인할 수 있다.

<br>

### 44.3.7 `PATCH` 요청

PATCH 는 특정 리소스의 일부를 수정할 때 사용한다. 다음 예제에서는 todos 리소스의 id로 todo 를 특정하여 completed 만 수정한다. PATCH 요청 시에는 `setRequestHeader` 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 한다.

`public` 폴더에 아래 `patch.html`을 추가하고 브라우저에서 `http://localhost:3000/patch.html`로 접속한다.

~44-07~

```html
<!DOCTYPE html>
<html>
<body>
  <pre></pre>
  <script>
    // XMLHttpRequest 객체 생성
    const xhr = new XMLHttpRequest();

    // HTTP 요청 초기화
    // todos 리소스의 id로 todo를 특정하여 completed만 수정
    xhr.open('PATCH', '/todos/4');

    // 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정
    xhr.setRequestHeader('content-type', 'application/json');

    // HTTP 요청 전송
    // 리소스를 수정하기 위해 페이로드를 서버에 전송해야 한다.
    xhr.send(JSON.stringify({ completed: false }));

    // load 이벤트는 요청이 성공적으로 완료된 경우 발생한다.
    xhr.onload = () => {
      // status 프로퍼티 값이 200이면 정상적으로 응답된 상태다.
      if (xhr.status === 200) {
        document.querySelector('pre').textContent = xhr.response;
      } else {
        console.error('Error', xhr.status, xhr.statusText);
      }
    };
  </script>
</body>
</html>
```

```json
{
  "id": 4,
  "content": "React",
  "completed": false
}
```

위와 같이 `completed` 의 정보가 `false` 로 바뀐 것을 확인할 수 있다.

<br>

### 44.3.8 `DELETE` 요청

todos 리소스에서 id 를 사용하여 todo 를 삭제한다.
`public` 폴더에 다음 `delete.html`을 추가하고 브라우저에서 `http://localhost:3000/delete.html`로 접속한다.

~44-08~

```html
<!DOCTYPE html>
<html>
<body>
  <pre></pre>
  <script>
    // XMLHttpRequest 객체 생성
    const xhr = new XMLHttpRequest();

    // HTTP 요청 초기화
    // todos 리소스에서 id를 사용하여 todo를 삭제한다.
    xhr.open('DELETE', '/todos/4');

    // HTTP 요청 전송
    xhr.send();

    // load 이벤트는 요청이 성공적으로 완료된 경우 발생한다.
    xhr.onload = () => {
      // status 프로퍼티 값이 200이면 정상적으로 응답된 상태다.
      if (xhr.status === 200) {
        document.querySelector('pre').textContent = xhr.response;
      } else {
        console.error('Error', xhr.status, xhr.statusText);
      }
    };
  </script>
</body>
</html>
```

```
{}
```
위와 같이 정보가 삭제되었다.
