# Call by \*

### Call by Value

- JS의 원시형(Number, String, Boolean, undefined 등)는 함수의 인자로 넘겨질 때 전혀 다른 값을 준다.

### Call by Reference

- JS의 객체(객체, 배열, 함수 등)은 함수의 인자로 넘겨질 때 해당 변수의 주소값이 넘겨진다.

### Call by Sharing

- **객체를 함수의 인자로 넘겨줄 때, 객체 변수 자체를 다른 객체를 가리키게 하면 값이 변하지 않는 상황이 발생한다.**

```//javascript
// 예시
function fun1 (arr) {
  arr = ['new'];
}

let arr = [0, 1, 2, 3];
fun1(arr);
console.log(arr) // [0, 1, 2, 3];
```

- 이러한 현상은 자바스크립트에서 call by refernece 형식을 취할 때 원본을 전달하는 것이 아닌 원본의 복사본을 전달하기 때문에 발생한다.
- 아래는 위 코드 상황에서의 변화를 그림으로 표현한 것이다.

<img src="https://i.imgur.com/4CsoGCM.png" width=500/>

- 위의 상태에서 아래의 상태로 변한다.

<img src="https://i.imgur.com/3OsdqBA.png" width=500/>

- 이러한 변화를 나타내기에 원본 객체의 변화는 없는 것이다.
