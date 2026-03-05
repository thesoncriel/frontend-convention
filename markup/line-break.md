# Markup - Line Break

문서를 다루는 HTML 에서 줄바꿈(Line Break)은 흔히 발생되는 일입니다.

웹 프론트에서 줄바꿈 기능은 아래와 같이 크게 3가지로 나누어 렌더링 가능합니다.

- `<br>` 태그 사용
- `<pre>` 태그 내에서 줄바꿈 특수기호 (\n) 사용
- `css white-space: pre, pre-line, pre-wrap` 이 적용된 요소 내에서 줄바꿈 특수기호 (\n) 사용

## 일반적인 마크업

기본적으론 `<br>` 태그를 이용하나 필요에 따라 `<pre>`를 함께 사용할 수 있습니다.

```html
<p>
  줄바꿈 하고 싶어요!<br/>
  줄바꿈 했어요~!
</p>

<!--
  pre 에서는 공백이 그대로 출력됩니다.
  그러므로 불필요한 공백과 줄바꿈은 임의로 지워야 합니다.
-->
<pre>줄바꿈 하고 싶어요!
줄바꿈 했어요~!</pre>
```

## 메시지 출력

modal 이나 tooltip, toast (혹은 snackbar) 계통의 메시지 알림은 이미 만들어진 UI 컴포넌트나 서비스를 활용하는 경우가 많습니다.

이때는 react 기준, component instance 인 jsx 를 쓰지 않고 `\n` 를 사용합니다.

```tsx
// oops!
feedbackService.toast(
  <>
    장바구니에 상품이 담겼습니다.<br />
    장바구니를 확인 해 주세요!
  </>
);

// good!
feedbackService.toast(
  '장바구니에 상품이 담겼습니다.\n장바구니를 확인 해 주세요!'
);
```

```tsx
// 예시 컴포넌트의 props type 입니다.
interface Props {
  condition?: boolean;
}

// oops!
function Client({ condition }: Props) {
  const message = condition ? (
    <>
      하나면 하나지 둘이겠느냐<br />
      둘이면 둘이지 셋이겠느냐
    </>
  ) : (
    <>
      사뿐히 즈려밟고<br />
      가시옵소서~
    </>
  );

  return (
    <Modal content={message} />
  );
};

// good!!
const MSG_CONDITION_YES = '하나면 하나지 둘이겠느냐\n둘이면 둘이지 셋이겠느냐';
const MSG_CONDITION_NO = '사뿐히 즈려밟고\n가시옵소서~';

function Client({ condition }: Props) {
  const message = condition ? MSG_CONDITION_YES : MSG_CONDITION_NO;

  return (
    <Modal content={message} />
  );
};
```

## 엔티티 기반 텍스트

BE API나 정적 json 데이터 컨텐츠에 각종 줄바꿈(\n)이 있을 경우, 이를 임의로 `<br />` 로 바꾸지 않습니다.

대신, 줄바꿈이 적용될 요소에 `<pre>` 태그를 사용합니다.

만약 `word wrap`이 함께 필요하다면, `css white-space: pre-line, pre-wrap` 을 활용합니다.

```tsx
// 아래와 같은 데이터가 온다고 가정합니다.
const entity = {
  id: '1234',
  author: 'theson',
  group: 'pome',
  contents: '하나면 하나지 둘이겠느냐\n둘이면 둘이지 셋이겠느냐',
};

// 위 데이터를 사용하는 예제 입니다.
function App() {
  return (
    <Client text={entity.contents} />
  );
}

// 예시 컴포넌트의 props type 입니다.
interface Props {
  text: string;
}

// wrong...
function Client({ text }: Props) {
  return (
    <p>
      {text.split('\n').map((str, index) => (
        index === 0
          ? str
          : (
            <>
              <br/>
              {str}
            </>
          )
      ))}
    </p>
  );
};

// good !
function Client({ text }: Props) {
  return (
    <pre>
      {text}
    </pre>
  );
};

// good too !
const WordWrappedText = styled.p`
  white-space: pre-wrap;
`;
function Client({ text }: Props) {
  return (
    <WordWrappedText>
      {text}
    </WordWrappedText>
  );
};

```

> Word Wrap 이란?
>
> 문장이 길어지면, 그 문장이 소속된 요소 내에서 자동 줄바꿈되는 기능을 말합니다.

## 규칙의 의의

단순 줄바꿈에 jsx 를 남용하는 것을 지양하고 web browser 의 기본 기능을 활용함에 따라 코드량이 줄고 퍼포먼스 향상을 기대할 수 있습니다.

줄바꿈 특수기호를 사용함으로써 메시지나 단순 텍스트들을 react instance 가 아닌 `pure string` 으로만 관리할 수 있습니다.

이는 별도 constant 분리, storage 활용, json 비동기 처리, 혹은 추 후 BE API 전환등 필요에 따라 다양한 분리가 가능합니다.

최종적으로 텍스트 메시지와 코드의 분리를 가능케 합니다.

## 참고

- [white-space (MDN)](https://developer.mozilla.org/ko/docs/Web/CSS/white-space)
