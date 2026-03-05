# Git Operations: Commit Conventions

본 문서는 깃 커밋 메시지에 대한 규칙을 설명합니다.

기본적으로 [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) 내용을 기반으로 운용합니다.

전체적인 구조는 다음과 같습니다.

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Section 1 - Subject (Required)

제목은 커밋에 대한 간략한 정보를 남기는 곳으로서 `50자`를 넘기지 않습니다.

만약 넘기게 된다면 아래 `Body`를 참고하여 대신 적어두고 제목은 간결하게 바꿔줍니다.

제목 부분은 다음과 같은 규칙을 따릅니다.

```
<type>[optional scope]: <description>
```

type 에 대응되는 내용은 다음과 같습니다.

| type     | description                                                                                                                           |
| :------- | :------------------------------------------------------------------------------------------------------------------------------------ |
| feat     | 기능 추가 및 변경.<br/>단순히 사용자 안내문구를 바꾸거나 css 스타일링만 살짝 바꾸는 것도 해당됨.                                      |
| fix      | 버그 수정. 잘못된 기능을 고쳤을 때 해당됨.                                                                                            |
| refactor | 기존 기능 리팩터링                                                                                                                    |
| test     | 각종 테스트.<br/>unit(vitest or jest), visual(storybook), e2e(cypress) 에 해당됨.                                                     |
| style    | eslint 나 convention 에 따른 코딩 스타일 변경<br />🙅‍♂️ 주의! - css 변경건을 말하는게 아님!                                       |
| docs     | README.md 나 라이브러리 및 코드, 현재 프로젝트에 대한 각종 문서화 작업들.<br/>주석(comment)만 추가하거나 바꿀 경우도 해당함.          |
| chore    | 기타 잡다한 변경들.<br />버전업, 패키지 추가/변경, 환경설정 변경, ci 수정, snippets 추가, 테스트에 사용되는 fixture 나 mocking 자료등 |

### Examples

```
feat: 로그인 화면에 email 유효성 검증 기능 추가

// event feature module 에 한하여 적용된 예시
style(event): 이벤트 템플릿에 대한 eslint 적용

fix: 회원 가입 시 발생된 토큰 오류 수정

refactor: 상품 검색 목록의 중복된 코드 제거

docs: README.md 의 빌드 내용 보완

test: 주문서에 cypress 를 이용한 e2e 테스트 코드 추가

chore: package.json 내 버전업

chore: tsconfig 및 eslint 내용 업데이트

chore: 주문상품조회 테스트용 fixture 추가
```

## Section 2 - Body

**선택사항(Optional)** 입니다.

커밋 메시지가 많을 경우 부가적인 설명을 위해 적습니다.

자유롭게 적되 내용이 너무 많아질 경우 PR에서 별도로 설명하거나 commit 을 나눠서 가급적 메시지를 간결히 유지하도록 합니다.

## Section 3 - Footer

**선택사항(Optional)** 입니다.

기본적으로 연관되어 참고 할 이슈 ID나 참고할 링크를 넣는 공간 입니다.

가령, 오류가나 hotfix 를 했는데, 문제가 발생된 이슈ID를 알고 있어 이를 참고하고자 할 때 추가 할 수 있습니다. (어디까지나 선택사항)

아래는 작성 가능한 타입 입니다.

- Refs: 현재 커밋과 연관된 다른 이슈 ID.
- See also: 참고할 문서나 외부 PR 등 추가적으로 언급하고 싶은 링크를 달아둡니다.
- BREAKING CHANGE: 주요 변경 사항. 다른 리뷰어가 눈에 띄게하여 확인해야 할 사항을 적습니다. 태깅 명칭은 `대문자`로 둡니다.

## 전체 작성 예시

아래는 제목과 바디, 푸터가 함께 들어간 예시 입니다.

다소 과장된 내용이 있으므로 어디까지나 작성 방법에 대해 참고만 바랍니다.

```
// 제목
feat: 회원탈퇴 기능 추가

// 본문 - 문장 예시
서비스를 개발 하였으나 회원탈퇴가 기존엔 불가하여 추가하게 된 기능 입니다.

// 본문 - 목록 예시
- 회원탈퇴 사유를 받는 페이지 개발
- 해당 페이지의 라우팅 적용
- 전용 non-standard 컴포넌트 추가

// 푸터
BREAKING CHANGE: 회원탈퇴가 기존 ruby 기반으로 된 기능에 이슈가 있어 react 기반으로 다시 수행되도록 하였습니다. 기존 ruby 기반의 페이지는 더이상 동작이 되지 않기에 redirect 처리 해 두었습니다. <-- 눈에 띄게 강조하고 싶은 변경사항.
Refs: #456 <- 메인 이슈 ID.
See also: #6678, #1123 <- 추가적으로 달아준 이슈 ID.
```