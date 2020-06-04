---
title: CSR & SSR
date: 2020-06-04 00:06:94
category: cs
---
## SPA & MPA

![image](https://user-images.githubusercontent.com/39187116/83652198-503d8100-a5f5-11ea-90fc-625fd88c3713.png)

- SPA (Single Page Application)

웹 어플리케이션에 필요한 모든 정적 컨텐츠를 **최초 한번에** 다운로드 받는다. 그 후 새로운 페이지 요청이 있을 경우, 페이지 갱신에 **필요한 데이터만** 전달받아 페이지를 갱신한다.

즉, 하나의 HTML을 기반으로 자바스크립트를 이용해 동적으로 화면의 컨텐츠를 바꾸는 방식의 웹 어플리케이션이다.

- MPA (Multiple Page Application)

새로운 페이지를 **요청할 때마다** 정적 컨텐츠를 다운로드 한다. 그에 맞춰 **전체 페이지**를 다시 렌더링한다. 인터넷 주소창에 주소를 입력하거나, 링크를 클릭하면 그에 맞는 정적 컨텐츠를 다운받고 새로고침이 일어난다.

즉, 사용자가 페이지를 요청할 때마다, 웹 서버가 요청한 UI와 필요한 데이터를 HTML로 파싱해서 보여주는 방식의 웹 어플리케이션이다. 

그래서 SPA를 CSR(Client Side Rendering) 방식으로, MPA을 SSR(Server Side Rendering) 방식으로 렌더링한다고 말한다.

## CSR (Client Side Rendering)

CSR에서는 브라우저가 서버에 정적 컨텐츠를 최초에 한번 요청한 후 로드되면 사용자의 상호작용에 따라 JS를 이용해서 동적으로 렌더링을 시킨다.

### 장점?

- 첫 로딩만 기다리면, 동적으로 빠르게 렌더링이 되기 때문에 사용자 경험이 좋다.
- 서버에 요청하는 횟수가 훨씬 적다 -> 서버의 부담이 덜하다.
- 개발하기에 심플하다. ( 프론트엔드와 백엔드가 분리 + 크롬으로 디버깅도 쉽다 )

### 단점..

- 최초에 모든 정적컨텐츠를 다운받아야 하기 때문에 SSR 방식보다 초기 로딩속도가 느리다. ( 리소스를 Chunk 단위로 묶어서 요청할 때만 다운받게 하는 방식이 있지만 완벽한 방법은 아니라고 한다. )

- 검색엔진의 검색 봇이 크롤링을 하는데 어렴울 겪는다..( SEO가 좋지 않다.)

## SSR(Server Side Rendering)

SSR에서는 브라우저가 페이지를 요청하면 서버는 페이지에 맞는 정적 컨텐츠를 응답해주는 방식으로 렌더링을 시킨다.

### 장점?

- 초기 로딩속도가 빠르다. 따라서 사용자가 컨텐츠를 빨리 볼 수 있다.
- JS를 이용한 렌더링이 아니기 때문에 검색엔진 최적화가 가능하다.

### 단점..

- 매번 페이지를 요청할 때마다 새로고침이 일어난다. 따라서 UX가 SPA방식에 비해 좋지않다.
- 서버에 매번 요청을 하기 때문에 서버의 부하가 커진다.
- 프론트엔드와 백엔드가 밀접하게 연관되어 개발할 때 SPA보다 어려움이 있다.



## 결론

SPA, MPA를 상에 맞게 사용하면 된다..ㅎㅎ



### Reference

1. https://medium.com/@adamzerner/client-side-rendering-vs-server-side-rendering-a32d2cf3bfcc
2. https://velog.io/@thms200/SPA-vs.-MPA
3. https://docs.microsoft.com/en-us/archive/msdn-magazine/2013/november/asp-net-single-page-applications-build-modern-responsive-web-apps-with-asp-net
