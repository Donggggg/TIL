# getStaticPaths

페이지가 Dynamic Route를 가지고 있고 `getStaticProps`를 사용한다면 정적으로 생성될 경로 목록을 정의해야 합니다.

동적 경로를 사용하는 페이지에서 `getStaticPaths`라고 불리는 함수를 export하면 Next.js는 `getStaticPaths`에 명시된 모든 경로를 정적으로 프리 렌더링할 것 입니다.

```tsx
// pages/posts/[id].js

// Generates `/posts/1` and `/posts/2`
export async function getStaticPaths() {
  return {
    paths: [{ params: { id: '1' } }, { params: { id: '2' } }],
    fallback: false, // can also be true or 'blocking'
  }
}

// `getStaticPaths` requires using `getStaticProps`
export async function getStaticProps(context) {
  return {
    // Passed to the page component as props
    props: { post: {} },
  }
}

export default function Post({ post }) {
  // Render post...
}
```

[`getStaticPaths API 레퍼런스`](https://nextjs.org/docs/api-reference/data-fetching/get-static-paths)에서 `getStaticPaths`에서 사용 가능한 모든 인자와 props를 확인할 수 있습니다.

## When should I use getStatidPaths?

동적 경로를 사용하는 정적 프리 렌더링 페이지나 다음과 같은 경우 `getStaticPaths`를 사용하면 됩니다:

- 헤드리스 CMS에서 데이터가 오는 경우
- 데이터베이스에서 데이터가 오는 경우
- 파일 시스템에서 데이터가 오는 경우
- 공개적으로 캐싱될 수 있는 데이터인 경우
- 페이지가 반드시 프리 렌더링(SEO를 위해)되야 하거나 매우 빨라야 하는 경우 - `getStaticProps`는 HTML과 JSON 파일을 생성하고 둘 다 성능을 위한 CDN에 캐시될 수 있습니다.

## When does getStaticPaths run

`getStaticPaths`는 상용 빌드 타임에만 실행되고 런타임 중에는 호출되지 않습니다. [다음 도구](https://next-code-elimination.vercel.app/)를 통해 클라이언트 사이드 번들에서 `getStaticPaths` 안에 작성된 코드가 제거된 것을 확인할 수 있습니다.

### How does getStaticProps run with regards to getStaticPaths

- `next build` 동안 `getStaticProps`는 빌드 시에 반환되는 모든 경로에 대하여 실행됩니다.
- `fallback: true`를 사용하면 `getStaticProps`는 백그라운드에서 실행됩니다.
- `fallback: blocking`을 사용하면 최초 렌더링 전에 호출됩니다.

## Where can I use getStaticPaths

- `getStaticPaths`는 반드시 `getStaticProps`와 함께 사용됩니다.
- `getServerSideProps`와 `getStaticPaths`를 사용할 수 없습니다.
- `getStaticProps`를 사용하는 Dynamic Route에서 `getStaticPaths`를 export 할 수 있습니다.
- 페이지가 아닌 파일에서는 `getStaticPaths`를 export 할 수 없습니다.
- 페이지 컴포넌트의 속성이 아닌 독립함수로 `getStaticPaths`를 export 해야 합니다.

## Runs on every request in development

개발 환경에서 getStaticPaths는 매 요청마다 호출될 것 입니다.

## Generating paths on-demand

`getStaticPaths`는 `fallback`으로 요구 시가 아닌 빌드 타임에 어떤 페이지가 생성될 지 결정하도록 해줍니다. 빌드 시 더 많은 페이지를 생성하는 것은 빌드를 느리게 합니다.

`paths`에 빈 배열을 반환하여 모든 페이지를 요청 시에 생성되도록 지연시킬 수 있습니다. 이것은 특히 여러 환경에 Next.js 애플리케이션을 배포할 때 특히 도움됩니다. 예를 들어, 미리보기를 위해 모든 페이지를 온디맨드에 생성하므로써 빌드를 빠르게 하는 경우가 있습니다. 수백개에서 수천개의 정적 페이지가 있는사이트에 도움이 됩니다.