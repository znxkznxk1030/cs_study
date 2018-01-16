#  TCP 는 어떻게 신뢰성 있는 통신을 할까?



## 1. 잘 받았으면 ACK, 못 받았으면 NAK

기본적으로 TCP(전송 제어 프로토콜)에서 신뢰성 있는 네트워크를 구성하기 위해서, 오류가 났다면 보냈던 TCP Segment를 다시 보내주게 된다. 이를 위해 수신자는 잘 받았다면 ACK(Positive Acknowledge)를, 중간에 오류가 났다면 NACK, NAK(Negative Acknowlede)를 송신자에게 보내주게 된다. 이를 통해 송신자는 다시 TCP segment를 보낼지 말지 결정하게 된다. 



그렇다면! 몇가지 질문들이 생기게 된다.

1. 어떻게 수신자는 TCP Segment에 오류가 있는지 알 수 있지?
2. 중간 중간 계속 오류 난 TCP Segment를 보내게 되면 순서가 뒤바뀔텐데 괜찮은 건가?
3. 만약 수신자가 송신자에게 ACK, NAK도 못보낸다면 송신자는 무한정 기다리는 건가?



## 2. 어떻게 수신자는 TCP Segment에 오류가 있는지 알 수 있지?

이를 확인하기 앞서 TCP Segment(TCP Header + Data Stream(일부분, as chunk))의 Header 부분을 볼 필요가 있다.



![tcp_header](/tcp_header.png)



위 TCP Header에서 오류를 체크하는 부분은 128비트 부터 시작하는 Checksum부분이다.

이 Checksum Error Detecting을 통해 수신자는 송신자가 보낸 데이터가 제대로 보내졌는지 확인 할수 있으며 잘 못보내졌을 경우 위 TCP Flag (NS, CWR, ECE, URG, ACK, PSH, RST, SYN, FIN)중에서 ACK flag를 reset(0)하여 보낸다.

만일 잘 보내졌을 경우 ACK flag를 set(1)하고 Acknowledgment number에 수신자가 받았던 sequence number에 1을 더한 sequence number + 1의 값을 넣어 보내준다. 이렇게 해야 순서가 뒤죽박죽인 TCP프로토콜에서 제대로 통신할 수 있는 부분이다.



## 3. 순서가 뒤바뀐 TCP Segment는 어떻게 처리하지? 



![tcp_header](/tcp_header.png)



순서가 뒤바뀐 TCP도 역시 전혀 문제가 되지 않는다. 왜냐하면 Sequence number가 있기 때문에 수신자 측에서 이러한 Sequence number순서대로 데이터 청크 (data chunks)들을 잘 붙여주기만 하면 되기 때문이다. 



## 4. 만약 수신자가 송신자에게 ACK, NAK도 못 보낼 상황이라면?

기본적으로 이 같은 경우에서는 timer, timeout 개념을 이용한다. 일정 시간동안 ACK 또는 NAK가 오지 않는다면 timeout된 시점에서 다시 TCP Segment를 보내주게 된다. 

 timer를 너무 길게 생성하면 timer로 인한 퍼포먼스의 하락이, 너무 짧게 설정하면 계속 다시 쓰기 때문에 역시 퍼포먼스의 하락을 가져온다. 

그렇다면 어떻게 timer를 설정할까?

이를 위한 다양한 알고리즘들이 있다. 대표적으로 Kan's algorithm이 있는데 대부분 RFC2988 (Computing TCP's Retransmission Timer)에 실려 있다.

(자세한 부분은 다음 스터디때 이야기 하도록 하겠습니다) 중요한 개념만 설명하자면 RTT(Round Trip Time)의 최근 추이를 통해 RTO(Retransmission Time Out)을 설정하는 것이다.



##  5. TCP와 관련된 중요한 단어 모음

*TCP(Transmisson Control Protocol)* : 전송제어 프로토콜

*TCP Segment* : 흔히 위와 같은 개념들을 TCP packet이라고 하는데 이는 잘못된 말이다. (packet은 link-layer에서 쓰는 단어이고) TCP 는 Segment, UDP는 Datagram이 맞는 말이라고 RFC에서 정의 하였다.

*MSS(Maximum Segment Size)* : 최대 세그먼트 사이즈로 default 값으로 IPv4에서는 536bytes(octets), IPv6에서는  1220Bytes(octets)로 설정되며, 바꾸고 싶을때 최초 연결 (3-way handshaking)때 TCP segment header의 옵션 부분에서 바꿀 수 있다.

*MTU(Maximum Transmission Unit)*  : 해당 레이어가 전송 할 수 있는 최대 프로토콜 데이터 단위

*RTT(Round Trip Time), RTD(Round-Trip Delay Time)* : 데이터 전송후 ACK신호 받을 때 까지의 시간 
