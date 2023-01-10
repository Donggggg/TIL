# Incremental Static Regeneration (증분 정적 재생성)

Next.js는 사이트를 빌드한 후에 정적 페이지를 생성하거나 업데이트하는 것을 제공한다. ISR은 전체 사이트를 재빌드할 필요 없이 각 페이지를 정적 생성하는 것을 허용한다. ISR을 사용하면 수백만 페이지를 확장하면서 정적의 장점을 유지할 수 있다.

> 노트: `experimental-edge` 런타임은 현재 ISR과 호환되지 않지만, `cache-control` 헤더에 `stale-while-revalidate`을 수동으로 설정하여 활용할 수 있다.
>

ISR을 사용하려면, `getStaticProps`에 `revalidate` prop을 추가하면 된다.

```tsx
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  )
}

// This function gets called at build time on server-side.
// It may be called again, on a serverless function, if
// revalidation is enabled and a new request comes in
export async function getStaticProps() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  return {
    props: {
      posts,
    },
    // Next.js will attempt to re-generate the page:
    // - When a request comes in
    // - At most once every 10 seconds
    revalidate: 10, // In seconds
  }
}

// This function gets called at build time on server-side.
// It may be called again, on a serverless function, if
// the path has not been generated.
export async function getStaticPaths() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // We'll pre-render only these paths at build time.
  // { fallback: blocking } will server-render pages
  // on-demand if the path doesn't exist.
  return { paths, fallback: 'blocking' }
}

export default Blog
```

빌드 타임에 프리 렌더링된 페이지에 요청이 있을 때, 처음에는 캐시된 페이지를 보여준다.

- 최초 ~ 10초 사이의 모든 요청은 캐시되고 즉각 처리된다.
- 10초 이후의 화면, 다음 요청도 여전히 캐시 페이지를 보여준다. (단, 데이터는 stale이다.)
- Next.js는 백그라운드에서 페이지 재생성을 발생시킨다.
- 페이지 재생성을 성공하면, Next.js는 캐시를 무효화하고 업데이트된 페이지를 보여준다. 백그라운드 재생성이 실패하면, 오래된 페이지가 대체되지 않을 것 이다.

생성되지 않은 페이지에 요청이 있을 때, Next.js는 첫 요청에 페이지를 서버에서 렌더링한다. 이후 요청들은 캐시에서 정적 파일을 전달할 것이다. Vercel의 ISR은 캐시를 전역으로 유지하고 롤백을 다룰 수 있다.

> 노트: upstream data provider가 기본적으로 캐싱 가능한지 확인해봐라. 관련 설정을 꺼야할 수도 있다. 그렇지 않으면 ISR 캐시를 업데이트하기 위해 신선한 데이터를 가져오지 못할 수 있다.
>

## On-demand Revalidation

만약 60초로 revalidate를 설정했다면, 모든 방문자는 같은 버전으로 생성된 사이트를 1분 동안 볼 것이다. v12.0.0부터 시작하여, Next.js는 특정 페이지의 캐시를 수동적으로 제거하기 위해 `On-Demand Incremantal Static Regeneration`을 제공한다. 이것은 다음과 같은 상황에서 더 쉽게 업데이트할 수 있게 해준다.

- headless CMS로부터 생성되거나 변경된 컨텐츠
- Ecommerce 메타 데이터 변경들(가격, 설명, 카테고리, 리뷰 등)

getStaticProps에서 on-demand revalidation을 사용하기 위해 revalidate를 설정할 필요 없다. revalidate가 제거되면, Next.js는 false(기본값)을 사용할 것이고 revalidate()가 호출될 때 페이지를 재검증할 것이다.

> 노트: 미들웨어는 On-Demand ISR 요청들에 의해 실행되지 않을 것이다. 대신, 재검증을 원하는 정확한 경로에 revalidate()를 호출하면 된다. 예를 들어, `pages/blog/[slug].js` 경로가 있고, `/post-1 → /blog/post-1`로 rewrite 시에 `res.revalidate(’/blog/post-1’)`를 호출해야 한다.
>

### Using On-Demand Revalidation

먼저, Next.js App만 아는 시크릿 토큰을 생성한다. 이 시크릿 값은 재검증 API Route로의 비인가 접근을 방지하기 위해 사용된다. 다음 URL 구조로 접근할 수 있다.(수동적으로나 webhook을 통해서)

```
https://<your-site.com>/api/revalidate?secret=<token>
```

다음으로, 애플리케이션에 시크릿 값을 환경 변수를 등록한다. 최종적으로, 재검증 API Route를 생성한다.

```tsx
// pages/api/revalidate.js

export default async function handler(req, res) {
  // Check for secret to confirm this is a valid request
  if (req.query.secret !== process.env.MY_SECRET_TOKEN) {
    return res.status(401).json({ message: 'Invalid token' })
  }

  try {
    // this should be the actual path not a rewritten path
    // e.g. for "/blog/[slug]" this should be "/blog/post-1"
    await res.revalidate('/path-to-revalidate')
    return res.json({ revalidated: true })
  } catch (err) {
    // If there was an error, Next.js will continue
    // to show the last successfully generated page
    return res.status(500).send('Error revalidating')
  }
}
```

### Testing on-Demand ISR during development

로컬 환경에서 `next dev`를 통해 실행하면, `getStaticProps`는 매 요청마다 발생한다. on-demand ISR가 정확한지 검증하기 위해 프로덕션 빌드와 프로덕션 서버를 돌려야 한다.

그렇게 하면, 정적 페이지가 성공적으로 재검증되는 지 확인할 수 있다.

### Error handling and revalidation

백그라운드 재생성을 다룰 시에 `getStaticProps`에 에러가 있거나 의도적으로 에러를 던진다면, 마지막에 성공적으로 생성된 페이지가 계속해서 보여질 것이다. 다음에 이어지는 요청이 있을 시에 Next.js는 `getStaticProps` 호출을 재시도할 것이다.

```tsx
export async function getStaticProps() {
  // If this request throws an uncaught error, Next.js will
  // not invalidate the currently shown page and
  // retry getStaticProps on the next request.
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  if (!res.ok) {
    // If there is a server error, you might want to
    // throw an error instead of returning so that the cache is not updated
    // until the next successful request.
    throw new Error(`Failed to fetch posts, received status ${res.status}`)
  }

  // If the request was successful, return the posts
  // and revalidate every 10 seconds.
  return {
    props: {
      posts,
    },
    revalidate: 10,
  }
}
```

### Self-hosting ISR

ISR은 `next start`를 사용하지 않는 self-hosted Next.js 사이트들에서도 동작한다.

이 접근은 쿠버네티스나 HashiCorp Nomad와 같은 컨테이너 오케스트라에 배포 중일 때 사용할 수 있다. 기본적으로 생성된 에셋들은 각 pod의 메모리에 저장될 것이다. 이것은 각 pod가 정적 파일들의 복사본을 가지고 있다는 것을 의미한다. 특정 pod에 요청이 오기 전까지 상한 데이터가 보여질 수도 있다.

모든 pod들의 일관성을 보장하려면, In-memory 캐싱을 막아야 한다. 이렇게 하면 Next.js 서버에게 파일 시스템 상의 ISR에 의해 에셋이 생성될 것이라고 알릴 것이다.

다른 컨테이너들 사이에 같은 파일 시스템 캐시를 재사용하기 위해 쿠버네티스에서 공유 네트워크 마운트를 사용할 수 있다. 같은 마운트를 공유함으로써 `next/image` 캐시를 포함하는 `.next` 폴더 또한 공유되고 재사용된다.

in-memory 캐싱을 제거하려면, next.config.js파일에서 isrMemoryCacheSzie를 0으로 설정하면 된다.

> 노트: 공유 마운트가 어떻게 설정되었는 지를 고려하여 동시에 캐시 업데이트를 시도할 때 여러 pod들 사이의 race condition을 고려해야 한다.
>