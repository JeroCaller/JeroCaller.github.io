---
title: "[JS][정규 표현식] 자바스크립트에서 정규 표현식 사용하기"
category: "javascript"
tag: ["web", "js", "javascript", "정규 표현식", "정규식", "regexp"]
use_math: false
---

참고할만한 문서

[정규 표현식](/python/regexp/) 

# 정규 표현식

정규 표현식(regular expression, regexp)은 패턴을 이용하여 문자열 내에서 특정 부분 문자열을 검색하거나 다른 부분 문자열로 대체할 때 쓰일 패턴을 지정하는 언어라 볼 수 있다. 

자바스크립트에서는 정규 표현식 사용을 위해 패턴(pattern)과 플래그(flag)를 사용할 수 있다. 패턴은 필수이며, 플래그는 선택적으로 사용 가능하다. 

자바스크립트에서 정규 표현식을 사용하려면 정규 표현식 객체를 생성하여 사용해야 한다. 여기에는 다음의 두 가지 방법이 있다. 

```jsx
let regexpObj = new RegExp("pattern", "flags");

let regexp = /pattern/flags; 
let regexpNoFlag = /pattern/;
```

예제 1-1

위 예제의 맨 첫 번째 줄에서는 `new RegExp` 코드를 명시적으로 사용하여 RegExp 객체를 생성하였다. 두 번째 코드부터는 슬래쉬(/)를 사용하였다. 두 슬래쉬 사이에는 패턴을, 두 번째 슬래쉬 바로 뒤에는 플래그를 덧붙일 수 있다. 이 두 번째 방법도 내부적으로는 RegExp 객체를 생성한다. 다만, 슬래시를 이용한 방법은 중간에 변수를 넣어 원하는 표현식을 넣을 수 없다. 마치 백틱(``) 내부에서 ${…} 를 사용하여 동적으로 문자열을 넣을 수 있는 기능과 비슷하다. 

# 플래그

플래그는 부분 문자열 검색 시 특정 조건을 추가하여 검색하는 것을 도와주는 역할을 한다. 플래그에는 다음이 존재한다. 

| 플래그 | 설명 |
| --- | --- |
| i | 영어 대소문자 구분없이 검색한다. “apple”과 “Apple”, “APPLE” 모두 검색 결과에 포함된다. |
| g | 원문 내에서 패턴과 중복하여 일치하는 모든 부분 문자열을 검색한다. 이 플래그가 없으면 맨 첫 번째 결과만을 반환. |

이 외에도 m, s, u, y 플래그가 있으며, 총 6개의 플래그가 있다. 

# 정규 표현식 관련 메서드

## 일치하는 결과 검색 : `str.match(regexp)`

정규 표현식은 문자열 메서드와 함께 사용한다. 

`str.match(regexp)` 메서드는 문자열(str)에서 regexp와 일치하는 부분 문자열을 찾는 기능을 한다. 

다음은 이를 이용한 예제이다.

```jsx
let source = "i wanna wash the dish in the Washington DC";
console.log(source.match(/wash/gi));
```

예제 2-1

```jsx
[ 'wash', 'Wash' ]
```

예제 2-1

```jsx
let source = "i wanna wash the dish in the Washington DC";
let regexp = new RegExp("wash", "gi");

console.log(source.match(regexp));
```

예제 2-2. RegExp 객체 생성을 이용한 예제. 출력결과는 예제 2-1과 동일하다.

위 예제에서는 g, i 플래그를 덧붙여 검색하였다. 그 결과, 패턴의 대소문자를 구분하지 않으면서도 일치하는 모든 결과를 모두 찾는다. 위 예제에서 찾고자 하는 패턴 ‘wash’에 부합하는 결과가 총 두 번 나왔다. 

다음은 g 플래그를 사용하지 않았을 때의 예제이다.

```jsx
let source = "i wanna wash the dish in the Washington DC";
let result = source.match(/wash/i);
console.log(result);
console.log(typeof result);
console.log(result[0]); // 패턴과 일치하는 문자열 내 첫 번째 부분 문자열
console.log(result.length);
console.log(result.index); // 검색된 부분 문자열의 위치
console.log(result.input);  // 원문
```

예제 2-3.

```jsx
[
  'wash',
  index: 8,
  input: 'i wanna wash the dish in the Washington DC',
  groups: undefined
]
object
wash
1
8
i wanna wash the dish in the Washington DC
```

에제 2-3 출력결과

g 플래그가 첨가되지 않은 경우, 패턴과 일치하는 첫 부분 문자열만 배열 인덱스 0에 담긴 배열이 반환된다. 그리고 해당 반환값에는 해당 부분 문자열의 위치를 알려주는 index, 원문을 알려주는 input 프로퍼티가 존재한다. 

한 편, match를 통해 원문에서 찾고자 하는 부분 문자열을 찾지 못한 경우, `match()` 메서드는 null을 반환한다.

```jsx
let matchResult = "Good morning".match(/w/i);
console.log(matchResult);

if(!matchResult) {
    console.log("Result not found");
} else {
    console.log("result: " + matchResult);
}
```

예제 2-4

```jsx
null
Result not found
```

예제 2-4 출력결과

## 다른 문자열로 치환 : `str.replace(regexp, replacement)`

`str.replace(regexp, replacement)` : 원문 str 내에서 regexp와 일치하는 부분 문자열을 replacement 인자에 전달된 다른 문자열로 치환해준다. g 플래그가 포함되어 있으면 원문 내 중복되어 일치되는 모든 부분 문자열들이 치환되고, 없으면 첫 번째 부분 문자열만 치환된다. 

```jsx
let source = "i wanna wash the dish in the Washington DC";
console.log(source.replace(/wash/gi, "clean"));
console.log(source.replace(/wash/i, "clean"));
```

예제 3-1

```jsx
i wanna clean the dish in the cleanington DC
i wanna clean the dish in the Washington DC
```

예제 3-1 출력결과

한 편, `replace()` 메서드의 두 번째 인자로 전달되는 문자열 내부에서 특수문자를 넣어 각기 다른 방식으로 문자열을 교체할 수 있다. 

| 특수문자 | 방식 |
| --- | --- |
| $& | 패턴과 일치하는 부분 문자열도 $& 위치에 포함시켜 치환한다. |
| $\`  패턴과 일치하는 부분 문자열 이전의 모든 문자열을 $\` 위치에 삽입하여 치환한다. |
| $$ | $ 문자 자체를 삽입한다.  |
| \$’, \$n, $`<name>` | 그 외 특수 문자들 |

다음은 위의 특수문자들을 사용한 예제이다.

```jsx
let source = "i wanna wash the dish in the Washington DC";
let pattern = /wash/ig;

// $& : 패턴과 일치하는 부분 문자열도 $& 위치에 포함시켜 치환한다. 
console.log(source.replace(pattern, "($& and clean)"));

// $` : 패턴과 일치하는 부분 문자열 이전의 모든 문자열을 $` 위치에 삽입하여 치환한다.
console.log(source.replace(pattern, "($` and clean)"));

// $$ : $ 문자 자체를 삽입한다. 
console.log(source.replace(pattern, "($$ and clean)"));

// 원문이 담긴 변수 source 내 값은 변하지 않는다.
console.log(source);
```

예제 3-2

```jsx
i wanna (wash and clean) the dish in the (Wash and clean)ington DC
i wanna (i wanna  and clean) the dish in the (i wanna wash the dish in the  and clean)ington DC
i wanna ($ and clean) the dish in the ($ and clean)ington DC
i wanna wash the dish in the Washington DC
```

예제 3-2 출력결과

## 일치 여부 확인 : regexp.test(str)

`regexp.test(str)` : 패턴과 일치하는 부분 문자열이 있는지 확인하고 이를 boolean으로 반환한다. 

```jsx
let source = "Good morning, captain.";
console.log(/good/i.test(source));
console.log(/caption/i.test(source));
```

예제 4-1

```jsx
true
false
```

예제 4-1 출력결과

---

References

[1] [패턴과 플래그](https://ko.javascript.info/regexp-introduction)