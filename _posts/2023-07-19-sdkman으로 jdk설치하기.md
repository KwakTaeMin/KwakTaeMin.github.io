---
layout : single
title : "sdkman으로 jdk설치하기"
categories:
  - java
tags:
  - jdk
  - sdk
  - sdkman
  - jdk설치
  - sdkman설치
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-19T2:00:00Z
---

### SDKMAN이란?
SDKMAN 대부분의 Unix 기반 시스템에서 여러 소프트웨어 개발 키트 의 병렬 버전을 관리하기 위한 도구입니다.   
예를 들면 JDK8, JDK11, JDK17 버전을 모두 설치한 뒤 기본 `JAVA_HOME`을 변경해가며 소프트웨어나 프로그램 특성 상 특정 버전을 사용해야만
사용 가능한 경우가 있습니다. 그런 경우에 `sdkman`을 이용하여 설치하게 되면 환경 구성이 용이하여 좋습니다.

### sdkman 설치

```shell
$ curl -s "https://get.sdkman.io" | bash
```   
위 명령어로 설치 한 후 
```shell
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
```
전역으로 환경 변수를 설정해주면 됩니다.
```shell
$ sdk version
```
sdk version을 치게 되고 다음과 같이 나오면 sdkman 설치는 끝입니다. 간단합니다. 
```shell

SDKMAN!
script: 5.18.2
native: 0.3.2


```

### sdkman으로 jdk 설치하기

저도 오늘 새로 산 macbook air m2 15인치에 `jdk`를 설치를 하기위해 sdkman을 설치했습니다.
이제 `jdk`를 설치할 것 입니다. spring 3 버전대를 사용하기 위해 jdk 17 버전을 설치해볼 예정입니다. 
   
sdkman으로 설치 가능한 jdk들이 뭐가 있는지 확인해봅니다. 
```shell
$ sdk list java
```

```shell
================================================================================
Available Java Versions for macOS ARM 64bit
================================================================================
 Vendor        | Use | Version      | Dist    | Status     | Identifier
--------------------------------------------------------------------------------
 Corretto      |     | 20.0.1       | amzn    |            | 20.0.1-amzn         
               |     | 17.0.7       | amzn    |            | 17.0.7-amzn         
               |     | 11.0.19      | amzn    |            | 11.0.19-amzn        
               |     | 8.0.372      | amzn    |            | 8.0.372-amzn        
 Gluon         |     | 22.1.0.1.r17 | gln     |            | 22.1.0.1.r17-gln    
               |     | 22.1.0.1.r11 | gln     |            | 22.1.0.1.r11-gln    
 GraalVM CE    |     | 20.0.1       | graalce |            | 20.0.1-graalce      
               |     | 17.0.7       | graalce |            | 17.0.7-graalce      
 GraalVM Oracle|     | 20.0.1       | graal   |            | 20.0.1-graal        
               |     | 17.0.7       | graal   |            | 17.0.7-graal        
 Java.net      |     | 22.ea.6      | open    |            | 22.ea.6-open        
               |     | 22.ea.5      | open    |            | 22.ea.5-open        
               |     | 22.ea.4      | open    |            | 22.ea.4-open        
               |     | 22.ea.3      | open    |            | 22.ea.3-open        
               |     | 21.ea.31     | open    |            | 21.ea.31-open       
               |     | 21.ea.30     | open    |            | 21.ea.30-open       
               |     | 21.ea.29     | open    |            | 21.ea.29-open       
               |     | 21.ea.28     | open    |            | 21.ea.28-open       
 JetBrains     |     | 17.0.7       | jbr     |            | 17.0.7-jbr          
               |     | 11.0.14.1    | jbr     |            | 11.0.14.1-jbr       
 Liberica      |     | 20.0.1.fx    | librca  |            | 20.0.1.fx-librca    
               |     | 20.0.1       | librca  |            | 20.0.1-librca       
               |     | 17.0.7.fx    | librca  |            | 17.0.7.fx-librca    
               |     | 17.0.7       | librca  |            | 17.0.7-librca       
               |     | 11.0.19.fx   | librca  |            | 11.0.19.fx-librca   
               |     | 11.0.19      | librca  |            | 11.0.19-librca      
               |     | 8.0.372.fx   | librca  |            | 8.0.372.fx-librca   
               |     | 8.0.372      | librca  |            | 8.0.372-librca      
 Liberica NIK  |     | 23.r20       | nik     |            | 23.r20-nik          
               |     | 23.r17       | nik     |            | 23.r17-nik          
               |     | 22.3.2.r17   | nik     |            | 22.3.2.r17-nik      
               |     | 22.3.2.r11   | nik     |            | 22.3.2.r11-nik      
 Microsoft     |     | 17.0.7       | ms      |            | 17.0.7-ms           
               |     | 11.0.19      | ms      |            | 11.0.19-ms          
 Oracle        |     | 20.0.1       | oracle  |            | 20.0.1-oracle       
               |     | 17.0.7       | oracle  |            | 17.0.7-oracle       
 SapMachine    |     | 20.0.1       | sapmchn |            | 20.0.1-sapmchn      
               |     | 17.0.7       | sapmchn |            | 17.0.7-sapmchn      
               |     | 11.0.19      | sapmchn |            | 11.0.19-sapmchn     
 Semeru        |     | 20.0.1       | sem     |            | 20.0.1-sem          
               |     | 17.0.7       | sem     |            | 17.0.7-sem          
               |     | 11.0.19      | sem     |            | 11.0.19-sem         
 Temurin       |     | 20.0.1       | tem     |            | 20.0.1-tem          
               |     | 17.0.7       | tem     |            | 17.0.7-tem          
               |     | 11.0.19      | tem     |            | 11.0.19-tem         
 Tencent       |     | 17.0.7       | kona    |            | 17.0.7-kona         
               |     | 11.0.19      | kona    |            | 11.0.19-kona        
               |     | 8.0.372      | kona    |            | 8.0.372-kona        
 Zulu          |     | 20.0.1       | zulu    |            | 20.0.1-zulu         
               |     | 20.0.1.fx    | zulu    |            | 20.0.1.fx-zulu      
               |     | 17.0.7       | zulu    |            | 17.0.7-zulu         
               |     | 17.0.7.fx    | zulu    |            | 17.0.7.fx-zulu      
               |     | 11.0.19      | zulu    |            | 11.0.19-zulu        
               |     | 11.0.19.fx   | zulu    |            | 11.0.19.fx-zulu     
               |     | 8.0.372      | zulu    |            | 8.0.372-zulu        
               |     | 8.0.372.fx   | zulu    |            | 8.0.372.fx-zulu     
================================================================================
Omit Identifier to install default version 17.0.7-tem:
    $ sdk install java
Use TAB completion to discover available versions
    $ sdk install java [TAB]
Or install a specific version by Identifier:
```

그럼 위와 같이 sdkman으로 설치할 수 있는 jdk list들이 줄줄이 나오게됩니다. 이중 하나 선택해서 설치하시면 됩니다. 
저는 `Temurin` 벤더사 의 17.0.7-tem 버전으로 설치해볼 예정입니다. 

```shell
$ sdk install java 17.0.7-tem
```

```shell
In progress...

############################### 100.0%

Repackaging Java 17.0.7-tem...

Done repackaging...
Cleaning up residual files...

Installing: java 17.0.7-tem
Done installing!


Setting java 17.0.7-tem as default.
```

위와 같이 설치 후 java default는 17.0.7-tem version으로 설정되었습니다.

```shell
$ java -version
openjdk version "17.0.7" 2023-04-18
OpenJDK Runtime Environment Temurin-17.0.7+7 (build 17.0.7+7)
OpenJDK 64-Bit Server VM Temurin-17.0.7+7 (build 17.0.7+7, mixed mode)
```
설치가 잘 되었는지 `java --version` 명령어로 확인하여보았습니다.   
 
추후 만약 jdk 11 버전을 사용해야 한다면, 위와 같이 11버전을 설치하시고 java default 명령어로 변경해주시면 됩니다.      
만약 `11.0.19-tem`을 설치했다면 위와 같이 default로 변경합니다.  

```shell
$ sdk install java 11.0.19-tem
```

```shell
$ sdk default java 11.0.19-tem  
```

위와 같이 여러 버전의 JDK를 설치하고 쉽게 환경을 변경하며 사용할 수 있는 sdkman을 설치하고 jdk를 설치해봤습니다.

### 참조
- [sdkman install documents](https://sdkman.io/install)
