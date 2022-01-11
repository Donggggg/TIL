# REST API

## REST

- `REpresentational State Transfer`의 약자로 자원의 이름으로 구분하여 해당 자원의 상태를 주고 받는 것이다.
- 클라이언트-서버 통신 방법 중 하나이다.
- `HTTP URI`를 통해 자원을 명시하고 `Method`를 통해 자원에 대한 `CRUD Operation`을 명시하는 것이다.
- 다양한 클라이언트 플랫폼이 등장하며 관심이 쏠린 아키텍쳐이다.

### 장/단점

- HTTP 프로토콜을 그대로 활용하기에 별도의 환경을 구축할 필요가 없다.
- HTTP 프로토콜이 적용되는 여러 플랫폼에 사용가능하다.
- REST API 메시지가 의도를 명확히 나타낸다.
- 서버-클라이언트 역할을 정확히 구분한다.
- 표준이 존재하지 않는다.
- 메소드의 수가 제한된다.
- 구형 브라우저에서 지원이 안될 수도 있다.

### 구성

1. 자원(Resource) : URI

- 모든 자원은 **ID값**이 존재하고 서버에 위치한다.
- `/groups/:gid`와 같은 URI이다.

2. 행위(Verb) : HTTP Method

- 연산에 맞는 **Method**로 요청한다.

3. 표현(Representation Of Resource)

- 서버가 클라이언트에게 보낼 **적절한 응답**이다.
- `JSON, XML, RSS, TEXT` 등 여러 형태가 존재한다.

### 특징

1. Server-Client (서버-클라이언트 구조)

- 자원이 있는 서버와 요청하는 클라이언트로 이루어진다.
- 서버는 API를 제공하고 비즈니스 로직을 처리 및 저장을 담당한다.
- 클라이언트는 사용자 인증이나 세션, 로그인 정보를 관리하고 책임진다.

2. Stateless (무상태성)

- 클라이언트의 Context를 서버에 저장하지 않는다.
- 서버는 각 요청을 별개의 것으로 인식하여 처리한다.

3. Cacheable (캐시 가능한)

- Last-Modifed나 ETag를 활용해 캐싱할 수 있다.
- 전체 응답 시간, 성능, 서버 자원 이용률을 향상시킬 수 있다.

4. Layered System (계층화)

- API 서버는 순수 비즈니스 로직만 처리하고 앞 단에 보안, 로드밸런싱, 암호화, 사용자 인증을 추가한다.
- 프록시, 게이트웨이 같은 중간 매체를 사용할 수 있다.
- 클라이언트는 서버와 통신하는지 중간 서버와 통신하는지 알 수 없다.

5. Uniform Interface (인터페이스 일관성)

- 특정 언어나 플랫폼에 종속되지 않는 형태로 제공된다.
- 이를 지키기 위해 여러 가지를 만족해야 하는데 이를 지키지 못해 REST라고 부르기 모호한 상황이 많다.

6. Code-On-Demand

- 서버에서 스크립트를 받아 실행할 수 있어야 한다.
- 옵션이기에 반드시 지킬 필요는 없다.

## REST API

- API(Application Programming Interface)란 컴퓨터 프로그램 간의 상호작용을 제공하며 정보를 교환하게 하는 것이다.
- REST API는 REST 아키텍쳐 기반으로 설계된 API이다.
- 확장성과 재사용성을 높여 유지보수와 운용이 편하다.

### 설계 규칙

- 슬래시(/)는 계층 관계를 나타내는데 사용한다.
- URI 마지막 문자로 슬래시(/)를 포함하지 않는다.
- 가독성을 위해 밑줄(\_)이 아닌 하이픈(-)을 사용한다.
- URI 경로에 대문자를 사용하지 않는다.
- URI에 파일 확장자를 포함하지 않는다. (Accept 헤더 활용)

## Uniform Interface의 조건

- **Idenfication Of Resources**
  - URI로 자원을 식별한다.
- **Manipulation Of Resources Through Represntations**
  - Representation(HTTP 메시지) 전송을 통해 리소스를 조작해야 한다.
- **Self-Descriptiven Messages**
  - 메시지는 스스로를 설명해야 한다.
  - 요청이나 응답의 content는 메시지만으로 해석할 수 있어야 한다.
  - `Host, Content-Type` 헤더를 정의하여 명시할 수 있다.
- **Hypermedia As The Engine Of Application State (HATEOS)**
  - 애플리케이션의 상태는 하이퍼링크를 이용해 전이되야 한다.
  - `Link` 헤더나 `HAL(Hypertext Application Language)`로 처리할 수 있다.

### 레퍼런스

https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html  
https://mangkyu.tistory.com/46  
https://www.youtube.com/watch?v=RP_f5dMoHFc
