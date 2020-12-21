---
title: flex layout & item
date: 2020-12-21 22:12:73
category: css
---

### flex 아이템에 적용하는 속성

**1. flex-basis**

- flex 아이템의 **기본** 크기를 설정한다.
- `flex-direction` 이 row일 때는 width, column인 경우는 높이를 설정
- 단위로는 css width, height 속성에 사용하는 각종 단위의 수가 들어갈 수 있다.
- `default` 값은 auto 이며 width 속성 값을 사용한다. 만약 width를 따로 설정하지 않으면 content의 크기를 따라간다.
- `content` 로 값을 부여하면, 컨텐츠의 크기를 따라간다. 이는 width를 따로 설정하지 않은 경우와 같다.

**2. flex-grow**

- `flex-basis`보다 커질 수 있는지를 결정하는 속성
- 0보다 크면 해당 아이템은 유연한 flexible 박스가 된다.
- `default` 값이 0이기 대문에, 따로 적용하기 전까지는 아이템이 늘어나지 않음.
- `flex-grow` 에 들어간 숫자의 의미는, 아이템들의 `flex-basis` 를 제외한 여백 부분을 **flex-grow에 지정된 숫자의 비율로 나누어** 가진다.

![image](https://user-images.githubusercontent.com/39187116/102730813-4a34a500-4379-11eb-82a0-5ee8fb9dfafe.png)

```css
.item {
  flex-grow: 1;
}
```

각 아이템들의 여백 비율을 1:1:1로 설정

![image](https://user-images.githubusercontent.com/39187116/102730930-a697c480-4379-11eb-9bbd-b340c047028d.png)

```css
.item {
  flex-grow: 1;
}
.item:nth-child(2) {
  flex-grow: 2;
}
```

아이템들의 여백 비율을 1:2:1로 설정

**3. flex-shrink**

- `flex-grow` 와 쌍을 이루는 속성. `flex-basis` 값 보다 작아질 수 있는지를 결정한다.
- `default` 값은 1이다. 0보다 크면 `flex-basis` 보다 작아질 수 있는 flexible한 박스가 된다.
- 0으로 세팅하면 아이템의 크기가 `flex-basis`보다 작아지지 않는다. (고정폭의 칼럼을 만들때 유용)

**4. flex**

- `flex-grow`, `flex-shrink`, `flex-basis` 를 한번에 쓸 수 있는 축약형

```css
.item {
  flex: 1;
  /* flex-grow: 1; flex-shrink: 1; flex-basis: 0%; */
  flex: 1 1 auto;
  /* flex-grow: 1; flex-shrink: 1; flex-basis: auto; */
  flex: 1 500px;
  /* flex-grow: 1; flex-shrink: 1; flex-basis: 500px; */
}
```

- 주의할 점은 `flex-basis` 를 생략하게 되면 `flex-basis` 의 값은 **0%**가 된다.

<br/>

### 개별 아이템을 정렬하기

**1. align-self**

- `align-items` 의 아이템 버젼 속성이다.
- 기본값은 `auto`로서, `align-items` 속성을 상속 받는다.
- `align-self`는 `align-items`보다 우선순위가 높다.
- 기본적인 속성 값의 동작은 `align-items`와 같다.

```css
.flex-container {
  display: flex;
  height: 300px;
  align-items: flex-end;
}

.flex-item:nth-child(2) {
  align-self: flex-start;
}
```

![image](https://user-images.githubusercontent.com/39187116/102782172-3d956880-43dc-11eb-8890-979413fe1718.png)

**2. 아이템의 배치 순서 order**

- 말 그대로 아이템의 순서를 결정

```css
.flex-container {
  display: flex;
}
.flex-item:nth-child(1) {
  order: 3;
}
.flex-item:nth-child(2) {
  order: 100000;
}
.flex-item:nth-child(3) {
  order: 2;
}
```

- C -> B - > A 순서

<br/>

### 유용한 기법들

**1. 아이템 1개만 오른쪽으로 나열하기**

```css
.flex-container {
  display: flex;
}

.flex-item {
  width: 150px;
}

.flex-item:last-child {
  margin-left: auto;
}
```

![image](https://user-images.githubusercontent.com/39187116/102736249-cc2bca80-4387-11eb-96cd-9e0c73fcf3e8.png)

- 원리: `margin` 을 auto로 주면 남아있는 `margin` 을 모두 소비한다.
- Block 요소에 `margin: 0 auto` 를 줘서 중앙 정렬 하는 방식과 같은 원리이다.

**2. 고정폭 칼럼과 가변폭 칼럼을 함께 만들기**

```css
.flex-container {
  display: flex;
}

.flex-item:nth-child(1) {
  width: 150px;
  flex-shrink: 0;
}
.flex-item:nth-child(2) {
  flex-grow: 1;
}
.flex-item:nth-child(3) {
  width: 250px;
  flex-shrink: 0;
}
```

- `flex-shrink` 속성을 0으로 할당해서 브라우저 크기가 줄어들 때 아이템이 flexible에 줄어드는 것을 막았다.
- 두번째 요소에는 `flex-grow` 를 1로 줘서 브라우저 크기가 늘어날 때 아이템이 flexible하게 넓어지도록 설정.

**3. 푸터 하단에 고정시키기**

예제 마크업

```html
<div class="page">
  <header>header</header>
  <section class="content">샬라샬라</section>
  <footer>footer</footer>
</div>
```

- `flex-grow` 을 이용해서 content 길이를 채워주는게 핵심!

```css
.page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.content {
  flex-grow: 1;
}
```

- 컨테이너가 최소 높이가 있어야 content 의 `flex-grow` 에 값을 줬을 때 잘 채워진다.
- 인터넷 익스플로어 11에서는 `min-height` 에 flex 정렬 옵션을 주면 올바르게 동작하지 않는 버그가 있다. 이런 경우 `min-height` 대신 `height` 을 주고 content에 `overflow` 속성을 주자!!
