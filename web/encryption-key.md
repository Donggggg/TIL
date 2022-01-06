# 대칭키와 비대칭키

- 두 방식 다 암호화 기법이다.

## 대칭키

- 같은 키를 사용해 암호화/복호화를 한다.
- 알고리즘 종류는 DES, 3DES, SEED, ARIA 등이 있다.
- 속도가 빠르다.
- 키를 교환할 때 탈취 가능성이 있다.
- 키를 요구하는 사용자에게 키를 분배해야 한다.

## 비대칭키

- 공개키와 비밀키를 사용해 암호화/복호화를 한다.
- 알고리즘 종류는 RSA, DSA 등이 있다.
- 공개키는 여러 개로 배포할 것이고 비밀키는 하나로 관리해야 한다.
- 각 키로 암호화하면 다른 키로 복호화를 할 수 있다.
- 공개키는 공개가 되어있기 때문에 따로 키를 분배할 필요가 없다.
- 속도가 느리다.