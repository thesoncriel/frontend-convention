# Architecture: Type Definition

업무용 프론트엔드 아키텍처를 다룰 때 각종 데이터 타입을 어떻게 선언하고 사용되는지 설명합니다.

## 용도에 따른 분류

소프트웨어 운영 주체인 기업과 그 내부 솔루션 구조에 따라 데이터를 다루는 형식은 각각 다를 수 있습니다.

다만, 작성자의 경험에 비추어 보았을 때, 일반적으로 2개의 큰 카테고리가 존재함을 알 수 있었습니다.

하나는 클라이언트 측 `내부 데이터`,

또 다른 하나는 서버 및 서드파티등의 `외부 데이터` 입니다.

이들은 같은 목적(업무 처리)을 가지고 있으나 그 성질이 다릅니다.

때문에 명칭을 달리하여 그 목적을 구분합니다.

전자인 내부 데이터는 `UI State` 로,

후자인 외부 데이터는 `Entity` 로 정의합니다.

### UI State

UI State 는 이름처럼 오직 View 와 그 부속 컴포넌트인 User Interface 부분에 특화된 데이터입니다.

이들 역할은 오로지 사용자 화면에만 관심을 가지는 자료입니다.

반면 서버나 서드파티측에선 이 데이터를 해석한다거나 어떻게 쓰이는지에 관심을 가지고 있지 않습니다.

### Entity

반면 Entity 는 오로지 외부 환경에 초점이 맞춰진 데이터입니다.

이들은 클라이언트 입장에선 절대 변경이 불가하며 외부 환경과 통신(주고 받기) 할 때 반드시 필요한 데이터입니다.

가령 서버측은 이 데이터에 매우 관심이 많습니다.

클라이언트측 역시 이 데이터의 전부, 혹은 일부가 필요합니다.

그러나 이 데이터를 그대로 가져다 쓸 경우 여러가지 문제(로직 복잡도 증가, 예외처리 추가, 코드 난독화 등)가 발생됩니다.

그에 따라 `Repository` 를 통하여 UI State 로 변환하여 사용합니다.

> ℹ️ 팁
>
> 이에 따른 문제와 해결 방법에 대해서는 [Repository](./feature_repository.md) 문서에서 다루고 있으므로 참고 바랍니다.

## 타임 정의 방법

UI State 와 Entity 는 용도의 특수성이 명확하므로 접미어로 각각 `UiState` 와 `Entity` 를 두어 구분합니다.

이 외 단순 데이터들은 `Data Transfer Object` 의 앞글자를 따서 `Dto` 라는 접미어를 붙입니다.

각종 메서드와 이벤트, 함수에 전달되는 인자는 `Arguments` 를 줄여서 `Args` 접미어를 붙입니다.

스토어와 데이터 교환은 위에서 언급된 `UiState` 혹은 `Dto` 를 사용해도 되나, 별도 타입이 필요하다면 `Payload` 를 사용합니다.

다음은 상기 내용과 나머지를 표로 정리한 것입니다.

### 클라이언트 데이터 타입

특별한 사유가 아니라면 타입명은 `PascalCase` 로 작성하며, 모든 필드는 `camelCase` 로 작성합니다.

| type                 | suffix   | desc.                                                                                                                  | examples                                                             |
| -------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| UI State             | UiState  | View 와 부속 UI 에서만 쓰이는 데이터.<br/>custom hooks 내 local store.<br/>container 나 ui component 내의 local state. | UserLoginUiState<br/>ProductDetailUiState<br/>OrderSearchItemUiState |
| Data Transfer Object | Dto      | 내/외부 전달 용도로써 Key Value Pair 로 구성된 Plain Object.                                                           | OrderCalcResultDto<br/>ImageSizeDto<br/>MatrixInfoDto                |
| UI Parameters        | UiParams | 브라우저를 통해 전달되는 query parameter 를 데이터화 시킨 것.<br/>path parameter 도 포함됨.                            | GoodsSearchUiParams<br/>ProductPageUiParams                          |
| Event Arguments      | Args     | 각종 이벤트 발생 시 각 핸들러에 전달되는 인자값들.                                                                     | ClickEventArgs<br/>InputChangeArgs<br/>TableRowSelectArgs            |
| Function Arguments   | Args     | 함수의 각종 인자들을 하나의 객체로 묶어서 보낼 때                                                                      | DimensionCalcArgs<br/>TableSizeMatcherArgs                           |
| Payload              | Payload  | store action 수행 시 전달되는 데이터.                                                                                  | PageViewPayload<br/>OrderProdSendingPayload                          |
| Store State          | State    | redux store 의 상태를 보관하는 데이터.                                                                                 | MemberDetailState<br/>OrderSearchState                               |
| Component Props      | Props    | UI Component 의 property 가 정의된 타입.<br/>내부 요소는 UI State 을 기반으로 이뤄진다.                                | UserInfoViewProps<br/>OrderSearchTableProps                          |

> ℹ️ 팁
>
> Props 는 컴포넌트 내부에서 선언되고 외부에 공개되지 않는다면, 단순히 `Props` 라는 이름으로 선언하고 사용해도 됩니다.
>
> UI Component 에서 state 활용 시 그 타입이 이미 선언된 UI State 일 경우 따로 만들지 않습니다.
>
> 함수 인자 사용 시 Args 뿐만 아니라 Dto 도 사용 가능합니다.

### 외부 데이터 타입

특별한 사유가 아니라면 외부에서 제공된 그대로의 표기법을 유지합니다.

> 가령 BE API 필드명 규칙이 `snake_case` 라면 이 표기법을 타입에 그대로 반영합니다.

| type                 | suffix   | desc.                                                                                                         | examples                                                                |
| -------------------- | -------- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Entity               | Entity   | BE API 나 외부 third-party API 를 통해 전달된 원본 데이터.<br/>id 나 seq 등 고유값을 가진다.                  | UserAuthLoginEntity<br/>MemberCartListEntity<br/>OrderProductItemEntity |
| Data Transfer Object | Dto      | Key Value Pair 로 구성된 Plain Object.<br/>외부 데이터 중 고유값이 없거나 엔티티로 명명하기 애매한 것들 대상. | MemberAddressDto<br/>ImageMainDto<br/>SingleFieldPayloadDto             |
| Request Parameters   | Params   | API 수행 시 필요한 각종 파라미터.<br/>query parameter 를 객체화 시킨것과 body request 용도를 구분하지 않음.   | UserLoginParams<br/>OrderSearchParams<br/>CoordinateUpdateParams        |
| Response             | Res      | 외부 통신 후 최종적으로 나오는 데이터의 root schema.                                                          | ErrorRes<br/>UserDetailRes                                              |



## 작성 시 주의사항

- type alias 를 제외한 모든 타입 선언들은 반드시 `interface` 로만 선언하여 사용합니다.
- UI State 와 같은 타입들은 언제든 redux 와 같은 Flux-Architecture 의 상태 관리자에서 쓰일 수 있습니다.
  - 따라서 이들 데이터들은 `Props` 같은 특수한 형태나 기타 다른 사유가 없다면 `Plain Object` 로 작성되고 쓰여야 합니다.
- 내부 필드 타입은 같은 Plain Object 나 primitive type 만 가능합니다.
  - 가령 Map 이나 Set, Promise, Error 와 같은 타입은 허용되지 않습니다. (직렬화가 안되기 때문)

이 외 통상적이지 않은 타입 선언에 대해서는 아래 문서를 참고바랍니다.

- [Don't use unusual types](../coding/dont-use-unusual-types.md)

## 규칙의 의의

작성된 타입의 접미어만 보아도 이 데이터의 쓰임새를 알 수 있습니다.

그 쓰임새에 따라 사용되는 곳이 제한되므로 데이터 흐름과 로직에 혼선을 줄일 수 있습니다.

약속된 범위에서만 사용하므로 각 계층별 데이터 관심사가 분리되고 보다 읽기 좋은 코드를 작성하는 바탕이 됩니다.