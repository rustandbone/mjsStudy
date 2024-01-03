- [39장 이벤트](#39장-이벤트)
- [39.6 DOM 조작](#396-dom-조작)
  - [39.6.1 innerHTML](#3961-innerhtml)
    - [39-41](#39-41)
    - [39-42](#39-42)
    - [39-43](#39-43)
    - [39-44](#39-44)
    - [39-45](#39-45)
    - [39-46](#39-46)
    - [39-47](#39-47)
    - [39-48](#39-48)
    - [39-49](#39-49)
  - [39.6.2 insertAdjacentHTML 메서드](#3962-insertadjacenthtml-메서드)
    - [39-50](#39-50)
  - [39.6.3 노드 생성과 추가](#3963-노드-생성과-추가)
    - [39-51](#39-51)
    - [요소 노드 생성](#요소-노드-생성)
    - [39-52](#39-52)
    - [39-53](#39-53)
    - [텍스트 노드 생성](#텍스트-노드-생성)
    - [39-54](#39-54)
    - [텍스트 노드를 요소 노드의 자식 노드로 추가](#텍스트-노드를-요소-노드의-자식-노드로-추가)
    - [39-55](#39-55)
    - [39-56](#39-56)
    - [요소 노드를 DOM에 추가](#요소-노드를-dom에-추가)
    - [39-57](#39-57)
  - [39.6.4 복수의 노드 생성과 추가](#3964-복수의-노드-생성과-추가)
    - [39-58](#39-58)
    - [39-59](#39-59)
    - [39-60](#39-60)
    - [39-61](#39-61)
  - [39.6.5 노드 삽입](#3965-노드-삽입)
    - [마지막 노드로 추가](#마지막-노드로-추가)
    - [39-62](#39-62)
    - [지정한 위치에 노드 삽입](#지정한-위치에-노드-삽입)
    - [39-63](#39-63)
    - [39-64](#39-64)
    - [39-65](#39-65)
  - [39.6.6 노드 이동](#3966-노드-이동)
    - [39-66](#39-66)
  - [39.6.7 노드 복사](#3967-노드-복사)
    - [39-67](#39-67)
  - [39.6.8 노드 교체](#3968-노드-교체)
    - [39-68](#39-68)
  - [39.6.9 노드 삭제](#3969-노드-삭제)
    - [39-69](#39-69)

# 39장 이벤트

# 39.6 DOM 조작

DOM 조작은 새로운 노드를 생성하여 DOM에 추가하거나 기존 노드를 삭제 또는 교체하는 것.  
DOM 조작에 의해 DOM에 새로운 노드가 추가되거나 삭제되면 리플로우와 리페인트가 발생하는 원인이 되므로 성능에 영향을 줌. 복잡한 콘텐츠를 다루는 DOM 조작은 성능 최적화를 위해 주의해서 다루어야 함.

## 39.6.1 innerHTML

Element.prototype.innerHTML 프로퍼티는 setter, getter 모두 존재하는 접근자 프로퍼티로서 요소 노드의 HTML 마크업을 취득하거나 변경함. 요소 노드의 innerHTML 프로퍼티를 참조하면 요소 노드의 콘텐츠 영역 내에 포함된 모든 HTML 마크업을 문자열로 반환함

### 39-41

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
    // #foo 요소의 콘텐츠 영역 내의 HTML 마크업을 문자열로 취득한다.
    console.log(document.getElementById('foo').innerHTML);
    // "Hello <span>world!</span>"
  </script>
</html>
```

textContent 프로퍼티는 HTML 마크업 무시하고 텍스트만 반환  
innerHTML 프로퍼티는 HTML 마크업이 포함된 문자열 그대로 반환

### 39-42

요소 노드의 innerHTML 프로퍼티에 문자열을 할당하면 요소 노드의 모든 자식 노드가 제거되고  
할당한 문자열에 포함되어 있는 HTML 마크업이 파싱되어 요소 노드의 자식 노드로 DOM에 반영됨

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello <span>world!</span></div>
  </body>
  <script>
    // HTML 마크업이 파싱되어 요소 노드의 자식 노드로 DOM에 반영된다.
    document.getElementById('foo').innerHTML = 'Hi <span>there!</span>';
  </script>
</html>
```

### 39-43

요소 노드의 innerHTML 프로퍼티에 할당한 HTML 마크업 문자열은 렌더링 엔진에 의해 파싱되어  
요소 노드의 자식으로 DOM에 반영됨

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li class="apple">Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 노드 추가
    $fruits.innerHTML += '<li class="banana">Banana</li>';

    // 노드 교체
    $fruits.innerHTML = '<li class="orange">Orange</li>';

    // 노드 삭제
    $fruits.innerHTML = '';
  </script>
</html>
```

사용자로부터 입력받은 데이터(untrusted input data)를 그대로 innerHTML 프로퍼티에 할당하는 것은 `크로스 사이트 스크립팅 공격`에 취약하므로 위험함. 마크업 내에 악성 코드가 있으면 파싱 과정에서 그대로 실행되기 때문.

### 39-44

스크립트 태그 삽입하여 자바스크립트가 실행되도록 하는 예제

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // innerHTML 프로퍼티로 스크립트 태그를 삽입하여 자바스크립트가 실행되도록 한다.
    // HTML5는 innerHTML 프로퍼티로 삽입된 script 요소 내의 자바스크립트 코드를 실행하지 않는다.
    document.getElementById('foo').innerHTML
      = '<script>alert(document.cookie)</script>';
  </script>
</html>
```

=> HTML5는 innerHTML 프로퍼티로 삽입된 script 요소 내의 자바스크립트 코드를 실행하지 않음. HTML5를 지원하는 브라우저에서 동작하지 않음.

하지만 script 요소 없이도 크로스 사이트 스크립팅 공격은 가능

### 39-45

간단한 크로스 사이트 스크립팅 공격

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="foo">Hello</div>
  </body>
  <script>
    // 에러 이벤트를 강제로 발생시켜서 자바스크립트 코드가 실행되도록 한다.
    document.getElementById(
      'foo'
    ).innerHTML = `<img src="x" onerror="alert(document.cookie)">`;
  </script>
</html>
```

innerHTML 프로퍼티를 사용한 DOM 조작은 구현이 간단하고 직관적이지만  
크로스 사이트 스크립팅 공격에 취약하다는 단점이 있음

**HTML 새니티제이션**
크로스 사이트 스크립팅 공격을 예방하기 위해 잠재적 위험을 제거하는 기능. DOMPurify 라이브러리 사용 권장

```html
DOMPurify.sanitize('<img src="x" onerror="alert(document.cookie)" />');
```

=> `<img src="x">`

또 다른 단점 : 요소 노드의 innerHTML 프로퍼티에 HTML 마크업 문자열을 할당하면 `요소 노드의 모든 자식 노드를 제거하고` 할당한 HTML 마크업 문자열을 파싱하여 DOM을 변경

### 39-46

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li class="apple">Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 노드 추가
    $fruits.innerHTML += '<li class="banana">Banana</li>';
  </script>
</html>
```

=> li.banana 요소 노드만 생성하여 자식 요소로 추가할 것 같지만  
#fruits 요소의 모든 자식 노드(li.apple)를 제거하고  
새롭게 요소 노드 li.apple, li.banana를 생성하여 자식 요소로 추가함

### 39-47

39-48 코드의 축약 표현

```javascript
$fruits.innerHTML += '<li class="banana">Banana</li>';
```

### 39-48

```javascript
$fruits.innerHTML = $fruits.innerHTML + '<li class="banana">Banana</li>';
// '<li class="apple">Apple</li>' + '<li class="banana">Banana</li>'
```

비효율적 : 기존의 자식 노드까지 모두 제거하고 다시 처음부터 새롭게 자식 노드를 생성해 DOM에 반영

### 39-49

또 다른 단점 : 새로운 요소를 삽입할 때 삽입될 위치를 지정할 수 없음
li.apple과 li.orange 요소 사이에 새로운 요소를 삽입하고 싶은 경우 innerHTML 프로퍼티를 사용하면 삽입 위치를 지정할 수 없음.

```html
<ul id="fruits">
  <li class="apple">Apple</li>
  <li class="orange">Orange</li>
</ul>
```

=> innerHTML 프로퍼티는 복잡하지 않은 요소를 새롭게 추가할 때는 유용하지만  
기존 요소를 제거하지 않으면서 위치를 지정해 새로운 요소를 삽입해야 할 때는 사용하지 않는 것이 좋음

## 39.6.2 insertAdjacentHTML 메서드

`Element.prototype.insertAdjacentHTML(position, DOMString)` 메서드는 기존 요소 제거하지 않으면서 위치를 지정해 새로운 요소 삽입

`두 번째 인수`로 전달한 HTML 마크업 문자열(DOMString)을 파싱하고  
그 결과로 생성된 노드를 `첫 번째 인수`로 전달한 위치(position)에 삽입하여 DOM에 반영

- 첫 번쨰 인수로 전달 가능한 문자열
  - beforebegin
  - afterbegin
  - beforeend
  - afterend

### 39-50

```html
<!DOCTYPE html>
<html>
  <body>
    <!-- beforebegin -->
    <div id="foo">
      <!-- afterbegin -->
      text
      <!-- beforeend -->
    </div>
    <!-- afterend -->
  </body>
  <script>
    const $foo = document.getElementById('foo');

    $foo.insertAdjacentHTML('beforebegin', '<p>beforebegin</p>');
    $foo.insertAdjacentHTML('afterbegin', '<p>afterbegin</p>');
    $foo.insertAdjacentHTML('beforeend', '<p>beforeend</p>');
    $foo.insertAdjacentHTML('afterend', '<p>afterend</p>');
  </script>
</html>
```

이 메서드는 기존 요소에는 영향을 주지 않고, 새롭게 삽입될 요소만을 파싱하여 자식 요소로 추가하므로  
innerHTML 프로퍼티보다 효율적이고 빠름

하지만, 마찬가지로 HTML 마크업 문자열을 파싱하므로 크로스 사이트 스크립팅 공격에 취약한 것은 동일

## 39.6.3 노드 생성과 추가

innerHTML 프로퍼티, insertAdjacentHTML 메서드는 HTML 마크업 문자열을 파싱하여 노드 생성하고 DOM에 반영

DOM은 `노드를 직접 생성/삽입/삭제/치환하는 메서드`도 제공

### 39-51

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 1. 요소 노드 생성
    const $li = document.createElement('li');

    // 2. 텍스트 노드 생성
    const textNode = document.createTextNode('Banana');

    // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
    $li.appendChild(textNode);

    // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    $fruits.appendChild($li);
  </script>
</html>
```

=> 새로운 요소 노드 생성하고, 텍스트 노드 생성하여, 요소 노드의 자식 노드로 추가하고, 요소 노드를 DOM에 추가

### 요소 노드 생성

`Document.prototype.createElement(tagName)` 메서드는 요소 노드를 생성하여 반환  
tagName에는 태그 이름을 나타내는 문자열을 인수로 전달

### 39-52

```javascript
// 1. 요소 노드 생성
const $li = document.createElement('li');
```

=> 기존 DOM에 추가하지 않고 홀로 존재하는 상태. 요소 노드를 생성할 뿐, DOM에 추가하지는 않음  
생성된 요소 노드를 DOM에 추가하는 `별도 처리 필요`

### 39-53

```javascript
// 1. 요소 노드 생성
const $li = document.createElement('li');
// 생성된 요소 노드는 아무런 자식 노드가 없다.
console.log($li.childNodes); // NodeList []
```

### 텍스트 노드 생성

`Element.prototype.createTextNode(text)` 메서드는 텍스트 노드를 생성해 반환  
매개변수 text에는 텍스트 노드의 값으로 사용할 문자열을 인수로 전달

### 39-54

```javascript
// 2. 텍스트 노드 생성
const textNode = document.createTextNode('Banana');
```

텍스트 노드는 요소 노드의 자식 노드. createTextNode 메서드로 생성한 텍스트 노드는 홀로 존재하는 상태이기에 요소 노드에 추가하는 처리가 `별도로 필요`.

### 텍스트 노드를 요소 노드의 자식 노드로 추가

`Node.prototype.appendChild(childNode)` 메서드는 매개변수 childNode에게 인수로 전달한 노드를 이 메서드를 호출한 노드의 마지막 자식 노드로 추가

appendChild 메서드 인수로 createTextNode 메서드로 생성한 텍스트 노드를 전달하면 appendChild 메서드를 호출한 노드의 마지막 자식 노드로 텍스트 노드 추가됨

### 39-55

```javascript
// 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
$li.appendChild(textNode);
```

=> 요소 노드와 텍스트 노드가 부자 관계로 연결됨. 기존 DOM에 추가되지는 않은 상태  
`요소 노드에 자식 노드가 하나도 없는 경우`, 텍스트 노드를 생성해 요소 노드의 자식 노드로 텍스트 노드를 추가하는 것보다 textContent 프로퍼티를 사용하는 편이 더욱 간편

### 39-56

```javascript
// 텍스트 노드를 생성하여 요소 노드의 자식 노드로 추가
$li.appendChild(document.createTextNode('Banana'));

// $li 요소 노드에 자식 노드가 하나도 없는 위 코드와 동일하게 동작한다.
$li.textContent = 'Banana';
```

=> 요소 노드에 자식 노드가 있는 경우, 요소 노드의 textContent 프로퍼티에 문자열을 할당하면 `요소 노드의 모든 자식 노드가 제거되고` 할당한 문자열이 텍스트로 추가되므로 주의해야 함

### 요소 노드를 DOM에 추가

`Node.prototype.appendChild` 메서드를 사용해 텍스트 노드와 부자 관계로 연결한 요소 노드를 #fruits 요소 노드의 마지막 자식 요소로 추가

### 39-57

```javascript
// 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
$fruits.appendChild($li);
```

=> 이 과정에서 비로소 새롭게 생성한 요소 노드가 DOM에 추가됨. 기존의 DOM에 요소 노드를 추가하는 처리는 이 과정뿐.  
DOM은 한 번 변경되고, 이때 리플로우, 리페인트가 실행됨

## 39.6.4 복수의 노드 생성과 추가

### 39-58

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits"></ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    ['Apple', 'Banana', 'Orange'].forEach((text) => {
      // 1. 요소 노드 생성
      const $li = document.createElement('li');

      // 2. 텍스트 노드 생성
      const textNode = document.createTextNode(text);

      // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
      $li.appendChild(textNode);

      // 4. $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
      $fruits.appendChild($li);
    });
  </script>
</html>
```

=> 3개의 요소 노드 생성해 DOM에 3번 추가, DOM이 3번 변경되는 것(리플로우, 리페인트 3번 실행) => 비효율적  
DOM을 변경하는 것은 높은 비용이 드는 처리이므로, 가급적 횟수를 줄이는 편이 성능에 유리

### 39-59

컨테이너 요소 사용.  
컨테이너 요소 미리 생성하고, DOM에 추가할 3개의 요소 노드를 컨테이너 요소에 자식 노드로 추가하고, 컨테이너 요소를 #fruits 요소에 자식으로 추가하면 DOM은 한 번만 변경됨

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits"></ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 컨테이너 요소 노드 생성
    const $container = document.createElement('div');

    ['Apple', 'Banana', 'Orange'].forEach((text) => {
      // 1. 요소 노드 생성
      const $li = document.createElement('li');

      // 2. 텍스트 노드 생성
      const textNode = document.createTextNode(text);

      // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
      $li.appendChild(textNode);

      // 4. $li 요소 노드를 컨테이너 요소의 마지막 자식 노드로 추가
      $container.appendChild($li);
    });

    // 5. 컨테이너 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    $fruits.appendChild($container);
  </script>
</html>
```

=> DOM을 한 번만 변경하므로 성능에 유리.

### 39-60

하지만 불필요한 컨테이너 요소(div)가 DOM에 추가되는 부작용이 있음 => 바람직하지 않음

```html
<ul id="fruits">
  <div>
    <li>apple</li>
    <li>banana</li>
    <li>orange</li>
  </div>
</ul>
```

DocumentFragment 노드를 통해 해결. DocumentFragment 노드는 문서, 요소, 어트리뷰트, 텍스트 노드와 같은 노드 객체의 일종. 부모 노드가 없어서 기존 DOM과는 별도로 존재.

컨테이너 요소와 같이 자식 노드들의 부모 노드로서 별도의 서브 DOM을 구성해 기존 DOM에 추가하기 위한 용도로 사용.

DocumentFragment 노드는 기존 DOM과는 별도로 존재. DocumentFragment 노드에 자식 노드를 추가하여도 기존 DOM에는 어떠한 변경도 발생하지 않음.  
DocumentFragment 노드를 DOM에 추가하면 자신은 제거되고 자신의 자식 노드만 DOM에 추가됨

### 39-61

`Document.prototype.createDocumentFragment` 메서드는 비어 있는 DocumentFragment 노드를 생성하여 반환

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits"></ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // DocumentFragment 노드 생성
    const $fragment = document.createDocumentFragment();

    ['Apple', 'Banana', 'Orange'].forEach((text) => {
      // 1. 요소 노드 생성
      const $li = document.createElement('li');

      // 2. 텍스트 노드 생성
      const textNode = document.createTextNode(text);

      // 3. 텍스트 노드를 $li 요소 노드의 자식 노드로 추가
      $li.appendChild(textNode);

      // 4. $li 요소 노드를 DocumentFragment 노드의 마지막 자식 노드로 추가
      $fragment.appendChild($li);
    });

    // 5. DocumentFragment 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    $fruits.appendChild($fragment);
  </script>
</html>
```

- DocumentFragment 노드를 생성하고
- DOM에 추가할 요소 노드를 생성하여
- DocumentFragment 노드에 자식 노드로 추가하고
- DocumentFragment 노드를 기존 DOM에 추가

실제로 DOM 변경이 발생하는 것은 한 번뿐(리플로우, 리페인트도 한 번 실행). 여러 개의 요소 노드를 DOM에 추가하는 경우, DocumentFragment 노드를 사용하는 것이 더 효율적

## 39.6.5 노드 삽입

### 마지막 노드로 추가

`Node.prototype.appendChild` 메서드는 인수로 전달받은 노드를 자신을 호출한 노드의 마지막 자식 노드로 DOM에 추가.  
노드를 추가할 위치 지정할 수 없음. 언제나 마지막 자식 노드로 추가.

### 39-62

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    // 요소 노드 생성
    const $li = document.createElement('li');

    // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
    $li.appendChild(document.createTextNode('Orange'));

    // $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    document.getElementById('fruits').appendChild($li);
  </script>
</html>
```

### 지정한 위치에 노드 삽입

`Node.prototype.insertBefore(newNode, childNode)` 메서드는 첫 번째 인수로 전달받은 노드를 두 번째 인수로 전달받은 노드 앞에 삽입.

### 39-63

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 요소 노드 생성
    const $li = document.createElement('li');

    // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
    $li.appendChild(document.createTextNode('Orange'));

    // $li 요소 노드를 #fruits 요소 노드의 마지막 자식 요소 앞에 삽입
    $fruits.insertBefore($li, $fruits.lastElementChild);
    // Apple - Orange - Banana
  </script>
</html>
```

### 39-64

두 번째 인수로 전달받은 노드는 반드시 insertBefore 메서드를 호출한 노드의 자식 노드이어야 한다. 그렇지 않으면 DOMException 에러 발생.

```html
<!DOCTYPE html>
<html>
  <body>
    <div>test</div>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 요소 노드 생성
    const $li = document.createElement('li');

    // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
    $li.appendChild(document.createTextNode('Orange'));

    // 두 번째 인수로 전달받은 노드는 반드시 #fruits 요소 노드의 자식 노드이어야 한다.
    $fruits.insertBefore($li, document.querySelector('div'));
    // DOMException
  </script>
</html>
```

### 39-65

두 번째 인수로 전달받은 노드가 null이면 첫 번째 인수로 전달받은 노드를 insertBefore 메서드를 호출한 노드의 마지막 자식 노드로 추가. appendChild 메서드처럼 동작

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 요소 노드 생성
    const $li = document.createElement('li');

    // 텍스트 노드를 $li 요소 노드의 마지막 자식 노드로 추가
    $li.appendChild(document.createTextNode('Orange'));

    // 두 번째 인수로 전달받은 노드가 null이면 $li 요소 노드를 #fruits 요소 노드의 마지막 자식 노드로 추가
    $fruits.insertBefore($li, null);
  </script>
</html>
```

## 39.6.6 노드 이동

DOM에 이미 존재하는 노드를 appendChild 또는 insertBefore 메서드를 사용하여 DOM에 추가하면 현재 위치에서 노드를 제거하고 새로운 위치에 노드를 추가. `노드가 이동한다.`

### 39-66

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
      <li>Orange</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 이미 존재하는 요소 노드를 취득
    const [$apple, $banana] = $fruits.children;

    // 이미 존재하는 $apple 요소 노드를 #fruits 요소 노드의 마지막 노드로 이동
    $fruits.appendChild($apple); // Banana - Orange - Apple

    // 이미 존재하는 $banana 요소 노드를 #fruits 요소의 마지막 자식 노드 앞으로 이동
    $fruits.insertBefore($banana, $fruits.lastElementChild);
    // Orange - Banana - Apple
  </script>
</html>
```

## 39.6.7 노드 복사

`Node.prototype.cloneNode([deep: true | false])` 메서드는 노드의 사본을 생성해 반환.

매개변수 deep에 true를 인수로 전달하면 노드를 깊은 복사(deep clone)하여 모든 자손 노드가 포함된 사본을 생성  
false를 인수로 전달하거나 생략하면 얕은 복사(shallow copy)하여 노드 자신만의 사본 생성. 얕은 복사로 생성된 요소 노드는 자손 노드를 복사하지 않으므로 텍스트 노드도 없음

### 39-67

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');
    const $apple = $fruits.firstElementChild;

    // $apple 요소를 얕은 복사하여 사본을 생성. 텍스트 노드가 없는 사본이 생성된다.
    const $shallowClone = $apple.cloneNode();
    // 사본 요소 노드에 텍스트 추가
    $shallowClone.textContent = 'Banana';
    // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
    $fruits.appendChild($shallowClone);

    // #fruits 요소를 깊은 복사하여 모든 자손 노드가 포함된 사본을 생성
    const $deepClone = $fruits.cloneNode(true);
    // 사본 요소 노드를 #fruits 요소 노드의 마지막 노드로 추가
    $fruits.appendChild($deepClone);
  </script>
</html>
```

## 39.6.8 노드 교체

`Node.prototype.replaceChild(newChild, oldChild)` 메서드는 자신을 호출한 노드의 자식 노드를 다른 노드로 교체함

첫 번째 매개변수 newChild에는 교체할 새로운 노드를 인수로 전달,  
두 번째 매개변수 oldChild에는 이미 존재하는 교체될 노드를 인수로 전달

oldChild 매개변수에 인수로 전달한 노드는, replaceChild 메서드를 호출한 노드의 자식 노드이어야 함

즉, replaceChild 메서드는 자신을 호출한 노드의 자식 노드인 oldChild 노드를 newChild 노드로 교체.  
이때 oldChild 노드는 DOM에서 제거됨

### 39-68

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // 기존 노드와 교체할 요소 노드를 생성
    const $newChild = document.createElement('li');
    $newChild.textContent = 'Banana';

    // #fruits 요소 노드의 첫 번째 자식 요소 노드를 $newChild 요소 노드로 교체
    $fruits.replaceChild($newChild, $fruits.firstElementChild);
  </script>
</html>
```

## 39.6.9 노드 삭제

`Node.prototype.removeChild(child)` 메서드는 child 매개변수에 인수로 전달한 노드를 DOM에서 삭제.

인수로 전달한 노드는 removeChild 메서드를 호출한 노드의 자식 노드이어야 함.

### 39-69

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>
  </body>
  <script>
    const $fruits = document.getElementById('fruits');

    // #fruits 요소 노드의 마지막 요소를 DOM에서 삭제
    $fruits.removeChild($fruits.lastElementChild);
  </script>
</html>
```
