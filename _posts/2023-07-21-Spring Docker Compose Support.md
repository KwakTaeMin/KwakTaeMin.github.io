---
layout : single
title : "Spring Docker Compose Support"
categories:
  - spring
tags:
  - spring
  - springboot
  - DockerComposeSupport
  - Support
  - Spring3.0
  - DockerCompose
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-21T1:00:00Z
---

### Spring Docker Compose Support 

![img.png](/assets/images/2307/13-1.png#center)

Spring initialize 중 새로운게 보여서 확인해보았다.
Spring Application이 시작될때 `docker-compose up` 명령어를 날려주는 것과 같은 기능을 제공하여 준다.
Spring Application이 종료될땐 `docker-compose down` 명령어를 날려주고 Spring Application이 시작될 떄와 종료될 때 docker-compose를 해주는 라이브러리인 것 같다.
`Spring Boot 3` 버전에서만 해당 기능을 사용가능하고 Project에 `docker-compose.yml` 파일 위치를 지정해주면 기능이 작동한다.   
참고로 `Spring Boot 3`은 `JDK 17` 이상을 사용해야만 합니다.
```gradle
dependencies {
    developmentOnly("org.springframework.boot:spring-boot-docker-compose")
}
```

`dependencies`에 해당 `spring-boot-docker-compose` 사용하여 컨테이너를 관리할 수 있습니다.   
해당 모듈을 추가하게 되면 다음과 같은 일을 하게 됩니다.
- 애플리케이션 디렉토리에서 `compose.yml` 및 기타 일반적인 작성 파일 이름을 검색합니다.
- compose.yml로 작성된 것을 통해 `docker compose up` 명령을 Call 합니다.
- 지원되는 각 컨테이너에 대한 서비스 연결 Bean 작성
- 에플리케이션이 종료될 때 `docker compose stop` 명령을 Call 합니다. 

> Spring Boot의 지원이 올바르게 작동하려면 docker compose 또는 docker-compose CLI 애플리케이션이 경로에 있어야 합니다.
> 혹은 전역 변수로 설정되어 있다면 문제 없을 것으로 보여집니다. 

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
      KAFKA_ADVERTISED_HOST_NAME: localhost
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./kafka-log:/kafka
    restart: unless-stopped
```

저 같은 경우에는 [kafka Docker로 설치하기(0)]() 이용하여 SpringBoot 기능에 Docker Compose Support 기능을 추가해볼 예정입니다.   
해당 파일을 `compose.yaml` 으로 지정만해두면 application이 시작될 때나 종료될 때 `docker compose up` , `docker compose stop`을 통해 컨테이너를 관리해주며 편리하게 개발할 수 있게 될 것 같습니다.   
앞으로 3.대 Spring Boot를 사용하며 많이 사용할 것 같아서 좋을 것 같습니다.


### 참조
- [Spring Docker Compose](https://spring.io/blog/2023/06/21/docker-compose-support-in-spring-boot-3-1)
- [Spring Docs Docker Compose](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#features.docker-compose)
