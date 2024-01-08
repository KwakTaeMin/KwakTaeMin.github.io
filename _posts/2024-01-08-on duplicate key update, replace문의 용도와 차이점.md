---
layout : single
title : "on duplicate key update, replace문의 용도와 차이점"
categories:
  - database
tags:
  - mysql
  - database
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2024-01-08T1:00:00Z
---

## REPLACE INTO
REPLACE INTO는 중복이 발생되었을 때 기존 레코드를 삭제하고 신규 레코드를 INSERT하는 방식이다.   
기존에 데이터를 삭제한 후 insert 하기에 id가 변경 될 수 있어 좋지 않은 방법인 것 같다.   
- key 중복 시 데이터를 삭제 후 insert
```sql
REPLACE INTO table_name VALUES (a, b, c);
```

## ON DUPLICATE UPDATE
ON DUPLICATE UPDATE의 장점은 중복 키 오류 발생 시 사용자가 원하는 값을 직접 설정할 수 있다는 점이다.
Update를 하기에 id가 변경되지 않는다. 
- key 중복 시 데이터 update
```sql
INSERT INTO table_name VALUES (a,b,c)
ON DUPLICATE KEY UPDATE inserted_cnt = inserted_cnt + 1;
```

### 참조
- [MySQL 중복 레코드 관리 방법 (INSERT 시 중복 키 관리 방법 (INSERT IGNORE, REPLACE INTO, ON DUPLICATE UPDATE))](https://jason-heo.github.io/mysql/2014/03/05/manage-dup-key2.html)


