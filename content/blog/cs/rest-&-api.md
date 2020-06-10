---
title: REST & API
date: 2020-06-10 14:06:80
category: cs
---
## REST?

Representational State Transfer의 약자로 전반적인 웹 어플리케이션에서 상호작용하는데 사용되는 웹 아키텍쳐 모델이다. 즉 자원을 주고 받는 웹 상에서의 통신 체계에 있어서 범용적인 스타일을 규정한 아키텍쳐라고 할 수 있다.

**그럼, REST는 웹 상에서 서버와 클라이언트가 자원을 주고받는데 사용되는 아키텍처 모델**이라고 할 수 있겠군

## API?

Application Programming Interface의 약자로 **기존에 있는 응용 프로그램을 통해서 데이터를 제공받거나 기능을 사용하고자 할 때 사용하는 인터페이스 및 규격**을 말한다. 따라서 REST API는 REST 아키텍처 기반으로 API를 설계한 것을 말하며 대부분의 서비스가 REST API를 말한다

그럼, **API는 서버와 클라이언트가 자원을 주고 받는데 사용되는 인터페이스고 이를 구현할 때 REST 기반으로 구현한 API가 REST API군**

## REST의 특징?

### 무상태성( Stateless )

HTTP의 특징과 비슷하게 서버는 클라이언트를 고려하지 않고 API 요청에 대해서만 처리한다. 

### 자체 표현성 ( Self-Desciptiveness )

REST의 자원 명세 규칙을 잘 치킨 API는 HTTP 메소드와 함께 그 자체로 의미를 지니기 때문에 요청 자체로 어떤의미를 지니는지 알아보기 쉽다.

### 캐싱 가능 ( Cacheable )

REST는 HTTP 표준을 기반으로 만들어졌기 때문에 HTTP의 특징인 캐싱을 사용할 수 있다.  서버에서 `GET` 메소드에 대한 응답으로 헤더의 `Last-Modified` 속성을 명시해주면, 클라이언트는 데이터의 변화가 없을 때 캐싱된 값을 사용한다. ( 서버의 부하가 줄어들겠군? )

### 균등한 인터페이스 ( Uniform Interface )

REST가 HTTP의 표준만 따른다면 어떠한 기술이던지 접목하여 사용할 수 있기 때문에 플랫폼이나 언어의 제약에 구애받지 않는다. 요즘은 REST API를 정의할 때 JSON을 많이 사용하지만 XML도 가능하다.

### 클랑이언트 - 서버 구조

REST 서버가 API를 제공하는 방식이기 때문에 클라이언트와 독립적으로 동작한다. 따라서, 서로간의 의존성이 줄어들고 클라이언트와 서버를 최대한 독립적으로 개발할 수 있도록 도와준다.

### 계층형 구조 (Layered System)

클라이언트는 계층형 구조가 불가능하지만 REST 서버의 경우, 보안/로드 밸런싱/암호화 등을 추가할 수 있고 Proxy 및 게이트웨이 등의 중간매체를 사용할 수 있다.

## REST API 명세(?)

### URI는 리소스를 표현해야 한다. 즉 자체 표현성이 있어야 한다. 

_URL 과 HTTP Method만 보고 어떤 요청을 하는지 알 수 있어야 해~_

- 리소스 명은 동사가 아닌 명사를 사용해야 한다.

```javascript
/students/1
```

- 리소스는 Collection과 Document로 표현하는데, Collection은 복수 Document는 단수를 사용한다.

```javascript
/locations/seoul/schools/3
```

여기서 `locations`는 Collections를 `seoul`은 Document를 표현한다.

### 리소스에 대한 행위는 HTTP의 Method로 표현해야 한다.

- GET은 리소스를 조회한다.
- POST는 리소스를 생헝한다.
- PUT은 리소스를 업데이트한다.
- DELETE는 리소스를 삭제한다.

즉, **자원에 대한 명세는 URL에 명사를 사용해서 표현하고 행위는 HTTP Method를 이용한다.**

### HTTP 상태코드

요청에 대한 응답의 상태코드 또한 명확하게 들려주는 것이 잘 설계된 REST API이다.

- 2xx: 성공 관련 (200 Ok, 201 Created)
- 3xx: 리다이렉션 (304 Not Modified -> 요청한 자원이 수정x 캐시된 자원을 사용하라고 권고 )
- 4xx : 클라이언트 에러 관련 (400 Bad Reequest, 401 Unauthorized)
- 5xx : 서버 에러 관련 (500 Interner Server Error)

### 잘못된 REST 사용..

- GET/POST의 부적합한 사용 : 기존의 조회/생성의 기능이 아닌 다른방식으로 사용한 경우
- 자체 표현력이 떨어지는 경우: URI와 HTTP 메소드를 통해서 자체적으로 어떤 자원에 어떤 행위를 하는지 알 수 있어야 한다.
- HTTP 응답코드 미사용
