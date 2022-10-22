## 챕터 5 - 더 좋은 액션 만들기

### 5-1. 요구사항을 설계﹒함수와 맞추기

이전에 약간 의아했던  `gets_free_shipping()` 함수의 문제점을 지적하며 시작한다.

```jsx
function gets_free_shopping(total, item_price) { 
  return item_price + total >= 20;
}

function calc_total(cart) {
  ...
  total += item.price;
	...
}
```

기존 작성된 코드는 다음과 같은 문제가 있다.

1. **함수의 매개변수가 적절하지 않다. (오설계)**
2. **가격의 총합을 계산하는 로직이 중복된다. (코드 스멜)**

첫 번째 문제를 해결하기 위해 `gets_free_shopping()` 함수의 인자를 변경합니다. 현재 카트의 상태로 무료 배송이 가능한지 구한다는 요구사항에 맞게 **인자로 cart 객체를 넘겨주도록 변경한다.**

두 번째 문제를 해결하기 위해 카트의 모든 제품 가격의 합을 구하는 `calc_total()` **함수를 재활용한다.**

```jsx
// 개선된 함수
function gets_free_shopping(cart) {
  return calc_total(cart) >= 20;
}
```

### 5-2. 암묵적 입출력 줄이기

앞선 장들에서 암묵적 입출력이 있는 함수는 액션이라고 칭했다. 그렇다고 해서 액션에 암묵적 입출력을 방치하면 안된다. 암묵적 입출력이 있다는 것은 결합도가 높다는 것을 의미하고 책에서는 **납땜**으로 표현을 한다. 반면에 암묵적 입출력이 적은 것은 붙이고 떼는 것이 쉬운 **커넥터**라는 표현을 사용한다. 납땜이 된 부분을 줄이고 커넥터로 변경하여 사용하기 쉽게 만들듯이 액션의 암묵적 입출력도 줄이는 것이 좋다.

**암묵적 입력**이 있는 함수는 **멀티 스레드 환경에서 동시성 문제가 발생할 수 있다**고 저자는 제시한다. 책의 예시로 shopping_cart_total 전역변수가 여러 곳에서 동시에 사용된다면 개발자는 이것을 고려하여 코드를 작성할 필요가 있다.

**암묵적 출력**이 있는 함수는 **암묵적 출력으로 인한 변경을 사용하고 싶지 않은 경우에 함수의 사용이 제한된다.** 책의 예시는 암묵적 출력으로 DOM을 바꾸는 함수가 있는데 DOM의 변경은 필요없고 계산의 결과만 필요한 경우 애매할 수 있다고 주장한다.

당연히 암묵적 입출력을 줄이면 **테스트하기 쉽고 재사용하기 쉽다.**

다음으로 아래의 기존 코드의 암묵적 입력을 제거해본다.

```jsx
// 기존 코드
function add_item_to_cart (name, price) {
    **shopping_cart** = add_item(**shopping_cart,** name, price);
    calc_cart_total();
}

function calc_cart_total() {
    **shopping_cart_total** = calc_total(**shopping_cart**);
    set_cart_total_dom();
    update_shipping_icons(**shopping_cart**);
    update_tax_dom();
}

function set_cart_total_dom() {
    **shopping_cart_total**;
}

function update_shipping_icons(cart) {
    let buy_buttons = get_buy_buttons_dom();
    for(let i = 0; i < buy_buttons.length; i++) {
        let button = buy_buttons[i];
        let { item: { name, price } } = button;
        let new_cart = add_item(cart, name, price);
        if(gets_free_shopping(new_cart))
            button.show_free_shipping_icon();
        else
            button.hide_free_shipping_icon();
    }
}

function update_tax_dom() {
    set_tax_dom(calc_tax(**shopping_cart_total**));
}
```

`shopping_cart` 와 `shopping_cart_total` 을 인자로 넘기거나 지역 변수를 활용하는 방향으로 변경했다. 그 결과 `shopping_cart_total`은 더 이상 참조되지 않게 되었다.

```jsx
// 암묵적 입력을 줄인 후의 코드
function add_item_to_cart (name, price) {
    **shopping_cart** = add_item(cart, name, price);
    calc_cart_total(**shopping_cart**);
}

function calc_cart_total(cart) {
    let total = calc_total(cart);
    set_cart_total_dom(total);
    update_shipping_icons(cart);
    update_tax_dom(total);
    **shopping_cart_total** = total; // shopping_cart_total은 이제 참조되지 않음
}

function set_cart_total_dom(total) {
    total;
}

function update_shipping_icons(cart) {
    let buy_buttons = get_buy_buttons_dom();
    for(let i = 0; i < buy_buttons.length; i++) {
        let button = buy_buttons[i];
        let { item: {name, price} } = button;
        let new_cart = add_item(cart, name, price);
        if(gets_free_shopping(new_cart))
            button.show_free_shipping_icon();
        else
            button.hide_free_shipping_icon();
    }
}

function update_tax_dom(total) {
    set_tax_dom(calc_tax(total));
}
```

`calc_cart_total` 함수의 역할이 과하다고 판단되어 `add_item_to_cart`로 옮깁니다. 그리고 더 이상 참조되지 않았던 `shopping_cart_total` 전역변수를 제거합니다.

```jsx
function add_item_to_cart (name, price) {
    shopping_cart = add_item(cart, name, price);
    let total = calc_total(shopping_cart);
    set_cart_total_dom(total);
    update_shipping_icons(shopping_cart);
    update_tax_dom(total);
}

function set_cart_total_dom(total) {
    total;
}

function update_shipping_icons(cart) {
    let buy_buttons = get_buy_buttons_dom();
    for(let i = 0; i < buy_buttons.length; i++) {
        let button = buy_buttons[i];
        let { item: {name, price} } = button;
        let new_cart = add_item(cart, name, price);
        if(gets_free_shopping(new_cart))
            button.show_free_shipping_icon();
        else
            button.hide_free_shipping_icon();
    }
}

function update_tax_dom(total) {
    set_tax_dom(calc_tax(total));
}
```

### 5-3. 계산을 분류하여 설계를 풀어내자

다음으로 **의미있는 계층**을 알아보기 위해 **계산을 분류**한다. 의미있는 계층이란 책의 뒷장에서 더 언급하게 된다. 여기서는 카트 관련을 C, 제품 관련을 I, 비즈니스 규칙 관련을 B로 분류한다.

```jsx
function add_item(cart, name, price) { // C, I
  var new_cart = cart.slice();
  new_cart.push({
    name: name,
    price: price
  });
  return new_cart;
}
```

**계층으로 분류하고 함수를 쪼개어 하나의 일만 하도록 작성하면 재사용, 유지보수, 테스트에 유리하다.** 또, 이를 조합하여 문제를 해결할 수 있다. 또 더 좋은 설계를 만들어낼 수 있다고 주장한다.

add_item() 함수는 장바구니에 제품을 담는 역할을 충실히 해낸다. 허나 이 함수는 더 쪼개어 작성할 수 있기도 하다.

```jsx
function make_cart_item(name, price) { // I
  return {
    name: name,
    price: price
  };
}

function add_item(cart, item) { // C
  var new_cart = cart.slice();
  new_cart.push(item);
  return new_cart;
}

add_item(shopping_cart, make_cart_item("shoes", 3.45));
```

이렇게 분리해내면 item구조만 알고 있는 make_cart_item()과 cart구조만 알고 있는 add_item()으로 나눠진다. 이렇게 나눔으로써 cart와 item에 변경이 생겨도 **독립적으로 확장할 수 있다.**

* 카피-온-라이트 (Copy-on-Write)
  여기서 말하는 카피-온-라이트는 객체에 변경은 복사본에서 처리하는 것으로 해석된다. 불변성을 지키는 코드 패턴으로 생각하면 될 것 같다.

다음으로 add_item() 함수를 다시 보면 cart와 item에 종속되지 않은 **일반화된 함수로 변경가능함이 보인다.**

```jsx
function add_element_last(array, elem) {
  var new_array = array.slice();
  new_array.push(elem);
  return new_array;
}

function add_item(cart, item) {
  return add_element_last(cart, item);
}
```

위와 같이 배열에 항목을 카피-온-라이트 방식으로 추가하는 유틸리티 함수를 만들어 냈다.

최종적으로 다음과 같은 코드를 확인할 수 있다.

```jsx
function add_element_last(array, elem) { // U
  var new_array = array.slice();
  new_array.push(elem);
  return new_array;
}

function add_item(cart, item) { // C
  return add_element_last(cart, item);
}

function make_cart_item(name, price) { // I
  return {
    name: name,
    price: price
  };
}
```

### 결론

- 일반적으로 암묵적 입출력은 인자와 리턴값으로 바꿔 없애는 것이 좋다.
- 설계는 엉켜있는 것을 푸는 것이다. 풀려있는 것은 언제든 다시 합칠 수 있다.
- 엉켜있는 것을 풀어 각 함수가 하나의 일만 하도록 하면, 개념을 중심으로 쉽게 구성할 수 있다.