## 챕터 14 - 중첩된 데이터에 함수형 도구 사용하기

### 14-1. 객체를 고차함수로 다루기

특정 필드에 연산이 필요하고, 그 연산 로직이 반복되면 또 악취가 발생한다. 연산을 처리하려면 명시적 인자로 바꿈과 동시에 콜백으로 넘겨줘야 한다. 그렇게 update() 메서드를 만들 수 있다. 하지만 객체는 중첩이 되는 상황이 빈번하고, 이론상 무한 중첩이 발생하면 중첩 레벨마다 updateN() 메서드를 만들어야 한다.

이를 해결하기 위해 nestedUpdate() 메서드를 작성해야 한다.

```tsx
function nestedUpdate(object, key, modify) {
  if(keys.length === 0) return modify(object);
  let key1 = keys[0];
  // if(!object.key1) throw Error('키값 잘못 넣었데이');
  let restOfKeys = drop_first(keys);
  return update(object, key1, (value1) => {
    return nestedUpdate(value1, restOfKeys, modify);
  });
}
```

다음으로 키값이 많아지는 경우 유지보수성이 떨어지는 함수가 될 수 있음을 제시하며 추상화벽(구현을 감춘 인터페이스 메서드들)을 활용하는 것을 추천한다. 나쁘지는 않은 것 같은데 나는 당장 구현할 때는 통으로 추상화벽으로 가져가고 추후에 추상화벽을 세분화하는 것의 장점이 보일 때 진행할 것 같다. (해당 예제 코드의 이해도가 부족해서 그럴 수도 있지만)

마지막으로 고차함수들을 통해 할 수 있는 것들을 정리하며 마무리한다. for loop를 대체하고, 객체 연산을 효율적으로 하고, 카피-온-라이트 로직을 활용하고, 특정 로직(로깅 등)을 모듈화하는 것들을 할 수 있었다.