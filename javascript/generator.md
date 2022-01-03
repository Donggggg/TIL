# 제네레이터

- **여러 개의 반환 값을 하나씩`(yield)` 리턴**할 수 있는 함수이다.
- 띄엄띄엄 들어오는 데이터 스트림(대용량)을 처리할 때 유용하다.

```//
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}
```

- 일반 함수와는 동작 방식이 달라 함수를 호출하면 코드가 실행되지 않고 **제네레이터 객체**가 반환된다.
- 제네레이터의 주요 메소드인 **next()를 통해 다음 yield를 만날 때**까지 실행이 지속된다.
- `next()`의 리턴은 `{value: data, done: boolean}` 형태이다.

```//
let range = {
  from: 1,
  to: 5,

  *[Symbol.iterator]() {
    for(let value = this.from; value <= this.to; value++) {
      yield value;
    }
  }
};
```

- 이터러블에서 학습한 범위 생성 객체를 다음과 같이 개선할 수 있다.

### 제네레이터 컴포지션

```//
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

function* generatePasswordCodes() {

  // 0..9
  yield* generateSequence(48, 57);

  // A..Z
  yield* generateSequence(65, 90);

  // a..z
  yield* generateSequence(97, 122);

}
```

- 위와 같이 `yield*` 문법을 통해 제네레이터 내부에 다른 제네레이터를 넣을 수 있다.

### 제네레이터 양방향 데이터 교환

- 단방향으로 리턴하는 것을 넘어 **양방향 데이터 교환**이 제공된다.
- `generator.next(arg)`를 호출하여 가능하다.
- `generator.thow(Error)`를 호출해 에러를 던질 수 있다.

```//
function* gen() {
  let result = yield "2 + 2 = ?";

  alert(result);
}

let generator = gen();

let question = generator.next().value;

generator.next(4);
```

- `question`에 `2 + 2 = ?`이 담긴다.
- 해당 결과를 처리하여 `generator.next(4)`를 호출해 `result`에 `4`를 넣고 출력한다.

### async 제네레이터

```//
async function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) {
    await new Promise(resolve => setTimeout(resolve, 1000));
    yield i;
  }
}

(async () => {
  let generator = generateSequence(1, 5);
  for await (let value of generator) {
    alert(value);
  }
})();
```

- 제네레이터에서 비동기를 사용할 수 있다.
- 함수 앞에는 `async`를 붙이고 반복문에 `for await` 문법을 사용한다.

### async 이터러블

- 보통 이터러블 객체를 만들 때 제네레이터 문법을 사용하는 것이 일반적이다.
- 제레네이터를 활용한 이터러블 객체에서 비동기를 추가하면 다음과 같다.

```//
let range = {
  from: 1,
  to: 5,

  async *[Symbol.asyncIterator]() {
    for(let value = this.from; value <= this.to; value++) {
      await new Promise(resolve => setTimeout(resolve, 1000));

      yield value;
    }
  }
};

(async () => {
  for await (let value of range) {
    alert(value); // 1, 2, 3, 4, 5
  }

})();
```

### 레퍼런스

https://ko.javascript.info/async-iterators-generators
