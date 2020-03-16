---
title: Redis 훑어보기
date: 2020-03-16 14:03:66
category: playground
---

## Redis 훑어 보기

<br/>

### 인트로

1. Redis 는 Remote Dictionary Server의 약자.
2. Redis는 key-value 형식의 Map 데이터 저장소. (NoSQL)

3. 인터넷 통신의 80%가 불과 20%의 사이트에 대한 엑세스로 추정되며, 이  20%의 웹 사이트 데이터를 캐시해두면 효율을 극적으로 향상 가능 ( 파레토의 법칙 ) -> Redis는 캐시솔루션으로 자주 사용됌

4. Redis는 **In-Memory 데이터베이스** , 기존 RDB는 디스크에 접근하기 때문에 속도가 느림.  Redis는 메모리에 상주하여 돌아가기 때문에 빠름.
5. 하지만 빠른건 특징 중 일부분. 다른 In-Memory 데이터 베이스와 다른점은 **다양한 자료구조** 를 지원 한다는 점!

<br/>

### 그래서 장점이 뭐냐면,,

![image](https://user-images.githubusercontent.com/39187116/76722704-23ad6200-6788-11ea-8cb5-ae14d1bb1899.png)

1. 만약 실시간 랭킹 서버를 구현 한다면?

   RDB는 데이터 저장 -> 저장된 데이터 SCORE 값으로 정렬 -> 읽기

   Redis는 Sorted-Set 자료구조 이용 ( **RDB보다 더 빠르고 간단**한 방법이 될 것 -> 개발 편의성 생산성 UPUPUP )

2. Redis는 트랜잭션의 문제도 해결 해준다.

    Redis는 싱글 스레드 기반으로 동작하기 때문에 **atomic** 함. 

<br/>

### Redis 서버가 down 되면 어떻게 대처 하지?

Redis는 데이터를 disk에 저장 할 수 있음. 여기에 2가지 방식이 있음

##### 첫번째는 Snapshot 방식

1. 사용자가 지정한 interval 마다 스냅샷을 찍어서 디스크에 .rdb파일로 저장
2. 특정 시점의 데이터를 파일로 저장하기 때문에 빠른 백업 가능
3. 하지만 interval 사이에 들어온 데이터는 보존 할 수 없음

##### 두번째는 AOF 방식

1. Write operation에 대한 로그를 파일로 남김
2. Redis가 Setup 될 때 로그를 읽어서 원본 dataset을 복구하는 방식
3. 하지만 일반적으로 Snapshot 방식 보다 느림

그럼 둘 중에 뭘 사용해야 하지? 레디스 공식문서는 **두 방식을 혼용해서 사용하기를 권장**. 주기적으로 Snapshot 방식으로 백업하고 interval 사이에 AOF 방식으로 로깅

<br/>

### 그럼 Redis 서버는 어떤 방식으로 구성할까?

![image-20200316140336733](/Users/hong/Library/Application Support/typora-user-images/image-20200316140336733.png)

- Redis Topology

Redis는 **Master-Slave**형태로 데이터를 복제해서 운영 가능 -> 데이터 복제는 non-blocking 상태로 이루어짐

- Redis Sharding

데이터를 샤딩하여 각 Redis 서버에 나누어 저장하여 각 서버에 있는 부하를 줄일 수 있음.

- **Redis Cluster**

>잠깐..cluster란?
>
>1. 각기 다른 서버를 하나로 묶어 하나의 시스템처럼 동작하게 함으로써 클라이언트에게 고가용성을 제공하는 것을 말함.
>2. 여러대의 서버에 데이터가 분산되어 저장되므로 트레픽이 분산되는 효과를 얻을 수 있음.
>3. 특정 서버에 장애가 일어나더라도 백업 서버의 보완을 통해 데이터의 유실없이 서비스를 이어나갈 수 있음.

1. Cluster를 구축 할 때에는 master 노드만 이용하면 안된다. > 한 노드에 장애가 발생하면 그 노드는 gg

2. Slave 노드를 함께 구성하면 master 노드의 데이터를 복제하여 갖고 있기 때문에 master 노드가 장애가 발생 하더라도 slave 노드가 master로 승격하여 중단없이 서비스를 제공 할 수 있음.

3. **Master와 Slave를 한 서버에 구성 하면 안된다.** 만약 한 서버에 구성하면 서버가 터졌을 때 둘다 사망하기 때문에 데이터 복구 할 수 없기 때문





#### Reference

- https://meetup.toast.com/posts/224?fbclid=IwAR21cLpXvgQAJg0ljXdsy-hh7C7NR_cWByVAZiN-JdUIuGSuaouYtAk32Tk
- https://engkimbs.tistory.com/869
- https://nakjunizm.com/2017/10/10/Redis_Backup_And_Restore/
- https://daddyprogrammer.org/post/1601/redis-cluster/
