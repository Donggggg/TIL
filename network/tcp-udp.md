# TCP와 UDP

- 전송 계층에 속하는 데이터 전송 프로토콜

## TCP (Transmission Control Protocol)

![](https://i.imgur.com/MXii8Fe.png)

### 특징

- 2개의 호스트가 통신을 하기 전에 연결이 요구된다. `#연결지향`
- `3-way handshake`로 연결을 설정하고 `4-way handshake`를 통해 해제한다.
- 높은 신뢰성과 순서대로 전송하는 것을 보장한다.
  - 흐름 제어(Flow Control)를 통해 송신자의 데이터 양을 조절한다.
  - 혼잡 제어(Congestion Control)를 통해 네트워크 상황을 감지하고 송신자의 데이터 양을 조절한다.
  - 에러 감지를 통해 잘못 전송되었을 경우 재전송한다.
- 전 이중(Full duplex)방식으로 두 호스트 모두 송신자와 수신자가 될 수 있다.
- 패킷의 단위가 `세그먼트(Segment)`이다.
- HTTP, FTP, SMTP, TELNET에서 사용된다.
- 스트리밍 서비스에 불리하다.

## UDP (User Datagram Protocol)

![](https://i.imgur.com/YIlPIbF.png)

### 특징

- 비연결형으로 연결을 설정하고 해제하는 과정이 없다.
- 신뢰성이 없고 전송되는 데이터의 순서를 보장하지 않는다.
  - 흐름제어, 혼잡제어가 없다.
  - 에러 감지는 헤더의 `체크섬(Checksum)`을 이용해 최소한만 한다.
- 패킷의 단위가 `데이터그램(Datagram)`이다.
- 서버와 클라이언트는 유니캐스트(1:1), 브로드캐스트(1:N), 멀티캐스트(1:M)가 가능하다.
- DNS, 비디오/오디오 스트리밍에 사용된다.
- TCP에 비해 헤더가 단순하다.

### 레퍼런스 및 이미지 출처

https://velog.io/@hidaehyunlee/TCP-%EC%99%80-UDP-%EC%9D%98-%EC%B0%A8%EC%9D%B4
https://mangkyu.tistory.com/15
