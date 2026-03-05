# Coding: Don't Use Unusual Types

Typescript 는 Javascript 의 Superset 인 만큼, 정말 다양한 타입 선언들을 지원합니다.

그러나 이러한 타입들을 무분별하게 선언하고 사용하면 코드 가독성이 떨어지고 작성자만 알 수 있는 코드가 될 수 있습니다.

이러한 타입들을 통틀어 통상적이지 않은 타입(unusual types)이라 부릅니다.

이 곳에서는 이러한 `비통상적 타입`들에 대해 알아보고 그 것에 대한 대안을 설명합니다.

## 임의의 문자열 타입은 지양합니다.

사용자의 입력이나 서버에서 내려준 문자열 자료에 특수한 규칙이 적용되어 있을 수 있습니다.

가령 해시태그처럼 앞에 `#` 이 붙는 경우입니다.

```ts
// do not! 🙅‍♂️

interface HashTagEntity {
  hash: `#${string}`;
  name: string;
}
```

이렇게 특수한 규칙이나 고정 문자열을 문자열 타입에 template literal 을 이용하여 명시할 수 있습니다.

하지만 이러한 타입은 통상적이지 않은 방법이므로 다음과 같이 주석을 보완하는 방향으로 작성합니다.

```ts
// do this! 🙆‍♂️

interface HashTagEntity {
  /**
   * 해시태그 값.
   * 
   * 값 앞에 "#"가 붙을 수 있음.
   */
  hash: string;
  /**
   * 해시태그 명
   */
  name: string;
}
```

## Entity 는 반드시 interface 객체여야 합니다.

서버와 같이 외부에서 받아들이는 데이터는 그 종류가 각양각색 입니다.

이러한 타입들을 내부의 UI State 와 구별하기 위해 임의의 타입을 선언하는 경우가 있습니다.

아래는 서버 API 응답값이 String Array 일 때 그것을 타입 별칭을 했을 때 예시입니다.

```json
{
  "description": "서버에서 내려주는 데이터가 아래와 같다고 가정합니다.",
  "data": {
    "payload": ["신발", "셔츠", "가방"]
  }
}
```

```ts
// 아래 예시의 SingleRes 타입 구조는 다음과 같습니다.

export interface PayloadRes<T> {
  payload: T;
}

export interface SingleRes<T> {
  data: PayloadRes<T>
}
```

```ts
// do not! 🙅‍♂️

// data.payload 의 타입
type GoodsResponseEntity = string[];

// api 에서 선언 예시
export const api = {
  fetchGoods() {
    return baseApi.get<SingleRes<GoodsResponseEntity>>('/api/goods');
  }
}
```

그러나 이러한 임의의 타입 선언은 자칫 코드에 대해 오해를 불러 일으킬 수 있습니다.

그렇기에 아래와 같이 통상적인 타입을 사용합니다.

```ts
// do this! 🙆‍♂️

// 별도 타입 선언은 없습니다.

export const api = {
  fetchGoods() {
    return baseApi.get<SingleRes<string[]>>('/api/goods');
  }
}
```

그래도 조금이나마 해당 타입에 대한 상세한 내용을 기록하고 싶다면, 해당 타입을 불러오는 함수에 주석으로 대신 보충하길 권장합니다.

```ts
// do this! 🙆‍♂️

export const api = {
  /**
   * 상품명 목록을 가져온다.
   * 
   * @see https://www.jira.com/blah-blah/thumbup
   * @also here and there..
   */
  fetchGoods() {
    return baseApi.get<SingleRes<string[]>>('/api/goods');
  }
}
```

## 원시타입(Primitive Type)은 type alias 하지 않습니다.

필요에 따라 원시타입에 해당하는 string, number, boolean 등을 type alias 하여 임의의 타입명을 지정하는 경우가 있습니다.

```ts
// do not! 🙅‍♂️

type MyNameType = string;

type MyAgeType = number;

type AmIYoungType = boolean | null;

function makeMyInfo(
  name: MyNameType,
  age: MyAgeType,
  young: AmIYoungType = null
) {
  // codes...
}
```

이러한 경우엔 아래와 같이, 사용되는 타입을 직접 명시해줍니다.

```ts
// do this! 🙆‍♂️

function makeMyInfo(
  name: string,
  age: number,
  young: boolean | null = null
) {
  // codes...
}
```

## 표준 내장 객체(Standard built-in objects)는 type alias 하지 않습니다.

Javascript 에서는 [표준 내장 객체](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects)를 기븐적으로 제공하여 다양한 업무에 활용할 수 있도록 도와주고 있습니다.

이들은 그 자체로 명확한 하나의 타입이기에 별도로 타입 별칭을 선언하지 않습니다.

```ts
// do not! 🙅‍♂️

type AgeListType = number[];
type MySetType = Set<string>;
type OurMapDataType = Map<string, UserDto>;
type CustomDictionaryType = Record<number, MyOptionUiState>;

const ages: AgeListType = [1,2,3];
const set: MySetType = new Set<string>();
const map: OurMapDataType = new Map<string, UserDto>();
const dic: CustomDictionaryType = {
  1: { name: 'pome' },
  2: { name: 'jordy' },
  3: { name: 'pome' },
};

function execution(
  arg0: AgeListType,
  arg1: MySetType,
  arg2: OurMapDataType,
  arg3: CustomDictionaryType,
) {
  // codes..
}
```

위의 경우는 아래와 같이 사용하길 권장합니다.

```ts
// do this! 🙆‍♂️

// case1: 변수들은 타입 추론(Type Inference)을 적극 활용합니다.
const ages = [1, 2, 3];
const ages: number[] = []; // 내부 요소 없이 초기화 할 경우엔 타입을 별도로 선언 해줍니다.
const set = new Set<string>();
const map = new Map<string, UserDto>();

// case2: 레코드 타입은 제네릭을 이용하여 표기합니다.
const dic: Record<number, MyOptionUiState> = {
  1: { name: 'pome' },
  2: { name: 'jordy' },
  3: { name: 'pome' },
};

function execution(
  arg0: number[],
  arg1: Set<string>,
  arg2: Map<string, UserDto>,
  arg3: Record<number, MyOptionUiState>,
) {
  // codes..
}
```

## Typescript 전용 Utility type 을 남발하지 않습니다.

Pick, Omit, union 및 intersection 과 같은 Typescript 전용 문법 및 유틸리티 타입들은 매우 유용합니다.

다만 모든 제품 코드에 이들 기법을 제한없이 사용 할 경우, 코드 가독성을 해칠 수 있기에 다음 내용을 유의하여 주십시요.

### Omit 대신 Base Type 을 선언합니다.

대표적인 사례가 아래와 같이 Omit 으로 특정 필드를 제외하는 것인데, 이는 Base Type 선언으로 대체합니다.

```ts
// do not! 🙅‍♂️

interface MemberUiState {
  id: number;
  name: string;
  age: number;
  address: string;
}

// address 필드가 불필요하여 Omit 을 사용하였습니다.
type SemiMemberUiState = Omit<MemberUiState, 'address'>;

const state: SemiMemberUiState = {
  id: 1,
  name: 'pome',
  age: 13,
}

const original: MemberUiState = { 
  ...state,
  address: '서울시',
}
```

아래는 위 내용을 Base Type 선언으로 대체한 예시입니다.

```ts
// do this! 🙆‍♂️

interface BaseMemberUiState {
  id: number;
  name: string;
  age: number;
}

// 기존 타입을 base type 기반으로 확장 시킵니다.
interface MemberUiState extends BaseMemberUiState {
  address: string;
}

const state: BaseMemberUiState = {
  id: 1,
  name: 'pome',
  age: 13,
};

const original: MemberUiState = { 
  ...state,
  address: '서울시',
};
```

### Pick 대신 필요한 별도 타입을 선언합니다.

Pick으로 원본 타입에서 필요한 몇몇 필드만 뽑아서 쓰는 경우가 굉장히 많습니다.

이럴 땐 단순히 `필요한 타입을 추가로 작성` 합니다.

```ts
// do not! 🙅‍♂️

interface MemberUiState {
  id: number;
  name: string;
  age: number;
  address: string;
}

// id 와 name 만 필요하여 이들 필드만 따로 사용하려 합니다.
type PureMemberUiState = Pick<MemberUiState, 'id' | 'name'>;

const state: PureMemberUiState = {
  id: 10,
  name: 'jordy',
};
```

```ts
// do this! 🙆‍♂️

interface MemberUiState {
  id: number;
  name: string;
  age: number;
  address: string;
}

// 별도의 타입을 추가하였습니다.
interface PureMemberUiState {
  id: number;
  name: string;
}

const state: PureMemberUiState = {
  id: 10,
  name: 'jordy',
};
```


## Utility type 을 통해서 만들어진 타입은 다른 타입과 혼용하지 않습니다.

종종 Omit 과 Pick 을 사용하여 불가피하게 비즈니스 로직을 구성해야 할 때가 있습니다.

이때 이들 타입을 이용하여 별칭(type alias)을 사용하지 않으며, 이들 구문을 통해 type extends 나 또 다른 Utility type 사용, 혹은 union 및 intersection 을 뒤이어 사용하지 않습니다.

```ts
// do not! 🙅‍♂️

interface UserUiState {
  name: string;
  level: number;
  location: string;
}

// level 만 제외해서 사용하는 타입.
type OmittedLevelUserUiState = Omit<UserUiState, 'level'>;

// level 만 제외한 것을 다시 타입 확장.
interface MemberUiState extends Omit<UserUiState, 'level'> {
  isHighLevel: boolean;
}

// intersection 을 이용한 타입 확장.
type MemberUiState = Omit<UserUiState, 'level'> & {
  isHighLevel: boolean;
}

// name 만 가져온 타입.
// Omit 했던 타입을 또 utility type 을 이용하여 변경하고 있습니다.
type CustomUserUiState = Pick<OmittedLevelUserUiState, 'name'>;
```

하나의 타입으로 이렇게 돌려 쓰면, 그 원본(origin) 타입을 추적하기 어려우며 설계 미스가 될 가능성이 높아집니다.

그래서 다음과 같은 방법을 권장합니다.

```ts
// do this! 🙆‍♂️

// 처음부터 level 이 없는 base type 을 별도로 선언합니다.
interface BaseUserUiState {
  name: string;
  location: string;
}

// level 만 제외해서 사용하는 타입.
// 필요에 따른 type alias 는 허용됩니다.
type LevelUserUiState = BaseUserUiState;

// base type 기반으로 확장.
interface MemberUiState extends BaseUserUiState {
  isHighLevel: boolean;
}

// intersection 을 이용한 타입 확장은 지양합니다.

// 필요하다면 name 만 존재하는 별도 타입을 선언합니다.
interface CustomUserUiState {
  name: string;
}
```

> ℹ️ 그럼 Omit 과 Pick 같은 Utility type 은 언제 쓰나요? 😭
>
> 공용(common) 기능이나 공통 라이브러리 등에서는 쓰일 수 있습니다.
>
> 가령 공통 컴포넌트의 Props Type 들은 필요에 따라 Utility type 을 자유롭게 쓰셔도 됩니다!
>
> 물론 이 것도 지나치면 좋지 않을테니 잘 판단해서 쓰셔야 합니다! 😅
>
> 단일 업무용(보통 feature module 내 코드들)으로는 가급적 지양합니다만, 때에 따라 이것이 필요한 경우가 있다면 사용 할 수 있습니다.
>
> 즉 반드시 필요하면 쓰되, 단순히 코드 작성의 편의성을 위함이라면 한번쯤 고민하고 설계의 변경이 필요한 부분은 아니었는지 짚고 넘어가시길 권장합니다! 👍


## 규칙의 의의

Pick 과 Omit 등을 통해 변형된 타입들을 이용하여 당장은 편하게 쓸 수 있습니다.

그러나 이러한 유연성을 지나치게 남용할 경우, 코드 난독화와 작업중 실수를 발생시키는 원인이 됩니다.

그리고 타입이 변경 되었다는 것은, 업무가 변경되었다는 의미입니다.

이를 별도의 타입 선언으로 끊어 놓지 않으면, 각 업무들은 의존성이 높아집니다.

그래서 이 부분을 미리 정리하여 예상치 못한 side-effect 를 예방할 수 있습니다.