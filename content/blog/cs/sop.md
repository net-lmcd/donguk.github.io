---
title: SOP
date: 2020-06-06 15:06:66
category: cs
---
## 동일 출처 정책 (Same Origin Policy)

- 어떤 출처에서 불러온 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호작용하는 것을 제한하는 보안 방식.
- 잠재적으로 해로울 수 있는 문서를 분리해, 공격받을 수 있는 경로를 줄인다.
- 즉,  동일 출처 정책은 웹 브라우저 보안을 위해 **프로토콜, 호스트, 포트**가 동일한 서버로만 ajax 요청을 주고 받을 수 있도록 한 정책

### 예시

- http://www.same-domain.com -> http://www.same-domain.com  ( same-origin )

- http://www.same-domain.com -/-> http://www.cross-domain.com ( cross-domain )

여기서 **origin이란 요청을 보낸 도메인**을 의미. 두번째 예시는 **같은 도메인으로 요청을 보낸 것이 아니기 때문에** 

> XMLHttpRequest cannot load '[http://localhost:3000'](http://localhost:3000'/). No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin '[http://localhost:8080](http://localhost:8080/)' is therefore not allowed access.

위와같은 에러를 만나게된다. 그러나.. 개발을 하다보면 다른 서버의 API를 요청하는 일이 번번히 발생한다. 이런 상황에서 불편을 해결하기 위해 등장한 것이 **Cross Origin Resource Sharing(CORS)** 정책이다.

## Cross Origin Resource Sharing(CORS)

- CORS 정책은 추가적인 HTTP 헤더를 사용하여 origin이 다른 서버에 요청할 때 요청한 웹 어플리케이션에 접근권한을 주는 메커니즘이다.
- 즉, **출처가 다른 도메인에서의 AJAX요청이라도 서버 단에서 데이터 접근 권한을 허용하는 정책**이다.

### In Server

클라이언트는 서버에 HTTP 요청을 보낼 때 HTTP 헤더의 `Origin` 속성에 자동으로 값이 할당된다.

```json
Origin: https://example.com
```

이 도메인에서 다른 출처의 자원을 사용하기 위해 ajax 요청을 했다고 하면 SOP 정책 때문에 에러가 발생한다. 따라서 서버는 `response` 헤더에 `Access-Control-Allow-Origin` 를 추가하여 요청을 허용할 수 있다.

```javascript
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*') // 모든 도메인 허용
  res.header('Access-Control-Allow-Origin', 'https://example.com') // 특정 도메인 허용
})
```

### Proxy Server

- 다양한 이유로( 주로 **보안**의 문제로 ) 직접 통신하지 못하는 두 개의 컴퓨터 사이에서 서로 통신할 수 있도록 돕는 역할을 가리켜 프록시  라고한다.  
- 리소스를 요청하고자 하는 서버에서 `origin`을 열어주지 못할 때 사용하면 유용하다.
- 프록시 서버가 실제 서버에 요청을 보내서 받아온 다음 그걸 `Access-Control-Allow-Origin` 설정을 적절히 하여 클라이언트에게 돌려주는 방법이다.
- 단, 이방법의 경우 기존의 로직보다 프록시 서버를 거쳐야 하기 때문에 (상대 적으로)느리다는 단점이 있다.

## JSONP ( JSON with Padding)

CORS가 나오기 이전에 사용하던 방식으로, `<script>` 태그를 사용하면 SOP 정책을 피할 수 있다.

```html
<script src="http://company.com/example.json"></script>
```

최신 브라우저에서는 거의 사용 안하고 오래된 브라우저에서 사용하는데 보안문제가 있어서 CORS를 권장한다.



## Reference

1. https://github.com/baeharam/Must-Know-About-Frontend/blob/master/Notes/security/sop.md
2. https://velog.io/@yejinh/CORS-4tk536f0db
