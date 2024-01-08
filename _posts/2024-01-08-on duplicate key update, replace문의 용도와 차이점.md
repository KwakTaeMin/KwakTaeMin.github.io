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
중복 키 위반이 발생하면(즉, 동일한 주 키 또는 고유한 키를 가진 레코드가 이미 존재하는 경우), MySQL은 기존 레코드를 삭제한 다음 새 레코드를 삽입합니다.   
본질적으로 `REPLACE`는 중복된 레코드를 처리하기 위해 `INSERT`와 `DELETE`를 결합한 것과 같습니다.
- key 중복 시 데이터를 삭제 후 insert
```sql
REPLACE INTO 테이블명 (id, column1, column2) VALUES (1, '값1', '값2');
```

## ON DUPLICATE UPDATE
ON DUPLICATE UPDATE의 장점은 중복 키 오류 발생 시 사용자가 원하는 값을 직접 설정할 수 있다는 점이다.
`UPDATE` 하기에 id가 변경되지 않는다.
`ON DUPLICATE KEY UPDATE`를 사용하면 중복된 키 위반이 발생하면 특정 열을 업데이트할 수 있는 열-값 할당 집합을 제공할 수 있습니다.

- key 중복 시 데이터 update
```sql
INSERT INTO 테이블명 (id, column1, column2)
VALUES (1, '값1', '값2')
  ON DUPLICATE KEY UPDATE column1 = '새로운값1', column2 = '새로운값2';
```
## 주요 차이
- REPLACE는 항상 기존 레코드를 삭제하고 새로운 레코드를 삽입하지만 ON DUPLICATE KEY UPDATE는 업데이트 동작을 사용자 정의할 수 있습니다.
- ON DUPLICATE KEY UPDATE는 일반적으로 특정 열만 업데이트하려는 경우에 더 효율적입니다. 이는 전체 레코드를 교체하는 대신 특정 열만 업데이트할 수 있기 때문입니다.

### 참조
- [MySQL 중복 레코드 관리 방법 (INSERT 시 중복 키 관리 방법 (INSERT IGNORE, REPLACE INTO, ON DUPLICATE UPDATE))](https://jason-heo.github.io/mysql/2014/03/05/manage-dup-key2.html)


