---
title: "[Java] 열심히 만든 자바 프로젝트를 jar로 패키징하기"
category: "Java"
tag: ["java", "jar", "JAR", "eclipse", "cmd"]
---

# jar란?

완성된 자바 프로젝트는 보통 프로젝트 폴더를 루트 폴더로 하고 수많은 자바 소스 코드 및 클래스 파일들이 그 안에 포함되어 있는 구조를 띌 것이다. 이렇게 만든 자바 프로젝트 폴더를 하나의 압축 파일로 만들어 패키징하고 이를 배포하면 사용자 입장에서는 좀 더 쉽게 사용할 수 있을 것이다. 이럴 때 사용하는 것이 바로 jar이다. jar는 Java Archive의 준말로, 여러 클래스 파일들과 이미지 등의 리소스들을 하나의 파일로 모아 압축하는 파일 확장자이다. 사실 ZIP 파일과 다를 바가 없다. 단지 자바와 관련된 컨텐츠들만 압축한다는 점이 다르다. 

jar의 장점은 다음과 같다. 

- 복잡한 패키지 구조를 사용자가 생각할 필요없이 하나의 jar 파일만 가져다가 사용하면 된다. 이를 달리 말하면, 사용자가 실수로 다른 사람이 배포한 패키지의 내부를 잘못 건드려 오류가 발생될 위험이 없어진다.
- 아무래도 압축을 하는 것이다보니 원래 폴더일 때보다 용량을 더 줄일 수 있어 서버에 저장하여 사용할 때에도 서버의 리소스를 덜 잡아먹을 것이고, 배포에도 용이하다.
- 명령어 창에서 명령어를 일일이 입력하여 컴파일 및 실행을 해보면 알겠지만, 자바 소스 코드 파일들의 저장 위치가 여러 폴더로 나뉘어 계층 구조를 이루면 일일이 컴파일할 소스 코드 지정 및 실행할 클래스 파일들의 경로를 지정하는 것이 매우 번거롭고, 경로를 잘못 지정해 오류가 빈번하게 날 수 있다. 그러나 이를 하나의 jar 파일로 압축하면 jar 파일 경로 하나만 생각하면 되기에 경로로 인한 오류를 줄일 수 있다.

그리고, 아예 독립적으로 실행할 수 있도록 제작된 자바 애플리케이션을 하나의 jar로 압축하든가, 아니면 라이브러리를 다른 사람들이 손쉽게 쓸 수 있도록 jar로 압축하여 패키징할 때 사용된다. (왠만하면 자바로 만든 것을 배포할 때 폴더 그대로가 아니라 jar로 압축하여 배포한다고 생각하면 되겠다)

jar 파일을 사용할 때에는 이를 압축 해제하여 사용하지 않고 압축된 상태 그대로 사용한다고 한다. 물론 필요에 따라 압축을 해제할 수도 있다. 

그러면 자바 프로젝트 폴더를 어떻게 jar 파일로 압축하고 이를 다른 곳에 사용할 수 있을까?

# 명령어 창에서 jar 파일 압축 및 해제해 보기

명령어 창에서 jar 명령어를 이용하면 특정 자바 패키지를 jar로 압축하거나 반대로 jar를 압축 해제할 수도 있다. 

이를 사용하려면 우선 jdk 폴더 내 bin 폴더 경로가 \[Path\] 환경 변수에 등록되어 있어야 한다. jar.exe라는 프로그램 자체가 해당 경로에 존재하기 때문이다. 이 과정은 [개발 환경](/java/java-개발환경/) 페이지에서 언급되어 있으니 참고. 

실습을 위해 다음의 “class-path”라는 이름의 간단한 자바 프로젝트를 준비하였다.

```java
class-path>
├─bin
│  │  App.class
│  │
│  └─mylib
│          PrintArray.class
│
├─lib
└─src
    │  App.java
    │
    └─mylib
            PrintArray.java
```

```java
import mylib.PrintArray;

public class App {

	public static void main(String[] args) {
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

한 가지 주의할 점은, 자바 패키지를 jar 파일로 압축하기 전에 반드시 소스 코드를 컴파일하여 클래스 파일들을 마련해둬야 한다는 것이다. 앞서 언급했듯, 보통 jar에는 클래스 파일들이 들어가기 때문이다. 

좀 더 사용하기 쉽게 하기 위해, 작업하고자 하는 프로젝트 경로로 이동한 후, 먼저 jar라고 입력해보자.

```shell
class-path> jar
Usage: jar [OPTION...] [ [--release VERSION] [-C dir] files] ...
Try `jar --help' for more information.
```

위와 같이 뜨면 jar 프로그램을 정상적으로 이용할 수 있다는 뜻이다. `jar --help` 라고 치면 관련 명령어들을 모두 살펴볼 수 있다. 

class-path 폴더를 jar 파일로 압축해볼 것이다. 압축할 때 사용하는 대표적인 옵션으로 다음이 있다. 

`-c` : jar 압축 파일을 만들겠다는 옵션이다. 

`-v` : 압축 과정 또는 결과를 보기 쉽게 명령창에 메시지로 출력하고자 할 때 쓰인다.

`-f` : 생성될 jar 압축 파일 이름을 지정할 때 쓰인다. 해당 옵션에서 한 칸 공백을 띄고 원하는 압축 파일명을 적으면 된다. 이 때 파일명 뒤에 확장자 .jar도 붙여야 한다. 압축 파일명 뒤에 한 칸 띄고 압축하고자 하는 클래스 파일들이 존재하는 디렉토리 경로를 입력한다. `-f (jar 압축 파일명) (클래스 파일 경로)` 식이다. 이렇듯 -f 옵션에는 그 뒤에 파일명 및 경로를 입력해야 하므로, -f 바로 뒤에 다른 옵션을 넣는 실수를 하지 않도록 주의하자. 

위 옵션들을 모두 동시에 사용하고자 한다면 `-cvf` 와 같이 축약하여 사용할 수 있다. 

다음은 class-path 디렉토리 내의 bin 폴더 내부의 “mylib”을 jar 파일로 압축하고자 할 때 사용한 명령어이다. 그 전에 먼저 bin 폴더로 현재 작업 경로를 이동하여 진행한다. 즉, 패키징할 디렉토리를 직속 하위 폴더(mylib)로 가지는 직속 상위 폴더(bin)를 현재 작업 경로로 지정하는 것이 좋다. 

```shell
class-path\bin> jar -cvf myJavaLibs.jar mylib
added manifest
adding: mylib/(in = 0) (out= 0)(stored 0%)
adding: mylib/PrintArray.class(in = 1927) (out= 818)(deflated 57%)
```

class-path 폴더 내부 구조를 살펴보면 다음과 같이 변경되어 있다. 

```shell
class-path
├─bin
│  │  App.class
│  │  myJavaLibs.jar
│  │
│  └─mylib
│          PrintArray.class
│
├─lib
└─src
    │  App.java
    │
    └─mylib
            PrintArray.java
```

압축된 jar 파일은 “반디집”과 같은 일반적인 zip 프로그램으로도 그 내부를 살펴볼 수 있다. 다만 일반적인 zip 프로그램으로는 jar 파일로 압축할 수는 없으니 이에 주의한다. 

해당 jar 파일을 일반적인 zip 프로그램을 이용하여 압축 해제한 뒤 그 내부를 살펴보면 다음과 같이 구성되어 있음을 알 수 있다. 

```shell
myJavaLibs
├─META-INF
│      MANIFEST.MF
│
└─mylib
        PrintArray.class
```

앞서 본 클래스 파일이 디렉토리 구조 그대로 유지하며 존재함을 알 수 있다. 그리고 jar 파일 내부에 패키징된 파일들에 대한 메타데이터를 담는 META-INF 폴더와 그 내부에 MANIFEST.MF 라는 파일이 들어 있다. 해당 파일 내부에는 다음과 같이 적혀져 있다.

```
Manifest-Version: 1.0
Created-By: 21 (Oracle Corporation)
```

만약 jar 명령어를 이용하여 jar 파일을 압축해제할 땐 다음의 옵션을 사용하면 된다. 

`-x` : 압축 해제

정확한 결과를 확인하기 위해, 앞서 압축한 myJavaLibs.jar 파일을 class-path 폴더에 위치시킨 후, class-path 폴더 내 bin 폴더를 미리 제거한 후, 다음의 명령어를 입력하여 압축을 해제하였다. 

```shell
class-path> jar -xvf myJavaLibs.jar
  created: META-INF/
 inflated: META-INF/MANIFEST.MF
  created: mylib/
 inflated: mylib/PrintArray.class
```

```shell
class-path
│  myJavaLibs.jar
│
├─lib
├─META-INF
│      MANIFEST.MF
│
├─mylib
│      PrintArray.class
│
└─src
    │  App.java
    │
    └─mylib
            PrintArray.java

```

현재 작업 폴더 내부에 mylib 폴더와 META-INF 폴더가 압축 해제되어 저장되었다. 

방금 생성된 mylib 폴더와 META-INF 폴더를 다시 삭제하겠다. 

이렇게 생성된 jar 파일만 있으면 jar로 압축된 패키지 폴더가 없어도 제대로 작동할 수 있다. src 폴더 내부의 mylib 폴더를 삭제하였다. 그 후, myJavaLibs.jar 파일을 lib 폴더 내부로 이동하였다. 해당 jar 파일을 라이브러리로 사용할 것이기 때문이다. 그러면 다음의 디렉토리 구조로 변경될 것이다. 

```java
class-path
│
├─lib
│      myJavaLibs.jar
│
└─src
        App.java
```

cmd 창의 현재 작업 디렉토리가 class-path인 상태에서, 먼저 App.java 파일을 컴파일 하려면 다음의 명령어와 경로를 입력해야 한다.

```shell
class-path>javac -cp lib/myJavaLibs.jar -d bin src/App.java
```

일반적인 상황과는 달리, jar 파일이 포함되어 있을 때는 javac 뒤에 -cp 옵션이 붙는다. 해당 jar 파일 내부에 필요한 클래스 파일(.class)이 있기에 -cp 옵션을 통해 해당 jar 파일명까지 지정해준 것이다. 일반적인 클래스 파일일 때에는 해당 파일이 포함된 폴더의 경로까지만 입력했던 것에 비해, 여기서는 jar 파일명까지 입력해야 한다. 그 뒤에 -d 옵션을 통해 src/App.java 소스 코드를 bin 폴더에 컴파일한다. 위 명령어 실행 후 class-path 폴더 내부 구조는 다음과 같이 바뀌어 있다. 

```shell
class-path
│
├─bin
│      App.class
│
├─lib
│      myJavaLibs.jar
│
└─src
        App.java
```

bin 폴더와 함께 그 안에 App.class가 생성되었다. 

컴파일한 클래스 파일을 실행할 때 다음의 명령어를 입력한다. 

```shell
class-path> java -cp lib/myJavaLibs.jar;bin App
(실행결과)
[1, 2, 3]
```

여기서도 똑같이 -cp 옵션을 통해 jar 파일 경로명을 컴파일러에게 알려준다. 그리고 App.class 파일이 포함된 bin 폴더 경로도 입력해줌으로써 모든 필요한 클래스 파일들의 경로를 알려주는 것이다. 그 뒤에 main 메서드가 포함된 App.class의 App을 입력하면 위와 같이 정상적으로 실행된다. 

## 실행 클래스 파일을 포함하여 jar 파일로 압축할 경우

앞선 예제에서 jar 파일로 압축한 것은 라이브러리였다. 즉, 자체적으로는 main 메서드가 없어 실행할 수 없고 말 그대로 외부에서 사용할 라이브러리를 jar 파일로 압축하는 과정을 살펴보았다. 이번에는 main 메서드가 포함되어 있어 자체적으로 실행할 수 있는 클래스 파일이 있을 때 이를 jar 파일로 압축하는 방법에 대해 살펴보겠다. 즉, 그 자체로 독립적인 자바 애플리케이션일 경우이다. 

먼저 “main-jar”라는 이름을 가지는 프로젝트 폴더를 다음과 같은 구조로 준비하였다. 

```shell
main-jar
│
├─bin
│      App.class
│
├─lib
└─src
        App.java
```

App.java에는 그저 “Hello, World!”를 출력하는 main 메서드가 정의되어 있다. 이 파일을 미리 컴파일하여 bin 폴더 내에 위치시켰다. 

이 App.class를 jar로 압축해보겠다. 

```shell
main-jar\bin> jar -cvf MyJavaApp.jar .
added manifest
adding: App.class(in = 461) (out= 311)(deflated 32%)
```

참고로, MyJavaApp.jar를 MyFolder라는 폴더 내에 압축을 풀어보면 다음의 구조를 띈다. 

```shell
MyFolder
│  App.class
│
└─META-INF
        MANIFEST.MF
```

MyJavaApp.jar 파일을 main-jar 바로 아래에 위치시켰다. 

```shell
main-jar
│  MyJavaApp.jar
│
├─bin
│      App.class
│
├─lib
└─src
        App.java
```

그리고 main-jar 폴더에서 cmd 창에 다음의 명령어를 입력하여 MyJavaApp.jar 내부의 App.class를 실행하려고 시도하였다. 

```shell
main-jar> java -jar MyJavaApp.jar
no main manifest attribute, in MyJavaApp.jar
```

jar 파일을 직접 실행하려면 위와 같이 `java -jar (jar파일명)` 식으로 입력해야 한다. 

그런데 위 메시지가 뜨면서 실행되지 않음을 알 수 있다. 이는 jar 파일 내부의 MANIFEST.MF 파일 내부에 “Main-Class” 속성이 존재하지 않기에 생기는 문제이다. 즉, 위 과정이 제대로 실행될려면 MANIFEST.MF 파일 내부는 다음의 내용으로 구성되어 있었어야 했다.

```
Manifest-Version: 1.0
Created-By: 21 (Oracle Corporation)
Main-Class: App
```

MANIFEST.MF

해당 파일 내부에 Main-Class 속성과 그 옆에 App 클래스를 등록해야 한다. 그래야 App 클래스 내부의 main 메서드가 실행되는 것이다. 

이번에는 MyJavaApp.jar를 삭제하고 처음부터 다시 시작해보자. 다시 main-jar/bin 폴더로 이동하여 cmd 창에서 이번에는 다음과 같이 입력해보자. 

```shell
main-jar\bin> 
jar --main-class App --create --file MyJavaApp.jar -C . App.class
```

`jar --main-class App -cvf MyJavaApp.jar .` 명령어는 작동하지 않아서 위와 같이 변경하여 진행하였다. 위 명령어의 각각의 뜻은 다음과 같다. 

- `--main-class 클래스명` : Main-Class 속성으로 지정할 클래스명을 입력한다.
- `--create` : `-c` 와 같은 역할.
- `--file jar파일명` : `-f` 와 같은 역할
- `-C (클래스파일이 포함된 폴더 경로) (클래스파일명(확장자포함))` : jar로 압축할 대상 클래스 파일의 경로 및 파일명을 입력할 때 사용. -C 처럼 대문자인 것에 주의

위 명령어를 실행하면 아까와 같이 bin 폴더 내에 MyJavaApp.jar 파일이 생성된다. 해당 파일을 아무 곳에나 압축 해제하여 보자. 그 후, MANIFEST.MF 파일을 찾아 그 내용을 보면 다음과 같다. 

```
Manifest-Version: 1.0
Created-By: 21 (Oracle Corporation)
Main-Class: App

```

MANIFEST.MF

아까와 달리 Main-Class 속성과 함께 그 속성값으로 App이란 클래스가 등록된 것을 확인할 수 있다. 

이제 MyJavaApp.jar를 실행시켜보자.

```shell
main-jar\bin> java -jar MyJavaApp.jar
Hello, World!
```

정상적으로 잘 작동함을 알 수 있다. 

# Eclipse에서 jar 파일 내보내기 및 가져와서 사용하기

이클립스 IDE를 사용하면 훨씬 간편하게 jar 파일을 내보내거나 가져와서 사용할 수 있다. 

실습을 위해, 이클립스에서 다음의 프로젝트 폴더를 준비하였다.

![1.PNG](/images/2024-08-25/java-jar-packaging/1.png)

이번에는 mylib 패키지를 이클립스에서 jar로 압축해보겠다. 

## 이클립스에서 jar 파일 압축

1. jar 파일로 압축하고자 하는 패키지가 포함된 프로젝트에 마우스 우클릭 → \[Export\]를 누른다. 
    
    ![2.png](/images/2024-08-25/java-jar-packaging/2.png)
    

1. 새 창의 목록에서 \[Java\] 폴더의 \[JAR file\]을 선택한 후 \[Next >\] 버튼을 클릭한다. 
    
    ![3.PNG](/images/2024-08-25/java-jar-packaging/3.png)
    

1. 다음 창에서, \[Select the resouces to export\] 목록을 보면 jar 파일로 압축하고자 하는 패키지가 포함된 프로젝트인 \[JarStudy\]가 선택되어 있음을 확인할 수 있다. 아래의 \[Select the export destination\] 항목에서 \[Browse\] 버튼을 누른 뒤, jar 파일을 저장할 폴더를 지정하고, 원하는 jar 파일명을 입력하면 된다. 아래 사진에서는 JarStudy 폴더 내에 MyJavaLibs.jar라는 이름으로 저장되도록 설정하였다.      
참고로, 자바 소스 코드 및 다른 리소스들까지 jar 파일로 패키징하길 원하면 \[Export Java source files and resources\] 에 체크하면 된다. 여기서는 제외하였다.      
만약 jar 파일로 패키징하고자 하는 클래스 파일들 중 하나가 main 메서드가 포함되어 있고, 이를 직접 실행할 파일이라면 여기서 \[Next\]를 누르고 다음 창에서 또 눌러서 \[JAR Manifest Specification\]이란 창으로 이동한다. 거기서 아래에 있는 \[Main Class\] 항목에 등록하고자 하는 클래스명을 등록하면 된다. 여기서는 라이브러리로 사용될 것이기 때문에 그렇게 하진 않고, 아래 사진에서 \[Finish\]를 눌렀다. 
    
    ![4.PNG](/images/2024-08-25/java-jar-packaging/4.png)
    

3-1. 참고로 \[JAR Manifest Specification\]이란 창은 다음과 같이 생겼다. 

![5.PNG](/images/2024-08-25/java-jar-packaging/5.png)

1. 그러면 앞서 설정한 이름의 .jar 파일이 생성될 것이다. 여기서는 JarStudy 프로젝트 폴더 내부에 생성하도록 했기에 다음과 같이 해당 jar 파일이 생성됨을 알 수 있다. 
    
    ![6.PNG](/images/2024-08-25/java-jar-packaging/6.png)
    

## 이클립스에서 jar 파일을 가져와 사용하기

여기서는 앞서 생성한 MyJavaLibs.jar를 사용할 것이고, 라이브러리로 사용할 것이다. 

1. 사용하고자 하는 프로젝트 폴더에 마우스 우클릭 → \[Build Path\] → \[Configure Build Path\] 클릭
    
    ![7.png](/images/2024-08-25/java-jar-packaging/7.png)
    

1. 새 창에서 \[Java Build Path\] 화면이 뜰 것이다. 만약 그렇지 않다면 왼쪽 목록에서 [Java Build Path]를 클릭하면 된다. 아래 사진에서 \[Libraries\] 탭으로 이동한 후, \[Classpath\]를 선택한다. 그 후 오른쪽의 \[Add External JARs\] 버튼을 클릭한다. 
    
    ![8.PNG](/images/2024-08-25/java-jar-packaging/8.png)
    

1. 가져오고자 하는 jar 파일을 찾아 선택한 후 \[열기\] 버튼 클릭.
    
    ![9.PNG](/images/2024-08-25/java-jar-packaging/9.png)
    

1. 그러면 다음과 같이 \[Classpath\]에 jar 파일이 추가됨을 볼 수 있다. 오른쪽의 \[Apply\] 클릭 후 \[Apply and Close\] 버튼을 클릭한다. 
    
    ![10.PNG](/images/2024-08-25/java-jar-packaging/10.png)
    

1. 그러면 다음과 같이 \[Referenced Libraries\] 라는 폴더와 함께 그 안에 앞서 추가한 jar 파일이 생성되었음을 볼 수 있다. 이로써 .jar 파일을 가져와 사용할 준비가 된 것이다. 
    
    ![11.PNG](/images/2024-08-25/java-jar-packaging/11.png)
    

이제 이 jar로 패키징된 라이브러리를 사용해보자. 정확한 실습을 위해 앞서 만든 src 폴더 내 mylib 폴더를 삭제하였다. 

App.java 파일을 킨 상태에서 키보드로 F11을 눌러보면 다음과 같이 잘 작동된다.

![12.PNG](/images/2024-08-25/java-jar-packaging/12.png)

역시 왠만하면 명령 프롬프트 창에서 직접 명령어를 입력하는 수작업 방식보다는 이클립스 같은 IDE를 사용하는게 정신적으로도 이롭다…

---

References

[1] 에이콘아카데미(강남) 강의

[2] [[Java] jar 파일이란?](https://velog.io/@wpdlzhf159/Java-jar란)

[3] [JAR vs WAR](https://goodgid.github.io/Jar-vs-War/)

[4] [cmd에서 JAVA 컴파일 및 class파일 실행(라이브러리 함께)](https://thecardeveloper.tistory.com/20)

[5] [JAR (파일 포맷)](https://ko.wikipedia.org/wiki/JAR_(파일_포맷))