# getServerSideProps

페이지에서 `getServerSideProps`라고 불리는 함수를 export하면 Next.js는 `getServerSideProps`에서 반환된 데이터를 사용하여 각 요청마다 해당 페이지를 프리 렌더링할 것입니다.

```tsx
export async function getServerSideProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```

> 렌더링 형태과 상관 없이 `props`가 페이지 컴포넌트에 전달될 것이고 클라이언트 사이드에서 초기 HTML로 보여지게 될 것입니다. 이것은 페이지가 정확하게 하이드레이트 되도록 합니다. 클라이언트에서 이용하지 못하도록 `props`에 예민한 정보는 전달하면 안됩니다.
>

## When does getServerSideProps run

`getServerSideProps`는 서버 사이드에서만 실행되고 브라우저에서는 절대 실행되지 않습니다. 페이지가 `getServerSideProps`를 사용한다면 다음과 같습니다:

- 페이지에 직접 요청하는 경우, `getServerSideProps`는 요청 시간에 실행되고 해당 페이지는 반환된 props로 프리 렌더링될 것입니다.
- 페이지를 `next/link`나 `next/router`를 통한 클라이언트 사이드 트랜지션로 요청되는 경우, Next.js는 서버로 `getServerSideProps`를 실행시키는 API 요청을 보냅니다.

`getServerSideProps`는 페이지를 렌더링하는 데에 사용되는 JSON을 반환합니다. 이러한 모든 작업은 Next.js에 의해 자동으로 다뤄질 것이고 개발자는 `getServerSideProps`를  정의하기만 하면 추가로 무언가를 할 필요가 없습니다.

[`next-code-elimination tool`](https://next-code-elimination.vercel.app/)을 사용하면 Next.js가 클라이어트 사이드 번들에서 제거하는 내용을 확인할 수 있습니다.

`getServerSideProps`는 페이지에서만 export될 수 있습니다. 페이지 파일이 아닌 곳에서는 export할 수 없습니다.

`getServerSideProps`는 독립 함수로 export 해야 하는 것을 기억해야 합니다. 페이지 컴포넌트의 속성으로 `getServerSideProps`를 추가하면 동작하지 않을 겁니다.

[`getServerSideProps API 레퍼런스`](https://nextjs.org/docs/api-reference/data-fetching/get-server-side-props)는 사용되는 모든 파라미터와 props를 다룹니다.

## When should I use getServerSideProps

`getServerSideProps`는 요청 시간에 패치해야만 하는 데이터의 페이지를 렌더링할 때만 사용합니다. 이것은 `authorization` 헤더나 `geo location`과 같은 요청의 데이터나 프로퍼티의 특징 때문일 수도 있습니다. `getServerSideProps`를 사용하는 페이지는 요청 시간에 서버 사이드 렌더링할 것이고 `cache-` 헤더가 설정될 때만 캐시될 것입니다.

요청 동안에 데이터를 렌더링할 필요가 없다면 클라이언트 사이드나 `getStaticProps`에서 데이터를 패칭하는 것도 고려해야 합니다.

### getServerSideProps or API Routes

서버에서 데이터를 패치하고 싶을 때 `getServerSideProps`에서 API route를 호출하 것 보다 API Route에 도달하고 싶을 수 있습니다. 이것은 비율적이 접근이고 서버에서 실행 중인 `getServerSideProps` 및 API Routes로 인해 추가 요청이 발생할 것입니다.

다음 예제를 보시면 됩니다. API 라우트가 CMS로 부터 몇 가지 데이터를 패치하기 위해 사용됩니다. API route는 `getServerSideProps`에서 직접 호출 될 것입니다. 이것은 성능을 감소시키는 추가 호출을 생산합니다. 이를 대신하여 `getServerSideProps` 내의 API 라우트 내부에서 사용되는로직을 직접 import하면 됩니다. 이것은 CMS, database, 다른 API 등을 `getServerSideProps`에서 직접 호출할 수 있다는 뜻입니다.

### getServerSideProps with Edge API Routes

`getServerSideProps`는 서버리스 및 엣지 런타임에서도 사용되고 props 설정도 가능합니다. 허나 현재 엣지 런타임에서는 응답 객체에 접근할 수 없습니다. 예를 들어 `getServerSideProps`에서 쿠키를 추가하지 못한다는 것입니다. 응답 객체에 접근하기 위해서는 기본 런타임인 Node.js 런타임을 사용해야 합니다.

`config`를 수정하여 [각 페이지의 기본 런타임](https://nextjs.org/docs/advanced-features/react-18/switchable-runtime#page-runtime-option)을 명시적으로 설정할 수 있습니다.

```tsx
export const config = {
  runtime: 'nodejs',
}
```

## Fetching data on the client side

페이지가 자주 업데이트 되는 데이터를 포함하고 있다면 프리 렌더링할 필요가 없습니다. 클라이언트 사이드에서 데이터를 패치하면 됩니다. 유저 특정 데이터가 예시입니다:

- 먼저, 데이터 없이 즉시 페이지를 보여줍니다. Static Generation을 사용하여 페이지 중 일부는 프리 렌더링 될 것입니다. 없는 데이터를 위한 로딩 상태를 보여주면 됩니다.
- 그리고 클라이언트 사이드에서 데이터를 패치하고 준비되면 보여주면 됩니다.

예를 들자면 사용자 대시보드 페이지에 이러한 접근이 효과적입니다. 왜냐하면 대시보드는 프라이빗하고 사용자 특수 페이지이고 SEO가 적절하지 않기에 프리 렌더링될 필요가 없습니다. 요청 시간에 데이터 패칭이 필요한 데이터는 자주 업데이트 됩니다.

## Using getServerSideProps to fetch data at request time

다음 예제는 어떻게 요청 시간에 데이터를 패치하고 결과를 프리 렌더링하는지 보여줍니다.

```tsx
function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // Pass data to the page via props
  return { props: { data } }
}

export default Page
```

## Caching with Server-Side Rendering (SSR)

동적 응답을 캐시하기 위해 `getServerSideProps` 안에서 `Cache-Control` 캐싱 헤더를 사용할 수 있습니다. 예를 들어, `stale-while-revalidate`이 있습니다.

```tsx
// This value is considered fresh for ten seconds (s-maxage=10).
// If a request is repeated within the next 10 seconds, the previously
// cached value will still be fresh. If the request is repeated before 59 seconds,
// the cached value will be stale but still render (stale-while-revalidate=59).
//
// In the background, a revalidation request will be made to populate the cache
// with a fresh value. If you refresh the page, you will see the new value.
export async function getServerSideProps({ req, res }) {
  res.setHeader(
    'Cache-Control',
    'public, s-maxage=10, stale-while-revalidate=59'
  )

  return {
    props: {},
  }
}
```

[캐싱](https://nextjs.org/docs/going-to-production)에 대해 더 배워 보세요.

## Does getServerSideProps render an error page

getServerSideProps 안에서 에러가 던져지면 pages/500.js 파일을 보여줄 것입니다. 500 페이지 생성에 대해 더 배우고 싶다면 문서를 확인하면 됩니다. 개발 중에는 해당 파일이 사용되지 않을 것이고 개발 오버레이가 대신 보여질 것입니다.