---
layout : single
title : "Kafka Docker로 구성하기 (0)"
categories:
  - kafka
tags:
  - kafka
  - docker
  - 설치
  - 카프카
  - 도커
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-07T1:00:00Z
---

### Kafka 란?

![img.png](/assets/images/2307/11-1.png#center)

Apache Kafka는 별도의 시작이나 끝이 없는 스트리밍 이벤트 데이터 또는 일반 데이터를 수집, 처리, 저장하는 데 널리 사용되는 이벤트 스트리밍 플랫폼입니다   
카프카에 대해 공부해보는 시간을 가지고 싶어 하나하나 파악해보는 포스팅을 시작할 예정입니다.   
도커로 구성 후 기능파악을 해보는 시간을 가지도록 하겠습니다.   

카프카 도커로 설치부터해서 직접 사용하여 여러가지 만들어 보는 시간을 가져보려고 합니다   
공부도하고 포스팅도하고 시작해보겠습니다~

***

### Kafka Docker 설치하기
   
도커로 설치하는 이유는 매우 간단하고 어느 환경에서든 바로 설치가 가능하기에 `docker`로 설치하는 것을 선호한다.   
여유가 되시는 분은 직접 설치하고 사용하는 것도 좋지만 다른 환경에서도 바로바로 설치가 쉽기에 `docker-compose`를 이용할 예정입니다.   
`docker-compose`를 이용하여 쉽게 카프카를 설치할 수 있도록 제공해주는 `repository`가 있어서 이를 이용하려 합니다.

위의 해당 명령어를 `terminal`에 입력하여 source를 받고 해당 [주소](https://github.com/wurstmeister/kafka-docker)로 접속하여 README.md를 읽어보면 상세하게 설명되어있습니다.   
READMD.md에서 읽어보면 사전 준비가 필요합니다.

> #### 사전준비 
>- [docker-compose 설치](https://docs.docker.com/compose/install/)
>- 도커 호스트 IP와 일치하도록 `docker-compose.yml`에서 `KAFKA_ADVERTISED_HOST_NAME`을 수정합니다(참고: 여러 브로커를 실행하려는 경우 호스트 IP로 localhost 또는 127.0.0.1을 사용하지 마십시오.) 
>- Kafka 매개변수를 사용자 지정하려면 `docker-compose.yml`에 환경 변수로 추가하기만 하면 됩니다. `message.max.bytes` 매개변수를 늘리려면 환경을 `KAFKA_MESSAGE_MAX_BYTES: 2000000`으로 설정하십시오.
>- 자동 `Topic` 생성을 끄려면 `KAFKA_AUTO_CREATE_TOPICS_ENABLE: 'false'`를 설정합니다
>- Kafka의 log4j 사용은 `LOG4J_`로 시작하는 환경 변수를 추가하여 사용자 정의할 수 있습니다. 이는 `log4j.properties`에 매핑됩니다.
>  - 예시) `LOG4J_LOGGER_KAFKA_AUTHORIZER_LOGGER=DEBUG, authorizerAppender`
   
위 내용은 해당 [github repository](https://github.com/wurstmeister/kafka-docker) README.md 파일 번역 한 것과 같아요 ㅎㅎ
브로커를 여러개 사용한다는 개념은 카프카 서버를 여러 대 사용한다는 뜻과 같습니다. 그럴때에는 호스트 IP를 localhost로 설정하지 않는 것입니다.   
`Topic`은 카프카의 `MySql`과 비교하자면 `Table`과 같은 것입니다. 해당 `Topic`의 이름을 자동 생성하지 않을 거면 해당 설정을 false로 설정해달라는 것과 같아요.   
마지막 사전 준비의 내용은 `log4j`의 추가 설정 방법을 `docker-compose`에 설정하는 방법을 안내해주는 것 같아요   
예시와 설정하게되면 `log4j.properties` 파일에 내용에 들어가게 되는 거죠. 

```
git clone https://github.com/wurstmeister/kafka-docker 
```

위의 명령어로 소스도 받고 이제 시작 해볼까요?   
사용방법은 간단합니다. 해당 폴더에 들어가서 명령어를 이용하면 됩니다.
- 시작 `docker-compose up -d`
- 중지 `docker-compose stop`
- 여러대의 브로커를 사용하여 실행할 경우 `docker-compose scale kafka=3`

중요한거는 `docker-compose` 버전이 `2`이상이여야 위의 명령어가 통하는 것 같아요   
저 같은 경우에도 버전이 `1.x` 대 여서 올렸는데 `docker-desktop`에서 쉽게 업데이트가 되더라구요 이용해보시면 좋을 것 같습니다   
`docker-compose -v` 혹은 `docker-compose --version` 명령어를 통해 버전 확인 후 사용해야합니다.   
해당 명령어는 해당 docker-compose.yml 파일을 통하여 실행됩니다. 추가적으로 설정하거나 변경해야할 부분이 있다면 해당 내용을 수정하여 사용합니다. 
```yaml
version: '2'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    ports:
      - "2181:2181"
    restart: unless-stopped

  kafka:
    build: .
    ports:
      - "9092"
    environment:
      DOCKER_API_VERSION: 1.22
      KAFKA_ADVERTISED_HOST_NAME: 192.168.99.100
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    restart: unless-stopped
```
위의 보시면 `zookeeper` 와 `kafka` 두개가 보이는데 `kafka`를 설치하기 위해서는 `zookeeper`가 필수입니다.   
분산처리를 도와주는 도구정도라고만 이해하고 넘어가는 것도 좋은 것 같아요   
여러대의 `kafka broker`를 관리해준다고만 생각해줘도 좋을 것 같습니다. 필요할 때 더 설명해보겠습니다.   
`docker-compose up -d` 명령어를 치게되면 `kafka` `zookeeper` 이미지를 다운받고 서버를 올려줍니다.   
처음에만 이미지를 다운받으니 다음부터는 빠르게 실행되는 것을 볼 수 있습니다. 
`docker-compose stop` 명령어를 통해 중단 시키고나서 다음 포스팅에 `kafka`에 대한 다른 기능들도 사용해보도록하겠습니다. 
    
`broker`를 늘려서 실행하는 방법은 안되네요 확인해보고 다시 작성해야할 것 같아요   

   

### 참조 
- [카프카 도커 설치 참조 블로그](https://tommypagy.tistory.com/226) 
- [wurstmeister github repository](https://github.com/wurstmeister/kafka-docker) 
