# CORS

- 교차 출처 리소스 공유(Cross Origin Resource Sharing)는 한 출처(브라우저)에서 다른 출처의 선택한 자원에서만 접근할 수 있는 권한을 부여하는 체계이다.

### 출처(Origin)란?

- URI의 `Protocol, Host, Port`를 합친 것이다.
- 포트 번호는 케이스에 따라 제외되기도 한다.

### SOP

- 동일 출처 정책(Same Origin Policy)은 같은 출처에서만 리소스를 공유할 수 있다는 정책이다.

## 동작 방식

- 요청 헤더에 `Origin`에 출처를 포함한다.
- 응답 헤더의 `Access-Control-Allow-Origin`에 허용된 출처를 포함하고 `Origin`과 비교 후 CORS 판단을 한다.

### Preflight Request

- 보통 가장 많이 마주치는 케이스이다.
- 예비 요청과 본 요청으로 나뉜다.
- 예비 요청은 OPTIONS 메소드가 사용된다.
- 예비 요청은 본 요청 전에 안전한지 검사하는 것이다.

### Simple Request

- 예비 요청없이 본 요청을 보낸 후 검사한다.
- Simple Request를 위해서 만족해야할 조건이 많기에 성립하기가 어렵다.
  - `GET, HEAD, POST` 메소드 중 하나여야 한다.
  - `Accept, Accept-Language, Content-Language, Content-Type...`를 제외한 헤더를 사용하면 안된다.
  - `Content-Type`은 `application/x-www-form-urlencoded, multipart/form-data, text/plain`만 허용된다.

### Credentialed Request

- 인증된 요청을 사용하는 방법이다.
- 브라우저의 비동기 리소스 요청 API인 `XMLHttpRequest`나 `fetch`는 쿠키 정보나 인증 관련 헤더를 암시적으로 포함하지 않는다.
- 이때 해당 헤더를 담게 해주는 것이 `credentials` 옵션이다.
  - `same-origin`, 같은 출처 간 요청에만 인증 정보를 담을 수 있다.
  - `include`, 모든 요청에 인증 정보를 담을 수 있다.
  - `omit`, 모든 요청에 인증 정보를 담지 않는다.
- `same-origin`이나 `include`를 포함하면 `Access-Control-Allow-Origin`만 확인하는 것이 아니라 인증 검사도 진행한다.
- `Access-Control-Allow-Origin` 헤더에 와일드카드가 허용되지 않는다.
- 응답 헤더에는 `Access-Control-Allow-Credentials: true`가 존재해야 한다.

## 우회 방법

### Access-Control-Allow-Origin 설정하기

- 정석대로 서버에서 `Access-Control-Allow-Origin` 헤더에 알맞은 값을 세팅한다.
- Nginx나 Apache같은 웹 서버에서 설정할 수 있고 백엔드 프레임워크 단에서 미들웨어로 설정할 수 있다.

### Webpack Dev Server 리버스 프록시 활용하기

- local 환경에서 개발할 때 편리하게 해결할 수 있다.
- webpack-dev-server의 프록시 기능을 사용한다.
- 배포 환경에서 환경 변수로 설정해야하는 불편함이 있을 수도 있다.

### 레퍼런스

https://developer.mozilla.org/ko/docs/Web/HTTP/CORS  
https://evan-moon.github.io/2020/05/21/about-cors/
