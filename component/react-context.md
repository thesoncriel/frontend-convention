# Component: React Context

React 에는 [Context API](https://ko.legacy.reactjs.org/docs/context.html) 라는 별도의 상태관리 기능을 제공합니다.

본 문서에서는 이들에 대한 일반적인 사용법을 명시합니다.

## 주요 사용처

React Context (이하 컨텍스트)는 다음과 같은 2가지 사용으로 제한합니다.

- Parameter Context: 페이지 쿼리 파라미터 전달
- UI Context: 표현 컴포넌트간의 상태 전달

## Parameter Context

일반적으로 페이지에서 파라미터를 이용 시 단순 문자열이 아닌 객체로 파싱하여 사용합니다.

이 때 해당 파라미터 자료를 하위 컴포넌트에 전달 시 props drilling 이 많아져서 불필요한 이벤트와 자료 전달이 이뤄질 수 있습니다.

```tsx
import { Link } from 'react-router';

interface MainUiParams {
  keyword: string;
  name: string;
}

interface ChildProps {
  params: MainUiParams;
}

function Third({ params }: ChildProps) {
  return <Link to={`/next?keyword=${params.keyword}`}>블라블라 링크</Link>
}
function Second({ params }: ChildProps) {
  return <Third params={params} />
}
function First({ params }: ChildProps) {
  return <Second params={params} />
}

function MainPage() {
  const params = useQueryParams(refineMainUiParams);

  return (
    <First params={params} />
  );
}
```

위 예시처럼 단순히 하위 표현 컴포넌트의 하이퍼링크를 위해 파라미터를 전달하는 목적이라면,

`Parameter Context` 를 만들어 쓰길 권장합니다.

```tsx
// mainUiParams.context.ts
import { createContext, useContext } from 'react';

const MainUiParamsContext = createContext<MainUiParams>(
  createMainUiParams(),
);

export const MainUiParamsCtxProvider = MainUiParamsContext.Provider;

export function useMainUiParamsCtx() {
  const value = useContext(MainUiParamsContext);

  return value;
}

// next code..
import { Link } from 'react-router';
import { MainUiParamsCtxProvider, useMainUiParamsCtx } from './mainUiParams.context.ts';

interface MainUiParams {
  keyword: string;
  name: string;
}

function Third() {
  const params = useMainUiParamsCtx();

  return <Link to={`/next?keyword=${params.keyword}`}>블라블라 링크</Link>;
}
function Second() {
  return <Third />;
}
function First() {
  return <Second />;
}

function MainPage() {
  const params = useQueryParams(refineMainUiParams);

  return (
    <MainUiParamsCtxProvider value={params}>
      <First />
    </MainUiParamsCtxProvider>
  );
}
```

### 주의사항

parameter context 는 상기 예시 처럼 하위 컴포넌트의 하이퍼링크에 쓰일 파라미터 전달 목적으로만 이용해야 합니다.

즉, 해당 컨텍스트의 자료로 인하여 표현 컴포넌트의 렌더링이 달라지거나 로직이 달라지는 경우에 사용해선 안됩니다.

이런 경우라면 컴포넌트 설계를 바꾸어 전용 props 를 선언하여 사용하도록 변경 해야합니다.

아래는 잘못된 내용을 올바르게 변경하는 예시입니다.

```tsx
// wrong
function Third() {
  const params = useMainUiParamsCtx();

  // nope~! 이렇게 쓰면 안됩니다!!
  if (!params.name) {
    return <Text>블라블라 {params.name}</Text>;
  }

  return <Link to={`/next?keyword=${params.keyword}`}>블라블라 링크</Link>;
}

// good
interface ThirdProps {
  textMode?: boolean;
}

function Third({ textMode }: ThirdProps) {
  const params = useMainUiParamsCtx();

  // 별도 전용 속성을 두어 이 것으로 렌더링을 결정합니다.
  if (textMode) {
    return <Text>블라블라 {params.name}</Text>;
  }

  // 파라미터 컨텍스트로 받아온 값은 오로지 하이퍼링크 쿼리 파라미터 용도로만 사용합니다.
  return <Link to={`/next?keyword=${params.keyword}`}>블라블라 링크</Link>;
}
```

## UI Context

Redux Store 나 외부 환경 데이터 교환이 아닌, 특정 표현 컴포넌트끼리의 데이터 교환이 필요할 때 쓰입니다.

현재 디자인 시스템 기준, Accordion 컴포넌트가 적용된 대표적인 사례입니다.

이렇게 표현 컴포넌트 끼리만 데이터를 교환 할 일이 없다면 일반적인 상태 관리는 `Redux Store` 를 활용합니다.

## 규칙의 의의

React Context API 의 무분별한 사용으로 컴포넌트 로직이 복잡해지지 않도록 제약을 둡니다.

이를 통해 상태관리 기능이나 파라미터 공유 방법을 코드상에서 일관성있게 유지하고 관리할 수 있습니다.