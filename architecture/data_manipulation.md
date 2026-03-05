# Architecture: Data Manipulation

본 문서는 아키텍처 운용 방법 중 `데이터 조작기` 에 대하여 설명합니다.

## 개요

클라이언트 내부에서는 주로 `UI State` 에 의존하여 업무 로직이 형성됩니다.

그러나 출처가 같음에도 다른 목적으로 쓰이는 경우가 종종있습니다.

가령 특정 라우트(route)를 통해 진입한 페이지 A 와 B 가 있다고 가정했을 때,
이들에 쓰이는 query parameter 가 동일하지만, 내부적으로 다르게 쓰이는 경우입니다.

또한 사용자 입력 데이터에 대해서 적절히 정제(refinement)해야 할 경우도 생기므로
업무에 응용 시 적절히 변환하여 사용해야합니다.

## 함수 작성 방법

데이터 조작은 크게 생성(creation)과 변환(transformation), 정제(refinement) 3가지로 나뉘어집니다.

이들은 특별한 사유가 없다면 순수함수(pure function)로 작성 되어야 합니다.

데이터 조작기 함수명 예시는 다음과 같습니다.

| prefix                         | desc.                                                                                             | examples                                                                         |
| ------------------------------ | ------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| create                         | UI State 혹은 Dto 와 같은 데이터 생성 팩토리.                                                     | createMemberInfoUiState<br/>createProductDetailDto<br/>createCoordiUpdatePayload |
| convert<br/>convertAToB<br/>to | 자료 1:1 변환기.                                                                                  | convertToViewUiState<br/>toOrderUiState<br/>toUpdateParams                       |
| combine<br/>combineAs          | 2개 이상의 데이터를 1개의 데이터로 변환.                                                          | combineAsStoreUiState<br/>combineUserAndPartnerAsCompanyParams                   |
| refine                         | 1개 데이터에서 의도된 범위로 각 필드값을 조정함.<br/>데이터 내 필드가 일관성을 유지하도록 변경함. | refinePageUiParams<br/>refineUserInputUiState                                    |

이 외에도 데이터를 조작하는 의미를 가지는 함수명칭이라면 사용해도 좋습니다.

> ℹ️ 팁
> 
> refine 은 가능한한 UI State 나 DTO 같은 구조체(struct) 나 Record 와 같은 `Plain Object` 타입에서만 사용합니다.

## 파일 위치와 명칭

특별한 사유가 없는 한, 조작기 함수의 위치는 각 모듈 내 `manipulates` 폴더에 두어야 합니다.

또한 이들 파일명은 다음과 같은 규칙을 갖습니다.

| name pattern                  | desc.                        | examples                                       |
| ----------------------------- | ---------------------------- | ---------------------------------------------- |
| {featureOrSubName}.create.ts  | 데이터 생성 함수 모음.       | orderProduct.create.ts                         |
| {featureOrSubName}.convert.ts | 데이터 변환, 정제 함수 모음. | storeInfo.convert.ts<br/>userDetail.convert.ts |

## 코드 예시

아래는 UI State 를 생성하는 예시입니다.

```ts
// manipulates/pomeLover.create.ts
import { PomeLoverSearchUiState, MbtiEnum } from '../uiStates';

export function createPomeLoverSearchUiState() {
  const result: PomeLoverSearchUiState = {
    id: 0,
    name: '',
    location: '',
    mbti: MbtiEnum.ENTP,
  };

  return result;
}
```

아래는 UI Params 를 변경하는 예시입니다.

```ts
// manipulates/pomeLover.convert.ts
import { refinePage } from '@common/utils';
import { PomeLoverSearchUiParams, MbtiEnum } from '../uiStates';
import { LOOKPIN_LOVER_PAGE_SIZE_LIST } from '../constants';

const refineSize = createPageSizeRefiner(LOOKPIN_LOVER_PAGE_SIZE_LIST);

export function refinePomeLoverSearchUiParams(raw: Record<string, string>) {
  const result: PomeLoverSearchUiParams = {
    page: refinePage(raw.page, 1),
    size: refineSize(raw.size),
    keyword: raw.keyword || '',
  };

  return result;
}
```

## 사용처 제한

데이터 생성(creation) 함수는 필요한 곳 어디서나 쓰일 수 있습니다.

단, 표현 컴포넌트에서는 가급적 스스로 만들지 말고 props 에 의존하도록 구성하는 것을 권장합니다.

또한 현재 모듈을 벗어난 곳에서는 import 하는 것을 금지합니다.

## 참고

데이터 조작 및 생성은 이곳에서만 이뤄지지 않습니다.

필요에따라 `Repository` 에서도 운영됩니다.

자세한 건 [Feature Repository](./feature_repository.md) 문서를 참고 바랍니다.