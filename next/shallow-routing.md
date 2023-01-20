# Shallow Routing

Shallow routing은 `getServerSideProps`, `getStaticProps`, `getInitialProps`을 포함한 데이터 패칭 함수를 다시 실행하지 않고 URL을 변경할 수 있도록 합니다.

상태 손실 없이 router 객체를 통해 업데이트된 `pathname`과 `query`를 받을 수 있습니다.

shallow routing을 가능하게 하려면 `shallow` 옵션을 `true`로 설정하면 됩니다. 다음 예제를 봅시다:

```tsx
import { useEffect } from 'react'
import { useRouter } from 'next/router'

// Current URL is '/'
function Page() {
  const router = useRouter()

  useEffect(() => {
    // Always do navigations after the first render
    router.push('/?counter=10', undefined, { shallow: true })
  }, [])

  useEffect(() => {
    // The counter changed!
  }, [router.query.counter])
}

export default Page
```

URL은 `/?counter=10`으로 업데이트될 것이고 페이지는 교체되지 않을 것입니다. 오직 라우트의 상태만 변경되었습니다.

아래와 같이 `componentDidUpdate`를 통해 URL 변경을 관찰할 수 있습니다.

```tsx
componentDidUpdate(prevProps) {
  const { pathname, query } = this.props.router
  // verify props have changed to avoid an infinite loop
  if (query.counter !== prevProps.router.query.counter) {
    // fetch data based on the new query
  }
}
```

## Caveats

Shallow routing은 현재 페이지의 URL 변경에서만 동작합니다. 예를 들어 pages/about.js라는 다른 페이지가 있다고 가정하고 다음을 실행해 봅니다:

```tsx
router.push('/?counter=10', '/about?counter=10', { shallow: true })
```

새로운 페이지이기에 현재 페이지는 언로드될 것이고 Shallow routing을 요청했어도 새로운 것을 로드하고 데이터 패칭을 기다릴 것입니다.

shallow routing이 미들웨어와 함께 사용된다면 새 페이지와 현재 페이지가 일치하는지 확인하지 않습니다. 미들웨어가 동적으로 리라이트될 수 있고 shallow로 생략한 데이터 패치 없이는 클라이언트 사이드에서 확인할 수 없기 때문에 shallow route 변경은 항상 shallow로 취급해야 합니다.