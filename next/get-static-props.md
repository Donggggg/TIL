# GetStaticProps

페이지에서 `getStaticProps` 함수를 export하면 Next는 `getStaticProps`에 의해 반환되는 props를 사용하여 빌드 타임에 해당 페이지를 프리 렌더링합니다.

```tsx
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```

> 렌더링 타입에 상관 없이 어떠한 props든 페이지 컴포넌트에 전달될 것이고 클라이언트 사이드의 초기 HTML에서 볼 수 있습니다. 이것은 페이지가 정확히 하이드레이트 되도록 합니다. 민감한 정보는 클라이언트에서 이용 가능하도록 props에 전달하지 않아야 합니다.
>

## When Should I use getStaticProps?

`getStaticProps`는 다음 상황에서 사용합니다:

- 사용자의 요청보다 이른 빌드 타임에 페이지를 렌더링하는 데 필요한 데이터를 사용 가능한 경우
- 헤드리스 CMS로 부터 데이터가 오는 경우
- 페이지가 반드시 프리 렌더링 되어야 하고 빨라야 하는 경우 — getStaticProps는 HTML과 JSON 파일을 생성하고 둘 다 성능을위해 CDN에 캐시될 수 있습니다.
- 유저 특수 데이터가 아니어서 공개적으로 캐시될 수 있는 데이터인 경우. 특정 상황에서 경로를 재작성하는 미들웨어를 사용하여 이 조건은 무시할 수 있습니다.

## When does getStaticProps run

`getStaticProps`는 항상 서버에서 실행되고 클라이언트 상에서는 실행되지 않습니다. 이 [도구](https://next-code-elimination.vercel.app/)를 사용하여 `getStaticProps` 안에 쓰여진 코드가 클라이언트 사이드 번들에서 제거된 것을 확인할 수 있습니다.

- `getStaticProps`는 항상 `next build` 동안에 실행됩니다.
- `getStaticProps`는 `fallback: true`를 설정하면 백그라운드에서 실행됩니다.
- `getStaticProps`는 `fallback: blocking`을 설정하면 최초 렌더링 전에 호출됩니다.
- `getStaticProps`는  `revalidate`를 사용하면 백그라운드에서 실행됩니다.
- `getStaticProps`는 `revalidate()`를 사용하면 요구 시 백그라운드에서 실행됩니다.

Incremental Static Regeneration과 결합하면 getStaticProps는 오래된 페이지가 재검증 되는 동안 백그라운드에서 실행되고 신선한 페이지가 브라우저에 제공됩니다.

getStaticProps는 정적 HTML을 생성하므로 (쿼리 파라미터나 HTTP 헤더와 같은) 유입 요청에 접근할 수 없습니다. 페이지의 요청에 접근할 필요가 있다면 getStaticProps에 추가로 미들웨어 사용을 고려해야 합니다.

> 쿼리 파라미터는 `next/router`로 접근할 수 있지 않나요?
>

## Using getStaticProps to fetch data from CMS

다음 예제는 CMS에서 블로그 포스트 목록을 어떻게 가져오는 지 보여줍니다.

```tsx
// posts will be populated at build time by getStaticProps()
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// This function gets called at build time on server-side.
// It won't be called on client-side, so you can even do
// direct database queries.
export async function getStaticProps() {
  // Call an external API endpoint to get posts.
  // You can use any data fetching library
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // By returning { props: { posts } }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  }
}

export default Blog 
```

`[getStaticProps` API 레퍼런스](https://nextjs.org/docs/api-reference/data-fetching/get-static-props)는 `getStaticProps`에서 사용할 수 있는 모든 파라미터와 props를 다룹니다.

## Write server-side code directly

`getStaticProps`는 서버 사이드에서만 실행되므로 클라이언트 사이드에서는 절대 실행되지 않습니다. 심지어 브라우저 JS 번들에도 포함되지 않으므로 브라우저에 데이터베이스 쿼리를 보내지고 않고 직접 작성할 수 있습니다.

이것은 `getStaticProps`에서 API rotue를 패칭하는 대신 서버 사이드 코드를 직접 작성할 수 있는 것을 의미합니다.

다음 예제를 살펴 봅니다. API route는 CMS에서 몇 가지 데이터를 패치하기 위해 사용됩니다. 해당 API route는 `getStaticProps`에서 직접 호출됩니다. 이것은 추가 호출을 생산하고 성능을 감소시킵니다. 대신에 CMS에서 데이터를 패칭하는 로직은 `lib/` 디렉터리를 사용하여 공유할 수 있습니다. 그러면 `getStaticProps`에서 공유할 수 있습니다.

대안으로 데이터를 패치하기 위해 API route를 사용하지 않는다면 fetch() API를 getStaticProps에 직접 사용할 수 있습니다.

Next가 클라이언트 사이드 번들에서 제거된 것을 검증하고 싶다면 [next-code-elimination tool](https://next-code-elimination.vercel.app/)을 사용하면 됩니다.

## Statically generates both HTML and JSON

`getStaticProps`로 페이지가 빌드 타임에 프리렌더링될 때, 페이지 HTML 파일에 추가로 `getStaticProps` 실행 결과를 가지고 있는 JSON 파일을 생성합니다.

이 JSON 파일은 `next/link`나 `next/router`를 통한 클라이언트 사이드 라우팅에서 사용됩니다. `getStaticProps`를 사용하여 프리 렌더링된 페이지로 전환하는 경우 Next는 JSON 파일을 패치하고 페이지 컴포넌트의 props로 사용합니다. 이것은 클라이언트 사이드 페이지 이동이 JSON을 사용하므로써 `getStaticProps`를 호출하지 않는 것을 의미합니다.

ISR을 사용하는 경우, `getStaticProps`는 클라이언트 사이드 전환에 필요한 JSON을 생성하기 위해 백그라운드에서 실행될 것입니다. 같은 페이지에서 여러 요청이 만들어지는 형태로 확인할 수 있는데 이는 의도된 것이며 최종 사용자 성능에 영향이 없습니다.

## Where can I use getStaticProps

`getStaticProps`는 페이지에서만 export될 수 있습니다. `_app, _docuemnt, _error`와 같은 페이지가 아닌 파일에서는 export할 수 없습니다.

이 규제에 대한 이유 중 하나는 리액트는 페이지가 렌더링되기 이전에 요구하는 데이터를 모두 가지고 있어야 한다는 것입니다.

또한 `getStaticProps`는 독립 함수이고 페이지 컴포넌트의 속성으로 추가하면 동작하지 않습니다.

> Note: 커스텀 앱을 생성했다면 페이지 컴포넌트에 전달한 `pageProps`를 확인해야 합니다, 그렇지 않으면 props가 비어있을 수도 있습니다.
>