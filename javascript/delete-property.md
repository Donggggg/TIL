# JavaScript 객체의 Property를 삭제하는 다양한 방법

### 문제 상황
- 어드민 프로젝트의 Warning을 제거하던 중 unused variables 관련 작업에서 사이드 이펙트를 만남
- `const {unusedVar, ...lefts} = myObject;`
- 여기서 `unusedVar` 이 사용되지 않았기에 제거함
- 하지만 위에 작성된 코드는 `unusedVar`라는 속성을 제거하고 `lefts`를 사용하기 위해 작성된 것..

**코드의 목적이 한 눈에 파악되지 않는 문제와 lint가 잡는 warning이기에 개선해야 한다고 느낌**

### 해결책
방법1 → `delete myObject.unusedVar`
delete로 객체에 키를 지울 수 있다.
하지만 해당 방법은 느림

방법2 → `myObject.unusedVar = null 이나 undefined`
해당 방식으로 객체의 프로퍼티를 지운다기보다는 값을 날려버린다가 맞다.
delete를 쓸때보다 100배정도 빨라 속도가 중요할 때 사용하면 좋을 것 같음

방법3 → `const deleteProperty = (key, {[key]:_, ...newObj} => newObj`
불변성을 지키면서 키를 삭제하고 싶다면 함수를 정의하여 사용하면 됨
엄연히 따지자면 삭제보다는 해당 프로퍼티를 제외한 다른 객체를 생성함.


### 성능 테스트
```
const temp1 = {a: 1, b: 2, c: 3};
const temp2 = {...temp1};
const temp3 = {...temp2};

const deleteKey1 = (key, obj) => delete obj[key];
const deleteKey2 = (key, obj) => obj[key] = undefined;
const deleteKey3 = (key, {[key]: _, ...newObj}) => newObj;

console.time('Delete Command');
deleteKey1('b', temp1);
console.timeEnd('Delete Command');

console.time('Set undefined');
deleteKey2('b', temp2);
console.timeEnd('Set undefined');

console.time('Spread Operator');
const newObj = deleteKey3('b', temp3);
console.timeEnd('Spread Operator');
```

```
// 결과
Delete Command: 0.077ms
Set undefined: 0.018ms
Spread Operator: 0.028ms
```

- delete가 확실히 느리다. (실제로 끊는 작업이 들어가니까)
- 그러나, 각자의 장단이 있기에 상황에 맞게 사용하면 좋을 것 같다. 