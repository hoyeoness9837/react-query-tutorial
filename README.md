# 💻 React-query, Redux-Toolkit

## 📃 json-server

- json-server 실행

```
   yarn server-json
```

## 📃 React-Query 개요 및 기능

### 개요

- React를 위한 강력하고 성능 좋은 데이터 동기화
  `전역 상태`를 건드리지 않고도 React 및 React Native 애플리케이션에서 데이터를 가져오고, 캐시하고, 업데이트할 수 있습니다.

<br />

### 기능

1. 선언적 및 자동

   - 데이터 가져오기 로직을 ​​손으로 작성하는 것은 끝났습니다. React Query에 데이터를 가져올 위치와 데이터가 얼마나 필요한지 알려주면 나머지는 자동입니다. React Query는 구성이 필요 없는 즉시 캐싱, 백그라운드 업데이트 및 오래된 데이터를 처리합니다.

2. 간단하고 친숙한

   - promise 또는 async/await로 작업하는 방법을 알고 있다면 이미 React Query를 사용하는 방법을 알고 있는 것입니다. 관리할 전역 상태, 감속기, 정규화 시스템 또는 이해해야 할 무거운 구성이 없습니다. 데이터를 해결하는(또는 오류를 발생시키는) 함수를 전달하기만 하면 나머지는 기록입니다.

3. 강력하고 구성 가능

   - React Query는 모든 사용 사례에 맞는 노브와 옵션을 사용하여 쿼리의 각 관찰자 인스턴스까지 구성할 수 있습니다. 전용 devtools, 무한 로딩 API 및 데이터 업데이트를 쉽게 만들어주는 일류 mutation 도구와 연결되어 있습니다. 걱정하지 마십시오. 모든 것이 성공을 위해 미리 구성되어 있습니다!

4. 적은 코드. 더 적은 엣지 케이스.
   - 리듀서, 캐싱 로직, 타이머, 재시도 로직, 복잡한 비동기/대기 스크립팅(계속 진행할 수 있습니다...) 대신 문자 그대로 평소 하던 코드의 아주 작은 부분을 작성합니다. React Query를 사용할 때 작성하는 코드가 얼마나 적은지 또는 얼마나 많은 코드를 삭제하는지에 놀랄 것입니다.

<br />

## 📃 React-Query 구성 요소

<br />

### 🤔 QueryClientProvider, QueryClient

- App.js에 Context Provider로 이하 컴포넌트를 감싸고 queryClient를 내려보내줌 ⇒ 이 context는 앱에서 비동기 요청을 알아서 처리하는 background 계층이 됨
- QueryClientProvider는 구성 요소를 사용하여 QueryClient를 연결하고 응용 프로그램에 제공
- QueryClient를 사용하여 캐시와 상호 작용할 수 있습니다.

```jsx
import { QueryClient, QueryClientProvider } from "react-query";

const queryClient = new QueryClient();

function App() {
  return (
   <QueryClientProvider client={queryClient}>
      <div>블라블라</div>
   </QueryClientProvider>;
  );
}
```

<br />

### 🤔 useQuery

```jsx
const getSuperHero = useCallback(() => {
  return axios.get("http://localhost:4000/superheroes");
}, []);

const { isLoading, data } = useQuery("super-heroes", getSuperHero);
```

<br />

### 🤔 useQuery Error Handle

- useQuery 주로 사용되는 3가지 return 값 중에서 isError와 error 프로퍼티로 에러처리를 할 수 있다.

```js
const getSuperHero = useCallback(() => {
  return axios.get("http://localhost:4000/superheroes1");
}, []);

const { isLoading, data, isError, error } = useQuery(
  "super-heroes",
  getSuperHero
);
```

<br />

### 🤔 React Query Devtools

- React Query는 전용 devtools를 제공한다.
- devtools를 사용하면 React Query의 모든 내부 동작을 시각화하는데 도움이 되며 문제가 발생하면 디버깅 시간을 절약할 수 있다.

```jsx
import { ReactQueryDevtools } from "react-query/devtools";

<AppContext.Provider value={user}>
  <QueryClientProvider client={queryClient}>
    // ...
    <ReactQueryDevtools />
  </QueryClientProvider>
</AppContext.Provider>;
```

```
1. initialIsOpen: Boolean
  - true이면 개발 도구가 기본적으로 열려 있도록 설정할 수 있다.
2. panelProps: PropsObject
  - 패널에 props을 추가할 수 있다. 예를 들어 className, style, onClick 등
3. closeButtonProps: PropsObject
  - 닫기 버튼에 props를 추가할 수 있다.
4. toggleButtonProps: PropsObject
  - 토글 버튼에 props를 추가할 수 있다.
5. position?: "top-left" | "top-right" | "bottom-left" | "bottom-right"
  - 기본값: bottom-left
  - devtools 패널을 열고 닫기 위한 로고 위치
```

<br />

### 🤔 React Query 캐싱

- 캐시 라이플 사이클

```
* Query Instances with and without cache data(캐시 데이터가 있거나 없는 쿼리 인스턴스)
* Background Refetching(백그라운드 다시 페칭)
* Inactive Queries(비활성 쿼리)
* Garbage Collection(가비지 컬렉션)
```

1. A 쿼리 인스턴스가 mount 됨
2. 네트워크에서 데이터 fetch 하고 A라는 query key로 캐싱함
3. 이 데이터는 fresh 상태에서 staleTime(기본값 0) 이후 stale 상태로 변경됨
4. A 쿼리 인스턴스가 unmount 됨
5. 캐시는 cacheTime(기본값 5min) 만큼 유지되다가 가비지 콜렉터로 수집됨
6. 만일 cacheTime이 지나기 전에 A 쿼리 인스턴스가 새롭게 mount되면, fetch가 실행되고 fresh한 값을 가져오는 동안 캐시 데이터를 보여줌

<br />

### 🤔 staleTime, cacheTime

1. staleTime
   - 데이터가 fresh -> stale 상태로 변경되는데 걸리는 시간
   - fresh 상태일때는 쿼리 인스턴스가 새롭게 mount 되어도 네트워크 fetch가 일어나지 않는다.
   - 데이터가 한번 fetch 되고 나서 staleTime이 지나지 않았다면 unmount 후 mount 되어도 fetch가 일어나지 않는다.
2. cacheTime
   - 데이터가 inactive 상태일 때 캐싱된 상태로 남아있는 시간
   - 쿼리 인스턴스가 unmount 되면 데이터는 inactive 상태로 변경되며, 캐시는 cacheTime만큼 유지된다.
   - cacheTime이 지나면 가비지 콜렉터로 수집된다.
   - cacheTime이 지나기 전에 쿼리 인스턴스가 다시 마운트 되면, 데이터를 fetch하는 동안 캐시 데이터를 보여준다.
   - cacheTime은 staleTime과 관계없이, 무조건 inactive 된 시점을 기준으로 캐시 데이터 삭제를 결정한다.

<br />

### 🤔 isFetching, isLoading

- isFetching : 데이터가 fetch될 때 true, 캐싱 데이터가 있어서 백그라운드에서 fetch되더라도 true
- isLoading : 캐싱된 데이터가 없을때 fetch 중에 true

```jsx
const getSuperHero = useCallback(() => {
  return axios.get("http://localhost:4000/superheroes");
}, []);

const { isLoading, isFetching, data, isError, error } = useQuery(
  "super-heroes",
  getSuperHero,
  {
    cacheTime: 5000,
  }
);
```

<br />
