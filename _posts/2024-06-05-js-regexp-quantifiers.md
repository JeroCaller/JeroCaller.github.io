---
title: "[JS][정규 표현식] Quantifiers"
category: "javascript"
tag: ["web", "js", "javascript", "regexp", "정규 표현식", "quantifier"]
---

`40+12-12/34*100` 이라는 문자열이 주어졌을 때, 해당 문자열로부터 모든 숫자들을 추출하고자 한다면 어떻게 해야할까? \d 문자를 여러 번 쓰는 것도 애매하다. 두 자리 수 뿐만 아니라 세 자리 수도 있기 때문이다. 

이와 같이, 특정 횟수만큼 반복되는 특정 문자를 검색하고자 한다면 정량자(quantifier)를 사용하면 된다. 정량자에는 다음의 기호들이 있다. 

# {n}

/\d{3}/과 같이 특정 문자 뒤에 중괄호가 있고, 그 중괄호 안에 숫자 n이 들어가는 경우, 이는 바로 앞의 문자가 n번만큼 반복되는 부분 문자열을 찾겠다는 의미이다. \d{3}은 \d\d\d와 같은 셈이다. 

중괄호 안에 숫자를 어떻게 넣느냐에 따라 그 의미가 약간 달라진다. 

- {n} : 딱 n번만큼만 반복되는 문자열을 찾는다. n-1도, n+1번 반복되는 문자도 검색 결과에 포함되지 않는다.

```jsx
let source = "drawer machine dream good";
console.log(source.match(/\s\w{5}\s/g));  // 다섯글자인 단어 찾기
```

예제 1-1

```jsx
[ ' dream ' ]
```

예제 1-1 출력결과

- {n, m} : n번에서 m번 사이로 반복되는 문자열을 검색. 예를 들어, /ab{2, 4}c/ 라는 패턴이 있으면, “abbc”, “abbbc”, “abbbbc” 모두 검색 결과에 포함된다. 즉, b라는 문자가 a와 c 사이에서 2회에서 4회 사이로 반복되면 되는 것이다.

```jsx
let source = "cool coool col cl coooooooool";
console.log(source.match(/co{1,2}l/g));
```

예제 1-2

```jsx
[ 'cool', 'col' ]
```

예제 1-2 출력결과

- {n,} : 두 번째 숫자를 생략하는 경우, n번 이상 반복되는 모든 부분 문자열들이 검색 결과에 포함된다. /ab{2,}c/라고 하면, b라는 문자가 2회 이상 반복되기만 하면 검색 결과에 포함되는 것이다.

```jsx
// 일의 자릿수 이상의 숫자들을 검색
console.log("40+12-12/34*100".match(/\d{1,}/g));
```

예제 1-3

```jsx
[ '40', '12', '12', '34', '100' ]
```

예제 1-3 실행결과

# 축약형 정량자들

{n}을 대신하는 더 축약된 버전의 정량자들이 존재한다. 

- `+` : {1,}과 동일하다. 즉, 한 개 이상의 부분 문자열을 의미한다.

```jsx
// 일의 자릿수 이상의 숫자들을 검색
console.log("40+12-12/34*100".match(/\d+/g));
```

예제 2-1

```jsx
[ '40', '12', '12', '34', '100' ]
```

예제 2-1 출력결과

- ? : {0,1}과 동일. 즉, 없거나 하나가 있거나를 의미한다.

```jsx
let source = "i wanna be a bee saying bye to the world";
console.log(source.match(/b\w?e/g));
```

예제 2-2

```jsx
[ 'be', 'bee', 'bye' ]
```

예제 2-2 출력결과

- `*` : {0,}과 동일. 즉, 없거나 한 개 이상이거나를 의미한다.

```jsx
let source = "cool coool col cl coooooooool";
console.log(source.match(/co*l/g));
```

예제 2-3

```jsx
[ 'cool', 'coool', 'col', 'cl', 'coooooooool' ]
```

예제 2-3 출력결과

---

References

[1] [Quantifiers +, *, ? and {n}](https://ko.javascript.info/regexp-quantifiers)