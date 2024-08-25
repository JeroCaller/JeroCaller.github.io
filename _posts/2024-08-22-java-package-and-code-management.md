---
title: "[Java] Package와 코드 관리"
category: "Java"
tag: ["java", "package", "javac", "class path", "CLASSPATH"]
---

[패키지, 클래스 패스](/java/java-package-and-classpath/) 페이지에서 자세히 다루지 못한 것을 여기서 다루겠다. 

# Package

자바 소스 코드와 클래스 파일들이 너무 많아지면 이를 관리하기가 어려워지므로, 서로 관련있는 파일들끼리 하나의 폴더 안에 묶는 게 관리하기 더 좋아질 것이다. 자바에서는 이를 위해 “패키지”라는 것을 지원한다. 

패키지는 서로 관련 있는 소스 코드들을 하나의 폴더에 넣는 것에서 끝나지 않는다. 각 소스 코드의 맨 위에는 자신이 속한 폴더의 경로명을 마침표(.)를 구분자로 하여 선언해줘야 한다. 그래야 패키지로 인식된다. 예를 들어 어떤 자바 프로젝트 디렉토리 구조가 다음과 같을 때,

```java
Project
│
└─myLib
	└─  printlib
			└─  PrintArrayTool.java
		        PrintArrayToolStatic.java
```

printArrayTool.java 파일 내에서 맨 첫 번째 줄에 다음과 같이 패키지 선언을 할 수 있다.

```java
package myLib.printlib;
```

자신의 소속을 밝히는 것과 같다고 보면 된다.

이렇게 패키지 선언을 하면, 상위 폴더 내부의 자바 소스 코드가 `myLib.printlib.PrintArrayTool` 형태로 사용하든지 아니면 `import myLib.printlib` 과 같이 사용하여 해당 클래스명만 가져다 쓸 수 있다. 

# 소스 코드와 클래스 파일 관리

원래 자바 프로젝트를 생성하면 다음의 구조를 띤다. 

```java
project/
	bin/
	lib/
	src/
```

이 중 소스 코드 파일(.java)은 src 폴더 내부에, 소스 코드를 컴파일 한 후 나온 클래스 파일(.class)은 bin 폴더에 저장하는 것이 원칙이며, 만약 소스 코드에서 가져다가 쓸 라이브러리가 있다면 이는 lib 폴더에 넣는다고 한다. 

그런데 src 폴더에서 작성한 자바 파일을 단순히 `javac App.java` 라는 명령어로 컴파일을 실행하면 클래스 파일도 해당 자바 파일과 동일한 위치인 src 폴더 내에 위치하게 된다. 한 두 개의 파일 쯤은 그냥 직접 클래스 파일을 bin 폴더에 옮길 수도 있지만, 프로젝트 규모가 조금만 커져도 일일이 그렇게 하는 것은 어려울 것이다. src 폴더 내 자바 파일을 컴파일하면서도 그 결과로 나온 클래스 파일을 자동으로 bin 파일에 저장시키게 하려면 어떻게 해야할까? 

이 때의 명령어는 다음과 같다. (cmd 기준)

```bash
javac -d 클래스파일저장경로 컴파일할파일경로
```

예를 들어 다음의 프로젝트 구조가 있다고 가정하겠다.

```shell
MyStudy
│
├─bin
│
├─lib
│
└─src
        App.java
```

그리고 cmd 창에서 현재 작업 경로가 MyStudy라는 루트 디렉토리로 가정하겠다. 현재 작업 경로를 기준으로 상대 경로를 이용하여 cmd창에서 다음과 같이 입력하면…

```shell
javac -d bin src/App.java
```

폴더 구조를 다시 살펴보면 다음과 같이 바뀐다.

```shell
MyStudy
│
├─bin
│      App.class
├─lib
│
└─src
        App.java
```

즉, src 폴더에 있던 App.java를 컴파일하고 나온 App.class 파일이 bin 폴더에 저장되었음을 알 수 있다. 

이렇듯, -d 옵션은 소스 파일을 컴파일 한 바이트 코드를 다른 위치에 저장할 때 사용하는 옵션이다. 

참고로, `javac -d bin src/*.java` 에서 보듯,  `*` 이라는 와일드 카드를 파일명으로 사용하면 해당 폴더 내에 모든 java 파일들을 한꺼번에 컴파일 할 수 있다. 

그렇다면 cmd창에서 현재 작업 경로를 바꾸지 않고도 원하는 위치에 있는 클래스 파일을 실행할 때는 어떻게 해야할까? 이럴 때는 다음의 명령어 구조를 이용한다.

```shell
java -cp [바이트 코드 파일이 위치한 폴더 경로] [패키지.바이트 코드 파일명]
```

여기서 -cp는 classpath의 줄임말로, 클래스 파일이 위치한 경로를 명시하여 클래스 파일을 찾아 실행시킬 때 사용하는 옵션이다. 

앞서 든 위 예제에서 bin 폴더 내에 위치한 App.class 파일을 MyStudy 위치에서 실행하려면 다음의 명령어를 입력한다. 

```shell
MyStudy> java -cp bin App
(출력결과)
1
2
3
```

# 패키지가 포함된 소스 파일 컴파일하고 실행하기

이번에는 MyStudy 루트 디렉토리 내 구조가 다음과 같다.

```shell
MyStudy
├─bin
├─lib
│
└─src
    │  App.java
    │
    └─mylib
            PrintArray.java
```

그리고 각 자바 파일 내 소스 코드는 다음과 같다.

```java
import mylib.PrintArray;

public class App {
    public static void main(String[] args){
        int[] num = {1, 2, 3};

        PrintArray pa = new PrintArray();

        pa.printArray(num);
    }
}
```

App.java

```java
package mylib;

import java.util.Arrays;

public class PrintArray {
    public void printArray(int[] arr) {
		System.out.println(Arrays.toString(arr));
	}
	
	public void printArray(double[] arr) {
		System.out.println(Arrays.toString(arr));
	}
	
	public void printArray(char[] arr) {
		System.out.println(Arrays.toString(arr));
	}
	
	public void printArray(String[] arr) {
		System.out.println(Arrays.toString(arr));
	}
	
	public void printArray(int[][] arr) {
		System.out.println("{");
		for (int[] row : arr) {
			System.out.println("    " + Arrays.toString(row) + ",");
		}
		System.out.println("}");
	}
	
	public void printArray(double[][] arr) {
		System.out.println("{");
		for (double[] row : arr) {
			System.out.println("    " + Arrays.toString(row) + ",");
		}
		System.out.println("}");
	}
	
	public void printArray(char[][] arr) {
		System.out.println("{");
		for (char[] row : arr) {
			System.out.println("    " + Arrays.toString(row) + ",");
		}
		System.out.println("}");
	}
	
	public void printArray(String[][] arr) {
		System.out.println("{");
		for (String[] row : arr) {
			System.out.println("    " + Arrays.toString(row) + ",");
		}
		System.out.println("}");
	}
}

```

PrintArray.java

PrintArray.java는 배열 내 각 요소들을 모두 순회하여 출력하는 기능을 가진다. 이 소스 코드를 패키지로 하여 App.java에서 가져다가 해당 기능을 사용하고 있는 것이다. 이제 이 자바 파일들을 컴파일하고 실행해보자. 

먼저 컴파일을 다음과 같은 명령어로 실행하였다.

```shell
MyStudy> javac -d bin src/App.java
src\App.java:1: error: package mylib does not exist
import mylib.PrintArray;
            ^
src\App.java:7: error: cannot find symbol
        PrintArray pa = new PrintArray();
        ^
  symbol:   class PrintArray
  location: class App
src\App.java:7: error: cannot find symbol
        PrintArray pa = new PrintArray();
                            ^
  symbol:   class PrintArray
  location: class App
3 errors
```

App.java 파일 내에서 PrintArray.java 파일을 가져다가 쓰는 의존 관계라고 해서 App.java만 컴파일을 하려고 하면 위와 같이 에러가 나는 것을 확인할 수 있다. 즉, PrintArray.java 파일까지 같이 컴파일 해야 한다. 

이제 다음의 명령어로 고쳐 실행해봤다.

```shell
javac -d bin App.java src/mylib/PrintArray.java
```

그러면 폴더 구조가 다음과 같이 변경된다.

```shell
MyStudy
├─bin
│  │  App.class
│  │
│  └─mylib
│          PrintArray.class
│
└─src
    │  App.java
    │
    └─mylib
            PrintArray.java
```

src 폴더 구조와 똑같이 클래스 파일들이 bin 폴더 내에 생성되었음을 알 수 있다. 

이와 같이 컴파일 할 소스 코드가 둘 이상일 때 다음과 같은 형태로 명령어를 입력하면 된다.

```shell
javac -d [클래스파일저장폴더] [컴파일할소스코드1] [소스코드2], ...
```

이번에는 bin 폴더 내 파일을 실행해보겠다.

```shell
MyStudy> java -cp bin App
(출력 결과)
[1, 2, 3]
```

# 클래스 파일들이 흩어져 있을 경우

클래스 파일들이 여기저기에 흩어져 있는데, 이들을 모아서 실행하려면 어떻게 해야할까? 

이럴 때도 `java -cp` 명령어를 이용한다. 다만, -cp 옵션 뒤에는 클래스 파일들이 위치한 각각의 경로들을 세미콜론(;)으로 구분하여 모두 입력해줘야 한다. 다음과 같이 말이다. 

```shell
java -cp 클래스파일경로1;클래스파일경로2[;...] 실행할클래스파일명
```

예를 들어, 다음의 프로젝트 디렉토리가 존재한다. 

```java
MyStudy
│
├─lib
└─src
    │  App.class
    │  App.java
    │  PrintArray.java
    │
    └─mylib
            PrintArray.class
```

그리고 cmd 창에서 현재 작업 디렉토리가 MyStudy/src라고 가정하겠다. 이 상태에서 다음의 명령어를 입력하면,

```shell
MyStudy/src> java App
Exception in thread "main" java.lang.NoClassDefFoundError: PrintArray
        at App.main(App.java:5)
Caused by: java.lang.ClassNotFoundException: PrintArray
        at java.base/jdk.internal.loader.BuiltinClassLoader.loadClass(BuiltinClassLoader.java:641)
        at java.base/jdk.internal.loader.ClassLoaders$AppClassLoader.loadClass(ClassLoaders.java:188)
        at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:526)
        ... 1 more
```

위와 같이 실행이 되지 않는다. App.java 내에서 사용하는 PrintArray.class 파일을 컴파일러가 인식하지 못했기 때문이다. 

이 문제를 해결하기 위해, 우선 현재 작업 경로를 MyStudy로 옮긴 후, 다음의 명령어를 입력하였다. 

```shell
MyStudy> java -cp src;src/mylib App
[1, 2, 3]
```

-cp 옵션 바로 뒤에는 같이 실행할 클래스 파일들이 위치한 각각의 폴더 경로들을 입력하여 컴파일러에게 해당 클래스 파일들의 위치를 알려주는 것이다. 여기서는 src 폴더 내에 있는 App.class와 src/mylib 폴더 내부에 있는 PrintArray.class 이 두 파일을 동시에 실행해야하므로 각 경로들을 세미콜론(;)으로 구분지어 입력한 것이다. 클래스 파일 경로를 입력한 뒤에는 main 메서드를 포함하여 실행할 수 있는 클래스 파일명을 입력한다. 여기서는 main 메서드가 존재하는 파일이 App.class 파일이므로 App이라고 입력한 것이다. 이렇게 입력하면 컴파일러가 각 클래스 파일들의 위치를 인식하여 결과적으로 프로그램을 실행할 수 있게 되는 것이다. 

## CLASSPATH 환경 변수 등록으로 조금 더 편하게 실행하기

앞선 글과 같이, 클래스 파일들이 여러 곳에 흩어져 있는 경우 각 클래스 파일들이 위치한 디렉토리 위치들을 입력하면 실행할 수 있었다. 그런데, 위 예제에서는 흩어져 있는 클래스 파일들이 2개라 그렇지, 만약 3개, 아니 100개 이상이면 일일이 그만큼의 클래스 파일 경로들을 입력해야 한다. 소스 코드를 수정하고 다시 컴파일하고 이 클래스 파일들을 다시 실행해야 할 때 똑같은 긴 명령어를 입력하는 것도 곤욕이다. 

이럴 때 흩어져 있는 클래스 파일들의 경로들을 한 번만 등록하고 나중에 소스 코드 수정 및 재컴파일을 하더라도 간단한 명령어만 입력해도 실행되도록 할 수 있다. 바로 클래스 패스를 환경 변수로 등록하는 것이다. 

[개발 환경](/java/java-개발환경/) 페이지에서 jdk 폴더 위치를 환경 변수에 등록함으로써 현재 작업 위치가 어디에 있건 상관없이 자바 컴파일 및 실행을 할 수 있는 것을 살펴보았다. 이와 비슷한 과정을 거쳐 클래스 패스들을 등록하면 어느 위치에서든 해당 프로그램을 실행시킬 수 있다. 

class-path(MyStudy 이름을 바꿈) 프로젝트 디렉토리의 상위에는 java-study라는 디렉토리가 존재한다. 이 경로로 이동하여 앞서 흩어져 있던 클래스 파일들을 실행해보겠다. 

```shell
C:\java\java-study> java -cp src;src/mylib App
Error: Could not find or load main class App
Caused by: java.lang.ClassNotFoundException: App

C:\java\java-study> java -cp ./class-path/src;./class-path/src/mylib App
[1, 2, 3]
```

물론 class-path 디렉토리가 현재 작업 위치였을 때의 명령어 그대로 입력하면 실행이 되질 않는다. 현재 작업 위치가 바뀌었기 때문이다. 이제 다음의 과정을 거치면 해당 위치에서도 App.java 프로그램을 실행시킬 수 있으며, 다른 위치에서도 간단한 명령어 입력을 통해 해당 프로그램을 실행시킬 수 있다. 

클래스 패스 환경 변수 등록 방법 (윈도우 10 기준)

1. 작업 표시줄에 있는 검색창에 “시스템” 이라고만 입력해도 “시스템 환경 변수 편집” 항목이 뜨는데 이를 클릭한다. 
    
    ![20240825_19332284.png](/images/2024-08-22/java-package-and-code-management/1.png)
    
2. 새로 나타난 \[시스템 속성\] 창에서 \[고급\] 탭의 아래에 있는 \[환경 변수(N)…\] 버튼을 클릭한다. 
    
    ![2.PNG](/images/2024-08-22/java-package-and-code-management/2.png)
    

3. 사용자 변수와 시스템 변수 중에 아무거나 하나 택한다. 여기서는 사용자 변수를 택하겠다. 목록 밑에 있는 \[새로 만들기(N)…\] 버튼을 클릭한다. 
    
    ![3.PNG](/images/2024-08-22/java-package-and-code-management/3.png)
    

4. \[새 사용자 변수\] 창이 뜨면 \[변수 이름\]에는 “CLASSPATH”라고 입력한다. 그 후, \[변수 값\]에는 실행시킬 각각의 클래스 파일들의 경로를 세미콜론(;)을 구분자로 하여 입력하면 된다. 여기서는 앞서 보았던 class-path/src 폴더와 class-path/src/mylib 폴더를 입력하였다. 다 되었으면 \[확인\] 버튼을 누른다. 
    
    ![4.PNG](/images/2024-08-22/java-package-and-code-management/4.png)
    

5. 앞선 창으로 되돌아와 사용자 변수의 목록을 보면 “CLASSPATH” 항목이 추가된 것을 볼 수 있다. 확인을 위해 해당 항목을 더블클릭하여 열어보자.
    
    ![5.PNG](/images/2024-08-22/java-package-and-code-management/5.png)
    

6. 아래와 같이 클래스 파일 경로들이 목록으로 뜨는 것을 볼 수 있다. 나중에 등록할 클래스 파일 경로가 더 추가되거나 기존의 클래스 패스를 수정, 삭제해야할 때 이 창에서 작업을 수행하면 된다. \[확인\] 버튼을 누른 후, 나머지 남아있는 창들에 대해서도 모두 \[확인\] 버튼을 누르자. 
    
    ![6.PNG](/images/2024-08-22/java-package-and-code-management/6.png)
    

7. 기존에 있던 cmd 창에서는 앞서 설정한 환경 변수가 반영되지 않으므로 새 cmd창을 연다. 그 후 앞선 java-study 경로로 이동한다. 그 후 간단히 `java App`  이란 명령어를 입력하기만 해도 다음과 같이 정상적으로 실행된다. 여기서 java 명령어 뒤에 main 메서드가 포함된 실행 가능한 클래스 파일인 App.class의 파일명을 입력한 것이다. 
    
    ![7.PNG](/images/2024-08-22/java-package-and-code-management/7.png)
    
    심지어는 전혀 연관이 없는 디렉토리인 C 루트 폴더로 이동해서 같은 명령어를 입력하더라도 똑같이 동작된다. 
    
    ![8.PNG](/images/2024-08-22/java-package-and-code-management/8.png)
    

이렇게 어떤 위치에서든 원하는 프로그램을 경로 입력 없이 간단한 명령어만으로도 실행시킬 수 있음을 확인하였다. 물론, 나중에 클래스 패스에 등록한 파일들을 더 이상 실행할 이유가 없어졌다면 해당 경로들을 지워주는게 좋겠다. 그렇다고 앞서 환경 변수에서 만든 \[CLASSPATH\] 자체를 없애지는 말고, 대신 현재 디렉토리를 의미하는 마침표(.)만 남기고 나머지 경로들은 모두 지우게 하면 나중에 환경 변수로 인한 문제는 없을 것이다. 자바 파일을 저장만 해도 자동으로 컴파일하고 바로 실행할 수 있게 해주며, 컴파일한 클래스 파일들을 모두 자동으로 bin 폴더에 저장시켜주는 기능을 가진 이클립스 등의 IDE를 모종의 이유로 사용할 수 없을 때 위에서 소개한 방법대로 하면 쉽게 자바로 짠 프로그램을 실행시킬 수 있게 된다. 

결론) 그냥 왠만하면 맘 편하게 이클립스 같은 IDE 쓰자. 수작업으로 명령어 창에서 컴파일하고 실행하다보면 프로젝트 규모가 커질수록 잦은 실수로 인한 오류가 떠 스트레스만 받는다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 신용권, “혼자 공부하는 자바”, (한빛미디어, 2024)