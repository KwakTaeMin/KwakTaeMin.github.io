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
   
위와 같이 프로젝트를 만들게 되면 해당 모듈을 implement 합니다. 

```gradle
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-websocket'
}
```

```java
@Configuration
@EnableWebSocketMessageBroker
public class StompConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        // 메시지 구독 요청 URL
        registry.enableSimpleBroker("/subscriber");
        // 메시지 발행 요청 URL
        registry.setApplicationDestinationPrefixes("/publisher");
    }

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/ws")
                .setAllowedOrigins("*");
    }
}
```

위와 같이 `@EnableWebSocketMessageBroker` 설정 후   
구독 Prefix URL 설정과 발행 Prefix 설정을 해줍니다.   
WebSocket 또한 EndPoint를 설정하여 줍니다.   

```java
@Controller
public class ChatController {
    @MessageMapping("/chatroom") // 실제 매핑은 publisher/chatroom
    @SendTo("/subscriber/chatroom")
    public String hello(String message) {
        return message;
    }
}
```

`@MessageMapping` 으로 주석에 써있는 것 처럼 메시지를 발행합니다.   
그리고 `@SendTo`로 구독하고있는 `/subscriber/chatroom` 구독자들에게 return값을 전달하여 줍니다.   

위 코드를 작성한 후 [apic](https://apic.app/)이라는 Tool을 이용하여 테스트 해볼 수 있습니다.  
저 같은 경우 아래의 스크린 샷 처럼 설정하여 진행하였습니다.   

![img.png](/assets/images/2307/14-2.png#center)

코드는 [Github](https://github.com/KwakTaeMin/chat-stomp)에 올려 놓았습니다. 


### 참조
- [Spring messaging-stomp-websocket](https://spring.io/guides/gs/messaging-stomp-websocket/)
- [STOMP wiki](https://en.wikipedia.org/wiki/Streaming_Text_Oriented_Messaging_Protocol)
- [STOMP-테스트를-위한-apic-알아보기](https://velog.io/@soluinoon/STOMP-%ED%85%8C%EC%8A%A4%ED%8A%B8%EB%A5%BC-%EC%9C%84%ED%95%9C-apic-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)
