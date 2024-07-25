---
title: "[JS][정규 표현식] 문자 클래스"
category: "javascript"
tag: ["web", "js", "javascript", "regexp", "정규 표현식", "정규식", "문자 클래스", "character class"]
---

어떤 문자열 내에서 숫자 1을 찾고자 한다면 /1/ 과 같이 패턴을 작성하여 찾을 수 있을 것이다. 그런데 예를 들어 주민등록번호 정보가 `(info)123456-?xxxyyy` 와 같이 주어졌고, 여기서 숫자인 모든 문자들을 찾고자 한다면 어떻게 해야 할까? 1, 2와 같은 리터럴 문자(문자가 다른 의미를 가지지 않는 문자 그 자체를 말함. a, b와 같은 문자들은 알파벳 a, b 이외에 다른 의미를 가지지 않으므로 리터럴 문자이다. 반면 \n 이란 문자는 “개행”이라는 다른 뜻을 가지므로 이 문자는 리터럴이 아니다. literal이 “문자 그대로”란 뜻임을 참고하면 이해하기 쉬울 것이다)만으로는 어려울 것이다. 이 경우, 숫자 집합을 지칭하는 숫자 클래스인 \d 를 사용하면 된다. 즉, /\d/ 라고 패턴을 지정하면 1, 2, 3, 4, 5, 6 이 모든 숫자를 찾아낼 수 있다. 이렇게 특정 문자들의 집합을 클래스로 나타내 \d 와 같이 기호로 나타낸 것을 문자 클래스(character class)라 한다. 

문자 클래스에는 다음과 같이 존재한다. 

| 문자 클래스 | 설명 |
| --- | --- |
| \d | 0~9의 숫자에 해당하는 문자들 |
| \s | 공백(스페이스), 탭(\t), 개행(\n) 등의 공백 기호들을 지칭 |
| \w | 영어를 포함하는 라틴 문자, 숫자, 언더스코어(_)를 포함. 한글과 같은 비 라틴 문자는 포함되지 않는다. |
| \D | \d와 일치하지 않는 모든 문자. 즉, 숫자가 아닌 문자. |
| \S | \s와 일치하지 않는 모든 문자. 즉, 공백이 아닌 문자. |
| \W | \w와 일치하지 않는 모든 문자. 비 라틴 문자, 공백 등의 문자들이 이에 해당 |
| . (점, dot) | 개행(\n) 문자를 제외한 모든 문자 |

참고로, 영소문자로 표기되는 문자 클래스 \d, \s, \w와 반대되는 성질의 클래스이면서 영대문자를 사용하는 클래스 \D, \W, \S를 “반대 클래스”라고 한다. 

다음은 위 문자 클래스들을 사용한 예제들이다.

```jsx
let source = "(info)123456-?xxxyyy";
let regexp = /\d/g;
let result = source.match(regexp);
console.log(result);

// 결과로 나온 숫자들의 배열을 합쳐 일련번호로 만든다.
console.log(result.join(''));
```

예제 1-1

```jsx
[ '1', '2', '3', '4', '5', '6' ]
123456
```

예제 1-1 출력결과

패턴 내에 문자 클래스들을 여러 종류, 여러 개 사용할 수 있으며, 리터럴 문자들과 같이 사용할 수 있다. 

```jsx
let source = "i wanna wash the dish in the Washington DC";
console.log(source.match(/\swash\s/ig));

// \w 는 영어를 포함한 라틴 문자 및 숫자, 밑줄(_)만 검색한다. 
// 한글과 같은 비 라틴 문자는 \w에 포함되지 않는다.
let koreanSource = "그럴 수 밖에";
console.log(koreanSource.match(/\s\w\s/ig));

// \W 반대 클래스를 이용하여 한글을 찾는다.
console.log(koreanSource.match(/\s\W\s/ig));
```

예제 1-2

```jsx
[ ' wash ' ]
null
[ ' 수 ' ]
```

예제 1-2 출력결과

```jsx
let pattern = /B.D/;
console.log("BCD".match(pattern));
console.log("B.D".match(pattern));
console.log("B D".match(pattern));
console.log("B\nD".match(pattern));
console.log("BD".match(pattern));
```

예제 1-3

```jsx
[ 'BCD', index: 0, input: 'BCD', groups: undefined ]
[ 'B.D', index: 0, input: 'B.D', groups: undefined ]
[ 'B D', index: 0, input: 'B D', groups: undefined ]
null
null
```

예제 1-3 출력결과

dot 문자 클래스는 개행을 제외한 모든 문자열을 의미한다. 위 예제에서 `/B.D/` 와 같이 사용하였는데, 문자 B와 D 사이에 반드시 개행을 제외한 어떤 문자 하나가 포함되어야 검색 결과에 포함된다. BD와 같이 사이에 아무런 문자도 없으면 검색 결과로 잡히지 않는다. 한 편, 공백도 문자이므로, “B D” 문자열도 두 알파벳 사이에 공백이란 문자가 존재하므로 검색 결과에 반영된다. 

만약 . (dot) 문자 클래스를 이용하여 개행 문자까지도 포함하여 검색하고자 한다면, 패턴 뒤에 s 플래그를 덧붙이면 된다. 

```jsx
let pattern = /B.D/s;
console.log("B\nD".match(pattern));
```

예제 1-4

```jsx
[ 'B\nD', index: 0, input: 'B\nD', groups: undefined ]
```

예제 1-4 출력결과

---

References

[1] [문자 클래스](https://ko.javascript.info/regexp-character-classes)