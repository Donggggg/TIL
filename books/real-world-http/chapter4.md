## 챕터  4 - HTTP/1.1의 신택스: 고속화와 안정성을 추구한 확장

### 4.1 통신 고속화

HTTP/1.1 시대에 규격화된 것 가운데 가장 큰 주체는 TLS를 통한 안정한 통신 제공이다.그 다음으로는 파이프라이닝과 Keep-Alive를 통한 통신 고속화이다.

### 4.1.1 Keep-Alive

Keep-Alive는 TCP/IP 통신을 효율화하는 구조이다. 통신마다 재연결하지 않기 때문에 접속 대기 시간이 줄어 통신 처리량이 많아지며 속도가 올라간 것처럼 느낄 수 있다. HTTP 1.5RTT, HTTPS 2RTT만큼의 시간을 줄일 수 있다.

클라이언트나 서버에서 Connection: Close로 종료하기 전에는 연결이 유지된다. 끊는 타이밍을 누군가 정의하는 것이 애매하기에 타임아웃을 둬서 설정한다. 연결을 유지하는 것은 비용이기에 짧은 시간에 끊는 것에 의미를 두어 설정한다.

### 4.1.2 파이프라이닝

현재는 잘 사용하지 않지만, 현 방법의 기초가 되었기에 중요하다.

파이프라이닝은 최초 요청이 완료되기 전에 다음 요청을 보내는 기술이다. 서버는 요청이 들어온 순서대로 응답한다. 이 응답 순서는 HOL 블로킹으로 인해 특정 케이스에는 유의미한 성능 향상이 보이지 않는 문제점이 있다. 또, 파이프라이닝 미지원 서버를 위해 확인 과정이 필요하는 등 성능을 제대로 살리기 힘든 상황이었다.

### 4.1.3 스트림

파이프라인은 HTTP/2에서 스트림이라는 구조로 다시 태어났다.

HTTPS 통신이 기본인 HTTP/2는 프록시가 중계의 역할에 포커싱하기에 프록시의 동작에 대한 걱정이 사라졌고, 시분할 통신이 가능한 스트림은 서버에서 준비된 순서로 응답을 할 수 있고, 우선순위를 정해 응답을 할 수 있게 되었다.

### 4.2 전송 계층 보안 (TLS)

웹 서비스에 필수가 된 HTTPS는 HTTP 프로토콜의 평문 통신을 암호화하기 위해 TLS 암호화를 넣은 것이다. TLS는 너무 다양한 요소가 함께 있으므로 이 책에서는 범위를 벗어나지 않고 설명한다.

### 4.2.1 해시 함수

입력 데이터를 규칙에 따라 해시값으로 추출하는 함수이다. 해시함수는 다음과 같은 특징을 가지고 있다.

- h(A) = X가 항상 성립한다.
- 같은 알고리즘은 길이가 고정된다.
- 원래 데이터를 유추하기 어렵다 (약한 충돌 내성)
- 같은 해시 값을 생성하는 두 개의 데이터를 찾기 어렵다 (강한 충돌 내성)

해시 함수는 컴퓨터에서 다양한 용도로 쓰인다. 예를 들어 다운로드 파일 검사, TCP 오류 제어 등으로 이런 용도로 사용되면 체크섬 혹은 핑거프린트라고 불린다.

VCS인 Git에서도 파일 관리 시 파일명이 아닌 파일 내용을 바탕으로 해시 값을 사용하고 저장한다. 이를 통해 같은 파일인지 아닌지 빠르게 파악이 가능하다.

### 4.2.2 공통 키 암호와 공개 키 암호 그리고 디지털 서명

암호 통신에서 기본적인 해시 함수를 활용하는 것은 쉽게 깨질 수 있어 사용하지 않는다. 암호화에서 중요한 것은 알고리즘이 유출되어도 안전하게 통신할 수 있는 것이다. 암호화 알고리즘은 공개하고, 암호화 키는 따로 준비하는 방식이 일반적이다. TLS에서는 공개키(대칭키), 개인키(비대칭키) 방식이 사용된다.

디지털 서명은 비대칭키를 응용한 예이다. 받는 사람이 비밀키로 암호화된 데이터를 받고 그것을 공개키로 열어보고 본문과 동일한 것이 나오면 조작되지 않은 것을 알 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2484bcc1-9f86-4da9-93a8-4b898d729e9e/Untitled.png)

### 4.2.3 키 교환

키 교환은 클라-서버 간 키를 교환하는 것이다. 간단한 방법으로는 공통 키를 생성한 다음 서버의 공개키로 암호화해 보내는 방법이 있고, 키교환 전용 알고리즘도 있다. 책에서는 디피-헬먼 알고리즘을 소개한다.

클라-서버가 각 키 재료를 만들어 교환하고 계산해서 키를 얻는 특징이 있다. 이하 과정은 책에서 설명한다.

### 4.3 PUT 메서드와 DELETE 메서드의 표준화

1.0에서는 옵션이었던 PUT, DELETE도 필수 메더스도 추가됐다. 이로써 CRUD를 HTTP로 표현해낼 수 있게 됐다. 위 메서드들은 폼으로는 보낼 수 없고, XMLHttpRequest를 사용해야 한다.

### 4.4 OPTIONS, TRACE, CONNECT 메서드 추가

또 1.1에서는 OPTIONS, TRACE, CONNECT 메서드도 추가됐다.

### 4.4.1 OPTIONS

OPTIONS 메서드는 서버가 받아들일 수 있는 메서드 목록을 반환한다. Allow 헤더에 메서드들을 ,로 구분지어 응다반다.

대부분 웹 서버는 OPTIONS 메서드를 허용하지 않고, 405 Method Not Allowed로 거부한다. **왜?**

### 4.4.2 TRACE(TRACK)

TRACE 메서드는 응답에 Content-Type을 message/http, 스테이터스 코드 200을 붙여 요청 헤더와 바디를 그대로 반환한다. 이는 XSS의 취약성과 조합해 BASIC 인증 사용자 이름과 패스워드를 장악할 수 있는 XST 취약성으로 인해 거의 사용되지 않는다.

### 4.4.3 CONNECT

CONNECT 메서드는 HTTP 프로토콜상에 다른 프로토콜의 패킷을 흘릴 수 있게 한다. 프록시 서버를 서쳐 대상 서버에 접속하는 것을 목적으로 한다.

### 4.5 프로토콜 업그레이드

1.1 버전부터는 HTTP 이외의 프로토콜로 업그레이드 할 수 있다.

1. HTTP에서 TLS를 사용한 안전한 통신으로 업그레이드(TLS/1.0, TLS/1.1, TLS/1.2)
2. HTTP에서 웹소켓을 사용한 양방향 통신으로 업그레이드(websocket)
3. HTTP에서 HTTP/2로 업그레이드(h2c)

TLS로의 업그레이드는 보안이 지켜지지 않는 문제가 있고, 현재 모든 통신이 TLS화되고 있으며 TLS의 핸드셰이크 시 프로토콜 선택 기능을 사용하도록 권장하므로 HTTP/2에서는 프로토콜 업그레이드 기능이 삭제됐다. 현재의 프로토콜 업그레이드는 거의 웹소켓용이다.

### 4.5.1 클라이언트 쪽에서 업그레이드를 요청

클라이언트에서 업그레이드를 하는 경우에 Upgrade와 Connection 헤더를 포함한 요청을 보낸다. 보안을 신경쓴다면 OPTIONS 메서드로 미리 업그레이드 가능한 프로토콜을 확인한다.

### 4.5.2 서버 쪽에서 업그레이드를 요청

스테이터스 코드 426 Upgrade Required를 및 클라이언트측에서 업그레이드할 때와 동일한 헤더를 붙인다. 단 이 경우에 핸드셰이크가 유지되는 것은 아니고 다시 클라이언트에서 핸드셰이크를 해야한다.

**Keep-Alive 있으면 괜찮지 않나?**

### 4.5.3 TLS 업그레이드의 문제점

TLS 업그레이드는 클라-서버 전체 경로를 암호화하지 않으면 의미가 없다. 프록시가 정보를 훔치거나 의도와 다른 요청을 서버에 보내는 중간자 공격 등에 취약하기 때문이다.

이외에 TLS 통신으로 업그레이드 하는 방법은 http 통신을 301 Redirect로 https 통신을 할 수 있게 만드는 것이나 HTTPS로만 접속가능하게 브라우저에 스스로 알리는 HTTP Strcit Transport Security(HTST) 방법이 있다.

### 4.6 가상 호스트 지원

1.0에서는 하나의 웹서버로 하나의 도메인을 다루는 것이 전제였다. 이는 비효율적이므로 하나의 웹 서버로 여러 도메인을 다룰 수 있는 방법이 1.1에서 지원됐다.

웹 서버는 Host 헤더를 보고 분기할 수 있다. 클라이언트에서 Host를 붙이는 것뿐이지만, 서버에서는 그 정보를 바탕으로 같은 서버 컨텐츠를 구분해서 보낼 수 있다.

### 4.7 청크

1.1에서 지원되는 새로운 데이터 표현으로 전체를 한꺼번에 전송하지 않고 작게 나눠 전송하는 청크 방식이 있다. 스트리밍 다운로드/업로드라고 불리기도 한다.

큰 용량의 통신도 서버측에서 메모리를 낭비하지 않을 수 있고, 클라이언트에서는 read 타임을 짧게 할 수 있고, 특정 컨텐츠라면 다운로드된 부분만 표시하거나 [인터레이스](https://bwessay.tistory.com/201) 방식 표시도 할 수 있어 응답 속도가 빨라진다.

청크는 Transfer-Encoding헤더가 chunked로 설정되어 있고, Content-Length 헤더는 빠진다. 바디는 16진수로 표시된 파일 크기와 그 뒤로 데이터가 이어진다. 0이 나오면 청크 전송이 끝났다는 신호이다.

### 4.7.1 메시지 끝에 헤더 추가

청크 형식의 경우에는 청크 메시지 끝에 헤더를 추가할 수 있다. Trailer 헤더 있는 헤더키값은 나중에 전송된다고 알려주는 것이다. Transfer-Encoding, Content-Length, Trailer 헤더는 위 헤더에서 제외된다.

### 4.8 바디 전송 확인

서버에 먼저 받아들일 수 있는지 물어보고 2단계 전송을 할 수 있게 됐다. 클라이언트는 Expect: 100-continue 헤더와 바디를 제외한 모든 헤더를 지정해 문의한다. 100 Continue 응답이 오면 바디를 붙여 다시 전송한다. 417 Expectation Failed가 돌아오면 전송 불가능이다.

curl 커맨드는 컨텐츠 크기가 1025바이트 이상이면 이렇게 동작하고, 이것을 억제하려면 Expect 헤더를 비워야한다.