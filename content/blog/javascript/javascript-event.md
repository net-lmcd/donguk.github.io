---
title: Javascript Event
date: 2020-05-20 09:05:13
category: javascript
---

### 1. 이벤트 핸들러를 등록하는 방법

1. 인라인 이벤트 핸들러 방식

```html
<button onclick="handler()">click</button>
...
<script>
	function handler() { alert('clicked!') }
</script>
```

이때 함수가 즉시 호출되는 것이 아니다. `button`  요소의 `onclick` 프로퍼티에 `function onclick(event) { handler() }` 가 할당되는 구조이다. 따라서 여러개의 핸들러 함수가 등록될 수 있다.

여기서 `this` 는 이벤트 핸들러 함수가 일반 함수로서 호출되므로 `window` 를 가리킨다.

```html
<button onclick="handler1(); handler2();">click</button>
...
<script>
	function handler1() { alert('clicked!') }
  function handler2() { alert('clicked!') }
</script>
```

2. 이벤트 핸들러 프로퍼티 방식

이 방식은 하나의 요소에 **하나의 이벤트 핸들러** 만 등록 가능하다.

```html
<button class="btn">click</button>
...
<script>
	const btn = document.querySelector('.btn')
  btn.onclick = function() {
    alert('clicked')
  }
</script>
```

여기서 이벤트 핸들러는 메소드이므로 이벤트 핸들러 내부의 `this` 는 이벤트 핸들러가 바인딩된 요소를 가리킨다. ( 이벤트 객체의 `currentTarget` 프로퍼티와 같음 )

3. **addEventListener** 메소드 방식

`addEventListener` 메소드를 이용하여 DOM 요소에 이벤트를 바인딩하고, 해당 이벤트가 발생했을 때 실행될 콜백 함수를 지정한다.

- 하나 이상의 이벤트 핸들러를 추가할 수 있다.
- 캡처링과 버블링을 지원한다.
- HTML 요소뿐만아니라 모든 DOM요소 ( HTML, XML, SVG )에 대해 동작한다.

_addEventListenr 메소드는 IE9 이상에서 동작한다. 그 이하에서는 attachEvent 메소드를 사용한다._

```html
<labael>User name <input type="text"/></labael>
...
<script>
	const $input = document.querySelector('input[type=text]')
  $input.addEventListener('blur', function() {
    alert('blur event occured')
  })
</script>
```

이벤트 핸들러가 콜백 함수이지만 `this` 는 이벤트 리스너에 바인딩된 요소를 가리킨다. ( 이벤트 객체의 `currentTarget` 프로퍼티와 같음 )

**단, 화살표 함수가 이벤트 핸들러로 사용되면 항상 현재 스코프의 상위 컨텍스트를 가리킨다.**

<br/>

### 2. 이벤트 캡처링과 버블링

계층적 구조를 가진 HTML 요소에 이벤트가 발생할 경우 연쇄적 반응이 일어난다. 즉 이벤트가 **전파( Event Propagation)** 되는데 방향에 따라 버블링과 캡처링으로 구분된다.

자식요소에서 발생한 이벤트가 부모 요소부터 시작하여 이벤트를 발생시킨 자식요소 까지 도달하는 것을 **캡처링** 이라하고, 자식요소에서 발생한 이벤트가 부모 요소로 전파되는 것을 버블링이라고 한다.

**버블링과 캡처링은 둘 중에 하나만 발생하는 것이 아니라 캡처링부터 시작하여 버블링으로 종료한다.** 즉, 이벤트가 발생했을 때 캡처링 -> 버블링으로 순차적으로 발생한다.

`addEventListener` 메소드의 세번째 인자에 `true` 로 설정하면 캡처링으로 전파되는 이벤트를 캐치하고, `false` 또는 미설정하면 버블링으로 전파되는 이벤트를 캐치한다.

```html
<body>
  <p>버블버블 <button>캡처캡처</button></p>
</body>
<script>
  const body = document.querySelector('body')
  const p = document.querySelector('p')
  const button = document.querySelector('button')
	body.addEventListener('click', () => console.log('body'))
  p.addEventListener('click', () => console.log('p'), true)
	button.addEventListener('click', () => console.log('button'))
</script>
```

위에서 만약 button을 클릭한다면,  p태그는 캡처링으로 전파되는 이벤트만 캐치하기 때문에

```javascript
p
button
body
```

와 같은 순으로 콘솔이 찍히게 된다.

<br/>

### 3. Event 객체

- 이벤트가 발생하면 `event` 객체는 동적으로 생성된다.

- `event` 객체는 이벤트 핸들러에 암묵적으로 전달되고, 이벤트 핸들러는 이를 전달받을 첫번째 매개변수를 명시적으로 선언해줘야 한다. 
- `React` 에서는 Native 이벤트가 아닌 React Event 객체를 사용한다.

#### Event.target ( Property )

**실제로 이벤트를 발생시킨 요소**를 가리킨다. 

```html
<button class="btn-class">click</button>
...
<script>
  function handler(e) {
    console.log(e.target.className) // btn-class
  }
	document.querySelector('button').addEventListener('clcick', handler)
</script>
```

#### Event.currentTarget ( Property )

이벤트에 바인딩된 DOM 요소를 가리킨다. 즉 `addEventListener` 앞에 기술된 객체를 가리킨다.

```html
<button class="btn-class">click</button>
...
<script>
  function handler(e) {
    console.log(e.currentTarget === this) // btn-class
  }
	document.querySelector('button').addEventListener('clcick', handler)
</script>
```

#### Event.type

발생한 이벤트의 종류를 나타내는 문자열을 리턴한다.

#### Event.eventPhase

이벤트 흐름 상에서 어느 단계에 있는지를 반환한다.

| 반환값 | 의미        |
| :----- | :---------- |
| 0      | 이벤트 없음 |
| 1      | 캡쳐링 단계 |
| 2      | 타깃        |
| 3      | 버블링 단계 |

<br/>

### 4. Event Delegation ( 이벤트 위임 )

다수의 자식요소에 각각 이벤트 핸들러를 바인딩하는 대신 하나의 부모 요소에 이벤트 핸들러를 바인딩하는 방법이다. 이는 이벤트 버블링을 이용한 기법이며 실제로 이벤트를 발생시킨 요소를 알아내기 위해서 `event.target` 을 이용한다.

> 추가적으로, `React`에서는 모든 이벤트를 `document` 객체에 등록하고 Event Delegation 기법을 이용하여 처리한다. 

<br/>

### 5. 기본 동작의 변경

#### e.preventDefault()

폼을 `submit`  하거나 링크를 클릭하면 다른 페이지로 이동하게 된다. 이와 같이 **요소가 가지고 있는 기본 동작을 중단시키기 위한 메소드가 `preventDefault()` 이다.

#### e.stopPropagation()

어느 한 요소를 이용하여 이벤트를 처리한 후 이벤트가 부모 요소로 전파되는 것(버블링)을 중단시키기 위한 메소드이다. 부모 요소에 동일한 이벤트에 대한 다른 이벤트 핸들러가 지정된 경우 사용한다.

<br/>

### 6. Custom Event

- DOM 트리가 충분히 복잡해진 상황에서 트리 구조를 건너뛰어 이벤트를 전달해야 하는 상황이 생길 때 유용하게 사용한다.
- `element`는 아직 생성되지 않은 이벤트를 리슨할 수 있다.

```html
<form>
  <textarea></textarea>
</form>
...
<script>
	const form = document.querySelector('form')
  const textarea = document.querySelector('textarea')
  form.addEventListener('customExample', (e) => {
    console.log(e.data)
  })
  const event = new CustomEvent('customExample', {
    bubbles: true,
    data: textarea.value
  })
  textarea.addEventListener('input', (e) => {
    e.target.dispatchEvent(event)
  })
</script>
```

<br/>

### 7. Reference

1. https://developer.mozilla.org/ko/docs/Web/Guide/Events/Creating_and_triggering_events
2. https://poiemaweb.com/js-event
3. https://d2.naver.com/helloworld/9297403
