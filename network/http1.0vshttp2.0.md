# HTTP 1.1 vs HTTP 2.0

## HTTP/1.1

### 특징

- HTTP/1.0이 1996년에 세상에 처음 배포되고 3년 후인 1999년에 HTTP/1.1이 배포되었다.
- HTTP/2.0 배포 전까지 15년동안 지속되었다.
- 파이프라이닝이 도입되었다.

### HTTP Pipelining

<p align="center">
    <img align="center" src="https://i.imgur.com/K0tTYdH.png"/>
</p>

- HTTP/1.0에서는 Connection 당 하나의 요청만 처리 가능했다.
- 그렇기에 동시 전송이 불가능하고 응답을 기다린 후에 다음 요청을 처리할 수 있었다.
- 이는 여러 번의 Handshake도 유발하고 현대의 거대한 크기의 멀티미디어 리소스들은 `Network Latency`를 발생시킨다.
- 이러한 문제점을 해결하기 위해 도입된 것이 `Pipelining`이다.
- 하나의 Connection에 여러 개의 요청을 보내는 방식이다.

### HOL(Head Of Line Blocking)

![](https://i.imgur.com/p10ngBn.png)

- 하지만, 완전한 멀티플렉싱이 아닌 응답 처리를 미루는 방식이므로 각 응답의 처리는 순차적으로 처리된다.
- Head 요청이 거대한 크기를 요청한다면 뒤의 요청들은 지연될 수 밖에 없다.
- 이러한 현상을 `Head Of Line Blocking`이라고 부른다.

### Heavy Headers

- 헤더에 많은 메타 정보들이 저장되어있는데 주로 중복된 데이터들이 많다.
- 쿠키 정보도 매 요청마다 보내야 한다.
- 이러한 헤더값을 반복해서 전송하는 것은 전송 속도를 지연시킨다.

## HTTP/2.0이 나오기 전에 했던 노력들

### 이미지 스프라이팅(Image Spriting)

- 여러 개의 작은 이미지를 하나의 이미지로 합쳐서 관리한다.
- 좌표값으로 구분했는데 이는 CSS로 관리했다.

### 도메인 샤딩(Domain Sharding)

- 리소스를 여러 개의 도메인으로 나누어 저장한다.
- 여러 개의 도메인으로 병렬적으로 요청한다.
- 브라우저의 최대 Connection 개수가 정해져 있으므로 절대적인 해결책은 아니다.

### CSS/JS/Image 압축

- 리소스의 용량을 줄이기 위해 CSS, Javascript, Image 파일을 압축해서 통신한다.

### Data URI

- 이미지 파일을 이진화하여 URI값에 저장한다.

## HTTP/2.0

- 구글이 제안한 SDPY 프로토콜을 기반으로 등장했다.
- 1.1 버전을 완전히 탈바꿈한 것이 아니라 성능 개선을 목표로 업그레이드했다.

### Multiplxed Streams

- 1.1 버전의 파이프라이닝의 개선안으로 하나의 Connection으로 동시에 여러 개의 메시지를 주고 받을 수 있다.
- Stream 단위로 주고 받기에 HOL Blocking이 발생하지 않는다.

### Stream Prioritization

- 응답에 대한 우선순위를 정해 응답 순서를 조정한다.
- 리소스간 의존관계를 고려해 설정된다.
- Image보다 CSS를 높은 우선 순위로 응답해 렌더링을 빨라지게 하는 것이 하나의 예시다.

### Server Push

- 서버가 요청없이 응답을 보내는 방법이다.
- 기존에 클라이언트가 HTML 문서를 수신 후 해석하며 다시 서버에 요청을 보냈다.
- 2.0 버전에서는 서버에서 먼저 필요한 리소스를 클라이언트의 요청 없이 먼저 보내서 요청을 최소화하여 성능을 향상시킨다.

### Header Compression

- Header 정보 압축을 위해 `Header Table`과 `Huffman Encoding 기법`을 사용한다.
- 이는 `HPACK 압축 방식` 이라고도 부른다.
- 중복된 헤더값은 Table에서 검출하여 index 값만 전송한다.
- 중복되지 않은 헤더값은 Huffman Encoding 기법으로 인코딩 후 전송한다.

### 레퍼런스 및 이미지 출처

https://ijbgo.tistory.com/26
