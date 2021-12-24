# V8 엔진

### V8 엔진이란?

- Google에서 만든 Javascript & 웹 어셈블리 엔진이다.
- 오픈 소스이고 C++로 작성되었다.
- Chrome과 node.js 그리고 최신에는 Edge에서도 사용되고 있다.
- V8 엔진은 인터프리터 방식처럼 한 줄씩.. 컴파일하지 않는다.
- 소스 코드 전체를 Byte Code로 만들고, 후에 런타임에 최적화 기법을 추가하며 최적화한다.
- 그렇기에 V8 엔진은 JIT(Just-In-Time) 컴파일러이다.

### 내부 쓰레드

1. 소스 코드를 컴파일하고 실행하는 메인쓰레드가 있다.
2. 코드를 최적화하는 쓰레드가 있다.
3. 프로파일링을 하는 프로파일러 쓰레드가 있다.
4. 그 외 가비지 컬렉션을 위한 몇 개의 쓰레드가 있다.

## 동작 방식

### 동작 순서

![V8 엔진 동작 순서](https://i.imgur.com/mV0lX7i.png)

1. Blink(HTML, CSS 엔진)에서 `<script>` 태그를 만나면 Javascript 컴파일링을 시작한다.
2. 소스 코드를 `파서(Parser)`로 보낸다.
3. 파서는 소스 코드를 분석한 후 `AST(Abstract Syntax Tree), 추상 구문 트리`로 변환한다.
4. AST는 `Ignition, 점화기`를 통해 `Byte Code(바이트 코드)`로 변환된다.
5. 컴파일된 바이트 코드를 실행하여 소스 코드가 실제로 동작하게 된다.

### Parser와 AST

- Parser는 Token을 가지고 Ignition이 사용할 AST를 만든다.
- AST는 소스코드를 트리 구조로 만든 자료구조이고, 컴파일러에서 주로 사용된다.
- V8은 모든 코드를 즉시 Parsing하지 않는다.
  - 실행하지 않을 코드를 컴파일하면 리소스가 낭비된다.
  - 컴파일 후 메모리에 적재하고 있어야 하기 때문에, 가비지가 회수하기 전까지 메모리가 낭비된다.
- 이를 해결하기 위해 `Pre-Parser`를 함께 진행한다.
- Token 중 참조하지 않는 Token을 Pre-Parser로 전달하여 실제 작업에 필요한 최소한을 파싱하고 Pre-Parser로 나중에 요청시 컴파일된다.

### Ignition(점화기)

- AST를 Byte Code로 컴파일 한다.

```
0xfde0821004e LdaConstant [0]
0xfde08210050 Star r1
0xfde08210052 Mov <closure>, r2
0xfde08210055 CallRuntime [DeclareGlobals], r1-r2
0xfde0821005a LdaGlobal [1], [0]
0xfde0821005d Star r1
0xfde0821005f LdaSmi [101]
0xfde08210061 Star r2
0xfde08210063 LdaSmi.Wide [201]
0xfde08210067 Star r3
0xfde08210069 CallUndefinedReceiver2 r1, r2, r3, [2]
0xfde0821006e Star r0
0xfde08210070 Return
```

- 위와 같이 레지스터와 관련된 형태로 표현된다.
- 바이트 코드는 기계어로 해석되기 바로 전단계이다.

### Turbofan

- Turbofan은 Profiler가 분석한 결과를 기반으로 컴파일된 바이트 코드를 최적화시킨다.
- 메모리 사용량 감소, 실행시간 단축, 코드 크기를 줄이고 속도 향상의 효과를 가진다.

```
1. Hot & Stable : 자주 호출되고 코드가 변하지 않는 경우
2. Small Function: 짧은 함수인 경우
```

- 위 두가지 경우에 최적화가 진행된다.

```
1. Graph Building: 바이트 코드 또는 AST를 Graph로 만든다.
2. Native Context Specialization & Inlining: Load/Store/Call IC를 기반으로 기본 컨텍스트에 특화된 Simple Graph를 생성한다.
3. Typed Opimization: Type에 따라 Simple Graph로 변환한다.
4. General Optimization: Graph를 기반으로 중복 제거 같은 최적화를 진행한다.
5. Code Generation: 죽은 코드를 제거하고, 레지스터에 할당한다.
```

- 위의 과정은 구글에서 제공한 Turbofan 내부 동작과정인데 굉장히 어렵다..;
