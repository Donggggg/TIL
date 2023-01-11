# Image Optimization

Next.js의 이미지 컴포넌트인 `next/image`는 모던 웹을 위해 진화한 HTML `<img>` 요소의 확장 버전이다. 이것은 우수한 Core Web Vital을 달성을 돕는 다양한 빌트인 성능 최적화를 포함하고 있다. 이 점수는 웹사이트의 유저 경험 지표에 중요하고, Google의 검색 노출 순위에 영향을 준다.

이미지 컴포넌트에 포함된 몇 가지 최적화는 다음과 같다:

- **성능 향상(Improved Performance):** 모던 이미지 포맷을 사용해 항상 각 기기에 맞는 사이즈의 이미지를 전달한다.
- **시각 안정성(Visual Stability):** 자동으로 누적 레이아웃 이동(CLS)를 방지한다.
- **빠른 페이지 로드(Faster Page Loads):** 이미지가 뷰포트에 진입할 때만 로드되고, 옵션으로 blur-up placeholder가 제공된다.
- **자산 유연성(Asset Flexibility):**  리모트 서버에 있는 이미지를 포함해 요구 시에 이미지 리사이징이 가능하다.

## Using the Image Component

애플리케이션에 이미지를 추가 하려면 ****`next/image` 컴포넌트를 import하면 된다.

```tsx
import Image from 'next/image'
```

이제, 이미지를 위한 `src`를 정의할 수 있다. (로컬이나 리모트 모두 가능하다.)

### Local Images

로컬 이미지를 사용하려면 `.jpg`, `.png`, `.webp` 등의 파일을 `import`하면 된다.

```tsx
import profilePic from '../public/me.png'
```

동적인 `await import()`나 `require()`은 지원되지 않는다. `import`는 반드시 정적이어서 빌드 타임에 분석될 수 있어야 한다.

Next.js는 import된 파일에 기반하여 이미지의 너비 및 높이를 자동적으로 결정한다. 이 값들은 이미지가 로딩되는 동안 CLS 현상을 방지하는 데에 사용된다.

```tsx
import Image from 'next/image'
import profilePic from '../public/me.png'

function Home() {
	return (
		<>
			<h1>
			<Image
        src={profilePic}
        alt="Picture of the author"
        // width={500} automatically provided
        // height={500} automatically provided
        // blurDataURL="data:..." automatically provided
        // placeholder="blur" // Optional blur-up while loading
      />
      <p>Welcome to my homepage!</p>
    </>
  )
}
```

### Remote Images

리모트 이미지를 사용하려면 `src` 속성은 상대 혹은 절대 경로의 URL 문자열이어야 한다. Next.js가 빌드 프로세스 중에 리모트 파일에 접근할 수 없기 때문에, `width`, `height`, `blurDataURL(옵션)` prop들을 수동으로 제공해야 한다.

```tsx
import Image from 'next/image'

export default function Home() {
  return (
    <>
      <h1>My Homepage</h1>
      <Imagesrc="/me.png"alt="Picture of the author"width={500}height={500}/>
      <p>Welcome to my homepage!</p>
    </>)
}
```

### Domains

가끔  리모트 이미지를 최적화하는 경우도 있는데, 이 때 빌트인 이미지 최적화 API를 사용하면 된다. 이것을 하려면 기본 설정에 `loader`를 두고 Image `src` prop에 절대 URL 경로를 입력하면 된다.

애플리케이션을 악의적인 사용자로부터 보호하고 싶다면 반드시 **next/image** 컴포넌트에 사용하고자 하는 원격 호스트 네임 리스트를 정의해야 한다.

### Loaders

앞의 예시에서 부분적인 URL인 “/me.png”가 리모토 이미지를 위해 제공되었다. 이것은 로더 아키텍처 때문에 가능한 것이다. loader는 이미지의 URL을 생성하는 기능이다. 그것은 제공된 src를 수정하고 다른 사이즈의 이미지들을 요청하기 위한 여러 URL들을 생성한다. 여러 URL들은 `srcset` 자동 생성에 사용된다. 사이트 방문자들은 각자의 뷰포트에 맞는 사이즈의 이미지를 전달받게 될 것이다.

Next.js의 애플리케이션을 위한 기본 로더는 웹 상의 어디든 이미지를 최적화하고 Next 웹 서버로부터 직접 전달하는 빌트인 API를 사용한다. 만약 CDN이나 이미지 서버에서 직접 이미지를 전달하고 싶다면 몇 줄의 Javascript로 로더 기능을 작성할 수 있다.

각 이미지의 로더를 `loader` prop이나 `loaderFile` 설정을 통해 애플리케이션 레벨에서 설정할 수 있다.

### Priority

각 페이지의 **Largest Contentful Paint(LCP)** 요소일 수 있는 이미지에는 `priority` 속성을 추가해야 한다. 이로써 Next.js는 LCP 지수의 유의미한 가속을 이끄는 이미지 로딩을 선행할 수 있다. (preload 태그나 priority 힌트와 같은)

> Priority Hint란?
브라우저에 필요한 리소스의 우선순위를 설정하여 Core Web Vital을 향상시키고 로딩 최적화를 이룰 수 있게 한다.
>

LCP 요소는 전형적으로 페이지의 뷰포트 내에서 보이는 가장 큰 이미지나 텍스트 블럭이다. `next dev`를 실행할 때 LCP 요소가 `priority` 속성이 없는 이미지 컴포넌트이면 콘솔 경고를 볼 수 있다.

LCP 이미지를 인지하게 된다면 다음과 같이 속성을 추가할 수 있다.

```tsx
import Image from 'next/image'

export default function Home() {
  return (
    <>
      <h1>My Homepage</h1>
      <Image
        src="/me.png"
        alt="Picture of the author"
        width={500}
        height={500}
        priority
      />
      <p>Welcome to my homepage!</p>
    </>
  )
}
```

## Image Sizing

이미지가 성능을 훼손하는 가장 일반적인 방법 중 하나는 이미지가 로드됨에 따라 페이지의 다른 요소들을 미는 레이아웃 시프트이다. 이 성능 문제는 사용자에게 짜증을 유발해서 Web Vital Core에 CLS라는 지표가 따로 있다. 이미지 기반 레이아웃 시프트를 피하는 방법은 항상 이미지에 사이즈를 지정하는 것이다. 이것은 브라우저가 이미지가 로드 되기 전에 정확히 필요한 공간을 예약할 수 있도록 한다.

next/image가 좋은 성능 결과를 보장하도록 디자인되었기 때문에 레이아웃 시프트에 기여하는 방법으로는 사용할 수 없고 다음 세 가지 방법 중 하나로 사이즈가 정해집니다.

1. 자동: `static import`를 사용한다.
2. 명시: `width` 및 `height` 속성을 포함한다.
3. 암시: 이미지가 부모 요소에 맞게 확장하여 채워지는 `fill`을 사용한다.

> **이미지의 사이즈를 모르면 어떨까?**

이미지의 사이즈에 대한 지식 없이 소스의 이미지에 접근해야 한다면 몇 가지 할 수 있는 방법이 있습니다:

**Use `fill`**

`fill` prop은 이미지의 부모 요소의 사이즈로 맞춰줍니다. 모든 미디어 쿼리 브레이크포인트와 일치하는 sizes prop에 따른 페이지 내 이미지의 부모 요소 공간을 확보하기 위해 CSS를 사용하는 것을 고려해봐라. 또한 object-fit의 fill, contain, cover이나 object-position과 같은 것을 공간 차지를 위해 사용할 수도 있다.

**Normalize your images**

만약 컨트롤할 수 있는 소스에서 이미지를 전달 중이라면 특정 사이즈로 이미지를 정규화하는 이미지 파이프라인을 수정하는 것을 고려해 봐라

Modify Your API calls

애플리케이션이 API 호출을 통해 이미지 URL을 검색하고 있다면, URL과 함께 이미지 치수를 반환하도록 API 호출을 수정할 수도 있다.
>

만약 제안된 방법들 중 당신의 이미지 사이징에 적합한 것이 없다면 next/image 컴포넌트는 기본 img 요소와 함께 잘 동작하게 설계되어 있다.

## Styling

이미지 컴포넌트 스타일링은 일반 img 요소를 스타일링 하는 것과 유사한데 신경 써야 할 약간의 가이드라인이 있다.

**Use `className` or `style`, not `styled-jsx`**

대부분의 케이스에서 className prop 사용을 추천한다. 이 방식은 CSS Module, global stylesheet 등에서 임포트할 수 있다.

인라인 스타일로 할당해 style prop 또한 사용할 수 있다.

styled-jsx는 현재 컴포넌트에 범위가 한정되기 때문에 사용할 수 없다. (스타일을 global로 지정하지 않는 한)

**When using `fill`, the parent element must have `position: relative`**

레이아웃 모드에서 이미지 요소의 적절한 렌더링을 위해 필수적이다.

**When using `fill`, the parent element must have `display: block`**

div 요소는 기본값이지만 외의 경우에는 명시해야 한다.