---
title: 생활코딩 리눅스 강좌(Shell)
date: 2019-11-09 16:11:83
category: linux
---

## Shell

<img width="490" alt="스크린샷 2019-11-09 오후 3 11 44" src="https://user-images.githubusercontent.com/39187116/68523950-5390b880-0303-11ea-9a27-045c2c85612e.png">

- harware - 컴퓨터의 기계적인 부분들 ( 메모리, 하드디스크 ,SSD, CPU 등등..)

- Kernel - 하드웨어를 제어하는 프로그램 ( 운영체제에서 코어역할을 한다.)
- Shell - 우리가 입력한 명령을 Kernel이 이해할수 있게 kernel에게 전달해줌. Kernel은 이를 하드웨어에 이해할 수 있게 전달한다. 

- <code>echo </code>$0 : 현재 사용중인 쉘 확인
- 결국 쉘은 사용자의 Standard input(키보드)을 받아서 커널이 이해할 수 있게 커널에 전달하고, 커널은 이를 하드웨어에 전달하여 처리하고, 이 결과를 쉘이 받아서 Standard output이나 Standard error로 사용자에게 전달하여 상호작용하는 소프트웨어 이다.

<br/>

## Shell Script

- <code>/bin directory</code> : 유닉스 계열의 컴퓨터에 기본적으로 존재하는 프로그램들이 위치한 directory

```bash
$ nano backup
...backup 파일 start
#!/bin/bash 
if ! [ -d bak ]; then //현재 디렉토리에 bak라는 directory가 존재하지 않는다면?
	mkdir bak
fi //if를 거꾸로 쓰면 조건문이 끝났다는 것을 컴퓨터에 알림
cp *.log bak // 현재 디렉토리의 log로 끝나는 파일들을 bak에 복사
```

- <code>#!/bin/bash</code> :  이 줄 밑에 작성된 코드들이 /bin/bash 라는 프로그램을 통해서 해석되어야 한다. 라는 약속 ( /bin/bash 는 example )
- <code>./[filename]</code> : 현재 경로의 파일 실행

<img width="473" alt="스크린샷 2019-11-09 오후 4 00 37" src="https://user-images.githubusercontent.com/39187116/68524432-1e3b9900-030a-11ea-9ce9-6f909a6b1334.png">

- 실행 권한이 없기 때문에 <code>'./backup' is not executable by this user (또는 Permisssion denied)</code> 라는 Standard error가 나왔다.(<code>ls -l</code> 로 나온 output을 보면 backup 파일에 x 권한이 없음.)  실행 권한을 주기 위해서는 <code>chmod ( change mode )</code> 명령어에 <code>+x (executable)</code> 권한을 추가 해줬다.

