# 이벤트 버블링과 캡처링

![](https://i.imgur.com/l5WTayL.png)

- 표준 DOM 이벤트 흐름은 3단계로 이루어져 있다.
- 캡처링, 타깃, 버블링 단계로 나뉜다.

## 버블링

![](https://i.imgur.com/yRfGmSd.png)

- 한 요소에 이벤트가 발생하면 document 객체에 도달할 때까지 부모에서도 이벤트가 발생한다.
- 거의 모든 이벤트에서 발생한다. (focus와 같은 몇몇 이벤트에서는 발생하지 않는다.)
- `event.target`은 현재 클릭된 요소, `this(event.currentTarget)`은 실제 핸들러가 부착된 요소이다.
- `event.stopPropagation()`으로 버블링을 막을 수 있다.
- 해당 이벤트로 발생하는 모든 버블링을 막기 위해선 `event.stopImmediatePropagation`을 사용해야 한다.
- 버블링을 막는 것은 꼭 필요한 경우에만 해야한다. (가능하면 커스텀 이벤트로 처리하는 것이 좋다.)

## 캡처링

- 캡처링은 document 객체부터 이벤트가 발생한 요소까지 내려온다.

### 레퍼런스 & 이미지 출처

https://ko.javascript.info/bubbling-and-capturing
