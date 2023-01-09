## 챕터 11 - 일급함수 2

### 11-1. 카피-온-라이트 리팩터링하기

앞 장에서 만든 배열 카피-온-라이트 함수가 여러 형태로 반복된다. 이를 콜백을 활용해 리팩터링한다. 이로써 얻을 수 있는 장점은 외부 라이브러리와 연동 및 성능 최적화가 있다.

### 11-1. 함수를 리턴하는 함수

다음으로 앞서나온 `withLogging()` 메서드를 고차함수를 활용해 리팩터링한다.

```tsx
function withLogging(callback) {
  try {
    callback();
  }
  catch (e) {
    sendErrorLog(e);
  }

function saveUser(user) {
  // Create User
}

withLogging(() => { saveUser(user) });
```

```tsx
function wrapLogging(func) {
  return (arg) => {
    try {
      func(arg);
    }
    catch (e) {
      sendErrorLog(e);
    }
  }
}

const saveUserWithLogging = warpLogging(saveUser);
saveUserWithLogging(user);
```

이렇 작성하면 `withLogging`으로 매번  감싸줄 필요없이 `wrapLogging` 을 통해 로그를 보내는 함수를 생성할 수 있다.

고차함수는 이렇게 반복을 줄이고 여러 장점을 취할 수 있다. 그렇다고 해서 프로그램 전체를 고차함수로 작성하는 것이 옳을까?에 대한 답변은 ‘아니다’라고 할 수 있다. 고차함수는 비교적 직관적이지 못하고 작성 비용이 있다. 고차함수가 가지는 장점이 진정으로 필요할 때 고차함수를 선택하는 것이 맞고 그렇지 않은 경우에는 사용하지 않는 것이 더 효율적일 수 있다.