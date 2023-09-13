---
layout : single
title : "Java Collection Map"
categories:
  - java
tags:
  - java
  - Map
  - HashMap
  - HashTable
  - ConcurrentHashMap
  - LinkedHashMap 

toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-09-13T1:00:00Z
---

## HashMap
> 데이터를 저장할 때 Key-Value 쌍으로 저장
> Key 중복 안됌
> Null 허용
> 동기화 X 멀티스레드 환경 주의 필요

- HashMap 여러 쓰레드에서 동시 접근 시 생기는 문제점

1. 해시 출돌이 발생하는 경우, 같은 인덱스에 여러 데이터가 중복 저장될 수 있다. 
이 때는 링크드 리스트를 이용하여 데이터를 추가로 연결하는데, 데이터가 많을수록 검색속도가 느려진다.
일정 이상의 링크드 리스트를 가지게 되면, 해당 인덱스에 대한 모든 데이터를 새로운 위치로 이동하여 rehasing 현상이 발생한다. 
2. 한 스레드 HaspMap의 값을 수정하는 도중에 다른 스레드가 같은 위치에 있는 값을 수정한다면, 값이 덮어써지는 문제 발생
3. 여러 쓰렏에서 동시에 HashMap에 데이터를 추가하면, 배열의 크기가 늘어나야하는 사오항에서도 여러 쓰레드가 동시에 배열의 크기를 조정하려고 할 수 있다.
이러한 경우에 서로 다른 스레드가 동시에 배열의 크기를 증가시키려고 할 수 있으며, 이로 인해 벼열의 크기가 너무 작아져서 데이터의 재배치(rehasing)이 자주 발생하게 된다.

- 재배치(reshing)이란 
> HashMap은 내부적으로 배열과 링크드 리스트를 사용하여 데이터 저장한다. 
> 이 때 배열의 크기는 HashMap의 초기 크기와 로드 팩터(load factor)에 따라 결정
> 만약 배열의 크기가 초기에 설정된 값보다 작아지면, 배열의 크기를 늘리고 모든 데이터를 새로운 배열에 다시 해싱(hasing)해야 한다.
> 이 작업이 데이터의 재배치(reshaing)이라고 합니다. 

- 위와 같은 문제점을 해결하려면?   
ConcurrentHashMap을 사용하거나, HashMap을 동기화하는 방법을 사용해야 한다.
ConcurrentHashMap은 동시에 여러 스레드가 접근해도 안전하게 데이터를 관리할 수 있도록 동기화를 제공한다.
또한, HashMap 동기화할 경우에는 동기화된 블록으로 HashMap의 접근을 제어하여 데이터의 일관성을 보장할 수 있다.


## HashTable

HashMap과 유사한 HashTable 구조를 사용하지만, HashMap과 달리 동기화(sychronization)을 제공한다. 
즉 멀티 스레드 환경에서 안전하게 사용할 수 있다. 하지만 동기화를 제공하므로 성능면에서 HashMap보다 떨어진다. 또한 Null을 허용하지 않는다.

- HashTable은 어떻게 동기화를 제공하는가?
HashTable은 모든 메서드에 synchronized 키워드를 사용하여 스레드 간에 동기화를 제공한다. 
sychronized 키워드를 사용하면, 한 번에 하나의 스레드만 해당 메서드에 접근할 수 있도록 제한한다. 따라서 동기화를 제공함으로써 멀티스레드 환경에서 데이터의 일관성 문제를 방지할 수 있다.
하지만, sychronnized 키워드를 사용하면 성능이 저하될 수 있기에 자바 1.5 부터는 ConcurrentHashMap이나 Collections.synchronizedMap 메서드를 사용하여 동시성을 보장하는 Map 자료구조를 사용하는 것이 권장된다.
이러한 자료구조들은 HashTable과 달리, 락(Lock)을 더 세분화 하여 성능을 향상시킬 수 있다.

## ConcurrentHashMap

ConcurrentHashMap은 멀티스레드 환경에서 안전하게 사용할 수 있는 해시 테이블 구조이다. HashMap과 비슷하지만, 동기화를 적용하는 대신 세분화 된 락(lock)을 사용하여 성능을 향샹시켰다.

- ConcurrentHashMap은 어떤식으로 Lock을 사용할까
1. 세그먼트(segment) 락
> ConcurrentHashMap은 내부적으로 여러 개의 세그먼트(Segment)로 나뉘어져 있다.
> 각 세그먼트(Segment)는 자체적으로 Lock을 가지고 있으며, 서로 독립적으로 작동한다.
> 이를 통해 여러 스레드가 동시에 접근해도 서로 다른 세그먼트에서 작업하므로, Lock 충돌이 발생할 확률을 줄일 수 있다.
 
2. 엔트리(entry) 락 
> ConcurrentHashMap은 엔트리(Entry)에 락을 가질 수 있다.
> 각 세그먼트는 엔트리 락을 사용하여, 해당 세그먼트 내에서 동시에 접근하려는 여러 스레드 간의 경쟁 제어한다.
> 엔트리 락은 세그먼트 락보다 작은 범위에서 락 충돌이 발생할 확률을 줄일 수 이싿.

- 해시 버켓과 세그먼트
ConcurrentHashMap은 여러 스레드에서 안전하게 사용할 수 있는 해시 맵이다. 이를 가능하게 하는 방법 중 하나는, 내부적으로 여러개의 해시 버킷을 여러 개의 세그먼트로 분할하여 관리한다.
각 세그먼트는 자체적으로 동기화되어있으므로, 여러 스레드에서 동시에 접근해도 안전하게 데이터를 처리할 수 있다.

- 해시 버킷
> 해시 테이블에서 데이터를 저장하는 공간
> 해시 버킷은 일반적으로 연결 리스트(linked list) 혹은 트리(tree)구조로 구성되어 있다.
> 각각의 해시 버킷은 배열의 한 인덱스에 해당하며, 버킷 내부에는 키(key)와 값(value) 쌍으로 저장하는 엔트리(entry) 객체가 저장된다.
> 해시 함수에 의해 계산된 해시 값이 버킷의 인덱스로 사용된다.

- 여러 개의 세그먼트(segment)로 분할
> ConcurrentHashMap은 내부적으로 세그먼트(segment)라는 작은 해시 테이블을 여러개 만들어 관리한다.
> 전체 해시테이블을 하나의 큰 해시 버킷으로 관리하는 것이 아니라, 작은 세그먼트 단위로 분할하여 병렬성(parallelism)을 높이는 것이다.
> 각각의 세그먼트는 자신만의 해시 버킷 배열을 가지고 있으며, 세그먼트 내부의 각 버킷은 엔트리(entry)객체로 구성된다.


```java
ConcurrentHashMap<String, Integer> concurrentHashMap = new ConcurrentHashMap<>(16, 0.75f, 4);

map.put("apple", 1);
map.put("banana", 2);
map.put("cherry", 3);
map.put("date", 4);
map.put("eggplant", 5);
map.put("fig", 6);
map.put("grape", 7);
map.put("honeydew", 8);
```

> 위의 코드에서 ConcurrentHashMap의 초기 크기는 16 / 로드 팩터(load factor) 0.75
> 위 코드에서 put 메서드를 사용하여 키-값 쌍을 추가하는데, 이 때 키의 해시 값을 기준으로 어떤 세그먼트에 데이터를 추가할지 결정된다.
> ex) "apple"의 해시값이 0이면, 0번 세그먼트에 저장되고, "banana"의 해시 값이 1이면, 1번 세그먼트에 저장된다.
> 각각의 세그먼트는 독립적으로 잠금을 가지고 있으므로, 여러 스레드에서 동시에 접근하더라도 세그먼트 간의 충돌이나 경합없이 안전하게 작업을 처리 할 수 있다.


## LinkedHashMap

...


### 참조
- [[JAVA] HashMap, HashTable, ConcurrentHashMap,LinkedHashMap 차이](https://peonyf.tistory.com/entry/JAVA-HashMap%EC%99%80-Enum)

