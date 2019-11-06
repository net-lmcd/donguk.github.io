---
title: 프로토콜 버퍼(Protocol Buffer)
date: 2019-11-07 00:11:76
category: cs
---

### 1. 프로토콜 버퍼란?

프로토콜 버퍼는 XML, JSON과 같은  **직렬화 데이터 구조** 이다. 여기서 직렬화 데이터 구조의 예시를 잠깐 들면,

hello를 '문자열 직렬화' 할 경우 8(h) 65(e) 6C(l) 6C(l) 6F(o) 로 표현되고,

어떤 객체를 'JSON 포맷 직렬화' 할 경우 {'something' : 'something value'} 처럼 표현될 수 있다.

이처럼 프로토콜 버퍼는 데이터를 표현하는 구조 라고 이해할 수 있겠다.

### 2. 프로토콜 버퍼의 원리

JSON 포맷과 비교하여 이해를 하면,

```json
//Person Class object JSON 포맷화
{
	"userName":"Martin",
  "favouriteNumber":1337,
  "interests":["daydreaming","hacking"]
}
```

데이터의 크기가 얼핏 잡아도 80byte가 넘는다. 근데 같은 데이터를 프로토콜 버퍼를 이용하여 나타낸다면?

<img width="670" alt="스크린샷 2019-10-28 오후 11 41 41" src="https://user-images.githubusercontent.com/39187116/67690406-2fb6a400-f9e0-11e9-8b4b-fe0e88095f46.png">

```protobuf
message Person{
	required string user_name = 1; 
	optional int64 favourite_number = 2; 
	repeated string interests = 3;
}
```

위 코드는 프로토콜 버퍼 데이터 타입으로 작성된 .proto 파일이다. JSON 포맷과 다른점은 불필요한 속성 값들이 1, 2, 3 으로 대체 됐다. 

그림을 보면 데이터의 첫 바이트를 5bit. 3bit로 나눴는데 5bit에는 대체된 번호 ( 1, 2, 3 )를 나타내고 있고 3bit에는 데이터 타입을 표현한다. ( 010 은 string ) 그리고 다음 바이트에는 이어지는 데이터의 길이를  나타내준다. 이와 같은 원리로 33byte에 80byte가 넘는 데이터를 표현 가능한 것이다.



### 3. 프로토콜 버퍼 특징

- 빠르다.

데이터의 크기가 작기 때문에 빠르다. 그리고 JSON 포맷과 다르게 프로토콜 버퍼를 사용하게 되면 바이트가 오기 때문에 별도의 파싱이 필요없이 메모리에 저장할 수 있다.  결론은 빠르다.

- 가독성이 안좋다.

JSON은 사람이 읽기에 편하지만, 프로토콜 버퍼는 .proto파일이 없다면 데이터를 이해 할 수 없다. 

### 4. 프로토콜 버퍼 컴파일러 설치 ( Mac )

1. <code>https://github.com/google/protobuf/releases</code> 이곳에서 자신이 필요한 프로토콜 버퍼를 다운 받는다.
2. 터미널에서 <code>brew install autoconf && brew install automake</code>
3. 1번에서 다운받은 protobuf 폴더에 들어가서 <code>./autogen.sh && ./configure &&. make</code> ( 오래 걸린다 )
   - Can't exec "glibtoolize": No such file 오류가 뜬다면 brew install libtool
   - 설치를 하는 protobuf폴더의 이름에 공백이 있으면 설치가 안된다.
4. 3번 과정이 끝난다면,

```bash
$ make check
$ sudo make install
$ which protoc 
$ protoc --version
```



### 5. 참고 자료

https://medium.com/@erika_dike/installing-the-protobuf-compiler-on-a-mac-a0d397af46b8

https://jeong-pro.tistory.com/190