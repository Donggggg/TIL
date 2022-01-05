# Sync & Async와 Blocking & NonBlocking

## Blocking NonBlocking

- 호출되는 함수가 바로 리턴되느냐 마느냐에 집중해야 한다.

### Blocking

- callee가 자신의 작업을 마칠 때까지 리턴을 하지 않으면 Blocking이다.
- 실세계에 비유하자면 내가 남에게 일을 시켰는데 남이 일을 끝낼 때까지 내 일을 못하는 것이다.

### NonBlocking

- callee가 바로 리턴을 하면 NonBlocking이다.
- 실세계에 비유하자면 내가 남에게 일을 시키고 나는 내 일을 하는 것이다.

## Synchronous/ Asynchronous

- 작업 완료 여부를 누가 신경쓰느냐에 집중해야 한다.

### Synchronous

- Synchronous라는 영단어의 뜻은 동기, 즉 같은 시간이라는 말인데 단어의 뜻으로 개념을 이해하기 쉽지 않다.
- 프로그래밍적으로 이해가 쉽다.
- caller가 함수의 작업 완료 여부를 계속 신경쓰면 동기이다.
- 다른 의미로는 특정 코드의 연산이 끝날 때까지 실행을 하며 계속 끝났는지 확인하는 것이다.
- 실세계에 비유하자면 내가 남에게 일을 시켜놓고 내 일을 하다가 주기적으로 끝났는지 물어보는 것이다.

### Asynchronous

- a가 붙어 부정형이 된 Asynchronous의 뜻은 비동기, 즉 같은 시간이 아님이라는 말인데 이도 프로그래밍적으로 이해를 하면 쉽다.
- callee가 함수의 작업이 완료되면 callback으로 처리하는 것과 같이 알아서 처리하는 것이 동기이다.
- 다른 의미로는 특정 코드의 연산이 끝날 때까지 실행을 멈추지 않고 다음 코드 실행하는 것이다.
- 실세계에 비유하자면 내가 남에게 일을 시켜놓고 내 일을 하다보면 남이 나에게 끝났다고 알려주는 것이다.

## 다양한 조합

![](https://i.imgur.com/DEKgq5U.png)

### Sync + Blocking

- 대표적으로 Java에서 사용되고 있는 방식이다.

### Async + NonBlocking

- 대표적으로 Javascript에서 사용되고 있는 방식이다.

### Sync + NonBlocking

- 실제로 사용해본 적은 없지만 Java 진영의 Future.isDone 메서드에서 사용된다.

### Async + Blocking

- Blocking으로 인해 제어권을 뺏겨 Async로의 장점이 없는 조합이다.
- 실제로 사용할 이유가 없지만 Node.js + SQL로 서버를 구성하게 되면 의도치 않게 마주치게 된다.
- Node는 싱글 스레드 기반으로 Async+NonBlocking 동작을 기대한다.
- 하지만 관계형 데이터베이스의 쿼리 과정은 DB의 안전성을 위해 Lock이라는 개념이 존재한다.
- Lock은 DB가 데이터를 관리하는 특정 단위 (테이블, 행 등)를 특정 쿼리가 선점할 수 있도록 해주는 개념이다.
- 그러므로 쿼리가 실행될 때 Lock으로 인해 다른 쿼리의 작업이 완료되는 것을 기다리는 상황이 발생한다.
- 이 때 Node에서는 NonBlocking을 기대하고 Async로 동작하지만 Lock에 의해 Blocking 되는 상황이 종종 발생한다.
- 이렇게 Async + Blocking 조합이 발생하는 상황이 존재하게 된다.
- 이는 Node만의 문제는 아니고 Java 진영의 JDBC를 사용할 때도 발생한다고 한다. 비록 싱글 스레드 기반인 Node에서 더 높은 확률로 발생하기에 더 대표적인 예시로 꼽히는 것이다.

### 레퍼런스 및 이미지 출처

준일님의 멘토링  
https://baek-kim-dev.site/38
