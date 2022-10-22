## 4장 - 액션에서 계산 빼내기

해당 장에서는 한 웹사이트의 상품 목록 및 장바구니 기능을 중심으로 액션에서 계산을 분리해 나가는 과정을 다룬다.

### 4-1. 절차형 프로그래밍으로 구현하기

먼저 요구사항을 절차형으로 작성해봅니다. 장바구니에 상품을 추가하는 초기 요구사항을 시작으로 20달러 이상 구매 시 무료 배송 및 불릿 추가, 세금 계산이 추가됩니다. 이때까지는 절차형으로 구현하여도 큰 불편함은 없게 느껴집니다.

하지만 이 상황에서 비즈니스 로직이 잘 동작하는지 테스트하기 위해서는 브라우저를 켜서 직접 조작한 뒤 DOM이 잘 변경되는지 눈으로 확인할 수 밖에 없습니다. 이 과정은 비효율적이고 개발자를 피곤하게 합니다. 이 문제를 해결하기 위해 책의 조지는 다음과 같이 제안합니다.

1. DOM 업데이트와 비즈니스 규칙은 분리되어야 합니다.
2. 전역변수가 없어야 합니다. → 갑자기 이런 결론이 나오는 건 이상하긴 하네.

다음으로 결제팀과 배송팀에서 우리의 코드를 재사용하는 상황이 발생합니다. 하지만 우리의 코드는 재사용하기 그리 좋지 않은 구조입니다. 이 문제를 해결하기 위해 책의 제나는 다음과 같이 제안합니다.

1. 전역변수에 의존하지 않아야 합니다.
2. DOM을 사용할 수 있는 곳에서 실행된다고 가정하면 안됩니다. → 함수에서 DOM 조작을 하지 않아야 한다.
3. 함수가 결괏값을 리턴해야 합니다.

### 4-2. 액션에서 계산 빼내기

먼저 함수의 입출력에 대해 이해해야 합니다. 함수는 입력과 출력이 있습니다. 또한 입/출력은 명시적/암시적으로 구분이 되고 다음과 같이 요약할 수 있습니다.

1. 명시적 입력 - 인자
2. 암시적 입력 - 인자 외 다른 입력 (전역변수 읽기, DOM 참조)
3. 명시적 출력 - 리턴값
4. 암시적 출력 - 리턴값 외 다른 출력 (전역변수 쓰기, DOM 조작)

액션은 암시적 입출력이 있는 함수입니다. 절차형으로 작성한 기존 코드는 위에서 암시적 입출력으로 구분한 전역변수와 DOM 관련 로직이 퍼져있기 때문에 액션 지옥입니다. 이제 액션에서 빼낼 수 있는 계산을 추출해야 합니다.

```tsx
// 리팩터링 전의 calc_cart_total()

function calc_cart_total() {
  shopping_cart_total = 0;
  for(let i = 0; i < shopping_cart.length; i++) {
    let item = shopping_cart[i];
    shopping_cart_total += item.price;
  }

  set_cart_total_dom();
  update_shipping_icons();
  update_tax_dom();
}
```

```tsx
// 서브루틴 추출하기 적용

function calc_cart_total() {
  calc_total();
  set_cart_total_dom();
  update_shipping_icons();
  update_tax_dom();
}

function calc_total() {
  shopping_cart_total = 0;
  for(let i = 0; i < shopping_cart.length; i++) {
    let item = shopping_cart[i];
    shopping_cart_total += item.price;
  }
}
```

```tsx
// 암시적 입출력 개선

function calc_cart_total() {
  shopping_cart_total = calc_total(shopping_cart);
  set_cart_total_dom();
  update_shipping_icons();
  update_tax_dom();
}

// 계산 함수 calc_total()
function calc_total(cart) {
  let total = 0;
  for(let i = 0; i < cart.length; i++) {
    let item = cart[i];
    total += item.price;
  }
}
```

위의 예시 코드와 같이 액션에서 계산을 추출할 수 있습니다. 계산으로 추출할 수 있는 로직을 서브루틴으로 분리하고, 해당 함수의 암시적 입출력을 명시적으로 바꾸면 액션에서 계산을 추출할 수 있게 됩니다. 이런 함수형 사고를 적용함으로써 액션을 줄이고 계산을 늘일 수 있었고 이는 테스트와 재사용하기에 유리하게 된 것입니다.

하지만, 특정 경우에 계산으로 빼낼 수 없기도 합니다. 이러한 경우에 어떻게 처리해야 하는지 다음 장에서 더 다뤄본다고 합니다.

이 장에서 느낀 점은 함수형으로 생각하고 적용함으로써 확실히 안전하게 코드를 작성할 수 있을 것 같다. 당연하게도 접근 범위가 넓은 영역(전역 변수, DOM)은 다루기 힘들다. 버그를 일으키기도 쉽고 버그가 발생했을 때 추적하기도 힘들다. 후에 절차형으로 작성되어 비슷한 문제를 가지고 있는 코드를 본다면 어디까지 적용이 되나 테스트 해보고 싶기도 하다.