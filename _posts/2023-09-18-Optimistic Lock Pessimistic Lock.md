---
layout : single
title : "Optimistic Lock 낙관적 락 / Pessimistic Lock 비관적 락"
categories:
  - Database
tags:
  - DB
  - Lock
  - Optimistic
  - Pessimistic
  - 낙관적락
  - 비관적락
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-09-18T1:00:00Z
---
## DB 충돌 상황을 개선할 수 있는 방법
- 테이블의 row에 접근 시 Lock을 걸고 다른 Lock이 걸려 있지 않을 경우에만 수정을 가능하게 할 수 있다.
- 수정할 때 내가 이 값을 수정했다고 명시하여 다른 사람이 동일한 조건으로 값을 수정할 수 없게 하는 것

## 비관적 락 (Pessimistic Lock)

비관적 락이란 트랜잭션이 시작될 때 Shared Lock 또는 Exclusive Lock을 걸고 시작하는 방법이다.
즉, Shared Lock을 걸게 되면 write를 하기위해서는 Exclusive Lock을 얻어야하는데 Shared Lock이 다른 트랜잭션에 의해서 
걸려 있으면 해당 Lock을 얻지 못해서 업데이트를 할 수 없다. 
수정하을 하기 위해서는 해당 트랜잭션을 제외한 모든 트랜잭션이 종료(commit) 되어야한다.  
비관적 락은 Repeatable Read 또는 Serializable 정도의 격리성 수준을 제공한다.

![img.png](/assets/images/2309/18-01.png#center)

위의 도식도를 보면서 비관적 락에 대해 이해해보도록한다.

1. Transaction 1에서 table의 Id 2번을 조회
2. Transaction 2에서 table의 Id 2번을 조회
3. Transaction 2에서 table의 Id 2번의 name을 Karol2로 변경 요청
   - 하지만 Transaction 1 에서 이미 Shared Lock을 잡고 있기에 Blocking
4. Transaction 1에서 이미 트랜잭션 해제(commit)
5. Blocking 되어있던 Transaction 2의 update 요청 정상 처리

Transaction을 이용하여 충돌을 예방하는 것이 비관적 락이다.

## 낙관적 락 (Optimistic Lock)

낙관적 락은 DB 충돌 상황을 개션할 수 있는 방법 중 2번째 수정할 때 내가 먼저 이 값을 수정했다고 명시하여 
다른 사람이 동일한 조건으로 값을 수정할 수 없게 하는 것 입니다.. 그런데 잘 보면 이 특징은 DB에서 제공해주는 
특징을 이용하는 것이 아닌 Application Level에서 잡아주는 Lock 입니다.  
다음 포스팅 DB 격리성


### 참조
- [[database] 낙관적 락(Optimistic Lock)과 비관적 락(Pessimistic Lock)](https://sabarada.tistory.com/175)


