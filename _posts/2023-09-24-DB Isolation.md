---
layout : single
title : "트랜잭션의 격리수준(isolation level)"
categories:
  - database
tags:
  - isolation
  - transaction
  - mysql
  - database
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-09-24T1:00:00Z
---

## 트랜잭션의 격리수준(transaction isolation level)

트랜잭션의 격리 수준(isolation level)이란 여러 트랜잭션이 동시에 처리될 때, 특정 트랜잭션이
다른 트랙잭션에서 변경하거나 조회하는 데이터를 볼 수 있게 허용할지 여부를 결정하는 것이다.
트랜잭션의 격리 수준은 격리(고립) 수준이 높은 순서대로 SERIALIZABLE, REPEATABLE READ, READ COMMITED, READ UNCOMMITED가 존재한다.
참고로 아래의 예제들은 모두 자동 커밋(AUTO COMMIT)이 false인 상태에서만 발생한다.

- SERIALIZABLE
- REPEATABLE READ
- READ COMMITED
- READ UNCOMMITED

### SERIALIZABLE
SERIALIZABLE은 가장 엄격한 격리 수준으로, 이름 그대로 트랜잭션을 순차적으로 진행시킨다.
SERIALIZABLE에서 *여러 트랜잭션이 동일한 레코드에 동시 접근할 수 없으므로,* 어떠한 데이터 부정합 문제도 발생하지 않는다.
하지만 *트랜잭션이 순차적으로 처리되어야 하므로 동시 처리 성능이 매우 떨어진다.*   
MYSQL에서 SELECT FOR SHARE/UPDATE는 대상 레코드에 각각 읽기/쓰기 잠금을 거는 것이다.
하지만 순수한 SELECT 작업은 아무런 레코드 잠금없이 실행되는데, 잠금 없는 일관된 읽기(Non-locking consistent read)란
순수한 SELECT 문을 통한 잠금 없는 읽기를 의미하는 것이다.

하지만 SERIALIZABLE 격리 수준에서는 *순수한 SELECT 작업에서도 대상 레코드에 넥스트 키 락을 일기 작금(Shared Lock)으로 건다.*
따라서 한 트랜잭션에서 넥스트 키 락이 걸린 레코드를 다른 트랜잭션에서는 절대 추가/수정/삭제할 수 없다.
SERIALIZABLE은 가장 안전하지만 가장 성능이 떨어지므로, 극단적으로 안전한 작업이 경우가 아니라면 사용해서는 안된다.



### REPEATABLE READ

일반적은 RDBMS는 변경 전의 레코드를 언두 공간에 백업해둔다. 그러면 변경 전/후 데이터가 모두 존재하므로, 동일한 레코드에
대해 여러 버전의 데이터가 존재한다고 하여 이를 MVCC(Multi-Version Concurrency Control, 다중 버전 동시성 제어)라고 부른다.
MVCC를 통해 트랜잭션이 롤백된 경우에 데이터를 복원할 수 있을 뿐만 아니라, 서로 다른 트랜잭션 간에 접근할 수 있는 데이터를 세밀하게 제어할 수 있다.
각각의 트랜잭션은 순차 증가하는 고유한 트랜잭션 번호가 존재하며, 백업 레코드에는 어느 트랜잭션에 의해 백업되었는지 트랜잭션 번호를 함께 저장한다. 
그리고 해당 데이터가 불필요해진다고 판단하는 시점에 주기적으로 백그라운드 쓰레드를 통해 삭제한다.

REPEATABLE READ는 MVCC를 이용해 *한 트랜잭션 내에서 동일한 결과를 보장하지만, 새로운 레코드가 추가되는 경우에 부정합이 생길 수 있다.*
이러한 REPEATABLE READ의 동작 방식을 자세히 살펴보도록하자   

예를들어 트랜잭션을 시작하고, id=50인 레코드를 조회하면 1건 조회되는 상황이라고하자 아직 트랜잭션을 종료되지 않았다. 참고로 트랜잭션을 시작한
SELECT와 그렇지 않은 SELECT 차이는 뒤에서 다시 살펴보도록하자.

![img.png](/assets/images/2309/28-01.png#center)

그리고 이 때 다른 사용자 A의 트랜잭션에서 id=50인 레코드를 갱신하는 상황이라고 하자 그러면 MVCC를 통해 기존 데이터는 변경되지만,
백업된 데이터가 언두 로그에 남게 된다.

![img_1.png](/assets/images/2309/28-02.png#center)

이전에 사용자 B가 데이터를 조회했던 트랜잭션은 아직 종료되지 않은 상황에서, 사용자 B가 다시 한번 동일한
SELECT 문을 실행하면 어떻게 될까? 그 결과 다음과 같다.

![img_2.png](/assets/images/2309/28-03.png#center)

사용자 B의 트랜잭션은(10) 사용자 A의 트랜잭션(12)가 시작하기 전에 이미 시작된 상태다.
이 때 REPEATABLE READ는 트랜잭션 번호를 참고하여 자신보다 먼저 실행된 트랜잭션의 데이터만을
조회한다. 만약 테이블에 자신보다 이후에 실행된 트랜잭션의 데이터가 조회한다면 언두 로그를 참고해서 데이터를 조회한다.   

따라서 사용자 A의 트랜잭션이 시작되고 커밋까지 되었지만, 해당 트랜잭션(12)는 현재 트랜잭션(10)보다
나중에 실행되었기 때문에 조회 결과로 기존과 동일한 데이터를 얻게 된다. 
*즉 REPEATABLE READ는 어떤 트랜잭션이 읽은 데이터를 다른 트랜잭션이 수정하더라도 동일한 결과를 반환할 것을 보장해준다.*
   
앞서 설명했듯이 REPEATABLE READ는 새로운 레코드의 추가까지는 막지 않는다고 하였다. 따라서
*SELECT로 조회한 경우 트랜잭션이 끝나기 전에 다른 트랜잭션에 의해 추가된 레코드가 발결될 수 있는데 이를 Phantom Read라고 한다.* 
하지만 MVCC 덕분에 일반적인 조회에서 유령 읽기는 (Pahntom Read)는 발생하지 않는다. 왜냐하면 자신보다 나중에 실행된 트랜잭션아ㅣ
추가한 레코드는 무시하면 되기 때문이다. 이러한 상황을 그림으로 표현하면 다음과 같다. 

![img_3.png](/assets/images/2309/28-04.png#center)

그렇다면 언제 유령 읽기가 발생하는 것일까? 바로 자금이 사용되는 경우이다. MySQL은 다른 RDBMS와 다르게 특수한 갭 락이 존재하기 때문에,
동작이 다른 부분이 있으므로 일반적인 RDBMS 경우 부터 살펴보도록 한다.
   
*마찬가지로 사용자 B가 먼저 데이터를 조회하는데, 이번에는 SELECT FOR UPDATE를 이용해 쓰기 잠금을 걸었다.
여기서 SELECT ... FOR UPDATE 구문은 베타적 잠금(비관적 잠금, 쓰기 잠금)을 거는 것이다.
읽기 잠금을 걸려면 SELECT FOR SHARE 구문을 사용해야 한다. 락은 트랜잭션이 커밋 또는 롤백될 때 헤제된다.*   

그리고 사용자 A가 새로운 데이터를 INSERT하는 상황이라고 하자. 일반적인 DBMS에서는 갭락이 존재하지 않으므로 id=50인 레코드만 잠금이 걸린 상태이고,
사용자 A의 요청은 잠금 없이 즉시 실행된다. 

![img_4.png](/assets/images/2309/28-05.png#center)

이 때 사용자 B가 동일한 쓰기 잠금 쿼리로 다시 한번 데이터를 조회하면, 이번에는 2건의 데이터가 조회된다.
동일한 트랜잭션 내에서도 새로운 레코드가 추가되는 경우에 조회 결과가 달라지는데, 이렇듯 다른 트랜잭션에서 수행한
작업에 의해 레코드가 안보였다 보였다 하는 현상을 Phantom Read(유령 읽기)라고 한다. 이는 다른 트랜잭션에서
새로운 레코드를 추가하거나 삭제하는 경우 발생할 수 있다. 

![img_5.png](/assets/images/2309/28-06.png#center)

이 경우에도 MVCC를 통해 해결될 것 같지만, 두 번째 실행되는 SELECT FOR UPDATE 때문에 그럴 수 없다.
MVCC에서는 데이터를 먼저 테이블에 반영하고, 언두 로그에 백업한다. 즉, SELECT FOR UPDATE로 잠금을 걸어도 테이블에는 반영이 되고
언두 로그에는 다른 트랜잭션에 의한 데이터가 계속해서 쌓이는 것이다. 만약 먼저 시작된 트랜잭션이 존재하여 작업을 하면 테이블에는 반영되고, 언두 로그에는
이전 트랜잭션의 데이터가 쌓인다. 그러므로 MVCC만으로 정확한 데이터 제공이 불가능하고 언두 로그에도 잠금을 걸어야 하는데, 언두 로그는 append only 형태이므로 잠글 수 없다.
따라서 SELECT FOR UPDATE 나 SELECT FOR SHARE로 레코드를 조회하는 경우에는 언두 영역의 데이터가 아니라
테이블의 레코드를 가져오게 되고, 이로 인행 Phantom READ가 발생하는 것이다.

하지만 MySQL에는 갭 락이 존재하기 때문에 위의 상황에서 문제가 발생하지 않는다. 
사용자 B가 SELECT FOR UPDATE로 데이터를 조회한 경우에 MySQL은 id 50인 레코드에는 레코드 락,
id가 50보다 큰 범위에는 갭 락으로 넥스트 키 락을 건다. 따라서 사용자 A가 id 51인 member를 insert를 시도한다면,
B의 트랜잭션이 종료(커밋 또는 롤백)될 때 까지 기다리다가, 대기를 치나치게 오래 하면 락 타임 아웃이 발생하게 된다.

![img_6.png](/assets/images/2309/28-07.png#center)

따라서 *일반적으로 MySQL의 REAPEATABLE READ에서는 Phantom Read가 발생하지 않는다.*
MySQL에서 Phatom Read가 발생하는 거의 유일한 케이스는 다음과 같다.

사용자 B는 트랜잭션을 시작하고 잠금없는 SELECT 문으로 데이터를 조회하였다. 그리고 
사용자 A는 INSERT 문을 사용해 데이터를 추가하였다. 이 때 잠금이 없으므로 바로 COMMIT된다. 하지만
사용자 B가 SELECT FOR UPDATE 로 조회르 ㄹ했다면, 언두 로그가 아닌 테이블로부터 레코드를 조회하므로 Phantom Read가 발생한다.


![img_7.png](/assets/images/2309/28-08.png#center)

하지만 이러한 케이스는 거의 존재하지 않으므로 MySQL의 REPEATABLE READ에서는 PHANTOM READ가 발생하지 않는다고 봐도 된다. 

- SELECT FOR UPDATE 이후 SELECT : 갭락 때문에 팬텀리드 X
- SELECT FOR UPDATE 이후 SELECT FOR UPDATE : 갭락 때문에 팬텀리드 X
- SELECT 이후 SELECT : MVCC 때문에 팬텀리드 X
- SELECT 이후 SELECT FOR UPDATE : 팬텀 리드 O

마지막으로 트랜잭션 내에서 실행되는 SELECT와 트랜잭션 없이 실행되는 SELECT의 차이를 살펴보도록 하자.
REPEATABLE READ에서는 트랜잭션 번홀ㄹ 바탕으로 실제 테이블 데이터와 언두 영역의 데이터 등을 비교하며
어떤 데이터를 조회할 지 판단한다. 즉 트랜잭션 안에서 실행되는 SELECT라면 항상 일관된 데이터를 조회하게 된다.
하지만 트랜잭션 없이 실행된다면, 데이터의 정합성이 깨지는 상황이 생길 수 있다. 커밋된 데이터만을 보여주는 READ COMMITTED 수준에서 둘의 차이가 거의없다.


### READ COMMITTED

READ COMMITTED는 *커밋된 데이터만 조회*할 수 있다. READ COMMITTED는 REPEATABLE READ에서 발생하는
*Phantom Read에 더해 Non-Repeatable Read(반복 읽기 불가능) 문제까지 발생*한다. 
예를 들어 사용자 A가 트랜잭션을 시작하여 어떤 데이터를 변경하였고, 아직 커밋은 하지 않은 상태라고 하자.
그러면 테이블은 먼저 갱신되고, 언두 로그로 변경 전이 데이터가 백업되는데, 이를 그림으로 표현하면 다음과 같다.

![img.png](/assets/images/2309/28-09.png)

이 때 사용자 B가 데이터를 조회하려고 하면, READ COMMITTED에서는 커밋된 데이터만 조회할 수 있으므로, 
REPEATABLE READ와 마찬가지로 언두 로그에서 변경 전의 데이터를 찾아서 반환하게 된다.
최정적으로 사용자 A가 트랜잭션을 커밋하면 그 때 부터 다른 트랜잭션에서도 새롭게 변경된 값을 참조할 수 있다.

![img_1.png](/assets/images/2309/28-10.png)

하지만 READ COMMITTED는 Non-Repeatable Read(반복 읽기 불가능) 문제가 발생할 수 있다.
예를 들어 사용자 B가 트랜잭션을 시작하고 name = "Minkyu"인 레코드를 조회했다고 하자.
해당 조회 조건을 만족하는 레코드는 아직 존재하지 않으므로 아무 것도 반환되지 않았다.

그러다가 사용자 A가 UPDATE 문을 수행하여 해당 조건을 만족하는 레코드가 생겼다고 하자. 
사용자 A의 작업은 커밋까지 완료된 상태이다. 이 때 사용자 B가 다시 동일한 조건으로 레코드를 조회하면
어떻게 될까? READ COMMITTED 는 커밋된 데이터는 조회할 수 있도록 허용하므로 결과가 나오게 된다.

![img_2.png](/assets/images/2309/28-11.png)

READ COMMITTED에서 반복 읽기를 수행하면 다른 트랜잭션의 커밋 여부에 따라 조회 결과가 달라 질 수 있다.
따라서 이러한 데이터 부정합 문제를 Non-Repeatable Read(반복 읽기 불가능)라고 한다.
Non-Repeatable Read는 일반적인 경우에는 크게 문제가 되지 않지만, 하나의 트랜잭션에서 동일한 데이터를
여러 번 읽고 변경하는 작업이 금전적인 처리와 연결되면 문제가 생길 수 있다. 

예를 들어 어떤 트랜잭션에서는 오늘 입금된 총 합을 계산하고 있는데, 다른 트랜잭션에서 계속해서 입금 내역을 커밋하는
 상황이라고 하자. 그러면 READ COMMITTED에서는 같은 트랜잭션일지라고 조회할 때 마다 입금된 내역이 달라지므로 문제가
생길 수 있는 것이다 따라서 격리 수준이 어떻게 동작하는지, 그리고 격리 수준에 따라 어떠한 결과가 나오는지 예측할 수 있어야 한다.
READ COMMITTED 수준에서는 애초에 커밋된 데이터만 읽을 수 있기 때문에 트랜잭션 내에서 실행되는 SELECT와 트랜잭션 밖에서
실행되는 SELECT의 차이가 별로 없다.

### READ UNCOMMITTED 

READ UNCOMMITTED는 *커밋하지 않은 데이터 조차도 접근할 수 있는 격리 수준*이다.
READ UNCOMMITTED에서는 다른 트랜잭션의 작업이 커밋 또는 롤백되지 않아도 즉시 보이게 된다.
예를 들어 사용자 A의 트랜잭션에서 INSERT를 통해 데이터를 추가했다고 하자. 아직 커밋 또는 롤백이
되지 않은 상태임에도 불구하고 READ UNCOMMITTED는 변경된 데이터에 접근할 수 있다. 이를
그림으로 표현하면 다음과 같다.

![img_3.png](/assets/images/2309/28-12.png)

이렇듯 어떤 트랜잭션의 작업이 완료되지 않았는데도, 다른 트랜잭션에서 볼 수 있는 부정합 문제를
Dirty Read(오손 읽기)라고 한다. Dirty Read는 데이터가 조회되었다가 사라지는 현상을 초래하므로
시스템에 상당한 혼란을 주게 된다. 만약 위의 경우에 사용자 A가 커밋이 아닌 롤백을 수행한다면 어떻게
될까?

![img_4.png](/assets/images/2309/28-13.png)

사용자 B의 트랜잭션은 id=51인 데이터를 계속 처리하고 있을 텐데, 다시 데이터를 조회하니 결과가 존재하지 않는
상황이 생긴다. 이러한 Dirty Read 상황은 시스템에 상당한 버그를 초래할 것이다. 그래서 READ UNCOMMITTED는 
RDBMS 표준에서 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준이다. 따라서 MySQL에서 사용한다면 최소한
READ COMMITTED 이상의 격리수준을 사용해야 한다.


### 트랜잭션 격리 수준(Transaction Isolation Level) 요역

앞서 살펴본 내용을 정리하면 다음과 같다. READ UNCOMMITTED는 부정합 문제가 지나치게 발생하고
SERIALIZABLE은 동시성이 상당히 떨어지므로 READ COMMITTED 또는 REPEATABLE READ를 
사용하면 된다. 참고로 오라클에서는 READ COMMITTED 기본으로 사용하며 MySQL에서는 REPEATABLE READ
를 기본으로 사용한다.


|                  | Dirty Read | Non-Repeatable | Phantom Read     |
|------------------|--------|----------------|------------------|
| READ UNCOMMITTED | 발생     | 발생             | 발생               |
| READ COMMITTED   | 없음     | 발생             | 발생               | 
| REPEATABLE READ  | 없음     | 없음             | 발생(MySQL 거의 없음)  | 
| SERIALIZABLE     | 없음     | 없음             | 없음               | 

격리 수준이 높아질수록 MySQL 서버의 처리 성능이 많이 떨어질 것으로 생각하는데, 사실 SERIALIZABLE이 
아니라면 크게 성능 개선 및 저하는 발생하지 않는다. 그 이유는 결국 언두 로그를 통해 레코드를 참조하는 과정이
거의 동일하기 때문이다. 따라서 MySQL은 갭 락을 통해 Phantom Read까지 거의 발생하지 않고, READ COMMITTED보다는
동시 처리 성능은 뛰어난 REPEATABLE READ 를 사용한다.



### 참조
- [[MySQL] 트랜잭션의 격리 수준(Isolation Level)에 대해 쉽고 완벽하게 이해하기](https://mangkyu.tistory.com/299)


