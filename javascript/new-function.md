# new Function 문법

```//javascript
let func = new Function ([arg1, arg2, ...argN], functionBody);

let sum = new Function('a', 'b', 'return a+b');
sum(1, 2); // 3
```

- `new Function 문법`을 사용하여 함수를 만들 수 있다.
- 서버에서 전달받은 문자열을 이용해 새로운 함수를 만들고 실행하는 것이 가능하다.
- 즉, 다이나믹하게 함수를 생성할 수 있다.

### 클로저에 관한 고찰

- `new Function 문법`을 통해 만든 함수의 `[[Enviroment]]`는 외부 렉시컬 환경을 참조하는 것이 아닌 **전역 렉시컬 환경**을 참조한다.
- 그러므로 new Function 문법을 통해 만든 함수는 외부 렉시컬 환경을 참조할 수 없어 **클로저로 활용할 수 없다**.
- 추후 프로덕션에 배포될 때 `압축기(minifier)`에 의해 압축될 때 주석이나 여분의 공백 등을 없애기 위해 지역 변수명을 짧게 바꿀 때 문제가 발생한다.
- 그렇기에 외부 인수에 직접 접근하는 방법보다 함수의 **매개변수를 통해 처리**하는 것이 아키텍처 관점에서도 좋고 에러에 취약하지 않다.
