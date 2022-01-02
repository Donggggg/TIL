# 비동기 콜백

## 콜백

```//
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;
  script.onload = () => callback(script);
  document.head.append(script);
}

loadScript('https://cdnjs.cloudflare.com/ajax/libs/lodash.js/3.2.0/lodash.js', script => {
  alert(`${script.src}가 로드되었습니다.`);
  alert( _ ); // 스크립트에 정의된 함수
});
```

- 위 코드와 같이 콜백 함수를 이용해 비동기 로직을 구현하는 것이 일반적인 접근법이다.

```//javascript
loadScript('/my/script.js', function(script) {

  loadScript('/my/script2.js', function(script) {

    loadScript('/my/script3.js', function(script) {
      // 세 스크립트 로딩이 끝난 후 실행됨
    });

  })

});
```

- 여러 개의 스크립트를 불러오고 싶다면 다음과 같이 콜백 함수의 내부에 또 콜백 로직을 추가해야한다.
- 함수 내부에 로직이 복잡해진다면 가독성도 떨어지고 구현도 힘들다.

![](https://i.imgur.com/Q8l4K6I.png)

- 이런 콜백 함수 중첩 현상을 `콜백 지옥(callback hell)`이라고 부른다.

```//
loadScript('1.js', step1);

function step1(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('2.js', step2);
  }
}

function step2(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('3.js', step3);
  }
}
...
```

- 함수를 분리해서 관리할 수도 있지만 이 방법 또한 코드가 찢어져 있어 함수를 읽기에 불편하다.
- `step*` 함수들은 콜백 지옥을 피하기 위해 작성된 함수이기에 의미없는 네이밍을 지어야 한다.

### 레퍼런스 및 이미지 출처

https://ko.javascript.info/
