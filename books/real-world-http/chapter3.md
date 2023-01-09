## 챕터3 - Go 언어를 이용한 HTTP/1.0 클라이언트 구현

### 3.1 Go 언어를 이용하는 이유

다른 언어보다 균형이 잘 잡혀 있기 때문에 사용한다.

- 간결한 스펙, 풍부한 표준 라이브러리
- 빠른 컴파일 속도, 확실한 타입 체크
- 멀티 코어 최적화된 실행 속도와 메모리 절약
- 간단한 크로스 컴파일
- 단일 바이너리 결과물의 쉬운 배포

이에 더해 다음 특징을 가져 교육용으로도 우수하다.

- 수도 코드에 적합하다
- syntax와 type check로 실수 방지가 쉽다.
- 표준 라이브러리만으로 HTTP 액세스 프로그램을 만들 수 있다.
- 실제 다양한 웹 CLI 클라이언트 구현에 사용된다.

### 3.2 Go 언어의 API 구성

Go언어는 다음 세 가지 HTTP API를 제공한다.

- 제한적이지만 간편한 API
- 쿠키 이용 가능하고 약간의 컨트롤이 가능한 API
- 모든 기능이 가능한 primitve API

### 3.3 이 장에서 다룰 레시피

1/2장에서 다룬 송수핀 패턴을 구현해본다. GET, POST 동작 및 쿠키, 프록시 등을 구현하며 위의 세가지 API를 활용해본다.

### 3.4 GET 메서드 송신과 바디, 스테이터스 코드, 헤더 수신

### 3.4.1 io.Reader

Go에서는 순차적인 입출력을 io.Reader, io.Writer 인터페이스로 추상화했다. 해당 인터페이스는 파일, 소켓 등에서 사용한다. http.Response 타입의 resp.Body도 해당 인터페이스를 가진다.

### 3.5 GET 메서드+쿼리 전송

### 3.6 HEAD 메서드로 헤더 가져오기

### 3.7 x-www-form-urlencoded 형식의 POST 메서드 전송

### 3.8 POST 메서드로 임의의 바디 전송

### 3.9 multipart/form-data 형식으로 파일 전송

### 3.9.1 전송할 파일에 임의의 MIME 타입을 설정한다

### 3.10 쿠키 송수신

### 3.11  프록시 이용

### 3.12 파일 시스템 액세스

### 3.13 자유로운 메서드 전송

### 3.14 헤더 전송

### 3.15 국제화 도메인