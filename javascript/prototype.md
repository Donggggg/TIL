# 프로토타입

- 자바스크립트 객체는 `[[Prototype]]`이라는 **숨김 프로퍼티**를 갖는다.
- null이나 다른 객체를 값으로 가질 수 있는데 값을 가질 경우 해당 객체를 **prototype**이라고 부른다.
- 어떤 객체에서 특정 프로퍼티를 읽을 때 해당 객체에 찾는 프로퍼티가 없다면 프로토타입을 찾아가 프로퍼티를 찾는다.
- 이것이 **프로토타입 상속**이다.
- `__proto__` 라는 `[[Prototype]]` 전용 getter/setter 로 설정할 수 있다.
- 최근에는 해당 방법보다 `Object.getPrototypeOf`나 `Object.setPrototypeOf`를 사용한다.
- 순환 참조는 할 수 없고 두 개 이상의 프로토타입을 상속할 수 없다.

## 함수의 프로토타입

- 리터럴이 아닌 new F와 같은 생성자 함수로 새로운 객체를 만들 수 있다.
- `F.prototype`에 객체나 null을 넣어 설정한다.
- 디폴트로 `constructor` 프로퍼티에 함수 자신을 가리키는 protortype 프로퍼티가 설정된다.

## 내장 객체의 프로토타입

- `new Object`나 `{}`로 객체를 생성하면 내장 객체 생성자 함수인 `Object`를 통해 생성된다.
- 새롭게 생성된 객체 인스턴스의 `[[Prototype]]`은 자연스레 `Object.prototype`을 가리킨다.

![](https://i.imgur.com/Gse4N01.png)

- `Date, Number, Function` 등 다양한 내장 객체들도 프로토타입 메서드를 지정해 놓는다.
- 내장 프로토타입의 루트는 항상 `Object.prototype`이다.

## 모던 프로토타입

- `Object.create(proto, descriptor), Object.getPrototypeOf(obj), Object.setPrototypeOf(obj, proto)`로 모던한 방식으로 프로토타입을 관리할 수 있다.
- **proto**를 사용하면 **proto**를 키로 접근할 때 오류가 발생한다.
- 그러므로 이런 상황에서는 `Object.create(null)`로 **플레인 객체**를 만들거나 `Map`을 활용해야한다.

### 레퍼런스 및 이미지 출처

https://ko.javascript.info/native-prototypes
