# keys(), values(), entries()

### 객체에서의 keys, values, entries

- 인자로 넘겨 사용할 수 있다. `ex. Object.keys(obj)`
- map, set, array와 다르게 '진짜' 배열을 리턴한다.

### 객체에서 배열의 고차함수 사용법

- `Object.fromEntries()` 를 활용해 할 수 있음.

```//javascript
let object = {
  x: 1,
  y: 2,
  z: 6,
};

object = Object.fromEntries(
  Object.entries(object).map(([key, value]) => [key, value * 2])
);

console.log(object);
```
