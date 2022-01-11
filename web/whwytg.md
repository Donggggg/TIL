# URL을 입력하고 벌어지는 일

### 웹 브라우저의 주소창에 `www.google.com`을 검색하면 어떤 일이 발생할까?

```
1. 검색어 판단
검색어인지 URL인지 먼저 구분한다.
검색어이면 검색 엔진이 처리한다.

2. 파싱 및 HSTS(HTTP Strict Transfer Security) 확인
URL이면 URL을 파싱한다.
HSTS에 포함되는지 확인해 https 프로토콜로 보낼지에 대한 여부를 결정한다.

3. DNS 캐싱 여부 확인
먼저 DNS가 브라우저에 캐싱되어 있는지 찾아본다.
없다면 OS의 host에 캐싱되어 있는지 찾아본다.
또 없다면 라우터에 캐싱되어 있는지 찾아본다.
그래도 없다면 ISP에 캐싱되어 있는지 찾아본다..

4. DNS LookUp
DNS 정보가 캐싱되어 있지 않다면 DNS LookUp을 진행한다.
root domain부터 top.. second.. third로 내려가며 찾는다.

5. HandShake
DNS에 해당하는 IP주소를 찾으면 TCP 소켓을 설정하고
OSI 7계층을 기반으로 설계된 네트워크 장비들을 통해
3-way HandShake(+과정)을 하여 연결을 한다.
HTTPS 프로토콜이라면 TLS HandShake(+과정)도 진행한다.

6. Request
연결이 성공적으로 되면 HTTP 프로토콜 내용을 설정하여 통신을 진행한다.
응용, 표현, 세션을 거쳐 전송층으로 내려가고
HTTP 기반이기에 TCP 프로토콜을 사용하여 chunk 단위로 잘라 전송한다.
HandShake 때와 마찬가지로 네트워크 장비를 통해 오르락 내리락하며 서버 측에 데이터를 전달한다.

7. Response
서버에 chunk가 도착하면 전송층까지 올라와 순서에 맞춰 합쳐진다.
에러가 있다면 재요청하고 최종 완성되면 메시지는 서버에 전달된다.
서버는 요청하는 리소스에 맞춰 응답 HTTP 메시지를 준비하고 클라이언트 측에 다시 전달한다.

8. Rendering
클라이언트는 요청했던 리소스를 받게되고 HTML 문서를 받을 것이다.
HTML 문서는 브라우저가 렌더링을 위해 파싱을 한다.
HTML 문서를 파싱하여 DOM 트리를 구성하고 link나 script 태그를 만나면
파싱을 중지하고 다시 데이터 통신을 진행한다.
스타일 파일을 받으면 스타일 파일은 파싱하여 CSSOM 트리를 구성한다.
이 과정을 반복해 DOM트리와 CSSOM 트리를 완성한다.
DOM 트리와 CSSOM 트리를 통해 브라우저에 실제로 보여줄 노드들로 이루어진 렌더트리를 구축한다.
만들어진 렌더 트리를 어느 위치에 어느 크기만큼 배치할지 정한다.
이 때, %, vh, vw와 같은 상대적인 위치를 고정적인 값으로 변환한다.
배치가 끝나면 실제로 그리는 작업을 진행한다.
텍스트, 이미지, 색, 그림자와 같은 스타일을 실제 픽셀값에 채워넣는다.
그리기 과정이 끝나면 브라우저의 창에 화면이 보이게 된다.

```

### 레퍼런스

https://github.com/SantonyChoi/what-happens-when-KR#arp  
https://www.linkedin.com/pulse/what-happens-when-you-type-googlecom-any-other-url-buitrago-vargas/
