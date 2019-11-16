---
title: 생활코딩 리눅스 강좌(Daemon)
date: 2019-11-14 02:11:96
category: linux
---

### Daemon 의 개념

- 데몬에 해당하는 프로그램은 **항상**  켜져 있음.
- <code>ls, mkdir, cd</code> 이런 프로그램들은 데몬이 아님.
- 소위 server ( 예를들어 web server ) 라고 불리는 프로그램은 데몬에 해당한다.
- 이런 데몬에 해당하는 프로그램을 service 라고도 부른다.



### Daemon의 예시

```bash
# Mac Os 기준, mac system dameon이 위치한 directory는 System/Library/LaunchDaemons
# third-party daemon이 위치한 directory는 /Library/LaunchDaemons
# 밑에 있는 예시는 ubuntu 기준

$ sudo apt-get install apache2 # web server apache 설치
$ cd /etc/init.d # service(데몬의 목적을 가진) 프로그램들이 위치하는 디렉토리
$ sudo service apache2 start # apache start, service 프로그램은 실행 시키려면 'service' 사용
$ ps aux | grep apache2 # 현재 실행된 프로세스 중 apache2 포함된 프로세스 확인
$ sudo service apache2 stop # apache2 종료, service로 실행되는 프로그램은 대부분 start, stop이 있음
```



- 컴퓨터가 부팅될 때 자동으로 Start 시키려면?

```bash
$ cd /etc/rc3.d # CLI 방식으로 실행된 데몬들 있는 디렉토리 , GUI 방식으로 실행된 데몬은 rc5.d
$ ls -l 
```

<img width="972" alt="스크린샷 2019-11-14 오전 2 20 20" src="https://user-images.githubusercontent.com/39187116/68788374-9ac1d500-0686-11ea-9f1d-5cd7d57d648a.png">

맨 왼쪽에 <code>l </code> 이 의미 하는건 링크 이다. SO2apache2 는 윈도우의 바로가기와 비슷하다.(<code>./S02apache2</code> 커맨드로 실행 가능 ) -> 오른쪽에 있는 경로가 service가 실제로 위치한 디렉토리를 의미한다. 여기서 S02의 S가 의미하는 것은 CLI 방식으로 컴퓨터가 재부팅 될 때 자동으로 실행 된다는 뜻 이다.  그리고 01, 02, 03은 자동으로 시작되는 service들 사이의 우선순위 이다.  반대로 S 대신에 K가 붙은 service도 있는데 이 service는 컴퓨터가 재부팅 될 때 종료 된다.

따라서 **CLI 방식으로 재부팅 될 때 프로세스가 자동으로 실행되게 하고 싶다면 <code>rc3.d</code> 디렉토리 밑에 S로 시작하는 이름으로 링크를 걸면 된다.**

