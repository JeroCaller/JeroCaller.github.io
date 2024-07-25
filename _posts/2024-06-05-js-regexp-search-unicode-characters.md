---
title: "[JS][정규 표현식] 유니코드 문자 검색"
category: "javascript"
tag: ["web", "js", "javascript", "정규 표현식", "정규식", "regexp", "unicode"]
---

참고할만한 글

[컴퓨터가 문자를 다루는 방식, unicode, encode & decode](/python/unicode-encode-decode/) 

많은 문자들은 유니코드에서 2바이트로 인코딩 되어 있으나, 2바이트로는 최대 65,536개의 문자들만 표현할 수 있기에, 그 외 범위에 있는 문자들에 대해서는 4바이트로 인코딩되어 있다. 그래서 2바이트로 할당된 문자 하나의 길이는 1로 나오나, 4바이트로 할당된 문자 하나의 길이는 2개로 나온다. 즉, 2바이트 문자 2개로 취급한다. 눈에 보이는 문자는 한 글자임에도 말이다. 참고로 이렇게 4바이트 문자 하나를 구성하기 위해 사용되는 2바이트짜리 2개의 문자쌍을 surrogate pair(서로게이트 쌍)이라 한다.

```jsx
console.log('a'.length);
console.log("와".length);
console.log("𝒳".length);
```

예제 1-1

```jsx
1
1
2
```

예제 1-1 출력결과

그래서 4바이트 문자를 해석할 때 2바이트 문자인 것으로 착각하여 해석하면 사실상 2바이트짜리 문자 2개에서 1개만 읽어오는 것이므로 잘못된 해석이 되며, 이 경우 문자가 깨져서 보일 수도 있다. 

정규 표현식에서 이러한 유니코드 문자들을 처리하려면 u 플래그를 같이 사용해야 한다. 여기에 더해, `\p{…}` 클래스를 이용하면 유니코드 프로퍼티를 지정하여 수많은 문자들 중 특정 범위 내에 포함된 문자만을 찾을 수 있다. 

# 유니코드 프로퍼티와 \p 클래스

유니코드에서 프로퍼티란, 문자들의 “카테고리”라고 볼 수 있다. 문자에도 일반적인 문자와 숫자, 수학 기호, 16진수 등 여러 가지 범주로 나눌 수 있으며, 이들 각각에 이름이 붙은 것이 프로퍼티라 볼 수 있다. 

예를 들어 `\p{L}` 이라고 사용하면, 괄호 사이의 L은 Letter를 뜻하며, 해당 패턴은 사람의 언어의 문자를 의미한다. (영어 알파벳, 한글, 일본어의 히라가나 및 가타가나 등등)

```jsx
let source = "w 와 2";
console.log(source.match(/\p{L}/gu));
console.log(source.match(/\p{L}/g));
```

예제 1-2 

```jsx
[ 'w', '와' ]
null
```

예제 1-2 출력결과

위 예제에서, `\p{L}`을 패턴으로 사용한 결과, 사람이 사용하는 언어에 해당하는 ‘w’와 ‘와’라는 단어가 검색 결과에 포함되었다. 여기서 ‘2’는 숫자이므로 L 프로퍼티에 포함되지 않는다. 한 편 마지막 코드에는 u 플래그를 빠트렸기 때문에 원하는대로 검색이 되지 않았다. 

`\p{Hex_Digit}` 에서 Hex_Digit은 16진수 숫자를 가리키는 프로퍼티이다. 즉, 0~9, a ~ f 로 표현되는 모든 16진수를 포함하는 프로퍼티이다. 다음은 이를 이용하여, css 스타일 내에서 사용된 16진수 rgb값을 추출하는 예제이다.

```jsx
/*
    css 스타일 코드 내에서 16진수로 나타낸 rgb hex code 값 찾기.
*/
let source = `<style>
    div {
        background-color: #123456;
    }
</style>`;
let pattern = `#`;
for (let i = 0; i < 6; i++) {
    pattern += `\\p{Hex_Digit}`;
}
pattern += `;`;
console.log(pattern);
let regexp = new RegExp(pattern, "u");
console.log(source.match(regexp));
```

예제 1-3

```jsx
#\p{Hex_Digit}\p{Hex_Digit}\p{Hex_Digit}\p{Hex_Digit}\p{Hex_Digit}\p{Hex_Digit};
[
  '#123456;',
  index: 44,
  input: '<style>\n    div {\n        background-color: #123456;\n    }\n</style>',
  groups: undefined
]
```

예제 1-3 출력결과

Script(표기 체계)라는 유니코드 프로퍼티도 존재한다. 이는 텍스트를 통해 정보를 표현하기 위해 사용되는 문자들의 조합이라 보면 된다. 한자, 그리스 문자, 한글 등 여러 개의 값들이 존재하며, 해당 프로퍼티에 대한 모든 값들을 알고자 한다면 References [2]를 참고하면 된다. 

이를 이용하여 특정 표기 체계의 문자만을 찾을 수 있다. `\p{sc=이름}` 형식으로 사용한다. 다음은 이를 이용하여 문자열 속에서 한글만을 찾는 예제이다.

```jsx
let source = "I am 신뢰 123";
console.log(source.match(/\p{sc=Hang}/gu));
```

예제 1-4

```jsx
[ '신', '뢰' ]
```

예제 1-4 출력결과

이 외에도 `\p{}` 클래스에 사용할 수 있는 유니코드 프로퍼티들이 다수 존재하며, 더 자세한 사항은 References [1] 참고.

---

References

[1] [유니코드: 'u' 플래그와 \p{...} 클래스](https://ko.javascript.info/regexp-unicode)

[2] [Script (Unicode)](https://en.wikipedia.org/wiki/Script_(Unicode))