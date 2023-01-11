# Handling Scripts

`next/script, 스크립트 컴포넌트` 는 Next 애플리케이션의 어디서든 써드파티 스크립트를 최적으로 로드할 수 있게 합니다. 이 컴포넌트는 HTML의 script 태그의 확장이고 여러 로딩 전략 중 상황에 맞는 것을 택할 수 있습니다.

## Overview

웹사이트는 분석, 광고, 고객 지원 위젯, 약관 동의 등의 기능을 더하기 위한 써드 파티 스크립트를 흔히 사용합니다. 그러나 이것은 사용자와 개발자의 경험성에 영향을 끼치는 문제를 생산합니다.

- 일부 써드파티 스크립트는 로딩 성능을 감소시키고 유저 경험을 악화합니다. 보여지고 있는 페이지 컨텐트를 막는다면 이 현상은 더 심각합니다.
- 개발자는 애플리케이션에서 써드파티 스크립트를  언제 어떻게  로드해야 페이지 성능 영향이 없는지 종종 확신할 수 없습니다.

브라우저는 HTML 상의 배치 순서와 `async` 및 `defer` 속성 사용 여부에 따라 script 태그를 로드하고 실행합니다. 그러나 네이티브 script 태그를 사용하는 것은 몇 가지 문제점을 생성합니다:

- 애플리케이션의 사이즈와 복잡성이 커져감에 따라 써드 파티 스크립트의 로딩 순서를 관리하는 것이 점점 어려워집니다.
- [스트리밍과 서스펜스](https://beta.nextjs.org/docs/data-fetching/streaming-and-suspense)는 새로운 컨텐트를 최대한 빨리 렌더링과 하이드레이팅하므로써 페이지 성능을 향상시킵니다. 그러나 script 태그(defer)는 추가 작업 없이는 호환되지 않습니다.

스크립트 컴포넌트는 써드 파티 스크립트를 로딩하기 위한 선언적 API를 제공함으로써 이 문제점들을 해결합니다. 스크립트 컴포넌트는 스트리밍이 지원되는 스크립트의 로딩 순서를 최적화 할 수 있는 빌트인 로딩 전략 집합을 제공합니다. 스크립트 컴포넌트에서 제공되는 각 전략들은 스크립트가 페이지 성능에 최소한의 영향을 미치며 로드될 수 있는 것을 보장하는 최적의 React와 Web API의 조합을 사용합니다.

## Usage

시작하기 위해서는 next/script 컴포넌트를 임포트하면 됩니다.

```tsx
import Script from 'next/script'
```

### Page Scripts

단일 경로의 써드 파티 스크립트를 로드하기 위해서는 `next/script`를 임포트하고 페이지 컴포넌트에 스크립트를 포함시키면 됩니다.

```tsx
import Script from 'next/script'

export default function Dashboard() {
  return (
    <>
      <Script src="https://example.com/script.js" />
    </>
  )
}
```

스크립트는 해당 페이지가 브라우저 상에서 로드될 때만 패치되고 실행될 것 입니다.

### Application Scripts

모든 경로에서 써드 파티 스크립트를 로드하고 싶다면, `next/script`를 임포트하고 스크립트를 `pages/_app.js`에 포함시키면 됩니다.

```tsx
import Script from 'next/script'

export default function MyApp({ Component, pageProps }) {
  return (
    <>
      <Script src="https://example.com/script.js" />
      <Component {...pageProps} />
    </>
  )
}
```

이 스크립트는 애플리케이션 상의 모든 경로에 접근 시 로드되고 실행될 것 입니다. Next는 사용자가 여러 페이지 사이를 이동하여도 오직 한 번만 로드되도록 합니다.

> Note: 드물게 매 페이지 마다 써드 파티 스크립트를 로드할 필요가 있습니다. 성능에 불필요한 영향을 최소화하기 위해 써드 파티 스크립트를 특정 경로에서만 포함시키는 것을 추천합니다.
>

### Strategy

비록 `next/script`의 기본 동작이 어떤 페이지에서든 써드 파티 스크립트를 로드하는 것을 허용하지만 `strategy` 속성을 사용하여 로딩 동작을 미세 조정할 수 있습니다.

- `beforeInteractive`: Next.js 코드 및 페이지 하이드레이션이 발생하기 전에 스크립트를 로드합니다.
- `afterInteractive`: (**default**) 페이지에 하이드레이션 이후에 스크립트를 초기에 로드합니다.
- `lazyOnload`: 브라우저 유휴 시간 동안 스크립트를 느지막히 로드합니다.
- `worker`: (experimental) 웹 워커에서 스크립트를 로드합니다.

각 전략과 유즈케이스에 대해 더 많이 배우고 싶다면  next/script API 레퍼런스 문서를 참조하면 됩니다.

> Note: `next/script` 컴포넌트가 브라우저에 의해 로드되었다면 그것은 DOM에 유지되고 클라이언트 사이드 이동은 스크립트를 재실행하지 않을 것 입니다.
>

### Offloading Scripts To A Web Worker (experimental)

> Note: woker 전략은 아직 안정화되지 않았고 app directory에서는 동작하지 않습니다. 유의하여 사용해주세요.
>

worker 전략을 사용하는 스크립트는 PartyTown을 사용하는 웹 워커에서 오프로드되어 실행됩니다. 이 방법은 남은 애플리케이션 코드를 메인 스레드에 맡겨 사이트 성능을 향상시킵니다.

이 전략은 아직 실험 단계이고 `next.config.js`에서 `nextScriptWorkers` 플래그를 켜야 사용 가능합니다.

```tsx
module.exports = {
  experimental: {
    nextScriptWorkers: true,
  },
}
```

이 후 `next`를 실행하면 Next.js는  설정에 필요한 패키지의 설치를 안내합니다.

설정이 완료되면 `strategy=”worker”`는 애플리케이션에 Partydown을 인스턴스화하고 웹 워커로 스크립트를 오프로드합니다.

```tsx
import Script from 'next/script'

export default function Home() {
  return (
    <>
      <Script src="https://example.com/script.js" strategy="worker" />
    </>
  )
}
```

웹 워커를 통해 써드 파티 스크립트를 로딩할 때 고려해야 하는 트레이드 오프가 많습니다. 더 많은 정보를 위해서는 Partytown의 [트레이드 오프 문서](https://partytown.builder.io/trade-offs)를 참고하면 됩니다.

### Inline Scripts

외부 파일에서 로드되지 않는 스크립트나 인라인 스크립트도 스크립트 컴포넌트에서 지원되지 않습니다. 이 경우에 중괄호 내에 자바스크립트를 작성할 수 있습니다.

```tsx
<Script id="show-banner">
  {`document.getElementById('banner').classList.remove('hidden')`}
</Script>
```

혹은 `dangerouslySetInnerHTML` 속성을 사용합니다.

```tsx
<Script
  id="show-banner"
  dangerouslySetInnerHTML={{
    __html: `document.getElementById('banner').classList.remove('hidden')`,
  }}
/>
```

> Note: Next.js가 스크립트를 추적하고 최적화하기 위해 인라인 스크립트에 반드시 id 속성을 할당헤야 합니다.
>

### Executing Additional Code

이벤트 핸들러는 스크립트 컴포넌트와 함께 특정 이벤트 발생 이후에 추가적인 코드를 실행하기 위해 사용할 수 있습니다:

- `onLoad`: 스크립트의 로딩이 끝난 후에 코드를 실행합니다.
- `onReady`: 스크립트의 로딩이 끝나고 컴포넌트가 마운트될 때마다 코드를 실행합니다.
- `onError`: 스크립트 로딩이 실패하면 코드를 실행합니다.

```tsx
import Script from 'next/script'

export default function Page() {
  return (
    <>
      <Script
        src="https://example.com/script.js"
        onLoad={() => {
          console.log('Script has loaded')
        }}
      />
    </>
  )
}
```

각 이벤트 핸들러와 뷰 예제에 대해 더 배우고 싶다면 `next/script` API 레퍼런스를 참조하면 됩니다.

### Additional Attributes

스크립트 컴포넌트에서 사용할 수 없지만 스크립트 태그에서는 사용할 수 있는 DOM 속성이 많이 있습니다(`nonce`나 `custom data attributes`). 추가 속성을 포함하면 HTML에 포함된 최적화된 스크립트 태그로 자동 전달됩니다.

```tsx
import Script from 'next/script'

export default function Page() {
  return (
    <>
      <Script
        src="https://example.com/script.js"
        id="example-script"
        nonce="XUENAJFW"
        data-test="script"
      />
    </>
  )
}
```