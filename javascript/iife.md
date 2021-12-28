## 즉시 함수 실행 표현식

- IIFE(Immediately-Invoked Function Expression)는 함수 선언과 동시에 실행하는 표현법이다.
- var 변수가 블록 레벨로 작성되지 않아 과거에 사용했던 방식이다.

```//javascript
// IIFE를 만드는 방법

(function() {
  alert("함수를 괄호로 둘러싸기");
})();

(function() {
  alert("전체를 괄호로 둘러싸기");
}());

!function() {
  alert("표현식 앞에 비트 NOT 연산자 붙이기");
}();

+function() {
  alert("표현식 앞에 단항 덧셈 연산자 붙이기");
}();
```
