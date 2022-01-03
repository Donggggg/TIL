# 반복 구문

### 레이블 지정

- 중첩된 반복문을 한 번에 빠져나올 때 사용하면 좋다.
  > outer: for(...)
  > for(...) {
  > break outer;
  > }

### 유사 배열

- 객체인데 인덱스 속성과 length가 있는 객체이다.
- Array의 메서드를 사용하지 못한다. (apply, call을 활용하면 사용할 수 있다.)
- `Array.isArray()` 함수나 `array instanceof Array`로 구분할 수 있다.

```
let unknown = {
  "0": 123,
  "1": 14343,
  "2": 1232,
  "length": 3
}
```

### 일반 객체를 iterator화 하기

- Symbol.iterator를 활용해서 아래와 같이 가능하다.

```//javascript
let range = {
  from: 1,
  to: 5,

  [Symbol.iterator]() {
    this.current = this.from;
    return this;
  },

  next() {
    if (this.current <= this.to) {
      return { done: false, value: this.current++ };
    } else {
      return { done: true, value: null };
    }
  },
};

for (let i of range) {
  console.log(i);
}
```

### 이터러블과 유사 배열

- 이터러블(iterable)은 Symbol.iterator 메서드가 구현된 객체
- 유사 배열은 length 프로퍼티가 존재하는 객체
- `Array.from` 메서드를 활용해 이터러블과 유사 배열을 진짜 배열로 만들 수 있다.

### async 이터레이터

```//
let range = {
  from: 1,
  to: 5,

  [Symbol.asyncIterator]() {
    return {
      current: this.from,
      last: this.to,

      async next() {
        if (this.current <= this.last) {
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      }
    };
  }
};

(async () => {
  for await (let value of range) {
    alert(value);
  }
})()
```

- 위에서 일반 객체를 이터레이터화 한 것을 비동기 이터레이터로 변경한 코드이다.
- `Symbol.iterator` 대신 `Symbol.asyncIterator`를 사용한다.
- `next()` 메서드는 프로미스 객체를 리턴해야 한다.
- 반복을 위해 `for await` 문법을 사용한다.
- 비동기 이터레이터는 전개연산자 문법을 사용할 수 없다.
-
