---
title: 생활코딩 리눅스 강좌(Group)
date: 2019-11-16 21:11:61
category: linux
---

## Group

user(소유자) 도 other도 아닌 어떤 사용자들을 그룹화 해보자

```bash
# test1 user와 test2 user를 developer 그룹으로 묶는 예제

# /var 안에 developer를 만들고 이 폴더의 그룹 권한을 developer로 지정해보자
$ cd /var # 변화가 많은 파일들이 모여있는 디렉토리
$ mkdir developer
Permission denied # 1
$ sudo mkdir developer
$ cd developer
$ echo 'hi my group' > 'group.txt'
Permission denied # 2
$ groupadd developer
Permission denied
$ sudo !! # 3
sudo groupadd developer
$ nano /etc/group # 4
$ usermod -a -G developer test1 # 5
Permission denied
$ sudo !!
$ sudo usermod -a -G developer test2
$ exit 
$ ssh [address] # 다시 접속 해야 수정된 사항 반영!
$ cd /var/developer
$ sudo chown root:developer . # 6
$ echo 'hi my group' > 'group.txt'
Permission denied # 같은 그룹이지만.. 그룹에 쓰기 권한이 없다.
$ sudo chmod g+w . # 현재 디렉토리의 그룹 권한에 w 권한 부여
$ echo 'hi my group' > 'group.txt'
$ ls -al # group.txt 확인 가능
```

1. /var 폴더의 소유자는 

![image](https://user-images.githubusercontent.com/39187116/68993037-9419a480-08b6-11ea-8899-ba18bb93f149.png)

. 부분을 확인 해보면 root 이다. 따라서 /var 디렉토리에 mkdir 프로그램을 실행 하기 위해서는 access mode의 other부분에 쓰기(w) 권한이 있어야 한다.

첫번째 방법은 root 사용자로 로그인 ( <code>su - root</code>) 하여 chmod 명령어를 통해 디렉토리 권한을 바꿔주는 방법(<code>chmod o=rwx /var</code>)이 있지만, 보안상 으로도 좋지 않고 귀찮은 방법 이다. 따라서 현재 사용자에 <code>sudo</code> 프로그램을 이용하여 mkdir 프로그램을 실행하자

2. 1번과 같은 other에 속한 사용자는 w 권한이 없어서 파일을 생성 할 수 없다. 하지만 이 폴더의 그룹을 developer로 변경 후 권한을 부여 한다면 developer에 속한 사용자는 /var/developer 폴더 안에서 자유롭게 읽고 쓰고 실행 하는 권한을 가질 수 있다.
3. <code>groupadd [group name]</code> 을 이용하여 그룹을 추가 하려고 하였지만, 이 명령어는 일반 사용자의 권한으로는 실행 할 수 없다.  sudo 권한을 이용해야 한다. **!! 를 이용하면 이전 명령어를 수행하게 할 수 있다.**
4. <code>/etc/group</code> 파일은 unix 계열의 시스템에서 그룹에 관한 정보를 담고 있는 파일이다. 여기서 그룹 정보를 확인 할 수 있다.
5. <code>usermod</code> 는 user modify의 약자이다. <code>usermod</code> 는 -a ( append ) -G (group) 옵션과 함께 사용자를 그룹에 지정할 수 있다.
6. <code>chown [owner]:[group-name] [directory]</code> : chown  프로그램은 현재 디렉토리의 소유자와 그룹을 변경 할 수 있다. 위 에서는 developer 디렉토리의 group을 developer로 변경한 것 이다.
