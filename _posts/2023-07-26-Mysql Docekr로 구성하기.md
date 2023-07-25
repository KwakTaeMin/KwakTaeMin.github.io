---
layout : single
title : "Mysql Docker로 구성하기"
categories:
  - docker
tags:
  - docker
  - mysql
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-24T1:00:00Z
---

## Mysql Docker로 구성하기 
```yaml
version: '4'
services:
  mysql:
    image: mysql:5.7.42
    platform: linux/amd64
    container_name: mysql-vacation
    ports:
      - "3308:3306"
    env_file:
      - .env
    command:
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    volumes:
      - ./init:/docker-entrypoint-initdb.d
      - ./data:/var/lib/mysql
```
port는 3308로 도커 컨테이너안의 3306과 연결해 주었으며, 로컬에 깔린 mysql 과 포트가 충돌되지 않도록 변경하였습니다.   

volume엔 ./init 폴더와 ./data를 폴더를 연결해두었으며 
/init엔 create_table.sql 파일을 넣어두면 미리 테이블을 만들어서 생성해줍니다.   
/data는 도커 컨테이너가 내려가더라도 저장한 데이터가 삭제되지 않도록 로컬에서 데이터를 관리해주도록 생성하였습니다.
.env 파일은 아래와 같이 설정해주며 mysql에 미리 root 비밀번호와 database schema 명을 설정하고 할 수 있다. 

```properties
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=dbname
MYSQL_USER=sa
MYSQL_PASSWORD=password
```

해당 소스는 [github docker 소스](https://github.com/KwakTaeMin/chat-stomp/tree/master/docker)에 올려두었습니다.   
