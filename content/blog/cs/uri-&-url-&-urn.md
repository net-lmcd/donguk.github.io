---
title: URI & URL & URN
date: 2020-06-10 13:06:25
category: cs
---
## URI ( Uniform Resource Identifier )

통합 자원 식별자라는 의미로 **인터넷 상의 리소스를 고유하게 식별할 수 있는 식별자**이다. URI에는 위치를 알려주는 **URL(Uniform Resource Locator)** 와 전 세계를 통틀어 자원의 고유한 이름을 의미하는 **URN(Uniform Resource Name)**이 존재한다.

![image](https://user-images.githubusercontent.com/39187116/84225814-13085000-ab1b-11ea-979e-f14a39963b7d.png)

## URL ( Uniform Resource Location )

리소스가 어디에 있는지를 명시하며 **해당 위치에서 어떻게 리소스를 얻어낼 것인가에 대한** 정보를 포함한다. 

만약에 우리집에 나 혼자만 살고 있다면 집 주소는 날 구별할 수 있는 유일한 지시자가 된다.( 나라는 자원이 있다는 것을 간접적으로 명시 -> 식별자 역할도 하고있음 ) 하지만 여러명에서 거주하게 되면 그 주소는 유일한 지시자가 될 수 없다. 이래서 등장한 개념이 URN이다.

## URN ( Uniform Resource Name )

**리소스를 유일하고 영구적인 이름으로 식별하지만 인터넷 상의 위치는 알려주지 않는다.**

만약, 지구상에 '홍동욱'이라는 이름이 유일하다면 URN의 조건에 부합하게 된다. 즉 매번 바뀌는 위치가 아닌 **유일한 식별자인 이름을 기준**으로 자원을 식별하겠다는 의도이다.

## Example

- URL
  - http://example.com/mypage.html (프로토콜: http)
  - ftp://example.com/download.zip (프로토콜: ftp)
  - mailto:[user@example.com](mailto:user@example.com) (프로토콜: mailto)
- URN
  - urn:isbn:0451450523 (책을 식별하는 ISBN 번호)
  - urn:uuid:6e8bc430-9c3a-11d9-9669-0800200c9a66 (전 세계에서 유일한 번호)

## Reference

1. https://github.com/baeharam/Must-Know-About-Frontend/blob/master/Notes/network/uri.md
