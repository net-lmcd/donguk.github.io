---
title: 생활코딩 리눅스 강좌(Permission)
date: 2019-11-16 17:11:52
category: linux
---

### Permission 의 예시

```bash
# donguk 유저
$ cd ~
$ touch perm.txt
$ ls -l perm.txt
-rw-rw-r-- 1 donguk donguk 0 Nov 16 16:24 perm.txt
$ echo 'hi' > perm.txt # echo 의 표준 출력을 redirection 하여 perm.txt 수정
```

<code>ls -l </code> 의 Standard output 중에서 왼쪽 donguk이 나타내것은 **소유자가 donguk 이라는 뜻** , 마지막 코드는 <code>echo 'hi'</code> 의 출력 값이 perm.txt에 출력 되어 저장이 된다. ( 물론 nano, vi 등 에디터를 이용해서 perm.txt 수정 가능 )

```bash
# permTest 유저
$ cd /home/donguk
$ echo 'I want eddit' > perm.txt
Permission denied
```

perm.txt의 소유자가 donguk인데 permTest 유저가 이를 수정하려고 해서 Permission denied가 나온다.

### Permission 이해

```bash
-rw-rw-r-- 1 donguk donguk 16 Nov 4 16:24 perm.txt
```

1. <code>-rw-rw-r—</code> 

   - 맨 앞에 있는 <code>-</code> : **type** , 파일이면 - 디렉토리면 d 링크면 l
   - <code>rw-rw-r—</code>  : **access mode** 
     1. 첫번째 <code>rw-</code> : **소유자**의 권한이다
     2. 두번째  <code>rw-</code> : 이 파일(또는 디렉토리, 링크) 가 속한 **그룹**의 권한이다.
     3. 세번째 <code>r—</code> : 이 OS에 속한 유저 중에서 소유자도 그룹도 아닌, **다른 사용자의 권한**이다. ( others )
   - 여기서 r 은 read, w는 write,  -에는 x가 들어올 수 있는데 이때 x는 execute

   따라서 perm.txt 파일은 donguk이라는 사용자만 읽고 쓸수 있기 때문에, permTest 사용자(others)는 w 권한이 없어서 Permission denied가 뜬 것이다.

2.  <code>donguk donguk</code>

   1. 첫번째 donguk은 이 파일의 소유자 이다. ( Owner )
   2. 두번째 donguk은 이 파일의 그룹이다. 

### Permission 변경 

```bash
$ chmod o+r perm.txt # other에게 읽기(read) 권한 부여
$ chmod g+r perm.txt # 그룹에 읽기 권한 부여
$ chmod u-w perm.txt # 소유자의 쓰기(write)을 뺌
$ chmod o+w perm.txt # other에게 쓰기 권한 부여
$ chmod o+w, g-r, u+r perm.txt # 한번에 여러개 설정 가능

#심화
$ chmod a=rwx perm.txt # 모든 사용자(a)가 rwx권한 얻는다.
$ chmod a=r perm.txt # 모든 사용자가 읽기 권한만 갖는다.
$ chmod a-r perm.txt # 모든 사용자에서 읽기 권한 해체
```

- <code>chmod [option] [file]</code> : file의 권한을 변경하는 커맨드 이다. 옵션을 구성할 때 **소유자는 u, 다른 사용자(other) 는 , 그룹은 g , 모든 사용자는 a로 나타낼수 있고 읽기는 r, 쓰기는 w, 실행은 x 를 사용하면 된다.**

- 실행 권한

<img width="508" alt="스크린샷 2019-11-16 오후 4 06 05" src="https://user-images.githubusercontent.com/39187116/68989491-0c6a7080-088b-11ea-8fc4-d57136c97741.png">

vi 편집기를 이용해서 'test success!' 를 출력하는 execute-test.sh 파일을 만들었다.

<img width="602" alt="스크린샷 2019-11-16 오후 4 10 36" src="https://user-images.githubusercontent.com/39187116/68989536-a8947780-088b-11ea-96f2-3035316e76fa.png">

/bin/bash 를 통해서 실행하면 파일이 실행 되지만( parser기를 통해서 특정 프로그래밍 언어를 실행 시키는건 제약이 없음..) , 이 파일을 만든 사용자가 파일을 실행하려고 하면 권한이 없다고 실행되지 않는다. ( access mode 부분이 rw- ) 따라서 소유자에게 실행권한을 추가 해줘야 한다.

```bash
$ chmod u+x execute-test.sh #소유자 에게 x권한 ( 실행 ) 부여
$ ./execute-test.sh
'test success!'
```



### Directory Permission

```bash
#donguk user
$ mkdir perm; cd perm; echo'hi' > perm.txt
$ ls -l
...
$ drwxrwxr-x 1 donguk donguk 0 Nov 16 16:24 perm
$ chmod o-r perm # other 의 읽기 권한 해체

#permTest user
$ cd /home/donguk
$ ls -l perm
Permission denied # read 권한이 없어서 파일열람 불가능

#donguk user
$ chmod o+r perm

#permTest user
$ ls -l perm
...(파일 정보들) # read 권한을 부여 받아서 열람 가능 

#donguk user
$ chmod o-w perm # otehr에 쓰기 권한 해체

#permTest user
$ cd perm
$ touch a.txt 
Permission denied # otehr는 perm 디렉토리의 w권한이 없기 때문.
$ rm perm.txt
Permission denied

#donguk user
$ chmod o+w perm

#permTest user
$ cd perm
$ rm perm.txt # w 권한을 받아서 삭제 가능

#donguk user
$ chmod o-x perm

#permTest user
$ cd perm # exectue 권한이 없어서 perm 디렉토리에 들어갈수 없음.

#donguk user
$ chmod o+x perm

#permTest user
$ cd perm # execute 권한을 받아서 들어갈수 있음.
```

디렉토리에서 <code>r</code> 이라는 access mode의 권한은 그 디렉토리안에 있는 파일들을 열람 할 수 있는냐 없느냐와 관련이 된다. 

그리고 디렉토리에서 <code>w</code> 권한은 그 디렉토리 안에 있는 파일의 수정, 생성, 삭제와 관련이 된다. 

마지막으로 디레곹리에서 <code>x</code> 권한은 해당 디렉토리에 <code>cd</code> 커맨드를 수행하는 것 과 관련된다.

만약 디렉토리 안에 디렉토리가 더 있고 한번에 권한을 바꾸고 싶다면 **<code>chmod -R [option] [dir name]</code>** 처럼 -R (recursive) 옵션을 주면 한번에 바꿔줄 수 있다.



### Permission을 변경하는 다른 방법

<img width="254" alt="스크린샷 2019-11-16 오후 4 50 20" src="https://user-images.githubusercontent.com/39187116/68990018-4dfe1a00-0891-11ea-8ed9-726fa1dfb152.png">

```bash
$ chmod 444 perm.txt # -r--r--r--
$ chmod 000 perm.txt # ----------
```

