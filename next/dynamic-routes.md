# Dynamic Routes

경로를 미리 정의하여 라우트를 만드는 것은 복잡한 애플리케이션에 적합하지 않습니다. Next에서는 다이나믹 라우트를 생성하기 위해 페이지에 괄호를 추가할 수 있습니다.

아래의 `pages/post/[pid].js` 페이지를 보십시오:

```tsx
import { useRouter } from 'next/router'

const Post = () => {
  const router = useRouter()
  const { pid } = router.query

  return <p>Post: {pid}</p>
}

export default Post
```

`/post1/`, `/post/abc`, 등 어떤 라우트도 `/pages/post/[pid].js`에 매치될 것입니다. 매치된 경로 파라미터는 페이지에 쿼리 파라미터로 보내질 것이고 다른 쿼리 파라미터와 합병될 것입니다.

예를 들어, `/post/abc` 라우트는 다음 query 객체를 가질 것입니다:

```tsx
{ "pid": "abc" }
```

유사하게, `/post/abc?foo=bar` 라우트는 다음 쿼리 객체를 가질 것입니다:

```tsx
{ "foo": "bar", "pid": "abc"}
```

그러나 라우트 파라미터는 같은 이름의 쿼리 파라미터를 오버라이드할 것입니다. 예를 들어, `/post/abc?pid=123` 라우트는 다음 쿼리 객체를 가질 것입니다.

```tsx
{ "pid": "abc" }
```

여러 다이나믹 라우트 세그먼트도 같은 방식입니다. `/pages/post/[pid]/[coment].js`는 `/post/abc/a-comment`와 매치되고 다음 쿼리 객체를 가질 것입니다.

```tsx
{ "pid": "abc", "comment": "a-comment" }
```

다이나믹 라우트에서 클라이언트 사이드 네비게이션 `next/link`로 다뤄집니다. links를 위의 예시에 맞는 라우트에서 사용하려면 다음과 같습니다:

```tsx
import Link from 'next/link'

function Home() {
  return (
    <ul>
      <li>
        <Link href="/post/abc">Go to pages/post/[pid].js</Link>
      </li>
      <li>
        <Link href="/post/abc?foo=bar">Also goes to pages/post/[pid].js</Link>
      </li>
      <li>
        <Link href="/post/abc/a-comment">
          Go to pages/post/[pid]/[comment].js
        </Link>
      </li>
    </ul>
  )
}

export default Home
```

## Catch all routes

다이나믹 라우트는 괄호 안에 …을 추가하여 모든 경로를 잡도록 확장할 수 있습니다. 예를 들어:

- `/pages/post[…slug].js`는 `/post/a`와 일치하고 `/post/a/b`, `/post/a/b/c`도 마찬가지 입니다.

일치하는 파라미터는 쿼리 파라미터로 페이지에 보내질 것이고 항상 배열일 것입니다. `/post/a`는 다음 쿼리 객체를 가질 것입니다:

```tsx
{ "slug": ["a"] }
```

`/post/a/b`의 케이스나 다른 매칭 경로에서는 새로운 파라미터가 배열에 추가될 것입니다.

```tsx
{ "slug": ["a", "b"] }
```

## Optional catch all routes

이중 괄호(`[[…slug]]`)에 파라미터를 넣어서 옵셔널로 모든 경로를 잡을 수 있게합니다.

예를 들어, `pages/post/[[…slug]].js`는 `/post`, `/post/a`, `/post/a/b` 등과 일치합니다.

옵셔널의 주요 차이점은 파라미터가 없는 라우트도 매치된다는 것입니다.

```tsx
{ } // GET `/post` (empty object)
{ "slug": ["a"] } // `GET /post/a` (single-element array)
{ "slug": ["a", "b"] } // `GET /post/a/b` (multi-element array)
```

## Caveats

- 미리 정의된 라우트는 다이나믹 라우트보다 우선 처리됩니다. 다음 예제를 봅시다:
    - `pages/post/create.js` - `/post/create`와 매치됩니다.
    - `pages/post/[pid].js` - `/post/1`, `/post/abc` 등과 매치되지만 `/post/create`와는 아닙니다.
    - `pages/post/[…slug].js` - `/post/1/2`, `/post/a/b/c`와 매치되지만 `/post/create`, `/post/abc`와는 매치되지 않습니다.
- [Automatic Static Optimization](https://nextjs.org/docs/advanced-features/automatic-static-optimization)에 의해 정적으로 최적화된 페이지는 라우트 파라미터 없이 하이드레이트 될 것입니다.

하이드레이션 후에, Next는 쿼리 객체에 라우트 파라미터를 제공하기 위해 애플리케이션에 변경을 유발할 것입니다.