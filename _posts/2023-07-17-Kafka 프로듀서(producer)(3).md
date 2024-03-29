---
layout : single
title : "Kafka 프로듀서(Producer) (3)"
categories:
  - kafka
tags:
  - kafka
  - 카프카
  - Producer
  - 프로듀서
toc: true
toc_label: "목록"
toc_icon: "bars"
toc_sticky: true
date: 2023-07-17T1:00:00Z
---

### Kafka 프로듀서(Producer)

> - 토픽에 메시지 전송
> - 브로커 설정 
> - Key 설정
> - Value 설정
> - KafkaProducer 객체로 ProducerRecord 생성
> - ProducerRecord 는 Topic, Value 만 보내거나 Topic,Key,Value 입력하여 전송

<div class="mermaid"> 
graph TB;
  A(Record)-->|레코드 전송|B(Serializer);
  B-->|Byte 배열로 변환|C(Partitioner);
  C-->|어디 파티션으로 보낼지 결정|D(Buffer);
  D-->|Batch로 묶어서 메시지를 저장|F(Sender);
  F-->|배치를 차례대로 Broker로 전송|G[Kafka Broker];
</div> 

### Sender 작동 방식 
Sender는 별도 쓰레드(Thread)로 Sender 동작합니다. Sender가 메시지를 보내는 동안 배치로 누적해서 쌓이게 됩니다.   
Record 전송은 전송대로 계속 배치 사이즈로 메시지를 저장하고 별도로 Sender 또한 배치로 쌓이는 메시지를 Broker로 전송하는 역할을 합니다.   
두개 서로 각각 쓰레드가 동작합니다.   
Sender는 배치가 다 차지 않아도 보낼 수 있으면 그냥 보냅니다.   
batch.size 는 최대 배치 크기 지정 배치가 다차면 바로 전송 배치 사이즈가 너무 작으면 한번에 보낼 수 있는 양이 적고 많이 보내야 하기 때문에 비효율 적입니다.  
linger.ms 전송 대기 시간 (기본값 0) 대기 시간이 없으면 배치를 바로 전송 대기시간을 주면 그 시간 만큼 기다렸다 배치를 전송합니다. 대기 시간을 주게되면 기다렸다가 전송합니다.   


### 레코드 전송 후 확인 방법    
Record 전송 후 아무것도 하지 않으면 전송 실패 인지 성공했는지 알 수 없습니다. 실패에 대한 별도 처리가 필요없는 경우에는 상관 없습니다.   
전송 결과를 확인해야 하는 경우에는 전송 후 만들어지는 객체는 Future를 가져와서 확인하면 됩니다. 하지만 get할 때마다 블로킹이 되어 처리량이 많이 저하된다고 합니다.   
또 다른 방법은 Callback을 사용하여 전송 결과를 알 수 있게 되는데 onCompletion에서 Exception을 받게되면 실패한 상황입니다. 이러한 방법에는 처리량을 저하하지 않습니다.  
성공과 실패를 확인해야한다면 Callback 방법을 사용하여 처리하는게 좋을 것 같습니다.   

### 전송 보장과 ACK
> - ack = 0 : 서버 응답을 기다리지 않음 / 전송 보장도 없음 / 처리량이 매우 높지만 유실 가능성이 높다 
> - ack = 1 : 파티션의 리더에 저장되면 응답 받음 / 리더 장애 시 메시지 유실 가능 / 처리량도 중간 리더 장애만 없으면 유실이 없다 
> - ack = all (또는 -1) 모든 리플리카에 저장되면 응답 받음 / 처리량이 낮지만, 유실 가능성이 거의 없다 

ack가 all이지만 broker의 `min.insync.replicas` 설정에 숫자만큼이 리플리카에 저장되는 것이므로 더 엄격하게 유실되지 않으려면 
`min.insync.replicas`가 리플리카 갯수만큼 설정되어야 합니다. 팔로워에 하나라도 실패한다면 실패로 돌아가고 저장할 수 없게 되므로 
리플리카 개수 만큼 설정하는 것은 비추하고 적당선의 중간 숫자를 설정하는 것이 좋을 것 같습니다.   

### 에러 유형
> 전송 과정에서 실패
> - 전송 타임 아웃
> - 리더 다운에 의해 새 리더 선출 진행 중
> - 브로커 설정 메시지 크기 한도 초과

> 전송 전에 실패 
> - 직렬화 실패, 프로듀서 자체 요청 크기 제한 초과
> - 프로듀서 버퍼가 차서 기다린 시간이 최대 대기 시간 초과 

### 실패 대응 : 재시도
재시도 가능한 에러들(전송 타임 아웃, 리더 다운에 의해 새 리더 선출 진행 중) 같은 경우에는 재시도를 하여 처리할 수 있습니다.   
프로듀서는 자체적으로 브로커 전송 과정에서 에러가 발생하면 재시도 가능한 에러에 대래 재전송 시도를 합니다.   
send() 메소드나 callback 메서드에서 exception이 나면 타입에 따라 send()를 재호출 해볼 수 있을 것 같습니다.   
재시도를 하는 경우에는 레코드가 전송시 브로커 응답이 늦게 와서 프로듀서가 실패로 인식 후 재시도를 하는 경우 중복 발송 가능 성이 있습니다.    


<div class="mermaid"> 
sequenceDiagram
  Producer->>+Broker: Record 전송
  Broker--x-Producer: Timeout Exception 발생
  Note over Producer,Broker: 저장은 되었지만 Producer 저장 확인 실패
  Producer->>+Broker: Record 재전송
  Broker-->>-Producer: 저장 성공 확인 (중복 저장 발생)
</div> 


참고로 `enable.idempotence` 속성을 고려해보면 중복 가능성이 떨어진다고 합니다. 필요한 경우 해당 속성을 참고하시면 좋을 것 같습니다.
아주 특별한 이유가 없다면 무한 재시도는 하지 않는게 좋을 것 같습니다.

### 실패 대응 : 기록 
추후 처리를 위해 기록합니다. 별도 파일이나 DB등을 이용하여 실패한 메시지를 기록 합니다. 추후에 수동또는 자동으로 보정 작업 진행하면 좋을 것 같습니다.   
send메소드에서 exception이 나거나 callback메서드에서 exception을 받는 경우 혹은 send가 리턴한 future의 get메서드에서 exeption이 나는 경우에 기록해두면 좋을 것 같습니다.

### 참조
- [카프카 조금 아는척하기2(개발자용) 프로듀서](https://www.youtube.com/watch?v=geMtm17ofPY)  
