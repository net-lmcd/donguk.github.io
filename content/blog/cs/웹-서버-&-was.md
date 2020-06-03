---
title: 웹 서버 & WAS
date: 2020-06-03 23:06:00
category: cs
---

## 웹 서버와 WAS(Web Application Server)

웹 서버와 WAS의 차이는 **어떤 타입의 컨텐츠**를 제공하느냐의 차이이다.

- 웹서버는 **정적인 컨텐츠(html, css, js)**를 제공하는 서버이다. 대표적으로 Apache와 Nginx가 있다.
- WAS는 **DB 조회나, 어떤 로직을 처리해야 하는 동적인 컨텐츠**를 제공하는 서버이다. 대표적으로 Tomcat이 있다.
- 웹 서버와 WAS는 독립적으로 존재할 수 있고, 대부분의 WAS는 정적인 컨텐츠를 제공해주고 있기 때문에, 웹서버 없이 WAS만 존재할 수 있다. 그래서 WAS는 웹 서버를 포함하는 개념이라고 생각할 수 있다.

## 웹 서버를 사용하는 이유?

![image](https://user-images.githubusercontent.com/39187116/83649878-a826b880-a5f2-11ea-80fa-ca34a5e96a38.png)

### WAS가 해야 할 일의 부담을 줄이기 위해서!

WAS  앞에 웹 서버를 둬서 웹 서버에서는 정적인 컨텐츠만 제공하고 WAS는 Application의 로직만 수행하도록 기능을 분배하여 서버의 부담을 줄일 수 있다.

### WAS의 환경설정 파일을 외부에 노출시키지 않도록 하기 위해서!

클라이언트를 연결하는 포트가 WAS와 직접 연결이 되어 있다면 WAS의 환경설정 파일이 외부에 노출될 가능성이 있다. 웹 서버를 WAS의 앞단에 배치시켜 클라이언트는 웹 서버 하고만 통신하게 하여 WAS의 보안을 강화시킬 수 있다.

### Reference

1. https://victorydntmd.tistory.com/121
2. https://jeong-pro.tistory.com/84

