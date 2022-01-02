# Promise

### 특징

```//
let promise = new Promise(function (resolve, reject) {
    // executor()
});
```

- 위와 같은 문법으로 생성할 수 있다.
- 기본 인자인 `resolve`와 `reject`는 각각 `값`과 `에러`를 넣어 executor 함수의 결과를 처리한다.
- promise 객체 내부에는 현재 상태인 `state`와 결과물인 `result`가 존재한다.

![](https://i.imgur.com/0HIGaWU.png)

- `state`는 기본값이 `pending`상태이고 처리된다면 `fulfilled`나 `rejected`로 변경된다.
- `result`는 기본값이 `undefined`이고 `value`나 `Error 객체`이다.
- 이미 settled된 promise를 다시 결과 처리를 하면 무시한다.

### then, catch, finally

- `then(func1, func2)` 함수의 `func1`은 성공한 결과를 처리하는 함수이고 `func2`는 실패한 결과를 처리하는 함수이다.
- `catch(func1)` 함수는 실패한 결과만 처리하는 함수이다.
- `finally(func1)` 함수는 결과에 상관없이 처리할 로직을 넣는 함수이다.

### 프로미스 체이닝

- 프로미스 객체는 `then` 함수를 가진 `thenable` 객체이다.
- 이를 활용해 다음과 같은 코드를 작성할 수 있다.

```//
new Promise(function(resolve, reject) {

  setTimeout(() => resolve(1), 1000);

}).then(function(result) {

  alert(result); // 1

  return new Promise((resolve, reject) => { // (*)
    setTimeout(() => resolve(result * 2), 1000);
  });

}).then(function(result) { // (**)

  alert(result); // 2

  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(result * 2), 1000);
  });

}).then(function(result) {

  alert(result); // 4

});
```

### 프로미스 API

### Promise.all

```//
let promise = Promise.all([...promises...]);
```

- 이터러블을 인자로 받고 배열 내부의 모든 프로미스 처리를 기다린다.
- 모든 요소의 처리가 끝나면 결과가 배열에 담겨 나온다.
- 거부되는 경우 모든 처리가 무시되고 에러가 결과가 된다.

### Promise.allSettled

```//
let urls = [
  'https://api.github.com/users/iliakan',
  'https://api.github.com/users/remy',
  'https://no-such-url'
];

Promise.allSettled(urls.map(url => fetch(url)))
  .then(results => { // (*)
    results.forEach((result, num) => {
      if (result.status == "fulfilled") {
        alert(`${urls[num]}: ${result.value.status}`);
      }
      if (result.status == "rejected") {
        alert(`${urls[num]}: ${result.reason}`);
      }
    });
  });
```

- 이터러블을 인자로 받고 배열 내부의 모든 프로미스 처리를 기다리는 것은 `Promise.all`과 동일하지만 성공/실패에 따라 다른 결과를 담고있다.
- 최신 기술이기에 폴리필을 구현해야 한다.

### Promise.race

```//
let promise = Promise.race(iterable);
```

- 이터러블을 인자로 받고 배열 내부 중 가장 빨리 처리된 프로미스 결과만 반환한다.

### Promise.resolve/reject

- `async, await`이 나온 후에 잘 안쓰인다.
- 각각 성공/실패 상태인 프로미스 객체를 만들어준다.

### 레퍼런스 및 이미지 출처

https://ko.javascript.info/
