# Uniform Resource Identifier, URI

<img src="https://i.imgur.com/qZvfW6y.png" width="400px"/>

- `통합 자원 식별자(Uniform Resource Identifier, URI`)는 인터넷에 있는 자원을 나타내는 유일한 주소이다.
- 하위 개념으로 **URL**과 **URN** 등이 있다.

### 구조

```
scheme:[//[user[:password]@]host[:port]][/path][?query][#fragment]
```

- `scheme`은 **프로토콜**을 의미한다.
- `user`와 `password`는 데이터에 접근하기 위한 **사용자명과 비밀번호**이다.
- `host`와 `port`는 접근할 대상의 **호스트명과 포트 번호**이다.
- `path`는 접근할 대상의 **경로에 대한 정보**이다.
- `query`는 접근할 대상에 전달하는 **추가적인 정보**이다.
- `fragment`는 **서브리소스를 식별**하기 위한 정보이다.

### URL

![](https://i.imgur.com/nnEh7D1.png)

- `Uniform Resource Locator, URL`은 네트워크 상에서 자원이 어디 있는지를 알려주기 위한 규약이다.
- 일반적으로 사이트의 도메인을 나타낸다.
- 웹 뿐만 아니라 컴퓨터 네트워크상의 자원은 모두 나타낼 수 있다.

### URN

- `Uniform Resource Name, URN`은 자원의 이름을 나타낸다.
- 프로토콜이 포함되지 않는다.

### 레퍼런스 및 이미지 출처

https://velog.io/@jch9537/URI-URL
