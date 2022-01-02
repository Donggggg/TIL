# async & await

### async

- **async 함수**는 항상 **프로미스 객체**를 반환한다.
- 아닌 값을 반환하더라도 이행된 프로미스 객체로 감싸 반환된다.

### await

- async 함수의 종료를 기다린다.
- thenable 객체에 사용가능하다.
- try, catch로 에러 핸들링이 가능하다.
