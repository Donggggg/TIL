# 이벤트 루프

![](https://i.imgur.com/XViTtyM.png)

- 자바스크립트는 단일 스레드, 정확히는 하나의 콜 스택에서 돌아간다.
- 싱글 스레드에서 비동기 요청을 처리 하기 위해 브라우저나 node에서 처리하는 방식이 이벤트 루프이다.

### Web APIs

- 브라우저나 node에 내장되어 있는 API이다.
- DOM, AJAX, Timeout을 관리한다.
- 관리하는 것들이 요청되면 콜백 큐에 적절히 넣어준다.

### Callback Queue

- 크게 태스크 큐(Task Queue), 마이크로태스크 큐(Microtask Queue)가 있다.
- 태스크 큐는 타이머 함수 콜백, UI 렌더링, requestAnimationFrame 함수 등이 들어온다.
- 마이크로태스크 큐는 Promise, MutationObserver가 들어온다.
- 우선순위가 마이크로태스크 큐가 태스크 큐보다 높다.

### 레퍼런스 및 이미지 출처

https://meetup.toast.com/posts/89
https://velog.io/@thms200/Event-Loop-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EB%A3%A8%ED%94%84
