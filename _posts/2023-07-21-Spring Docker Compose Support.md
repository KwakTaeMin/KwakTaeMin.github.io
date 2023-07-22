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
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-21T1:00:00Z
---

### Spring Docker Compose Support 

Spring initialize 중 새로운게 보여서 확인해보았다.
Spring Application이 시작될때 `docker-compose up` 명령어를 날려주는 것과 같은 기능을 제공하여 준다.
Spring Application이 종료될땐 `docker-compose down` 명령어를 날려주고 Spring Application이 시작될 떄와 종료될 때 docker-compose를 해주는 라이브러리인 것 같다.
Spring 3.대 버전에서만 해당 기능을 사용가능하고 Project에 `docker-compose.yml` 파일 위치를 지정해주면 기능이 작동한다.   



### 참조
- 
