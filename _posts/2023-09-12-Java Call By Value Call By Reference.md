---
layout : single
title : "Call By Value / Call By Reference "
categories:
  - java
tags:
  - java

toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-09-12T1:00:00Z
---

## Call by Value (값에 의한 호출)
- 인자로 받은 값을 복사하여 처리한다.
- 복사하여 처리하기 때문에 안전하다. 
- 원래의 값이 보존이 된다.
- 복사를 하기 때문에 메모리가 사용량이 늘어난다. 
  - Java에서 `String` + `String` 하지 않고 StringBuilder를 이용하여 `append()` 하는 개념 같다. 
- Java의 경우 함수에 전달되는 인자의 데이터 타입 (기본 자료형 / 참조 자료형) 에따라 방식이 달라진다.
  - 기본 자료형 : call by value 로 동작 (int, short, long, float, double, char, boolean)
  - 참조 자료형 : call by reference 로 동작 (Array, Class Instance)
- JAVA에서 Call by reference는 해당 객체의 주소값을 직접 넘기는 게 아닌 객체를 보는 또 다른 주소값 생성
  - reference의 참조하는 객체의 value를 변경한다.
## Call by Reference (참조에 의한 호출)
- 인자로 받은 값의 주소를 참조하여 직접 값에 영향을 준다. 
- 복사를 하지 않고 직접 참조에 하기에 빠르다.
- 직접 참조를 하기에 원래 값이 영향을 받는다. 
### 참조
- [Call by Value, Call by Reference 차이](https://sudo-minz.tistory.com/91)
- [java 에서의 call by value 와 call by reference1](http://dhplanner.blogspot.com/2009/11/java-%EC%97%90%EC%84%9C%EC%9D%98-call-by-value-%EC%99%80-call-by.html)

