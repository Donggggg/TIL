## Handshake

- TCP 프로토콜에서 양 단말의 연결과 해제를 하는 과정이다.
- SYN, ACK, FIN과 같은 FLAG를 통해 처리된다.

## 3way-Handshake

![](https://i.imgur.com/Z1AeqZC.png)

- 양 단말의 연결을 성립시키기 위한 과정이다.
- 상대방 호스트와 세션을 수립하는 것이다.
- SYN, ACK 플래그가 사용된다.

## 4way-Handshake

![](https://i.imgur.com/gCV550K.png)

- 양 단말의 연결을 해제하기 위한 과정이다.
- 상대방 호스트와 세션을 종료하는 것이다.
- ACK, FIN 플래그가 사용된다.

### 만약 서버에서 FIN 플래그를 보낸 후에 유실된 패킷이 남아있으면 어떻게 되는가?

- TCP는 신뢰성이 있는 프로토콜이기 때문에 유실된 패킷이 남아있으면 안된다.
- 만약 서버에서 클라이언트의 FIN 플래그를 받고 ACK 플래그를 보낸 후 모든 패킷을 보냈다고 생각하고 FIN 플래그를 보냈지만 유실된 패킷이 존재한다면 어떻게 되는가?
- 이러한 현상을 대비하여 클라이언트는 서버의 FIN을 수신하더라도 일정 시간 동안 세션을 남겨놓고 잉여 패킷을 기다린다.
- 이 과정을 TIME_WAIT이라고 한다.

### 레페런스 및 이미지 출처

https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-22%ED%8E%B8-TCP-3-WayHandshake-4-WayHandshake
