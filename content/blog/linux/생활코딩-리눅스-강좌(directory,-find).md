---
title: 생활코딩 리눅스 강좌(directory, find)
date: 2019-11-10 20:11:91
category: linux
---

###  Directory Structure

> https://www.thegeekstuff.com/2010/09/linux-file-system-structure/ 참고

- <code>/</code> : 최상위 디렉토리 ( root )

- <code>/bin</code> : binary 줄임말, 컴퓨터에서는 실행가능한 프로그램을 binary라고함 > 프로그램들이 모여있는 directory

- <code>/sbin</code> : 시스템 관리자가 쓰는 프로그램 ( <code>reboot, shutdown 등</code>)들이 모여있음.

- <code>/etc</code> : configuration files들이 모여있음, 프로그램의 설정을 담당하는 directory

- <code>/var</code> : variables files 의 줄임말, 이 dir 밑에 있는 파일들은 언제든 바뀔수 있음

  - 예로 /var/log dir에는 프로그램을 사용하다가 오류가 발생하면 자동으로 업데이트 된다.

- <code>/tmp</code> : 임시 파일들이 저장됌. 컴퓨터 재부팅하면 사라져 있음.

- <code>/home</code> : 사용자들의 디렉토리 예를들어 donguk 이라는 사용자가 있다면 <code>/home/donguk</code> 이 존재 

  <img width="420" alt="스크린샷 2019-11-10 오후 7 19 21" src="https://user-images.githubusercontent.com/39187116/68542353-3a653600-03ef-11ea-8bd8-3d03272327a6.png">

  -  <code>~</code>: 현재 사용자의 home directory로 이동

- <code>/lib</code> : /bin과 /sbin 에 있는 프로그램들이 사용하는 라이브러리들이 모여있는 directory
- <code>/opt</code> : 일반적인 프로그램들은 설치 될 때 자동으로 directory가 정해져서 설치되지만, 사용자가 직접 지정 해줘야 할 때가 있다. 이때 /opt 를 이용하면 좋다(?)

<br/>

### Fild find

- <code>locate</code> : locate는 directory를 뒤지지 않고 컴퓨터안에 있는 파일에대한 정보를 갖고 있는 database를 뒤진다. 이때, locate가 사용하는 database를 <code>mlocate</code> 라고 한다.

- <code>find [start-dir] [flag] [target]</code> : 직접 파일을 뒤지며 찾음.

  - (Tip) <code>man find | head</code> : <code>man find</code>가 return하는 결과물을 **파이프( | )**로 <code>head</code> 가 입력으로 받아서 출력

  - 예로, <code>find ~ -name "*.log"</code> : 현재 사용자의 모든 디렉토리를 뒤져서 이름에 .log가 들어간 모든 파일을 검색함.
  - . 는 현재 디렉토리, /는 root, ~는 현재 사용자 디렉토리

  ```bash
  //두번째 예시
  $ find . -type f -name 'test.txt' -exec rm -f {} \;
  ```

  - 위 예시는 현재 디렉토리에서 타입이 파일 이면서 이름이 test.txt인 파일을 찾는다. 그리고 묻지도 따지지도 않고.. 지운다.  <code>{}</code> 는 find에 검색된 조건이 맞는 파일명이 들어간다.

- <code>whereis [target]</code> : 실행 프로그램의 binary directory 경로를 찾아준다. 검색결과가 두개가 나온다면 후자는 메뉴얼의 경로

  <img width="321" alt="스크린샷 2019-11-10 오후 8 09 21" src="https://user-images.githubusercontent.com/39187116/68543092-0c372480-03f6-11ea-95c7-854904d1c219.png">

  - 파일을 다 뒤지는게 아니라, <code>$PATH</code> 라는 변수에 담겨있는 경로들만 뒤진다.
  - **$PATH ( 환경변수 )**

  <img width="718" alt="스크린샷 2019-11-10 오후 8 15 02" src="https://user-images.githubusercontent.com/39187116/68543162-d5154300-03f6-11ea-910b-3e0b4eeb5564.png">

  우리가 화면에 ls를 입력하면 컴퓨터는 $PATH안에 담겨있는 경로들을 하나하나 찾아본다.  그러다가 /bin 디렉토리에 ls라는 프로그램을 찾고 이를 실행 시키는 것이다. 

  mac에서는 공백으로 경로들이 구분 되지만, 다른 OS에서는 : 으로 구분 되기도 한다. 그리고 $PATH는 수정 가능하다.