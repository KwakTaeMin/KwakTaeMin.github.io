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

그리고 이 때 다른 사용자 A의 트랜잭션에서 id=50인 레코드를 갱신하는 상황이라고 하자 그러면 MVCC를 통해 기존 데이터는 변경되지만,
백업된 데이터가 언두 로그에 남게 된다.


...


### 참조
- [[MySQL] 트랜잭션의 격리 수준(Isolation Level)에 대해 쉽고 완벽하게 이해하기](https://mangkyu.tistory.com/299)


