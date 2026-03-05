# Coding: Don't use IIFE

IIFE 는 `Immediately Invoked Function Expression` 의 약자로서 `즉시실행 함수` 라는 의미 입니다.

본 문서는 즉시실행 함수 작성에 대한 제약과 그 이유, 대체 가능한 방법을 제시합니다.

## 즉시실행 함수는 지양합니다.

특별한 사유가 없다면 아래와 같은 즉시 실행 함수를 작성 하지 않습니다.

```ts
// before
function calcProductAmount(args: ProductChangeArgs) {
  const amount = (() => {
    const {
      price,
      quantity,
    } = args;
    
    return price * quantity;
  })();

  const taxValue = (function(amt: number) {
    if (amt > 0) {
      return 0;
    }

    return amt * 0.1;
  })(amount);

  return {
    ...args,
    amount,
    taxValue,
  };
}
```

위와 같은 내용은 다음과 같이 작성 합니다.

```ts
// after
function calcAmount(price: number, quantity: number) {
  return price * quantity;
}

function calcTax(amt: number) {
  if (amt > 0) {
    return 0;
  }

  return amt * 0.1;
}

function calcProductAmount(args: ProductChangeArgs) {
  const amount = calcAmount(args.price, args.quantity);
  const taxValue = calcTax(amount);

  return {
    ...args,
    amount,
    taxValue,
  };
}
```

> 상기 예제는 지면상 짧은 내용을 사용 하였습니다.
>
> 실제 업무 적용 시 저렇게 과도하게 짧은 함수를 여럿 만들 필요는 없습니다. 😅

### 과거엔 왜 이렇게 썼나요?

본디 JS는 global 과 local scope, 2개의 영역밖에 존재하지 않았습니다.

그래서 IIFE 가 고안되었고 다음과 같은 상황에 쓰였습니다.

- module pattern
  - 쓰이는 코드 내용들을 특정 영역(scope)에 가둬둠으로써 global 기능과 분리하고 격리하기 위함입니다.
- 보안성 강화
  - 정해진 스코프 내의 변수/함수는 외부로 return 하지 않는 한 절대 접근이 불가 합니다.

요약하자면 보안성 강화 & 영역 분리가 있겠습니다.

~~근데 가장 큰 이유는 그냥 함수 따로 빼기 귀찮아서 이렇게 하는 경우가 많았습니다...~~

### 지금은 왜 사용하면 안되나요?

ES6가 나오면서 `block scope` 가 생겼습니다.

그래서 특정 함수 내 별도의 영역 구분이 필요하다면 굳이 IIFE를 쓰지 않아도 됩니다.

그리고 IIFE는 webpack 으로 번들링 할 때 tree-shaking 대상이 되지 않거나 예기치 못한 side-effect 가 발생될 수 있습니다.

그리고 단순히 연산의 영역을 구분하기 위함이라면 `별도로 함수를 만들어 쓰기`를 권장 합니다.

왜냐하면 IIFE 사용시, 매번 함수 수행 될 때마다 재생성 & 실행 되기 때문에 이미 만들어진 함수를 사용하는 것 보다 상대적으로 overhead가 크기 때문입니다.

## 변경 예시 더보기

아래는 IIFE를 권장 하는 방법으로 바꾼 예시 입니다. 😄

```ts
// function example
// ----------------------------

// wrong
function doAnything(data: AnythingDto[]) {
  const result = (function () {
    const otherResult = data.filter(item => item.hasAnything);

    // inner logics..
    
    return otherResult;
  })();

  // outer logics..

  return result;
}

// ----------------------------

// good!!
// 사용하기 전에 먼저 선언합니다.
function filterByHasAnything(data: AnythingDto[]) {
  const otherResult = data.filter(item => item.hasAnything);

  // inner logics..
    
  return otherResult;
}
// 사용부
function doAnything(data: AnythingDto[]) {
  const result = filterByHasAnything(data);

  // outer logics..

  return result;
}
```

```ts
// class example
// ----------------------------

// wrong
class SomethingService {
  async run() {
    const result = ((res: ServerResponse) => {
      const otherResult = res.data.filter(item => item.hasLog);

      // inner logics..

      return otherResult;
    })(await repo.someDomain.fetchResponse());

    // outer logics..

    return result;
  }
  doSomething() {
    // blah blah..
  }
}

// ----------------------------

// good!!
class SomethingService {
  // 클래스와 관련된 함수고 여기서 밖에 쓰이지 않는다면 member method 로 구현하여 사용합니다.
  private manipulate(res: ServerResponse) {
    const otherResult = res.data.filter(item => item.hasLog);

    // inner logics..

    return otherResult;
  }
  async run() {
    // 단순히 가져와서 사용하는 형식으로 바꿔줍니다.
    const res = await repo.someDomain.fetchResponse();
    const result = this.manipulate(res);

    // outer logics..

    return result;
  }
  doSomething() {
    // blah blah..
  }
}
```

```tsx
// component example
// ----------------------------

// wrong
function CafeReviewerList({ items }: Props) {
  const data = useMemo(() => {
    const result = (() => {
      const otherResult = items.filter(item => item.isAvailable);

      // inner logics..

      return otherResult;
    })();

    // outer logics..

    return result;
  }, [items]);

  return data
    .map(item => (
      <CafeReviewerListItem key={item.id} item={item} />
    ));
};

// ----------------------------

// good!!
function filterByAvailable(items: CafeReviewerUiState[]) {
  const otherResult = items.filter(item => item.isAvailable);

  // inner logics..

  return otherResult;
}

function outerLogics(items: CafeReviewerUiState[]) {
  const result = filterByAvailable(items);
  // outer logics..

  return result;
}

function CafeReviewerList({ items }: Props) {
  const data = useMemo(() => {
    // 복잡한 데이터 조작 로직은 함수로 별도 분리하여 사용합니다.
    return outerLogics(items);
  }, [items]);

  return data
    .map(item => (
      <CafeReviewerListItem key={item.id} item={item} />
    ));
};

// ----------------------------

// best!!
// 특별한 사유가 없다면 애초에 이미 조작이 끝난 데이터만 전달합니다.
function CafeReviewerList({ items }: Props) {
  return items
    .map(item => (
      <CafeReviewerListItem key={item.id} item={item} />
    ));
};
```

### 규칙의 의의

내부 로직을 별도 함수로 구성함으로써 자연스레 코드 분리가 됩니다.

그리고 필요시 같은 내용에 대한 `재사용성 증가`가 이뤄질 수 있습니다.

내부 함수의 오버헤드를 줄이고 간결한 코드를 유지할 수 있습니다.

### 참고

- [Webpack Tree shaking](https://programmersought.com/article/29911737900/)