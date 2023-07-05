---
layout : single
title : "IntelliJ build.gradle No candidates found for method call."
categories:
  - intellij
tags:
  - intellij
  - flyway
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
---

### 목적 
- intellij 버그인지 Gradle 버그인지 Gradle에 있는 dependency를 못 읽을 때 다음과 같이 에러가 발생한다.
~~~
IntelliJ build.gradle No candidates found for method call.
~~~

### 해결 방법
   
![screenshot](https://imgur.com/WbFhfzp)   

- Command + Shift + A 를 누릅니다.
- reload를 검색한 후 Reload All Gradle Projects 를 클릭한다.
- 위와 같은 행위를 하면 Gradle Dependency 있는 프로젝트들을 다시 다운로드하여 빌드한다. 