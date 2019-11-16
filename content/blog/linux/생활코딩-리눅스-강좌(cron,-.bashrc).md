---
title: 생활코딩 리눅스 강좌(cron, .bashrc)
date: 2019-11-16 12:11:14
category: linux
---

## CRON

#### CRON 기본 명령어

- 정기적으로 명령을 실행시켜주는 소프트웨어 ( ex 백업, 정기적인 데이터 전송, 정기적으로 인터넷으로 시간조정? )
- <code>crontab -e </code> : 정기적으로 실행 시키고 싶은 작업을 설정 할 수 있음. 

```bash
$ #m h dom(day of month) mon(month) dow(요일) command
$ */1 * * * * date >> date.log # 1분에 한번 시간, dom, mon, dow 는 무시, 만약 10 1 24 이라면 -> 매달 $ 24일 1시10분
```

- <code>crontab -l</code> : 처리한 cron 들을 확인 할 수 있다.
- <code>crontab -r </code> : cron 삭제

#### CRON 주기 설정

```bash
$ *        *         *        *         *
$ 분(0~59) 시간(0~23) 일(1~31)  월(1~12)   요일(0~6) # 일요일은 0, 1부터 월요일~
$ * * * * * ls -l # ls -l 을 매분 실행
$ 0,20,40 * * * * ls -l # 매월매일매시간 0분,20분,40분에  ls-l 실행
```

- <code>tail [file]</code> :  file의 꼬리 부분을 확인 할 수 있다.
  - **<code>tail -f [file]</code>** : file의 꼬리부분을 주시하다가 업데이트 되면 자동으로 화면을 업데이트 시켜준다.

```bash
$ *1 * * * * date >> date.log 2>&1 # 표준 에러를 표준 출력화
```

**출력에서 숫자 2는 '표준 에러' 이고 숫자 1은 '표준 출력'이다.** 위 코드는 표준 에러를 표준 출력으로 Redirection 한 것 이다. ( 그냥 1만 하면 1이라는 파일에 저장된다. &를 붙여줘야 한다! ) 

#### 적용 사례

회사에서 매일 아침에 서버의 tmp 폴더와 서버 프로젝트의 이미지 폴더를 지워야 하는 일이 있었는데, Shell sript를 이용하여 tmp, 이미지 폴더를 비워주는 sh 파일을 만들고 이를 크론으로 주기적으로 실행 하였다.

```bash
$ crontab -e
### crontab
$ 0 8 * * * /home/ubuntu/clearTemp.sh 2> error.log # 매일 아침 8시마다 자동으로 비워주기 오류있으면 error.log 에 로깅
```



<br/>

## 쉘을 시작할 때 실행

- <code>alias [별명] = '[command]'</code> :  command에 단축키(별명?)을 붙여준다.

```bash
$ alias ..='cd ..' # .. 는 cd .. 기능을 수행한다.
$ alias c='clear' # c는 clear 기능을 수행.
```

- <code>.bashrc</code> : 사용자 루트 디렉토리 경로에 있는  <code>.bashrc</code> 를 수정하면 쉘 ( bash )이 실행 될 때 무언가를 수행하게 할 수 있다.
  - bash shell은 쉘이 실행 될 때 <code>.bashrc</code> 를 실행하기 때문.