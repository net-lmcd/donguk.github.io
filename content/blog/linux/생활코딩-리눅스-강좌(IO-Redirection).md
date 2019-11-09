---
title: 생활코딩 리눅스 강좌(IO Redirection)
date: 2019-11-09 05:11:68
category: linux
---

## IO Redirection

<img width="506" alt="스크린샷 2019-11-09 오전 12 26 33" src="https://user-images.githubusercontent.com/39187116/68488114-b5134180-0287-11ea-8d1b-62849422f230.png">

- IO Redirection ( Input Ouput Redirection ) : input 과 output의 방향을 바꾼다

  - UNIX 계열의 시스템은 Standard Input( Command ) 이 주어지면 Unix process를 거쳐 Standard output을 내보냄
  - <code>ls -l 1> result.txt</code> : ls -l 의 결 과물을 result.txt에 저장. Standard ouput의 출력 방향을 바꿔주는 방법 ( <code>></code> 가 수행, 1은 생략 가능하다.  )

  - Standard error은 Standard Input이 process에서 오류가 발생하면 생김 
  - Standard error은 redirection 할 수 없다. 하지만 <code>2> </code> 를 사용 한다면 error도 redirection 가능하다.

  ```bash
  #현재 디렉토리에 test.txt가 없을때
  $ rm test.txt // 오류 발생
  $ rm test.txt > result.txt //오류가 result.txt에 저장되지 않음 ( Standard error를 redirection )
  $ rm test.txt > result.txt 2> result2.txt // result2.txt에 오류가 저장됌
  ```

  - <code>cat </code> : 파일 읽기 
  - <code>cat < hello.txt </code> 명령어가 Standard Input으로 파일을 받는다면 파일의 내용을 Standard Output으로 내보낸다  < 는 input을 redirection 한 것이다.

  ```bash
  $ head -n1 linux.txt // -n1 은 Command line Arguments, linux.txt는 Standard Input
  $ head -n1 < linux.txt //위와 같음
  $ head -n1 < linux.txt > result.txt //input redirection과 output redirection 모두 일어남
  //linux.txt 의 첫번째 행을 result.txt에 저장
  ```

  

- input 과 output 흘러 나가는 이런 형태를 **IO stream** 이라고 한다.

## 추가적인 기능

```bash
$ ls -al > result.txt // Standard output 을 result.txt에 저장
$ ls -al > result.txt // 결과가 result.txt에 덮어 씌워짐
$ ls -al >> result.txt // 결과가 result.txt에 누적됌 ( append )

$ ls -al > /dev/null //실행결과를 /dev/null로 redirection, 화면에도 파일에도 출력x, 쓰레기통 같은곳임
```

