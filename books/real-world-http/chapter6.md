## 챕터 6 - Go 언어를 이용한 HTTP1.1 클라이언트 구현

### 6.1 Keep-Alive

HTTP 클라이언트는 반드시 body를 다 읽고 닫아야 한다. 소켓이라는 하나의 파이프는 시분할로 공유되기 때문에 종료를 명시하지 않으면 다음 작업을 더 추가할 수 없게 된다.

### 6.2 TLS

Go에서는 url이 [https://로](https://로) 시작하기만 해도 TLS 통신이 가능하다. Go언어 자체적으로 만든 것이 있기 때문이다.

### 6.2.1 인증서 만들기

기본적으로는 시스템이 가진 인증서를 사용해 인증서를 확인한다. 테스트용 인증서를 만들어 사용할 수 도 있다.

증명서를 만드는 과정은 다음과 같다.

- OpenSSL 커맨드로 비밀키 생성
- 인증서 요청 파일 생성
- 인증서 요청 파일에 서명해서 인증서 생성

보통 인증서 요청까지는 필요한 사람이 작성하고 서명은 인증기관에 유료로 맡긴다. 이번에는 테스트 용도이기에 서명도 스스로 해본다. 당연하게도 이 인증서는 서버 신원 보증의 역할은 하지 못한다. 대신 통신 경로 은닉화 및 컴퓨터에 수동으로 신뢰할 수 있게 만들 수 있다.

### 6.2.2 HTTPS 서버와 인증서 등록

```go
package main

import (
    "fmt"
     "log"
     "net/http"
     "net/http/httpuil"
)

func handler(w http.ResponseWriter, r *http.Request) {
    dump, err := httputil.DumpRequest(r, true)
    if err != nil {
        http.Error(w, fmt.Sprint(err), http.statusInternalServerError)
        return
    }
    fmt.Println(string(dump))
    fmt.Fprintf(w, "<html><body>hello</body></html>\n")
}

func main() {
    http.HandleFunc("/", handler)
    log.Println("start http listening :18443")
    err := http.ListenAndServeTLS(":18443", "server.crt", "server.key", nil)
    log.Println(err)
}
```

### 6.2.3 Go 언어를 이용한 클라이언트 구현

```go
package main

import (
    "log"
    "net/http"
    "net/http/httputil"
)

func main() {
    resp, err := http.Get("https://localhost:18443")
    if err != nil {
        panic(err)
    }
    defer resp.Body.Close()
    dump, err := httputil.DumpResponse(resp, true)
    if err != nil {
        panic(err)
    }
    log.Println(string(dump))
}
```

```go

```

### 6.3 프로토콜 업그레이드

기본 http 통신에서 벗어나 커스텀 프로토콜으로 통신할 수 있다.

### 6.3.1 서버 코드

`Upgrade`와 `Connection` 헤더를 세팅해주고 101 Switching Protocols를 돌려주면 프로토콜을 업그레이드하여 통신할 수 있다.

```go

```

우선 Http ResponseWriter를 http.Hijacker로 하이재킹한다. 하이재킹하면 기존 ResponseWriter는 아무런 메시지도 보내지 않고, 소켓을 직접 조작할 수 있게 된다.

다음으로 response.Write()를 통해 포맷화된 응답을 반환할 수 있다. 이를 활용해 헤더와 스테이터스 코드를 첫 응답으로 보낸다.

마지막으로 로우 레벨 소켓을 감싼 bufio.ReadWriter를 통해 소켓 송수신값을 다룬다.

### 6.3.2 클라이언트 코드

```go

```

클라이언트는 먼저 net.Dialer로 TCP 소켓을 연다. 이 후에는 서버와 마찬가지로 요청을 작성하고 request.Write()를 통해 보낸다.

그 다음, http.ReadResponse를 통해 응답을 읽어 온다.

마지막으로 서버와 동일하게 bufio를 활용하고 bufio.Reader를 통해 net.Conn, os.File, StdinPipe()등에서 돌아오는 io.Reader의 인터페이스를 가진 오브젝트는 모두 처리한다.

### 6.4 청크

Go언어에서 청크는 이미 net/http 에 내장되어 있다. 단, 사용하는 입장에서 인식하기는 어렵다.

http.Post로 2048바이트 이상이나 Request.ContentLength를 지정하지 않은 경우에 자동으로 청크 형식으로 업로드된다.

### 6.4.1 서버에서 송신하기

서버에서 청크 전송은 간단하다. http.Flusher를 사용하면 Flush()를 통해 가능해진다.

```go

```

### 6.4.2 클라이언트에서 순차적으로 처리하기(간단판)

bufio.Reader를 통해서 XML/JSON이 아닌 파싱이 쉬운 데이터는 활용할 수 있다.

```go

```

### 6.4.3 클라이언트에서 순차적으로 수신하기(완전판)

앞서 업그레이드된 HTTP를 다룰 때 TCP 소켓을 직접 다루며 대응했다. 청크도 이와 같이 다룰 수 있다.

청크의 기능은 다음과 같다.

- 16진수 청크 길이 전송
- 지정된 크기의 데이터 전송
- 길이 0이 전송되면, 응답 끝

```go

```

### 6.5 원격 프로시저 호출

Go 언어에서 net/rpc 를 통해 RPC를 실현하는 프레임워크를 제공한다.

다음 조건을 만족하면 net/rpc를 활용할 수 있다.

- 메서드가 속한 구조체의 형이 공개되어 있다.
- 메서드가 공개되어 있다.
- 메서드는 두 개 인수를 가지며, 양쪽 다 공개되어 있거나 내장형이다.
- 메서드의 두 번째 인수는 포인터이다.
- 메서드는 error 형의 반환값을 가진다.

```go

```

```go

```

RPC 관련 기능은 표준 API로 제공되지만 품질이 높지는 않다. path 및 코덱을 간단히 지정할 수 없기 때문이다. /_goRPC_로 기본 경로가 제공되고, 일름 또한 구조체이름.메서드이름으로 고정되어 있다.