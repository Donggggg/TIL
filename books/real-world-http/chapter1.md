## 챕터1 - HTTP/1.0의 신택스: 기본이 되는 네 가지 요소

해당 장에서는 HTTP의 역사를 통해 기본적인 내용을 다룬다. HTTP는 프로토콜으로써 다음과 같이 나눌 수 있고, 이 장에서는 다음 네 가지 요소에 초점을 맞춰 소개한다.

- 메서드와 경로
- 스테이터스 코드
- 헤더
- 바디

### 1.1 HTTP의 역사

HTTP는 웹 클라이언트와 웹 서버의 통신 절차 및 형식을 규정한 것이다. 정보 교환을 위한 약속이고 현재는 번역, 데이터 저장 API 등의 다양한 서비스 인터페이스로 사용되며 인터넷의 기초가 되었다. HTTP는 1990년 최초로 공개된 이후 계속 버전 업을 이뤄내고 있다.

- 1990년: HTTP/0.9
- 1996년: HTTP/1.0
- 1997년: HTTP/1.1
- 2005년: HTTP/2
- 2020년: HTTP/3 (시간되면 정리)

최초 버전인 0.9는 HTML 문서를 가져오는 단순한 프로토콜이었다. 이후에 폼 전송, 정보 갱신, 채팅 구현에 사용되는 등 유연성을 보이며 새로운 기능이 필요할 때마다 확장했다.

다음으로 HTTP 관련 몇 가지 고유 명사를 소개한다.

- IETF(The Internet Engineering Task Force): 인터넷 상호 접속성을 향상시키기 위해 만들어진 임의 단체
- RFC(Request For Comments): IETF가 만든 규약문서
- IANA(Internet Assigned Numbers Authority): 포트 번호, Content-Type 등 웹에 대한 데이터베이스를 관리하는 단체
- W3C(World Wide Web Consortium): 웹 관련 표준화를 하는 비영리 단체
- WHATWG(Web Hypertext Application Technology Working Group): 웹 관련 구격을 논의하는 단체

인터넷 사양을 정확히 알기 위해서는 위 기관들을 출처로 삼아야 한다.

### 1.1.1 테스트 에코 서버 실행

다음 HTTP 서버 예제를 작성한다.

```go
package main

import (
"fmt"
"log"
"net/http"
"net/http/httputil"
)

func handler (w http.ResponseWriter, r *http.Request) {
  dump, err := httputil.DumpRequest(r, true)
  if err != nil {
    http.Error(w, fmt.Sprint(err), http.StatusInternalServerError)
    return
  }
  fmt.Println(string(dump))
  fmt.Fprintf(w, "<html><body>hello</body></html>\n");
}

func main() {
  var httpServer http.Server
  http.HandleFunc("/", handler)
  log.Println("start http listening :18888")
  httpServer.Addr = ":18888"
  log.Println(httpServer.ListenAndServe())
}
```

### 1-2. HTTP/0.9로 할 수 있는 것을 시험하다.

HTTP/0.9는 굉장히 단순하다. 이를 curl 커맨드로 실험해본다.

isindex 태그는 현재는 없어졌지만 이전에는 해당 태그를 통해 검색을 제공했다. 역시서 현재까지 잔재하는 검색 관련 처리가 있다. URL 쿼리에 검색어를 넣는 것이 그러하다.

0.9에서는 앞에서 이야기했듯이 단순 경로 및 문서 응답만이 가능하다.

### 1-3. HTTP/0.9에서 1.0으로의 여정

0.9 버전은 문제가 많았다.

- 하나의 문서를 전송하는 기능뿐이다.
- Content-Type이 HTML 문서 하나 뿐이다.
- 클라이언트 측에서 검색 외의 요청이 불가능하다.
- 새로운 문장을 전송하거나 갱신/삭제가 불가능하다.
- 요청 및 응답 상태를 알 수 없다.

그렇기에 1.0이 등장하였고, 1.0의 요청에서는 요청 메서드, HTTP 버전, 헤더(Host, User-Agent, Accept)가 추가되었다. 응답에서는 HTTP 버전, 스테이터스 코드, 헤더가 추가되었다.

### 1-4. HTTP의 조상 (1) 전자메일

HTTP의 헤더는 요청과 응답에서 사용한다. 이 아이디어는 인터넷 보급 전 메일 시스템에서 왔다. 간단히 몇 개의 헤더를 알아보자면 다음과 같다.

- User-Agent: 클라이언트의 애플리케이션 명칭
- Referer: 클라이언트가 요청한 현재 페이지 URL
- Authorization: 인가를 위한 인증 정보
- Conent-Type: 파일 종류
- Content-Length: 바디 크기
- Content-Encoding: 압축 형식
- Date: 날짜

이 외에 헤더를 사용하고 싶다면 `x-` 를 prefix로 붙여 정의하면 된다.

### 1-4-3. MIME 타입

MIME 타입은 파일의 종류를 구별하는 문자열이다. 전자 메일에서 가져온 개념이다. 브라우저는 파일 종류에 따라 어떤 동작을 할지 관리하는데, 이 때 사용하는 식별자가 MIME 타입이다. “대항목/상세”로 구성되는데 이는 계속해서 추가/갱신되고 있다.

### 1.4.4 Content-Type과 보안

브라우저는 파일 종류를 특정하기 위해 Content-Type 헤더의 MIME 타입을 사용한다.

확장자를 사용하려고 해도 웹사이트에 따라 확장자가 전송되지 않을 수도 있다. 예전에 사용했던 GCI는 Conent-Type을 더 구체적으로 명시해야 했었다.

IE는 인터넷 옵션에 따라 MIME 타입이 아닌 파일 형식을 보고 추측하는 기능이 있었다. 이를 컨텐트 스니핑(Content Sniffing)이라고 한다. 서버 설정이 잘못되어도 제대로 사용자에게 보여지기에 장점같지만, text/plain이 스크립트로 실행되는 경우도 있어 보안 위험이 있다. X-Content-Type-Options: nosniff를 설정하여 끌 수 있다.

### 1.4.5 전자메일과의 차이

- 헤더+본문 구조는 같다.
- 요청에는 method+path
- 응답에는 status code

### 1.5 HTTP의 조상 (2) 뉴스그룹

오래전에 대규모 전자 게시판으로 사용되던 뉴스그룹이라는 것이 있다. 뉴스그룹은 분산 아키텍처로 구성되어 복수의 서버가 마스터/슬레이브 구조로 연결되어 있다. 즉 ,뉴스그룹에서는 클라이언트-서버, 마스터-슬레이브로 두 가지의 통신 케이스가 있다. 이때 사용되는 프로토콜이 NNTP(Network News Transfer Protocol)이다. HTTP는 뉴스그룹으로부터 메서드와 스테이터스 코드를 착안해 도입한다.

### 1.5.1 메서드

뉴스그룹은 LIST, HEAD, BODY, POST 메서드 등이 있었다.

HTTP의 경우 **파일 시스템(왜지?)**  같은 설계 철학으로 만들어졌다. GET, HEAD, POST부터 PUT, DELETE, PATCH 등이 있다. 이외에도 지원 및 제안이 되었다가 삭제된 메서드도 다수 존재한다.

너무나도 잘 알다시피 GET, HEAD, POST, PUT, DELETE 등 이 있다. 여러 메서드가 더 있었으나 많이 사라졌다.

### 1.5.2 스테이터스 코드

알다시피 100~500번대로 제공된다.

- 1XX: 정보성
- 2XX: 성공
- 3XX: 리다이렉션
- 4XX: 클라이언트 에러
- 5XX: 서버 에러

### 1.6 리다이렉트

300번대 일부는 리다이렉트를 지시하는 코드이다. 300번 이외의 경우에는 Location 헤더를 통해 리다이렉할 주소를 전달한다.

- 301 Moved Permanently
- 302 Found
- 303 See Other
- 307 Temporary Redirect
- 308 Permanent Redirect

301/308은 영구 이동일 때 사용한다. 검색 엔진도 이 응답을 받으면 새로운 페이지로 찾아간다. 308보다는 301이 SEO에 더 좋다.

302/307은 일시 이동일 때 사용한다. 모바일이나 어드민 페이지와 같은 곳으로 이동시킬 때 보통 사용한다.

303은 요청한 페이지에 컨텐츠가 따로 없거나, 반환할 페이지가 따로 있을 때 사용한다. 로그인 케이스가 대표적이다. (네이버 로그인은 쿼리로 리다이렉트한다.)

클라이언트는 위 상태 코드를 받으면 Location 주소로 다시 요청하고 이때 헤더도 함께 보낸다.

리다이렉트 시 GET 메서드로 자동 변경되는 스펙이 1.0과 1.1 사이에 반영되었고, 307/308은 변경을 위한 허가가 필요하다.

리다이렉트 횟수도 처음에는 5회, 1.1에서 무제한이지만 클라이언트에서 순환 참조를 탐지해야 한다.

리다이렉트의 단점은 다른 서버로 리다이렉트 시 TCP 세션과 HTTP 송수신이 반복된다. 구글에서는 리다이렉트는 5회 초과는 불허하고 3회 이하를 권장한다.

### 1.7 URL

URL(Uniform Resouce Locator)는 HTTP/1.0보다 먼저 HTTP/1.0을 예상하고 규격화되었다.

URI는 URL과 URN을 포함하는 범위를 가진다. 이때 URN은 자원의 이름만을 의미하고 URL은 주소를 의미한다. 웹시스템에서는 URN을 크게 다룰일이 없기에 보통 URI=URL로 받아들인다.

### 1.7.1 URL의 구조

URL은 `schema://user:password@hostname:port/path?query#fragment` 로 구성된다.

자주 보는 스키마는  http, https, mailto, file, ftp 등이 있다. user,password는 ftp 프로토콜에서 사용되고는 했다.

URL을 읽으면 정보가 점점 상세해진다. 구글 검색 엔진도 URL에 단어가 포함되면 더욱 높게 쳐준다. UTF-8 인코딩으로 다국어 지원도 가능해졌다. URL이 길어지는 경우도 드물게 있는데, HTTP 사양에는 제한이 없으나 브라우저단에서 제한이 있다.

### 1.7.2 URL과 국제화

예전에는 URL 도메인 이름으로 영숫자와 하이픈만 가능했으나 퓨니코드가 정해지면서 다국어로 도메인을 정할 수 있게 되었다.

### 1.8 바디

HTTP/1.0부터 요청에도 바디를 넣을 수 있게 되었다. 특히 HTML폼 XMLHTTPRequest가 대표적이다.

속도를 위해 바디를 압축하는 경우도 있다. Content-Encoding 헤더는 압축 알고리즘을 담고 있다.

### 1.8.1 GET 요청 시의 바디

HTTP의 메서드 중 일부는 바디를 포함하는 것이 어울리지 않는다. GET, HEAD가 대표적인데 URL에 포함되는 정보로 요청의 의미를 해석하자고 로이 필딩이 제안한다. 서버의 구현에 따라 거부하는 경우도 있고, REST 관점에서 봐도 어울리지 않기에 지양하는게 좋다.

### 메모

httpie

W3C, ECAM Internaional, WHATWG

CONNECT 메서드?

[https://ykarma1996.tistory.com/86](https://ykarma1996.tistory.com/86)