---
title: "[Java][Issue report] unmappable character (0xEB) for encoding x-windows-949"
category: "Java"
tag: ["java", "자바", "issue report"]
---

# 오류 개요

해당 오류는 코드 내 한글을 인식하지 못하고 깨져 나오기에 나오는 오류이다. 

다음은 해당 오류 예이다. 

![4.PNG](/images/2024-03-14/2024-03-14-java-unmappable%20character%20(0xEB)%20for%20encoding%20x-windows-949-1.png)

# 해결책

명령 프롬프트 창에서 자바 파일로부터 클래스 파일로 컴파일하려는 경우, 다음과 같이 뒤에 -encoding UTF8 명령어를 추가적으로 입력한다. 

```java
javac MyApp.java -encoding UTF8
```

그러면 문제 없이 컴파일되고, 정상적으로 실행됨을 확인하였다. 

---

References

[1] [자바 컴파일 오류 unmappable character (0xED) for encoding x-windows-949 해결 과정](https://like-a-forest.tistory.com/61)

[2] 이 방법으로는 해결하지 못했다.

[vscode java 실행 시 한글 깨짐 현상 인코딩](https://otrodevym.tistory.com/entry/vscode-java-실행-시-한글-깨짐-현상-인코딩)