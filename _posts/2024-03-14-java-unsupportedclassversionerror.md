---
title: "[Java][Issue report] UnsupportedClassVersionError"
category: "Java"
tag: ["java", "자바", "issue report"]
---

```java
Exception in thread "main" java.lang.UnsupportedClassVersionError: CallSomeClass has been compiled by a more recent version of the Java Runtime (class file version 61.0), this version of the Java Runtime only recognizes class file versions up to 52.0
```

cmd 창에서 자바 파일이 있는 폴더에 접근한 후, 자바 파일에 대해 자바 클래스 파일을 생성한 후에, 자바 파일을 java 파일명 명령어를 통해 실행하려고 하면 위의 에러가 발생했다. 위의 에러를 이해하려면 우선 JRE와 JDK에 대해 이해해야한다. 

JRE는 Java Runtime Environment의 약자로, 자바 실행 환경을 의미하며, 자바로 제작된 프로그램을 실행시킬 때 필요한 프로그램이다. 나의 경우, 마인크래프트-자바 에디션 게임을 할 때 강제로 설치했던 프로그램인 것으로 기억한다. 만약, 자바 개발은 전혀 하지 않고, 그저 자바로 제작된 프로그램을 이용하기만 할 목적이라면 JDK없이 이것만 설치받으면 될 것이다. 

JRE만 다운로드 받을 수 있는 사이트는 다음의 페이지이다.

[https://www.java.com/ko/download/ie_manual.jsp?locale=ko](https://www.java.com/ko/download/ie_manual.jsp?locale=ko)

2024-03월 현재 해당 사이트에 가보면 java 8 버전만을 다운로드 받을 수 있다. 

반면 JDK는 Java Development Kit, 즉 자바 개발 도구의 약자로, 즉 자바 개발을 하고자 할 때 사용되는 환경이다. JDK는 OracleJDK과 OpenJDK가 있는데, 그 중 OpenJDK가 무료로 이용 가능하다고 해서 이를 다운받았었다. JDK를 다운로드 받으면 JRE도 자동으로 다운로드 받는다고 한다. 그런데 OpenJDK 설치 폴더로 가봐도 어디에 JRE가 있는건지는 잘 모르겠다. 

아무튼, 나는 이미 예전부터 JRE 8 버전이 설치되어 있던 상태였고(한 때 마크를 즐겼으니까), 그 후에 자바 개발을 하려고 JDK 17 버전을 설치하였다. 이 때 cmd창에서 자바 버전을 확인해보면 다음과 같았다.

![Untitled](/images/2024-03-14/2024-03-14-java-unsupportedclassversionerror-1.png)

사실 위 사진은 jdk 21 버전으로 설치한 모습인데, 후에 17로 바꿨다. 

어찌됐건, 기존에 JRE 8 버전이 설치된 상태에서 따로 JDK 17 버전을 설치하였다. 이 상황에서 javac 파일.java 명령어로 .class 확장자 파일을 생성한 후, java 파일 명령어로 해당 자바 파일을 실행하려고 할 때 앞서 언급한 에러가 발생하며 실행이 되지 않았다. 이 상황에서 앞서 본 에러 메시지를 자세히 살펴보면, 현재 클래스 파일을 버전 61.0 버전으로 컴파일 시도를 했는데, 실제 자바 런타임 환경은 클래스 파일 버전을 52.0까지만 인식하기에 실행되지 않는다는 것이다. 클래스 파일 버전과 자바 버전 대응표는 대략 다음과 같다. 

52 - Java 8

53 - Java 9

…

61 - Java 17

클래스 파일 버전 숫자와 자바 버전 숫자가 동시에 1씩 증가하는 형태임을 알 수 있다. 즉, 저 에러의 요지는, JDK 17 버전으로 JRE 8 환경에서 클래스 파일을 생성하려고 하니 인식이 되지 않는다는 것이다. 

아무래도 앞서 언급한, 내가 예전에 설치한 JRE 8 때문에 벌어진 일인 것 같아 해당 프로그램을 제어판에서 제거하였다. 그 후 cmd 창에서 자바 버전을 재확인해보니 다음과 같았다. 

![1.PNG](/images/2024-03-14/2024-03-14-java-unsupportedclassversionerror-2.png)

자세히 보면, 아까는 Java로 시작하였는데 이번에는 모두 OpoenJDK 로 시작한다. JRE 환경이 바뀐 듯 싶다. 이 상태에서 기존에 생성한 클래스 파일들을 모두 지운 후 다시 클래스 파일을 생성한 후 다시 실행해보니 에러가 발생하지 않고 문제없이 실행되는 것을 확인하였다. 즉, 문제는 예전에 설치한 Java 8의 JRE 때문이었다. 

그런데 만약, 나중에 다시 마크 게임을 하고 싶어지면 그 때도 게임이 잘 동작될까? 이것은 아직 확인해보지 않았다. 만약 안된다면 어떻게 해야할까? 마크도 하고 싶고 자바 개발도 하고 싶다면? 일단 나중에 게임을 켜봐야 알 것 같다. 

---

References

[1]  UnsupportedClassVersionError 해결책

[How to Fix java.lang.UnsupportedClassVersionError \| Baeldung](https://www.baeldung.com/java-lang-unsupportedclassversion)

[2] [☕ JDK & JRE 초간단 설치 및 환경 변수 설정](https://inpa.tistory.com/entry/JAVA-📚-JDK-JRE-환경변수-설치)