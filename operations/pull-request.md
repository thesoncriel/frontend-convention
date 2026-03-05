# Pull Request

풀 리퀘스트(이하 PR)는 프로젝트에서 특정 업무를 완료하여 develop 이나 master 브랜치에 작업 내용을 접목시키는 요청을 말합니다.

이는 특수한 경우가 아니라면 Code Review 를 동반하며 동료 개발자들에게 `최소 1명 이상의 approve` 를 받아야 merge 할 수 있습니다.

이하는 PR 작성 요령 및 접근 방법에 대해 설명합니다.

## 요약 영상

아래 영상을 먼저 보고 본 문서를 읽어 나가길 권장합니다.

https://drive.google.com/file/d/1UIj3PBZrLBg3cNPZA3eWGB7bxJqT7kop/view?usp=sharing

> 미리 보기는 화질이 좋지 않으니 다운 받아 보시기 바랍니다.

## 작성 요령

먼저 PR 을 올린자를 작성자(Author), 코드 리뷰 대상자를 리뷰어(Reviewer)로 정의합니다.

작성자는 PR 제목과 본문을 다음 규칙에 따라 작성합니다.

### 제목

제목은 다음과 같은 양식으로 작성합니다.

```
[type]: subject
```

type 에 해당되는 내용은 다음과 같습니다.

참고로 note 에 별도로 언급이 없는 type 은 feature 나 develop 브랜치를 기반으로 이뤄지는 것들 입니다.

| type | desc. | note |
| :--- | :---- | :--- |
| Hotfix | 오류 수정과 그에 따른 긴급 배포 | master -> master |
| Release | 추가된 기능 배포 | develop -> master |
| BackMerge | 핫픽스된 내용을 develop 에 적용 | master -> develop |
| Revert | 적용된 내용을 되돌림 | develop -> develop<br />master -> master |
| Feat | 기능 추가 및 변경 | working -> develop<br />base branch -> develop |
| Sub     | 나눠진 하위 작업.<br />추가 될 기능이 매우 커서 PR을 나눴을 때 쓰임 | working -> base branch |
| Style | eslint 나 업무 규칙에 따른 코드 변경 | |
| Refactor | 각종 리팩터링 | |
| Test | 테스트 코드나 관련 fixture, mocking 변경 | |
| Chore | 버전업이나 개발환경, CI 설정 변경 등 자질구레한 것들 | |
| Docs | 프로젝트에 관련된 문서화 작업들 | |
| Try | 서버에 직접 배포하여 테스트가 필요한 상황들 | approved 없이 진행 가능 <br />테스트가 끝난 뒤 별도 PR 작성 후 리뷰 받아야 함 |

subject 에 해당하는 내용은 가급적 `50자`를 넘기지 않습니다.

제목은 현재 PR의 주요 맥락을 나타내어야 합니다.

가령, commit 에 test 나 chore, feat 가 혼합되어 있어도 해당 PR의 목적과 맥락이 `장바구니 기능 변경` 이라면 `Feat` 가 됩니다.

다만 commit 에 chore 나 test 밖에 없다면 각각 Chore 혹은 Test 로 타입을 지정합니다.


```
-- 작성예시
Feat: 로그인 기능 추가
Hotfix: 회원가입 오류 수정
Release: 장바구니 기능 추가 외 3건
```

### 본문

본문은 아래와 같은 형식으로 작성합니다.

```
## Updates

- 스토어 페이지 내 장바구니 기능 추가
- 상품 안내 문구 변경
- moment.js 를 day.js 로 일괄 변경

## Fixes

- 스토어 목록 내 페이징 레이아웃 수정
- 로그인 api 이슈 해결
- 가입약관의 잘못된 오타 수정

## Others

- 로그인 기능에 대한 e2e test 추가
- react-slick version up to 1.3.0

## Notes

스토어 상세 페이지에 대하여 리팩터링을 진행 하였는데, 이 부분에 대한 피드백을 상세히 부탁드립니다.

## Captures

![이미지](경로)

## Refs

#issue123
#issue345
```

본론의 각 영역은 아래와 같이 3가지가 있으며, 필요한 만큼만 추가하여 기재합니다.

| type | desc. |
| :--- | :---- |
| Updates | 주요 변경사항에 대한 목록. |
| Fixes | 고쳐진 오류나 해결된 이슈 목록. |
| Others | 그 외 언급 하고싶은 목록. |
| Notes | 리뷰어에게 알리고싶은 내용. |
| Captures | test 결과나 만들어진 컴포넌트 및 페이지에 대한 스크린샷 혹은 동영상들 |
| Refs | 본 PR과 관련하여 리뷰어가 참고할만한 issue 나 다른 PR 링크 |

## 변경내용은 500 라인 이하

변경사항이 너무 많다면, 리뷰어 입장에서 리뷰하기 곤란해 질 수 있습니다.

따라서 가급적 500 라인 이하를 권장하며, 이를 넘길 경우 아래의 `base branch` 를 이용합니다.

### Base Branch & Sub Feature Branch

리뷰할 내용이 너무 많을 경우 사용하는 브랜치 입니다. (이하 베이스 브랜치)

그렇지 않은 일반적인 상황에선 사용치 않습니다.

베이스 브랜치명은 `feat/업무내용_base` 와 같이 뒤에 `_base` 를 붙여주어 일반 기능 브랜치와 구별합니다.

운용 방법은 간단합니다.

분할된 여러 PR 을 Sub Feature Branch (이하 서브 브랜치)로 명시하고, 이들의 merge 대상을 이 곳 베이스 브랜치로 잡으시면 됩니다.

예를 들어 `상품 태그 기능 추가` 가 있고, 아래와 같은 PR 이 만들어진다 가정 하겠습니다.

- Feat: 상품 태그 - 표현 컴포넌트 추가
  - `feat/prod_tag_211010a`
- Feat: 상품 태그 - 스토어 작성
  - `feat/prod_tag_211010b`
- Refactor: 상품 태그 - 기존 상품태그 로직 기능 정리
  - `feat/prod_tag_211010c`

이들을 베이스 브랜치에 쓰기 위해선 `PR 제목`만 아래와 같이 고쳐서 사용합니다.

- Sub: 상품 태그 - 표현 컴포넌트 추가
- Sub: 상품 태그 - 스토어 작성
- Sub: 상품 태그 - 기존 상품태그 로직 기능 정리

즉 PR 제목 타입만 `Sub: ` 로 바꿔주면 됩니다.

이들에 대한 베이스 브랜치가 `feat/prod_tag_base` 로 선언되어 있다면, develop 브랜치가 아닌 `feat/prod_tag_base` 를 merge 대상으로 삼습니다.

#### PR 올리는 순서

각 서브 브랜치들은 그 순서에 유의하여 PR을 올려야 합니다.

애당초 서브 PR의 목적은 `작업된 라인수를 목적별로 쪼개어 리뷰어가 보기 편하게 하기 위함` 이기 때문입니다.

PR을 올릴 순서가 상기 예시의 것과 같다는 가정하에 이들 순서를 지키는 방법은 다음을 참고 합니다.

첫 시작은 아래와 같이 `feat/prod_tag_base` 를 대상으로 PR을 올립니다.
```
|  =PR= Sub: 상품 태그 - 표현 컴포넌트 추가
|  |
|  -- feat/prod_tag_211010a
| /
-- feat/prod_tag_base
|  develop
```

다음은 `feat/prod_tag_211010a` 를 대상으로 PR을 올립니다.

```
|
|  =PR= Sub: 상품 태그 - 스토어 작성
|  |
|  -- feat/prod_tag_211010b
|  |
|  =PR= Sub: 상품 태그 - 표현 컴포넌트 추가
|  |
|  -- feat/prod_tag_211010a
| /
-- feat/prod_tag_base
|  develop
```

다음은 `feat/prod_tag_211010b` 를 대상으로 PR을 올립니다.

```
|
|  =PR= Sub: 상품 태그 - 기존 상품태그 로직 기능 정리
|  |
|  -- feat/prod_tag_211010c
|  |
|  =PR= Sub: 상품 태그 - 스토어 작성
|  |
|  -- feat/prod_tag_211010b
|  |
|  =PR= Sub: 상품 태그 - 표현 컴포넌트 추가
|  |
|  -- feat/prod_tag_211010a
| /
-- feat/prod_tag_base
|  develop
```

이들 PR이 merge 된 모습은 다음과 같습니다.

```
|
|  -- [merged] feat/prod_tag_211010c - Sub: 상품 태그 - 기존 상품태그 로직 기능 정리
| /|
|  |  -- [merged] feat/prod_tag_211010b - Sub: 상품 태그 - 스토어 작성
|  | /| 
|  |  |  -- [merged] feat/prod_tag_211010a - Sub: 상품 태그 - 표현 컴포넌트 추가
|  |  | /|
|  |  |  |
|  |  | /
|  | /
| /
-- feat/prod_tag_base
|  develop
```

여기서 가장 윗쪽에서 merge 된 브랜치인 `feat/prod_tag_211010c` 에 주목하십시요.

실제 작업된 모든 내용이 포함된 브랜치는 `feat/prod_tag_211010c` 이기 때문입니다.

모든 PR 과 merge 가 완료 되었다면 이제 베이스 브랜치인 `feat/prod_tag_base` 를 `feat/prod_tag_211010c` 에 `rebase` 하십시요.

rebase 가 완료되면 다음과 같은 모습이 될 것입니다.

```
|
|  -- feat/prod_tag_base
|  |  [merged] feat/prod_tag_211010c - Sub: 상품 태그 - 기존 상품태그 로직 기능 정리
| /|
|  |  -- [merged] feat/prod_tag_211010b - Sub: 상품 태그 - 스토어 작성
|  | /| 
|  |  |  -- [merged] feat/prod_tag_211010a - Sub: 상품 태그 - 표현 컴포넌트 추가
|  |  | /|
|  |  |  |
|  |  | /
|  | /
| /
-- develop
```


#### PR 올리기

이 후 `feat/prod_tag_base` 에 상기 PR이 모두 merge 가 되면 아래와 같은 PR을 올립니다.

```
--제목
Feat: 상품 태그 기능 추가

--본문
## Refs

#PR123
#PR345
#PR678
```

상기와 같이 현재 베이스 브랜치에 merge 된 모든 PR들을 `Refs` 섹션에 링크로 추가해줍니다.

#### See also

아래 브랜치 전략의 `Base Branch` 와 `Sub Branch` 도 함께 참고 하시기 바랍니다.

- [Git: Branch Strategy](./git-branch-strategy.md)


## 예외사항

approve 를 받지 아니하고 곧바로 merge 가 가능한 상황은 다음과 같습니다.

1. 리뷰어가 모두 부재중이고 당장 배포가 필요한 상황
  - 슬랙 채팅방에 간단하게 사유를 알려주고 이 후 별도로 PR을 올려 리뷰를 받습니다.
2. 문제 확인과 해결을 위해 부득이하게 운영이나 스테이지 배포를 짧은 시간에 여러번 해야 할 경우
  - 문제가 해결되면 원인과 해결방법이 기재된 PR을 별도로 올려서 리뷰를 받습니다.

## 규칙의 의의

PR 제목만으로도 어떤 작업의도를 가지는지 파악이 가능합니다.

PR 본문의 포멧은 그 내용 그대로 릴리즈 노트등에 복붙하여 재활용 가능합니다.

짧은 변경사항은 리뷰어의 부담을 줄여주며 좀 더 심도있는 코드 리뷰가 가능해집니다.

베이스 브랜치는 많은 변경사항에 대한 코드 리뷰 시 도움이 되어 줍니다.