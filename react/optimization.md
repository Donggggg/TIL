# React Optimization

## 다양한 방법과 목적

- 최적화를 하는 목적은 불필요한 렌더링을 줄여 사용성을 높이기 위함이다.
- 리액트에서 DOM이 리렌더링되는 조건인 state변경과 props 변경을 최소화하는 것이 중요하다.
- 다음으로는 state/props가 변경되어도 이로 인한 자식 컴포넌트의 렌더링을 최소화하는 것이 있다.
- 다음은 이 두 가지 목표를 이룰 수 있는 방법들이다.

### 1. state 선언 위치

- state를 사용하는 컴포넌트의 **최소 높이 공통 조상 컴포넌트**에서 사용하는 것이 당연히 좋다.
- **state 변경**이 발생하면 해당 state를 가진 컴포넌트(하위 컴포넌트 포함)가 모두 리렌더링되기에 최소한의 컴포넌트를 리렌더링하기 위해 최소 높이에 둬야한다.

### 2. 객체 형태 state 관리 방식

- 객체를 state로 관리할 때는 가능한 의미적으로 작게 묶을 수 있도록 작성한다.

```//json
// 반을 관리하는 상태 (schollClass)
{
  classInfo : {
    name: "orange",
    studentNum: 31
  },
  students: [
    {name: "Karl", age: 17},
    {name: "Jane", age: 17},
    ...
  ]
}
```

- 위와 같이 반 하나를 관리하는 데이터를 하나의 상태로 관리하기 보다는

```//json
// 반 정보 (schoolClassInfo)
{
  name: "orange",
  studentNum: 31
}

// 학생 정보 (students)
[
  {name: "Karl", age: 17},
  {name: "Jane", age: 17},
  ...
]
```

- 이렇게 두 개를 나눠 관리하는 것이 좋다.
- 이유는 간단하다, 전자의 경우 학생의 정보가 바뀌고 반 정보가 바뀌지 않아도 위의 상태를 가진 컴포넌트들은 모두 리렌더링된다.
- 후자는 각 상태를 따로 관리하기에 각 상태의 변화에 맞게 렌더링시킬 수 있다.

### 3. React.memo

- React.memo로 래핑한 컴포넌트는 상위에서 리렌더링이 되어도 본인의 props가 변경되지 않았다면 리렌더링되지 않는다.

### 4. 배열 컴포넌트의 key값 설정

- 리액트는 최소한의 DOM 교체를 이뤄내기 위해 virtual DOM을 도입했다.
- 이때 사용되는 diff 알고리즘의 간단한 로직을 유지하고 효율적인 렌더링을 위해 배열 컴포넌트에는 key 속성이 강요된다.
- 이때, key 속성에 map 내장의 index 인자를 넣게되면 사실상 의미가 없어진다.
- 컴포넌트마다 실제로 unique한 값을 넣어야 한다.
- 왜냐하면, map의 index는 데이터에 실제 unique하지 않기 때문이다.

### 5. useMemo

- 연산이 굉장히 오래 걸리는 함수가 있고, 이 함수의 연산이 렌더링시 매번 발생한다면 비효율적이다.
- 이 연산 결과를 메모이제이션할 수 있는 Hook이 `useMemo` 이다.
- 보통 React.memo와 함께 사용되며 최적화를 유의미하게 만들어 낼 수 있는 방법이다.

### 6. useCallback

- `useMemo`는 함수의 리턴값을 메모이제이션할 수 있었다면, `useCallback`은 함수 자체를 메모이제이션할 수 있다.

### 7. props 전달 방식

- props를 넘겨줄 때, 인라인에서 새로운 객체를 생성하면 안된다.
- 이런 방식을 사용할 경우 렌더링마다 새로운 객체를 props로 넘겨 아무리 최적화를 했다하더라도 먹히지가 않는다.

### 레퍼런스

https://cocoder16.tistory.com/36