---
title: "[JS][정규 표현식] Escaping"
category: "javascript"
tag: ["web", "js", "javascript", "regexp", "정규 표현식"]
---

# Escaping

정규 표현식에는 \d, \w 등의 문자 클래스와 $, . ? 등의 여러 특수 문자들이 있다. 그런데 만약 문자 클래스 또는 특수 문자의 의미가 아닌 리터럴 문자로 사용하고 싶다면 어떻게 해야 할까? 예를 들어 패턴에 . (dot)이 들어가 있는데, 이 구두점이 “개행을 제외한 모든 문자들”을 의미하는 “문자 클래스”로서의 문자가 아니라 말 그대로 마침표를 뜻하는 “리터럴” 문자로서 사용하고자 한다면? 이럴 때는 백슬래시(\\)라는 이스케이프 문자를 바로 앞에 붙이면 그 뒤에 있는 문자는 리터럴 문자로 인식하게 된다. (\\. 와 같이 사용)

```jsx
let source = "um...";
console.log(source.match(/.../g));
console.log(source.match(/\.\.\./g));
```

예제 1-1

```jsx
[ 'um.' ]
[ '...' ]
```

예제 1-1 출력결과

정규 표현식에서는 괄호()도 특수 문자로서 사용되기에, 괄호 문자 그 자체를 원한다면 역시 앞에 백슬래쉬 기호를 붙이면 된다. 

```jsx
let source = "(대충 문장)";
console.log(source.match(/\(.....\)/g));
```

예제 1-2 

```jsx
[ '(대충 문장)' ]
```

예제 1-2 출력결과

자바스크립트에서는 정규식 패턴을 표시하기 위해 양 옆으로 슬래쉬(/) 기호를 사용하는데, 패턴 안에 슬래시 기호 자체를 포함하고자 한다면 역시 앞에 백슬래쉬를 붙이면 된다. 

# new RegExp

`new RegExp()` 를 이용한 방식에서는 슬래시 기호 대신 문자열을 표기하는 따옴표(”, “, ‘, ‘) 내부에 패턴을 작성한다. 그런데 여기에 “\d”와 같이 사용하면, 문자열 내부에서의 백슬래시는 \d와 같이 특수 문자로 인식되지 않고 이스케이프 문자로 인식된다. 

```jsx
let source = "123";
console.log(source.match(new RegExp("\d", "g")));  // 1
console.log(source.match(new RegExp("\\d", "g")));  // 2
```

예제 2-1

```jsx
null
[ '1', '2', '3' ]
```

예제 2-1 출력결과

따라서 이 경우, 앞에 백슬래시 기호를 더 넣어 “\\d” 와 같이 만들면 원래대로 의도한 특수 문자로서 사용할 수 있게 된다. 

---

References

[1] [Escaping, special characters](https://ko.javascript.info/regexp-escaping)