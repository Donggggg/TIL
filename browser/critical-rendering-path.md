# Critical Rendering Path

- CRP(Critical Rendering Path)는 브라우저가 HTML, CSS, JS를 화면에 픽셀로 변경하는 일련의 단계이다.
- 보통 우리가 부르는 렌더링 과정이다.
- 순서는 크게 `HTML GET 요청 -> HTML 파싱 및 DOM트리 구성 -> CSSOM 트리 구성 -> DOM과 CSSOM으로 렌더트리 구축 -> 페인트 및 레이어 구축 -> 레이어 컴포지트`이다.

### HTML 파싱 중에 추가 통신

- HTML을 파싱하다가 script나 src태그를 만나면 추가적인 리소스를 받기 위해 통신을 진행한다.
- 이때, HTML 파싱은 멈추게 되는데 그 이유는 다음과 같습니다.
- CSS의 경우 속성을 덮어쓰는 상황이 발생하기에 멈춘다.
- Javascript의 경우 JS 코드 내부적으로 DOM을 수정할 확률이 있기에 춘다.

### Layout

- 렌더 트리의 노드들이 가지고 있는 속성과 스타일에 따라 **브라우저의 화면에 어느 위치와 크기로 출력되는지 계산**하는 단계이다.
- `%, vh, vw`와 같은 상대적인 위치와 크기를 `뷰포트(Viewport)` 기준으로 `px` 값으로 변환한다.

### Paint

- Layout 계산이 완료되면 **실제 화면에 그리기** 시작한다.
- 계산된 값들을 실제 픽셀에 채워넣는다.
- 텍스트, 색, 이미지, 그림자가 처리된다.
- 처리해야할 스타일이 복잡할수록 오래 걸린다.

### 렌더링 파이프라인

![](https://i.imgur.com/YKcXwww.png)

- 브라우저가 렌더링하는 과정을 위 다섯 단계로 나눌 수 있다.
- Composite를 제외한 나머지 단계는 부분적으로 실행된다.

### Reflow (Layout)

- `style -> layout -> paint -> composite`
- 스크립트의 이벤트나 로직에 의해 HTML 요소의 크기나 위치가 수정되면 영향을 받는 자식 노드나 부모 노드들을 포함하여 Layout 과정을 다시 진행한다.
- 렌더 트리의 각 요소들의 크기와 위치를 다시 계산하게 되는데 이것이 Reflow이다.
- 초기 렌더링, 창 리사이즈, 노드 추가 및 제거, 요소의 위치 및 크기 변경, 폰트, 텍스트, 이미지 변경 등과 같은 경우에 발생한다.
- position, padding, overflow, width 등이 있다.

### Repaint (Paint)

- `style -> paint -> composite`
- Reflow가 일어나면 다시 그려주는 과정도 필요하고 그것이 Repaint이다.
- Reflow 상황 외에도 스타일 속성 중 Reflow가 필요하지 않는 값 (visibility, background-color)가 변경될 때에 발생한다.
- shadow, color, background-color, opacity, z-index 등이 있다.

### Composite

- `style -> composite`
- transform, cursor 등이 있다.

### Reflow와 Repaint를 고려해 최적화하기

- 사용하지 않는 노드에는 visibility: false 보다 display:none 사용하기 (렌더트리에서 제외시키기)
- Reflow 발생하는 속성 사용 피하기 (paint나 composite 속성으로)
- 영향을 주는 노드 줄이기 (렌드트리 줄이기)
- 프레임 줄이기 (렌더링 주기 줄이기)

### 레퍼런스

https://boxfoxs.tistory.com/408  
https://developer.mozilla.org/ko/docs/Web/Performance/Critical_rendering_path  
https://365kim.tistory.com/152
