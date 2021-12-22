# 반복문

### 레이블 지정

- 중첩된 반복문을 한 번에 빠져나올 때 사용하면 좋다.
  > outer: for(...)
  > for(...) {
  > break outer;
  > }

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
