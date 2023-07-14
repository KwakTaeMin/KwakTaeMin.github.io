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
```shell
kafka-topics.sh --bootstrap-server localhost:9092 --topic first_topic --create --partitions 3 --replication-factor 1
```
가볍게 위 명령어를 분석해보자면 `--bootstrap-server`는 카프카 브로커 서버를 지정해주면 된다. `--topic` 다음에는 토픽 이름 `--create` 생성한다는 의미이다.   
`--partitions` 브로커에 파티션을 3개 지정한다는 것이다 분산 처리를하기위해 하나의 토픽에 저장 시 3개의 파티션으로 나누어 작업을 처리할 수 있는 것과 같다. 
`--replication-factor`는 복제 조건을 뜻합니다. 0은 복제하지않는 정책 1은 하나의 다른 브로커에 복제한다는 의미이고 2는 전체 브로커에 대해 같게 복제하여 안정성을 높인다는 의미입니다.   

더 쓸건데 잠시만요~

### 참조
- [Kafka Topics CLI Tutorial](https://www.conduktor.io/kafka/kafka-topics-cli-tutorial/)  