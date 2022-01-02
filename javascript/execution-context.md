# 실행 컨텍스트

- 실행 컨텍스트는 실행 가능한 코드를 형상화하고 구분하는 추상적인 개념이다.
- 물리적으로는 객체의 형태를 가진다.
- 호이스팅, 외부 환경 정보 구성, this 결정 등이 이뤄지면서 다른 언어와 다른 특징이 나타나게 된다.
- 전역공간, 함수 실행, eval() 함수 실행, block(ES6+)을 만들면 생성된다.

## ES5 이전의 방식

<img src="https://i.imgur.com/DUJyOzj.png" width="400px"/>

## Variable Object (VO)

- 실행 컨텍스트가 생기면 자바스크립트 엔진은 실행에 필요한 여러 정보를 담을 객체를 생성한다.
- 이를 변수 객체라고 부른다.
- 변수, 매개변수, 인수 정보, 함수 선언을 갖는다.
- 전역 컨텍스트와 함수 컨텍스트가 가리키는 객체가 다르다.

### Global Object

- 최상위에 위치하며 유일하고 모든 전역 변수, 전역 함수 등을 포함하는 전역 객체이다.

### Activation Object

- 매개변수와 인수들의 정보를 담고 있는 arguments object가 추가된다.

## Scope Chain (SC)

<img src="https://i.imgur.com/P09CBLp.png" width="400px"/>

- 스코프 체인은 일종의 리스트로서 전역 객체와 중첩된 함수의 스코프를 레퍼런스로 차례로 저장하고 있다.
- VO를 가리키는 참조 리스트이다.
- 스코프 체인은 식별자 중에서 객체(전역 객체 제외)의 프로퍼티가 아닌 식별자, 즉 변수를 검색하는 매커니즘이다.
- 객체의 프로퍼티를 검색하는 매커니즘은 프로토타입 체이닝이다.

### this Value

- this에 규칙에 의한 적절한 값을 할당한다.

### 실행 컨텍스트 예시

- 자세한 동작 방식은 다음 링크를 통해 학습했다.
  [실행 컨텍스트 동작 방식](https://poiemaweb.com/js-execution-context)

## ES5 이후의 방식

![](https://i.imgur.com/XUfnCaB.png)

### Variable Enviroment

- 현재 컨텍스트 내의 식별자(변수)들에 대한 정보
- 외부 환경 정보
- 선언 시점의 Lexical Enviroment 스냅샷

### Lexical Enviroment

- 변경 사항이 실시간으로 반영된다.
- ES5 이전의 Variable Object, Activation Object, Scope Chain이 여기로 이전되었다.
- 자세한 설명은 [해당 글](https://github.com/Donggggg/TIL/blob/main/javascript/lexical-enviroment.md)에서 진행했다.

### ThisBinding

- 식별자가 바라봐야 할 객체이다.

### 레퍼런스 및 이미지 출처

https://poiemaweb.com/js-execution-context
https://junilhwang.github.io/TIL/Javascript/Domain/Execution-Context/
https://joontae-kim.github.io/2020/10/15/excution-context-2/
