# null과 undefined

### null과 undefined

- `null`은 비어있다.
- `undefined`는 정의되지 않았다.
- `==`는 자료형 변환을 안하고 비교한다.
- `<`와 같은 등호는 자료형을 변환하고 비교한다.
- `null == 0`는 `false`
- `null <= 0`는 `true`
- `null == undefined`는 `true`
- `null === undefined`는 `false`
- `undefined == 0`는 `false`
- `undefined <= 0`도 `false`

### null 병합 연산자 '??'

- `a ?? b`는 a가 `undefined`도 아니고 `null`도 아니면 a이고, 이 외에는 b이다.
- `||`와의 가장 큰 차이는 truthy를 찾기 위함과 아님이다.
- `a`가 `0`일 때, `a || b` 는 `b` 이고 `a ?? b` 는 `a` 이다.
