---
layout : single
title : "Spring WebSocket(WebSocket, SockJS, STOMP)"
categories:
  - spring
tags:
  - spring
  - springboot
  - WebSocket
  - SockJS
  - STOMP
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-20T1:00:00Z
---

### Spring WebSocket 

WebSocket은 TCP 위의 얇고(thin)하고 가벼운 계층입니다. 따라서 `subprotocols`을 사용하여 메시지를 사입하는데 적합합니다.
이 가이드에서는 Spring과 함께 STOMP 메시징을 사용하여 대화형 웹 애플리케이션을 만듭니다. 
STOMP는 하위 수준 WebSocket 위에서 작동하는 하위 프로토콜입니다.

> STOMP (Streaming Text Oriented Messaging Protocol)   
> 이전에 TTMP로 알려졌던 STOMP(Simple (또는 Streaming) Text Oriented Message Protocol)는 메시지 지향 미들웨어(MOM)와 함께 작동하도록 설계된 간단한 텍스트 기반 프로토콜입니다.
> STOMP 클라이언트가 프로토콜을 지원하는 모든 메시지 브로커(Message Broker)와 통신할 수 있도록 하는 상호 운용 가능한 유선 형식을 제공합니다.   
> 
> HTTP 프로토콜과 유사하며 TCP 위에서 다음과 같은 명령어로 통신합니다.
> - CONNECT 
> - SEND 
> - SUBSCRIBE 
> - UNSUBSCRIBE 
> - BEGIN 
> - COMMIT 
> - ABORT 
> - ACK 
> - NACK 
> - DISCONNECT

![img.png](/assets/images/2307/14-1.png#center)   
   
실제 예제 코드는 작성해보고 다시 포스팅 할게요


### 참조
- [Spring messaging-stomp-websocket](https://spring.io/guides/gs/messaging-stomp-websocket/)
- [STOMP wiki](https://en.wikipedia.org/wiki/Streaming_Text_Oriented_Messaging_Protocol)
