## 챕터 2 - HTTP/1.0의 시맨틱스: 브라우저 기본 기능의 이면

### 2.1 단순한 폼 전송(x-www-form-urlencoded)

단순 폼 전송은 key-value 구조로 query와 동일한 형태로 바디에 저장되어 전달이 되고, 이때 구분자를 식별하기 위해 인코딩처리를 한다.

### 2.2 폼을 이용한 파일 전송

multipart/form-data를 통해 파일을 전송할 수 있다. 이때는 한 통신에 여러 파일이 포함될 수 있어 파싱 정보 헤더에 함께 보낸다. 이는 바디에서 분리 기준이 된다.

또, 바디는 구분문자열을 기준으로 헤더+본문으로 구성된다. 각 항목마다 파일명, 파일 종류, 파일 내용과 같은 추가적인 메타 정보를 가질 수 있게 된다.

text/plain 이라는 MIME 타입도 있다. 보안 및 사용성의 이슈로 이는 잘 사용하지 않는다.

### 2.3 폼을 이용한 리다이렉트

스테이터스 코드를 사용한 리다이렉트는 URL 글자수 제한으로 인한 데이터양 제한 및 액세스 로그 등이 남을 우려가 있다. 이런 문제를 해결하기 위해 HTML 폼을 이용한 리다이렉션이다. 서버로부터 리다이렉트할 곳으로 보내고 싶은 데이터를 input 태그에 담아 돌려 받고, 문서가 load되면 즉시 submit하며 리다이렉트한다.

순간적으로 빈 페이지가 표시되거나 스크립트 비활성화가 실행되어 있으면 자동 전환이 안된다는 단점이 있다.

### 2.4 콘텐트 니고시에이션

서버-클라이언트간 통신 규격을 맞추는 헤더들이 있다.

- Accept ↔ Content-Type (MIME 타입)
- Accept-Language ↔ Content-Language (표시 언어)
- Accept-Charset ↔ Content-Type (문자의 문자셋)
- Accept-Encoding ↔ Content-Encoding  (바디 압축)

### 2.4.1 파일 종류 결정

Accept 헤더는 q키 값으로 원하는 파일 종류의 우선순위를 조정할 수 있다.

### 2.4.2 표시 언어 결정

Accept-Language 헤더도 마찬가지로 q키 값으로 원하는 언어의 우선순위를 조정할 수 있다.

### 2.4.3 문자셋 결정

chartset의 경우에는 Accept-Charset로 예전에는 실었으나 최근 브라우저는 모든 인코더를 내장하고 있어 따로 협의할 필요가 없다. 응답으로는 Content-Type에 실을 수 있고, HTML meta 태그(HTTP/2.0)에 실을 수 있다.

### 2.4.4 압축을 이용한 통신 속도 향상

컨텐츠 내용에 따라 다르지만 텍스트는 1/10, 압축률이 높은 Json은 1/20으로 압축할 수 있다. 통신 시간보다 압축/해제가 더 짧은 시간이 소요되기에 로드 속도가 더 빨라진다.

나아가 이용 요금에도 긍정적인 영향을 준다. 데이터 사용량이 줄어드는 만큼 비용이 줄고, 전파 송수신 전력을 줄여 전력 소비도 줄일 수 있다..ㅋㅋㅋㅋㅋ

구글은 gzip보다 압축 효율이 좋은 브토틀리(br)을 공개했다. 실제로 구글에서는 Accept-Encoding을 defalte, gzip, br로 세팅해뒀다.

### 2.5 쿠키

브라우저에 저장하는 key-value 스토리지이다. 쿠키를 활용하면 무상태성의 HTTP를 상태를 가진 것처럼 제공할 수 있다.

### 2.5.1 쿠키의 잘못된 사용법

쿠키는 영속성 문제가 있어 어떤 상황에는 저장이 안될 수도 있다. 예를들어 비밀 모드나 보안 설정에 따라 세션이 끝나면 초기화되거나 쿠키 보관 지시를 무시하기도 한다. 방문 기록 삭제나 개발자 도구를 통해 삭제할 수도 있다.

용량 문제도 있다. 쿠키는 최대 4kB이고, 매 통신마다 쿠키가 포함되기에 속도에 영향을 끼친다.

마지막으로 보안 문제이다. secure 속성을 부여하면 HTTPS 프로토콜에서만 쿠키가 전송되지만 HTTP 통신에서는 평문으로 전송된다. 암호화를 한다고 해도 사용자가 자유롭게 접근하기에 문제이다. 그러므로 민감한 정보는 넣는 것은 지양하고, 서명이나 암호화 처리가 필수적이다.

### 2.5.2 쿠키에 제약을 주다

쿠키의 수명, 전송지 등을 제한할 수 있다.

- Expires, Max-Age : 쿠키의 수명을 정한다.
- Domain: 전송할 대상 서버를 정한다.
- Path: 전송할 대상 서버의 경로를 정한다.
- Secure: https 프로토콜일 때만 쿠키를 전송한다.
- HttpOnly: 쿠키를 스크립트로 수정할 수 없게 한다.
- SameSite: 같은 출처에만 전송한다.

### 2.6 인증과 세션

많은 웹 서비스에 인증 기능이 많이 생겨나며 여러 방식이 역사에 존재해왔다.

### 2.6.1 BASIC 인증과 Digest 인증

BASIC 인증은 가장 간단하게 base64로 유저명:패스워드를 인코딩 한 것이다. SSL/TLS 통신이 없는 경우 정보가 너무 쉽게 노출된다.

이보다 강력한 것이 Digest 방식이다. 이 방법은 해시 함수를 이용한다. 역변환이 가능은 하나 굉장히 어렵다는 특징을 가진다. realm(보호 영역명), nonce(서버가 생성하는 랜덤 값), qop(보호수준) 등의 데이터가 있고 이런 데이터를 사용한 수식을 기반으로 response를 도출한다. 수식 데이터 중 nc라는 값이 있는데 이 값은 nonce 값을 사용해 전송한 횟수를 기록한다. 같은 nc값을 감지함으로써 리플레이 공격을 탐지한다.

클라이언트는 수식을 통해 도출한 response와 도출하는데 사용한 정보를 헤더에 포함해 서버에 전송한다. 서버도 헤더 정보로 저장된 인증 정보를 암호화하여 비교하면 일치여부를 판단할 수 있다.

### 2.6.2 쿠키를 사용한 세션 관리

위 두 방법은 아래 같은 문제점 때문에 요즘에는 잘 사용되지 않는다.

- 특정 path만 가드할 수 있다
- 요청마다 인증 과정을 거친다
- 로그인 페이지를 커스텀할 수 없다
- 명시적인 로그오프를 할 수 없다
- 로그인 단말 식별이 불가능하다

최근에는 세션/쿠키를 조합해 인증 기능을 제공한다. 우선 폼을 통해 아이디와 패스워드를 전송한다(SSL,TLS). 서버에서는 인증 후에 세션 토큰을 발행한다. 서버는 세션 토큰을 저장해두고 클라이언트에 쿠키로 응답한다. 이후에는 쿠키를 통해 인증 여부를 판단할 수 있다. CSRF 공격을 방어해야하니 랜덤 키를 포함시켜야 한다.

### 2.6.3 서명된 쿠키를 이용한 세션 데이터 저장

쿠키로 인한 통신량 증가는 사실 요즘에는 큰 문제가 아니다. 그렇기에 쿠키는 원목적인 스토리지로 활용할 수 있다. 이에 따라 몇몇 서버 프레임워크는 세션 스토리지를 쿠키로 활용한다. 이때 세션 값을 전자 서명을 활용해 암호화하여 사용한다.

이러한 방식의 장점은 서버에서 데이터 저장을 따로 할 필요 없다는 것이고, MSA나 멀티 서버 환경에서 세션 관리하기가 수월해진다.

### 2.7 프록시

프록시는 HTTP 통신을 중계, 캐싱, 방화벽, 이미지 필터링 등의 역할을 하는 서버이다.

프록시로 설정하면 경로명에 스키마 및 호스트명도 붙는다. 또, 프록시 서버 악용을 방지하기 위해 Proxy-Authenticate 헤더를 통해 보호할 수 있다. 중계되는 프록시는 중간 호스트 IP주소를 X-Forwared-For 헤더에 기록해둔다. 프록시와 비슷한 것이 게이트웨이가 있는데, 게이트웨이는 컨텐츠 수정을 하지 않는게 특징이다. 외부망에서 내부방에 접근할 때 두는 놈으로 사용한다.

### 2.8 캐시

웹사이트의 컨텐츠가 늘어나며 표시하는데 필요한 용량도 메가바이트 단위로 늘어났다. 이러한 문제를 해결하는 것이 캐시이다. 현재 캐시 전략은 꽤나 복잡하지만 초기 방식은 간단하다. 버전을 따라가며 캐시를 이해해본다.

### 2.8.1 갱신 일자에 따른 캐시

HTTP/1.0에서는 정적 컨텐츠가 위주였기에 갱신 여부만 판단하면 됐다. 서버는 Last-Modifed 헤더에 최종 수정 시간을 응답으로 보내고, 이후 클라이언트는 If-Modifed-Since 헤더에 해당 값을 넣어 요청한다. 헤더값의 시간과 서버 컨텐츠 시간을 비교해 변경됐으면 다시 보내고 그렇지 않으면 304를 보낸다.

### 2.8.2 Expires