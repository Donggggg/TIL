# Client side

클라이언트 사이드 데이터 패칭은 SEO 인덱싱이 요구되지 않는 페이지일 때나 데이터를 프리 렌더링할 필요 없을 때나 페이지의 내용이 자주 업데이트되는 경우에 사용하면 좋습니다. 서버 사이드 렌더링 API들과는 다르게 컴포넌트 레벨에서 클라이언트 사이드 데이터 패칭을 사용할 수 있습니다.

페이지 레벨에서 작성되는 경우, 데이터는 런타임에서 패치되고 데이터가 변하며 페이지의 내용이 변경됩니다. 컴포넌트 레벨에서 사용되는 경우, 데이터는 컴포넌트 마운트 시에 패치되고 컴포넌트의 내용은 데이터가 변하며 변경됩니다.

클라이언트 사이드 데이터 패칭 사용이 애플리케이션의 성능과 페이지 로드 속도에 영향을 끼칠 수 있다는 것을 알고 있어야 합니다. 이것은 데이터 패칭이 컴포넌트나 페이지 마운트 시점에 이뤄지고 데이터가 캐시되지 않기 때문에 그렇습니다.

## Client-side data fetching with useEffect

다음 예제는 useEffect 훅으로 어떻게 클라이언트 사이드에서 데이터를 가져오는 지 보여줍니다.

```tsx
import { useState, useEffect } from 'react'

function Profile() {
  const [data, setData] = useState(null)
  const [isLoading, setLoading] = useState(false)

  useEffect(() => {
    setLoading(true)
    fetch('/api/profile-data')
      .then((res) => res.json())
      .then((data) => {
        setData(data)
        setLoading(false)
      })
  }, [])

  if (isLoading) return <p>Loading...</p>
  if (!data) return <p>No profile data</p>

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.bio}</p>
    </div>
  )
}
```

## Client-side data fetching with SWR

Next.js 전에 팀에서 SWR 이라 불리는 데이터 패칭을 위한 리액트 훅 라이브러리를 만들었습니다. 이 라이브러리는 클라이언트 사이드 데이터 패칭 시에 강력 추천합니다. 캐싱, 재검증, 포커스 추적, 주기적인 리패칭 등을 다룰 수 있습니다.

다음 예제와 같이 사용하면 프로필 데이터를 가져오기 위해 SWR을 사용할 수 있습니다. SWR은 자동으로 데이터를 캐시하고 데이터가 오래되면 재검증할 것 입니다.

더 많은 정보를 위해서는 [SWR docs](https://www.notion.so/Client-side-6147262b08e54ce8bd1923b4a3eb84c5)를 참고하세요.