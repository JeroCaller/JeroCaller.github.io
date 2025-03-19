---
title: "커밋 메시지 작성 규칙"
category: "Git & Github"
tag: ["Git", "Github", "Commit", "Git Commit Message Style Guide"]
---

커밋 메시지를 작성할 때에도 일정한 규칙을 따르게 하면 체계적인 커밋 메시지 작성으로 이후 커밋 이력 추적 및 검색에 용이할 것이다. 또한 팀 협업 시에도 다른 이의 커밋 메시지를 통해 구체적으로 어떠한 것들을 커밋했는지 한 눈에 파악하기 쉬울 것이기에 협업에서도 간과해설 안될 중요한 요소 중 하나일 것이다. 

사실 커밋 메시지 스타일 가이드에 대해선 통일되어 있거나 모두가 반드시 지켜야 할 어떤 표준안 같은 게 있는 건 아니고, 개별 또는 각 회사에서 커밋 메시지에 대한 일관적, 체계적 규칙만 있으면 각자 원하는 스타일로 정할 수 있다고 한다. 한 편 조사 결과 [udacity style](https://udacity.github.io/git-styleguide/)이 가장 잘 알려져 있다고 한다. 그리고 조사해보니 다들 이 스타일을 기반으로 소개를 하고 있었고, 그렇지만 세부적으로는 각자 다른 규칙을 사용하는 점도 있었다. 이 글에서는 udacity style을 기반으로 하되, 다른 블로거 분들의 글들에서 별도로 언급하는 부분들도 종합하여 정리할 것이다.

# udacity style 기반 커밋 메시지 규칙

udacity에 대해선 잘 모르지만, 영어권에서의 IT 관련 온라인 강의 사이트인 것 같았다. 그리고 udacity style 공식 문서의 “Introduction” 내용을 미루어 보아 추측컨대, 이 커밋 스타일은 과제로 프로젝트를 해야하는 학생들에게 커밋 메시지를 어떻게 작성해야하는지 그 표준을 제시하기 위해 만들어진 것으로 추측된다. 그런데 이게 어쩌다보니 유명해져서 udacity와 전혀 관련없는 사람들도 이 규칙을 따르거나 참고하고 있어 사실상 표준처럼 사용되고 있는 것으로 보인다. 이 부분에 대해서는 필자도 그 깊은 내용까진 모르니 udacity에 대해 궁금하다면 직접 검색해보는 걸 추천한다. 

한 편 udacity style이 작성된 공식 사이트에서는 그 내용이 모두 영어를 기준으로 작성되어 있기에 일부는 우리나라와 그 스타일이 잘 맞지 않거나 어색한 부분이 있을 수도 있으니 이 점 참고하길 바란다. 

## 커밋 메시지 구조

커밋 메시지 구조는 크게 제목, 본문, 꼬리말 이 세 개로 구성된다. 

```
type: Subject

body

footer
```

예시 1-1. udacity style 문서 참고함. 

여기서 body, footer는 생략 가능하며, 각 부분은 한 줄의 개행을 통해 서로 구분할 수 있어야 한다. 한 편 제목(type, subject) 부분은 필수로 작성해야 한다. 

## Type

해당 커밋이 어떤 종류의 것인지 명시하기 위해 쓰인다. udacity style에선 다음의 타입들을 소개하고 있다. 

- feat : 해당 커밋이 새로운 기능 개발일 때 사용. (udacity 공식 문서에서는 딱 이러한 의미의 설명만 적혀있다)
    - 다른 블로거 분들의 설명을 첨언하자면, 요구 사항의 변경으로 기존 기능을 수정하게 될 때에도 여기에 포함된다고 한다.
- fix : 버그를 고친 코드일 때
- docs : 문서에 변화를 준 경우
- style : 포맷팅, 또는 세미콜론(;)을 놓친 것에 대한 보완 등 코드 작성 스타일에 변화가 있을 때. 코드 자체에 변화를 주는 리팩토링 등의 개념과는 다르다.
- refactor : 리팩토링 시. 여기서 리팩토링이란 기능의 변화 없이 코드를 개선시킨 것을 말한다. 성능이나 구조 개선이 여기에 해당한다.
- test : 테스트 추가 또는 리팩토링 시 사용. 실제 출시될 소프트웨어의 코드 변화는 여기에 해당되지 않는다.
- chore : 배포, 빌드 등 프로젝트의 기타 작업들에 대해 추가 또는 수정 시 사용. 방금 위에서 소개한 새 기능 및 테스트 코드, 문서, 스타일, 리팩토링과는 다른 기타 작업일 경우 여기에 해당됨.
    - 혹자는 코드 스타일의 변경도 여기에 포함된다고 하는데, 필자 개인적인 생각으론 이건 style에 가깝지 않나, 라는 생각이 든다.

여기까지는 udacity style 공식 문서에 나온 타입들이었다. 이 외에도 다른 블로거 분들이 소개하는 추가 타입들도 있었다. 

- feat의 경우, 새 기능 추가와 기존 기능 수정 이 두 가지가 포함되어 있는데, 상황에 따라서 이 두 가지를 분리하고자 한다면 feat 대신 new와 improve를 사용할 수 있다고 한다. 물론 이 경우는 선택 사항이니 feat 하나만 쓸지, 아니면 new와 improve로 나눠 쓸지는 각자 판단할 사항이다. (그런데 실제로는 보통 feat를 많이 쓰는 반면, new와 improve를 쓰는 건 아직 본 적이 없긴 하다)
    - new : 새 기능 추가 시
    - improve : 기존 기능 수정 시
- release : 제품 버전 출시를 위한 타입. 제품 출시를 위해 패키지 버전을 올리거나 릴리즈 버전 커밋을 찍고자 하는 경우.
- ci : CI 관련 설정. (CI/CD 할 때의 그 CI인 것 같다. 이 타입을 사용하는 경우 chore와 구분해서 샤용해야 할 것 같다)
- build : 빌드 관련 수정. (이 역시도 사용한다면 chore와 구분해서 사용해야 할 것이다)
    - ci, build의 경우도 각 팀에 따라 chore로 포함시켜 사용할지, 아니면 ci, build로 나눠 사용할지는 각자 자율적으로 판단해도 될 것 같다.
- perf: 성능 개선 시.

참고로 위에 소개한 모든 타입들을 다 사용해야 하는 건 아니다. 각 팀마다 자율적으로 판단하여 어떤 타입들을 사용할 것인지 정할 수 있다. 다만 보통 udacity 공식 문서에서 언급하는 fix, feat, docs, style, refactor, chore 등은 일반적으로 사용한다고 한다. 

## Subject(제목)

앞서 보인 예시 1-1에서 볼 수 있듯, udacity style에서는 제목 부분은 `type: subject` 구조로 타입과 제목만을 입력하도록 제시되어 있다. 하지만 다른 블로거 분들의 글에 따르면, 여기에 정보를 더 추가하여 사용할 수도 있다고 한다. 첫 번째 방식은 `type: [#issueNumber] - subject` 형식으로, Github 제공 issue 등 이슈 트래킹과 함께 사용하는 경우 해당 커밋과 관련 있는 이슈 번호를 제목 앞에 작성할 수 있다고 한다. 물론 이는 불필요하면 생략해도 된다고 한다. `feat: #123 - 로그인 기능 추가` 이런 식으로 사용할 수 있다. 두 번째 방식은 `type(scope) : subject` 형식으로, scope에는 해당 커밋이 다루고 있는 “범위”를 언급하면 된다. 주로 커밋이 다루고 있는 파일명을 작성하는 것 같다. 파일명 작성 시에는 App.js와 같이 확장자도 같이 작성하는 게 일반적이라고 한다. `feat(Login.jsx) : 로그인 기능 추가` 이런 식으로 사용 가능하다. scope도 생략 가능하다. 

이 두 방식은 원하는 방식으로 골라 사용해도 될 것 같다. 또는 두 방식 모두 혼합하여 사용해도 될 것 같다. scope와 이슈 ID 번호를 작성하는 위치가 달라 겹치는 곳이 없기 때문이다. 이것도 사실 각자 팀이나 프로젝트에서 알아서 판단할 사항이지 않을까 싶다. 

이 외에, udacity 공식 문서에서 언급하는 내용에는 다음이 있다. 

- 제목은 필수이다.
- 제목 길이는 50자를 넘어선 안된다.
    - 한 줄의 길이가 너무 길면 모니터 상에서 글이 잘려서 보이기 때문에 생긴 규칙으로 보인다. 혹자는 70자까지도 괜찮을 거라 한다.
- 제목 첫 글자는 대문자로 시작해야 한다.
    - 그런데 이건 영어를 전제로 한 이야기라, 한국어로 진행하는 경우에는 해당되지 않는 사항이다.
- 문장 맨 마지막에 마침표는 넣지 않는다.
- 명령어로 사용한다.
    - 그런데 여기서의 명령어는 역시 영어에 해당되는 사항인데, 예를 들어 “feat: Added Login feature”와 같이 과거형을 사용 하지 말고 “feat: Add login feature”에서의 Add처럼 동사원형을 사용하라는 의미이다.
    - 한국어의 경우에는 명사적 구조로 작성하면 될 것 같다. “feat: 로그인 기능 추가” 또는 “feat: 로그인 기능 추가함”과 같이 말이다. 개인적으로는 전자가 더 나은 것 같다.
- 제목은 타입과 같이 명시한다. 이건 앞서 소개한 타입 및 예시 1-1을 참고하면 된다.

이 외에 다른 블로거 분들이 소개하는 제목에 대한 추가적인 정보들은 다음과 같다.

- 이슈 번호를 넣을 때, 단 하나의 이슈만 연관된 게 아니라 여러 개의 이슈들이 연관되어 있는 경우에는 제목에 이슈 번호를 달지 않는 것이 좋다. 대신 연관된 여러 개의 이슈 번호들은 footer에 쓰는 것이 좋다. footer에서의 이슈 번호 작성 법은 아래의 Footer 챕터에서 후술할 예정.
- 제목만으로도 커밋의 내용을 한 번에 설명 가능하도록 작성하는 것을 추천.

## Body (본문)

모든 커밋이 본문을 작성해야 할 정도로 복잡한 건 아니기에 본문 작성은 선택 사항이다. 즉 불필요하다면 생략해도 된다. 반면 본문을 작성할 것이라면 해당 커밋이 어떤 일(how)을 하는지를 설명하기보다는 무엇(what)을, 그리고 왜(why) 그런 행위를 하는지 설명하는 것이 좋다. 커밋이 어떤 일을 하는지는 그 코드가 자체적으로 설명하고 있기 때문이다. 

또한 한 줄에 72자를 넘어선 안되며, 제목으로부터 한 줄 개행한 다음에 작성함으로써 제목과 본문을 구분하여 가독성을 확보해야 한다. 

또한 필요하다면 bullet 기호(-)를 이용하여 목록과 같은 형식으로 나열하여 설명해도 된다. 다음처럼 말이다.

```
Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here
```

예시 2-1. udacity 공식 문서에서 제시하는 body에서의 bullet 사용 예시.

bullet 사용 시 다음 문단과 한 줄 개행 간격을 유지하고, bullet 기호 다음에 한 칸 띄어서 작성하는 걸 추천하고 있다. 이것도 가독성을 고려해서 나온 규칙인 것 같다. 

해당 커밋으로 인해 예상되는 side effect나 일어나지도 모를 어떤 문제가 발생할 것으로 예상되면 이에 대해서도 본문에 작성하면 된다고 한다. 

## Footer (꼬리말)

역시 생략 가능하며, 작성한다면 본문(또는 본문 생략 시 제목)과 구별하기 위해 역시 한 줄 개행하고 작성한다. 

꼬리말에서는 주로 이슈 트래킹 사용 시 커밋과 연관된 이슈 트래킹 번호를 작성할 때 사용된다. 형식은 다음과 같다. 

```
Resolves: #123
See also: #456, #789
```

예시 3-1. 꼬리말에서의 이슈 번호 작성 형식. udacity 공식 문서 참고함. 

이슈 번호 작성 시 위 예시에서도 보이는 것처럼 사용 가능한 라벨들이 다음과 같이 존재한다.

- Resolves : 문의, 또는 요청에 의한 이슈.
- Closes : 일반적인 개발과 관련된 이슈.
    - Resolves와 Closes 개념의 차이가 모호하다 느꼈고, 이에 대해 자세히 설명해주는 자료가 없어 ai에게 물어봤는데, ai에 따르면 이 두 라벨은 둘 다 특정 이슈를 해결했다는 의미에선 같지만, 맥락 상의 미세한 차이가 있다고 한다. Resolves는 어떤 이슈를 “해결”했다는 것에 초점을 두고, Closes는 단순히 그 이슈가 해결되거나 더 이상 필요가 없어 닫고자 할 때 사용하는 “이슈를 닫는다”의 의미로 사용된다고 한다. 즉, Closes가 조금 더 일반적인 상황에서 쓰이는 라벨인 것 같다. 다만 다른 블로거 분의 글에 따르면, Resolves 이 라벨 하나만 사용해도 된다고 한다. 세부적인 늬앙스는 다르더라도 큰 맥락에서는 사실상 같은 의미니 그렇게 해도 괜찮을 것 같다.
- Fixes : 버그 수정과 관련된 이슈
- See also : 해당 커밋의 이슈와 연관된 또 다른 이슈들이 있을 경우 해당 이슈들의 번호들과 함께 작성 가능.

Github에서 제공하는 “Issue” 기능과 함께 사용하면, 커밋 메시지에 Resolves, Closes 등의 라벨과 함께 특정 이슈 번호를 작성하는 것만으로도 Github에서 이를 자동으로 파악하여 실제 Issue 탭에 있는 open되어 있는 특정 이슈들을 자동으로 닫아준다고 한다. 

# 커밋 메시지 예시

udacity 공식 사이트에서는 앞서 소개한 개념들을 하나로 모아 커밋 메시지의 예시를 다음과 같이 제시하고 있다. 이를 참고하면 커밋 메시지를 어떤 형태로 써야 할지 감이 잡힐 것이다. 

```
feat: Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789
```

예시. udacity 공식 문서에서 제시한 커밋 메시지 형식 예시.

# 글을 마치며…

이번 글에서는 커밋 메시지 작성 가이드에 대해 살펴보았고, 그 중에서도 널리 참고되어 사용되고 있는 udacity style을 기준으로 정리해보았다. 앞서 언급했지만, 해당 스타일이 널리 쓰인다는 것이지 이걸 강제로 사용해야만 하는 것은 아니기에, 다른 이들과 협업할 수 있을 수준으로 알아보기 쉽고 일관적, 체계적이기만 한다면 각 팀에서 커밋 메시지 작성 규칙을 자율적으로 정하여 사용해도 될 것 같다. 어찌되었건 이 일련의 내용들을 통해 알 수 있는 중요한 사실 하나는 협업을 위해서라도 또는 개인 프로젝트에서도 이후의 커밋 이력 추적 및 검색을 용이하게 하기 위해서라도 커밋 메시지 작성에 규칙을 두는 것도 중요하다는 것이다. 

---

References

[1] [[Git] Commit message 규칙](https://velog.io/@jiheon/Git-Commit-message-%EA%B7%9C%EC%B9%99)

[2] [Git Commit Message Style Guide - 개인/팀을 위한 커밋 메시지 스타일 가이드](https://blog.munilive.com/posts/my-git-commit-guide.html)

[3] [협업의 첫 번째 단계 ④ git branch 네이밍 규칙](https://minha0220.tistory.com/72)

[4] [Git Branch Switching, 협업순서,Commit 메시지 규칙](https://myeongsu0257.tistory.com/210)

[5] [[Git] Github, Git Commit Rule (깃허브 커밋 룰)](https://underflow101.tistory.com/31)

[6] udacity style guide 공식 문서

[Udacity Nanodegree Style Guide](https://udacity.github.io/git-styleguide/)

[7] [[기본기]Git Commit Message Style Guide](https://velog.io/@hello1358/%EA%B8%B0%EB%B3%B8%EA%B8%B0Git-Commit-Message-Style-Guide-8wcqdgcs)

[8] [[Git] 깃 커밋 메시지 컨벤션 (Git Commit Message Convention)](https://da-nyee.github.io/posts/git-git-commit-message-convention/)