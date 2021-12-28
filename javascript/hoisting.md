# 호이스팅

### var의 호이스팅

```//javascript
// 이렇게 코드를 작성해도
function sayHi() {
  phrase = "Hello";

  alert(phrase);

  var phrase;
}
sayHi();
```

```//javascript
// 이렇게 최상단으로 끌어 올려진다.
function sayHi() {
  var phrase;

  phrase = "Hello";

  alert(phrase);
}
sayHi();
```

- 위의 상황과 같이 끌어 올려지는 현상을 호이스팅(hoisting)이라고 한다.
- 선언은 호이스팅되지만 할당은 호이스팅되지 않는다.

### let, const의 호이스팅

```//javascript
function sayHi() {
  phrase = "Hello";

  alert(phrase); // reference error 발생!

  let phrase;
}
sayHi();
```

- let과 const 변수에도 호이스팅이 발생하지만 방식이 다르다.
- 위와 같이 작성 시 phase가 선언은 됐으나 참조할 수 없어 에러를 발생시킨다.

```//javascript
// TDZ 발생

let a = 10; // 전역변수 a선언
if(true){
  console.log(a); // ReferenceError: Cannot access 'a' before initialization
  let a = 20; // 지역변수 a 선언
 }
```

- 위와 같이 블록 레벨에 지역 변수를 선언하면서 호이스팅이 발생해 외부 렉시컬 환경에 a가 있음에도 자신의 환경 레코드에 a 변수가 있기에 TDZ(Temporal Dead Zone)이 발생한다.
