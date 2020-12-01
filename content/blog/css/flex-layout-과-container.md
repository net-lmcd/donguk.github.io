---
title: flex layout & container
date: 2020-12-02 00:12:02
category: css
---

### flex

- flex box / flexible box (유연한) 레이아웃을 위한 방식 -> 기존의 `inline` 보다 막강!
- `grid`는 `flex` 보다 조금 더 막강 ㅎㅎ
- 하지만! `flex` 만으로 가능한 레이아웃이 있음. 두개의 특징을 잘 알고 적재적소에 둘 다 활용하는게 가장 BEST
- IE는 10과 11에서 부분 지원

### flex layout 을 위한 기본적인 HTML 구조

```html
<div class="container">
  <div class="item">item1</div>
  <div class="item">item2</div>
  <div class="item">item3</div>
</div>
```

- 부모를 container
- 자식을 item
- 컨테이너는 flex의 영향을 받는 전체 공간이고, 설정된 속성에 따라 각각의 아이템들이 배치된다.
- `flex` 속성들은 컨테이너에 적용하는 속성, 아이템에 적용하는 속성 이렇게 두가지로 나뉜다.

### Container에 적용하는 속성

**1. display: flex**

![image](https://user-images.githubusercontent.com/39187116/100558650-a06e7500-32f2-11eb-8a5b-2790b55631f3.png)

- `flex` 아이템들은 가로방향으로 배치된다.
- 각 아이템들의 width는 자신이 가진 내용물의 width 만큼만 차지
- `float` 의 경우 자신의 내용물이 가진 높이 만큼의 height을 갖지만, `flex` 는 컨테이너의 높이를 따라간다 -> 컬럼들의 높이가 자동으로 다 맞춰진다.

**2. display: inline-flex**

![image](https://user-images.githubusercontent.com/39187116/100558673-b0865480-32f2-11eb-91fe-60d7b8236455.png)

- `flex` 와 비슷하게 동작하지만 컨테이너가 inline으로 동작한다.

**3. flex-direction**

**메인축**의 방향을 결정한다.

> 메인(main)축 ? `flex` 아이템들이 배치된 방향.

- 기본값은 `row`

```
A B C
```

- `column` 으로 하면 메인축의 방향이 세로로 바뀐다.

```
A
B
C
```

- `row-reverse`

```
C B A
```

- `column-reverse`

```
C
B
A
```

**4. flex-wrap**

- 컨테이너가 더 이상 아이템을 한 줄에 담을 여유공간이 없을 때 줄바꿈을 할지? 말지? 결정!
- 기본값은 `nowrap` : 줄바꿈을 하지 않는다. 넘치면 그냥 삐져 나감
- `wrap` : 줄바꿈을 한다. `inline-block` 처럼 동작한다.

```
A B
C D
```

- `wrap-reverse` : 줄바꿈을 한다. 단, 아이템을 역순으로 배치한다.

```
C D
A B
```

**5. Flex-flow**

- `flex-direction` 과 `flex-wrap` 을 한꺼번에 지정할 수 있다.
- `flex-direction` , `flex-wrap` 순서대로 써주면 된다.

```css
.flex-container {
  flex-flow: row wrap;
}
```

**6. justify-content (정렬)**

```
- justify는 메인 축을 기준으로 아이템 정렬
- align은 메인 축과 수직이 되는 수직 축을 기준으로 아이템을 정렬
```

- `flex-start` : default 옵션, 아이템을 시작점으로 정렬한다.

![image](https://user-images.githubusercontent.com/39187116/100679852-c0b23880-33b3-11eb-9efa-f71ca309709b.png)

이때, `flex-direction` 이 `column` 인 경우 위 에서 부터 정렬한다.

- `flex-end`: 아이템을 끝점으로 정렬한다.

![image](https://user-images.githubusercontent.com/39187116/100680031-1090ff80-33b4-11eb-8ba2-be032258b821.png)

이때, `flex-direction` 이 `column` 인 경우 아래에서 부터 정렬한다.

- `center` : 가운데로 정렬한다.

![image](https://user-images.githubusercontent.com/39187116/100680139-4930d900-33b4-11eb-88f3-3757ad3a1afc.png)

- `space-between` : 아이템들의 사이에 균일한 간격을 만들어 준다.

![image](https://user-images.githubusercontent.com/39187116/100680231-72ea0000-33b4-11eb-8b8e-bff46ec4d112.png)

- `space-around`: 아이템들의 둘레에 균일한 간격을 만들어 준다.

![image](https://user-images.githubusercontent.com/39187116/100680365-bf354000-33b4-11eb-881a-1310f142c0c4.png)

- `space-evenly`: 아이템들의 사이와 양 끝에 균일한 간격을 만들어 준다.

![image](https://user-images.githubusercontent.com/39187116/100680468-f60b5600-33b4-11eb-96cc-d0b20433c0b1.png)

단, 이 속성은 **IE와 Edge에서는 지원하지 않는다!**

**6. align-items (정렬)**

메인축과 수직이 되는 수직축을 기준으로 정렬한다!

- `strecth` : default 옵션, 아이템들을 수직축 방향으로 쫘~악 늘린다. 이 옵션으로 인해, 아이템들이 컨테이너의 높이에 따라서 쫙 채워지는 것
- `flex-start` : 아이템을 수직축의 시작점으로 정렬한다.
- `flex-end`: 아이템을 수직축의 끝점으로 정렬한다.
- `center`: 아이템을 수직축의 중앙으로 정렬한다.
- `baseline`: 아이템을 텍스트 베이스라인 기준으로 정렬한다.

![image](https://user-images.githubusercontent.com/39187116/100680958-0a9c1e00-33b6-11eb-916a-cb464f7ae5f2.png)

<div style="width: 100%; text-align: center;"><a href="https://velog.io/@ursr0706/vertical-align">출처</a></div>

**6.5 아이템 한 가운데에 정렬하기**

- `justify-content: center; align-items: center;` 를 이용하면 아이템을 쉽게 컨테이너 중앙에 정렬 가능하다.
- 아이템 이미지가 균일하지 않을 때 이 옵션을 이용해서 정렬하면 유용하다. ( padding 으로는 힘들쥐 )

```css
display: inline-flex;
justify-content: center;
align-items: center;
width: 30px;
height: 30px;
```

**7. align-content**

`flex-wrap: wrap` 이 설정된 상태에서, flex 아이템들의 행이 2줄 이상 되었을 때 **수직축 방향 정렬**을 어떻게 할 것인지 결정!

- default 값은 `stretch` : 쫙 늘린다. 이때 부모의 height을 각 행들이 나눠 갖는다.
- `flex-start`

<img src="https://user-images.githubusercontent.com/39187116/100758681-209bf400-3433-11eb-8ef8-bb9d372a7f07.png" width="300" height="300" />

- `space-between`

<img src="https://user-images.githubusercontent.com/39187116/100758988-783a5f80-3433-11eb-980e-d84364c07e41.png" width="300" height="400"/>

- `align-items` 에서 옵션들을 설명했던 것 처럼, `flex-end`, `space-around` , `center` , `space-evenly` 모두 같은 방식으로 정렬된다. `space-evenly` 는 ms 계열 브라우저에서 지원하지 않는다!
