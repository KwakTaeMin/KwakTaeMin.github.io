---
layout : single
title : "Kafka 토픽(Topic) (2)"
categories:
  - kafka
tags:
  - kafka
  - 카프카
  - Topic
  - 토픽
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-13T1:00:00Z
---

### Kafka 토픽(topic)

> - 메시지를 구분하는 단위 : 파일 시스템의 폴더와 유사
> - 한 개의 토픽(Totic)은 한 개 이상의 파티션(Partition)으로 구성
> - 프로듀서(Producer)가 토픽(Topic)에 전송
> - 컨슈머(Consumer)는 토픽(Topic)에서 읽는 역할

토픽(Topic)은 RDBMS에서 테이블(Table)과 저장 위치 저장할 대상이라고 생각합니다.   
토픽(Topic)을 생성 시 Kafka Command Line Tool을 이용하여 생성할 예정입니다.      
[kafka command line tool](https://kafka.apache.org/)은 공식 홈페이지에서 다운 받을 수 있습니다.   

### 카프카 토픽 리스트 확인하기
```shell
kafka-topics.sh --bootstrap-server localhost:9092 --list
```
가볍게 위 명령어를 치고 카프카에 CLI가 잘 먹히는지 확인하여 체크한다. [Kafka Docker로 구성하기(0)](https://kwaktaemin.github.io/kafka/Kafka-Docker-%EC%84%A4%EC%B9%98(0)/)을 통해서 설치하였고, 잘 적용되었는지 확인하면 좋을 것 같다.

### 카프카 토픽(Topic) 생성하기
```shell
kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --create --partitions 3 --replication-factor 1
```
가볍게 위 명령어를 분석해보자면 `--bootstrap-server`는 카프카 브로커 서버를 지정해주면 된다. `--topic` 다음에는 토픽 이름 `--create` 생성한다는 의미이다.   
`--partitions` 브로커에 파티션을 3개 지정한다는 것이다 분산 처리를하기위해 하나의 토픽에 저장 시 3개의 파티션으로 나누어 작업을 처리할 수 있는 것과 같다. 
`--replication-factor`는 복제 조건을 뜻합니다. 0은 복제하지않는 정책 1은 하나의 다른 브로커에 복제한다는 의미이고 2는 전체 브로커에 대해 같게 복제하여 안정성을 높인다는 의미입니다.   
생성 후에 위의 리스트 명령어를 작성하게되면 `first_topic`이라고 shell에 표현 될 것 입니다.   

카프카에 저장이 어떻게 되었는지 확인해보겠습니다.   
```shell
taemin ~/Documents/kafka-study/kafka-docker/kafka-log/kafka-logs-32de4d328dc6> ll
total 32
-rw-r--r--  1 taemin  staff     0B  7 14 22:15 cleaner-offset-checkpoint
drwxr-xr-x  6 taemin  staff   192B  7 14 22:17 first_topic-0
drwxr-xr-x  6 taemin  staff   192B  7 14 22:17 first_topic-1
drwxr-xr-x  6 taemin  staff   192B  7 14 22:17 first_topic-2
-rw-r--r--  1 taemin  staff     4B  7 14 22:16 log-start-offset-checkpoint
-rw-r--r--  1 taemin  staff    91B  7 14 22:15 meta.properties
-rw-r--r--  1 taemin  staff     4B  7 14 22:16 recovery-point-offset-checkpoint
-rw-r--r--  1 taemin  staff    52B  7 14 22:17 replication-offset-checkpoint
```
위의 그림에서 보다시피 `first_topic` 0,1,2라고 만들어졌어요 브로커(broker)는 1개이지만 파티션(partitions)이 3개라 하나의 브로커에 저장되있는 것을 볼 수 있습니다.

### 카프카 로그 파일 분석해보기 

```shell
taemin ~/Documents/kafka-study/kafka-docker/kafka-log/kafka-logs-32de4d328dc6/first_topic-0> ll
total 8
-rw-r--r--  1 taemin  staff    10M  7 14 22:17 00000000000000000000.index
-rw-r--r--  1 taemin  staff     0B  7 14 22:17 00000000000000000000.log
-rw-r--r--  1 taemin  staff    10M  7 14 22:17 00000000000000000000.timeindex
-rw-r--r--  1 taemin  staff     8B  7 14 22:17 leader-epoch-checkpoint
```
`first_topic-0` 폴더를 들어가게 되면 다음과 같은 파일 구성이 되어있습니다.    
로그(log)파일에는 세그먼트를 구성하는 실제 데이터가 들어있는 파일입니다. (00000000000000000000.log)   
인덱스(index)파일에는 논리적인 인덱스와 파일의 물리적인 인덱스를 매핑해주는 파일입니다. (00000000000000000000.index)   
파일명에서 등장하는 00000000000000000000는 해당 Segment의 Base Offset입니다.   



더 쓸건데 잠시만요~
`cleanup.policy` 
`retention.ms (default = 604800000 ms) 7일`
`retention.bytes (default = -1)`
`segments`   



### 참조
- [Kafka Topics CLI Tutorial](https://www.conduktor.io/kafka/kafka-topics-cli-tutorial/)  
- [토픽 생성 시 고려사항](https://n1tjrgns.tistory.com/296)
- [Kafka의 Topic, Partition, Segment, Message](https://leeyh0216.github.io/posts/kafka_concept/)