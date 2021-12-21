# 객체

### 참조 방식과 복사

- 객체는 참조값을 저장하고 있다.
- 단순 `=` 연산자로 복사할 경우 복사되지 않는다.
- `Object`의 내장함수인 `assign` 을 활용하면 한 레벨만 복사되는 얕은 복사가 가능하다.
- 깊은 복사를 위해선 중첩 객체까지 복사하는 함수를 구현하면 된다.

### 원시형으로 변환하기

- 형 변환 시 `hint` 를 기준으로 세 종류로 구분할 수 있다.
  > 1. string
  > 2. number
  > 3. default
- 보통 string / number, default 단위로 처리 한다.

- 변환 시 다음 알고리즘이 적용된다.

  > 1. 객체에 `obj[Symbol.toPrimitive](hint)` 메서드가 있는지 찾고 있다면 호출한다.
  > 2. 1에 해당하지 않고 hint가 string이면 obj.toString()이나 obj.valueOf()를 호출한다.
  > 3. 1과 2에 해당하지 않고 hint가 number나 defaultaus obj.valueOf()나 obj.toString()을 호출한다.

- 심볼을 활용해 구현하는 것이 제일 좋으나 toString() 하나로도 충분히 구현할 수 있다.
