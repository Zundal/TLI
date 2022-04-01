<https://redux-toolkit.js.org/rtk-query/usage/queries>

# Queries

## OverView

     This is the most common use case for RTK Query.

     이것은 RTK 쿼리의 가장 일반적인 사용 사례입니다.

    A query operation can be performed with any data fetching library of your choice,

    원하는 데이터 가져오기 라이브러리를 사용하여 쿼리 작업을 수행할 수

    but the general recommendation is that you only use queries for requests that retrieve data.

    있지만 일반적으로 데이터를 가져오는 요청에 대해서만 쿼리를 사용하는 것이 좋습니다.

    For anything that alters data on the server or will possibly invalidate the cache, you should use a Mutation.

    서버의 데이터를 변경하거나 캐시를 무효로 할 가능성이 있는 경우는, 변환을 사용할 필요가 있습니다.

---

    By default, RTK Query ships with fetchBaseQuery,

    디폴트로는 RTK Query에는 fetchBaseQuery가 부속되어 있습니다.

    which is a lightweight fetch wrapper that automatically handles request headers and response parsing in a manner similar to common libraries like axios.

    이것은 Axios와 같은 일반적인 라이브러리와 같은 방법으로 요청 헤더 및 응답 해석을 자동으로 처리하는 경량 페치 래퍼입니다.

    See Customizing Queries if fetchBaseQuery does not handle your requirements.

    fetchBaseQuery가 요구 사항을 처리하지 않는 경우 쿼리 사용자 지정을 참조하십시오.

---

## Defining Query Endpoints​

---

    Query endpoints are defined by returning an object inside the endpoints section of createApi, and defining the fields using the builder.query() method.

    쿼리 끝점은 createApi의 끝점 섹션 내에 개체를 반환하고 작성기를 사용하여 필드를 정의하여 정의합니다.query() 메서드.

    Query endpoints should define either a query callback that constructs the URL (including any URL query params), or a queryFn callback that may do arbitrary async logic and return a result.

    쿼리 엔드포인트에서는 URL을 구성하는 쿼리 콜백(URL 쿼리 파라미터 포함) 또는 임의의 비동기 로직을 실행하여 결과를 반환하는 queryFn 콜백을 정의해야 합니다.

    If the query callback needs additional data to generate the URL, it should be written to take a single argument. If you need to pass in multiple parameters,

    pass them formatted as a single "options object".

    쿼리 콜백에 URL을 생성하기 위해 추가 데이터가 필요한 경우 단일 인수를 사용하도록 작성해야 합니다.

    여러 매개 변수를 전달해야 하는 경우 단일 "옵션 개체" 형식으로 전달하십시오.

    Query endpoints may also modify the response contents before the result is cached, define "tags" to identify cache invalidation, and provide cache entry lifecycle callbacks to run additional logic as cache entries are added and removed.

    쿼리 엔드포인트는 결과가 캐시되기 전에 응답 내용을 변경하고 캐시 무효화를 식별하기 위한 "태그"를 정의하며 캐시 엔트리가 추가 및 삭제될 때 추가 로직을 실행하기 위한 캐시 엔트리 라이프 사이클 콜백을 제공할 수도 있습니다.

```javascript
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query'
import { Post } from './types'

const api = createApi({
  baseQuery: fetchBaseQuery({
    baseUrl: '/',
  }),
  tagTypes: ['Post'],
  endpoints: (build) => ({
    updatePost: build.mutation<Post, Partial<Post> & Pick<Post, 'id'>>({
      // note: an optional `queryFn` may be used in place of `query`
      query: ({ id, ...patch }) => ({
        url: `post/${id}`,
        method: 'PATCH',
        body: patch,
      }),
      // Pick out data and prevent nested properties in a hook or selector
      transformResponse: (response: { data: Post }, meta, arg) => response.data,
      invalidatesTags: ['Post'],
      // onQueryStarted is useful for optimistic updates
      // The 2nd parameter is the destructured `MutationLifecycleApi`
      async onQueryStarted(
        arg,
        { dispatch, getState, queryFulfilled, requestId, extra, getCacheEntry }
      ) {},
      // The 2nd parameter is the destructured `MutationCacheLifecycleApi`
      async onCacheEntryAdded(
        arg,
        {
          dispatch,
          getState,
          extra,
          requestId,
          cacheEntryRemoved,
          cacheDataLoaded,
          getCacheEntry,
        }
      ) {},
    }),
  }),
})
```

---

This is the most common use case for RTK Query.

A query operation can be performed with any data fetching library of your choice,  
but the general recommendation is that you only use queries for requests that retrieve data.  
For anything that alters data on the server or will possibly invalidate the cache, you should use a Mutation.  
By default, RTK Query ships with fetchBaseQuery, which is a lightweight fetch wrapper that automatically handles request headers and response parsing in a manner similar to common libraries like axios.  
See Customizing Queries if fetchBaseQuery does not handle your requirements.
이것은 RTK 쿼리의 가장 일반적인 사용 사례입니다.  
원하는 모든 데이터 가져오기 라이브러리를 사용하여 쿼리 작업을 수행할 수 있지만 일반적인 권장 사항은 데이터를 검색하는 요청에만 쿼리를 사용하는 것입니다.

서버의 데이터를 변경하거나 캐시를 무효화할 수 있는 모든 경우에는 돌연변이를 사용해야 합니다.  
기본적으로 RTK Query는 axios와 같은 일반적인 라이브러리와 유사한 방식으로 요청
헤더 및 응답 구문 분석을 자동으로 처리하는 경량 가져 오기 래퍼인 fetchBaseQuery와 함께 제공됩니다.  
fetchBaseQuery가 요구 사항을 처리하지 않는 경우 쿼리 사용자 정의를 참조하십시오.

## useQuery

> Composes useQuerySubscription and useQueryState and is the primary hook.  
> Automatically triggers fetches of data from an endpoint,  
> 'subscribes' the component to the cached data,  
> and reads the request status and cached data from the Redux store

> useQuerySubscription 및 useQueryState를 구성하고 프라이머리 훅입니다.  
> 엔드포인트에서 데이터 가져오기를 자동으로 트리거하고 구성 요소를 캐시된 데이터에 '가입'하며 Redux 저장소에서 요청 상태 및 캐시된 데이터를 읽습니다.
