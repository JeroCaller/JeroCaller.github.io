---
title: "[Java] 입출력 스트림 (I/O stream)"
category: "Java"
tag: ["java", "자바", "입출력 스트림", "i/o stream"]
---

게임을 할 때, 세이브를 위해선, 현재 상태를 파일로 생성하여 그곳에 기록하여 저장할 것이다. 또한, 게임을 할 때에도 사람의 키보드, 마우스의 입력을 통해 게임과 상호작용할 수 있으며, 게임 상태를 모니터로 출력함으로써 사람이 게임의 현재 상태를 확인할 수 있다. 이처럼, 프로그램은 보통 그 자체만으로 구동되기보다는 외부와의 데이터 입출력을 통해 상호작용하기 마련이다. 즉, 외부의 어떤 대상이 프로그램에 데이터를 입력하고, 반대로 프로그램이 외부 대상으로 데이터를 출력하기도 한다. 예를 들어 앞서 들었던 파일과의 입출력도 있을 것이고, 키보드, 모니터 입출력, 프린터 입출력 등이 있다. 이러한 입출력 방식을 입출력 모델이라 한다. 

한 편, 입출력 장치는 매우 많고 다양해서 각 장치마다의 입출력 방식들을 따르면 호환성도 떨어지고, 데이터를 입출력하는 과정도 굉장히 번거로워질 것이다. 따라서 자바에서는 입출력 장치의 종류에 상관없이, 모든 데이터 입출력을 입출력 스트림(IO stream, IO = input and output)으로 통제, 처리한다. 

참고로, 입출력 스트림은 이전에 봤던 [스트림 (stream)](/java/java-stream/) 과는 다른 개념이다. 

# 입출력 스트림 종류

입출력 스트림도 관점에 따라 여러 가지로 분류될 수 있다. (아래에 설명할 부분들은 모두 자바 응용 프로그램과 외부 입출력 장치 간의 입출력 스트림에 관한 설명이다)

- 외부 입출력 장치와 데이터를 교환하는 방향에 따라 다음과 같이 나눠진다.
    - 입력 스트림 : 외부 장치로부터 데이터를 읽어오는 스트림
    - 출력 스트림 : 외부 장치로 데이터를 출력하는 스트림.
- 데이터의 종류에 따른 구분.
    - 바이트 단위 스트림 : 음악, 이미지, 동영상 파일 등의 데이터를 입출력할 때 쓰이는 스트림. 스트림 내에서 데이터가 1byte 단위로 이동한다.
    - 문자 단위 스트림 : 문자 데이터를 위한 스트림으로, 문자는 2byte 단위로 처리한다. 문자를 바이트 단위로 처리하려고 하면 문자가 깨져서 보인다.
- 기능에 따라 다음과 같이 구분할 수 있다.
    - 기반 스트림 : 외부 장치에 직접 데이터를 읽어오거나 쓰는 스트림.
    - 보조 스트림(필터 스트림) : 기반 스트림과 달리 직접 외부 장치에 데이터를 읽고 쓰는 기능은 없고, 추가 기능만 더해주는 스트림. 항상 기반 스트림 또는 다른 보조 스트림을 생성자의 매개변수로 포함.

# 텍스트 파일 대상 입출력 스트림 생성 및 사용하기

## 파일 대상 출력 스트림 생성

이번에는 파일을 대상으로 하는 입출력 스트림에 대해 다뤄보겠다. 

다음은 텍스트 파일을 생성한 후, 텍스트 파일로의 출력 스트림을 생성한 다음, 해당 스트림을 통해 파일에 문자 하나를 쓰는 예제이다.

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

class TextFileWriteEx {
    public static void main(String[] args) throws IOException {
        OutputStream outStream = new FileOutputStream("IOStream/test.txt");

        outStream.write(67);
        outStream.close();
    }
}

```

참고로, 위 예제 코드가 작성된 자바 파일은 IOStream이라는 이름의 프로젝트 폴더명 안의 src 폴더 내부에 존재하는 상황이다. 따라서 텍스트 파일을 해당 프로젝트 루트 폴더에 생성하기 위해 위와 같이 텍스트 파일 주소를 지정하였다. 위 자바 코드를 실행하면, 콘솔창에는 아무런 메시지도 나오지 않지만, 대신 해당 루트 폴더에 “test.txt” 텍스트 파일이 생성된다. 해당 텍스트 파일을 열어보면 다음과 같이 문자 하나가 작성되어 있다. 

```java
C
```

위 예제 코드를 분석해보면 다음과 같다. 우선, 입출력 스트림을 위한 코드에서는 예외가 발생할 수 있다. 따라서 이를 메서드 내부에서 try ~ catch문을 통해 예외를 캐치할 수 있도록 하든가, 아니면 메서드 자체에 throws IOException과 같이 메서드의 시그니처 옆에 명시해줘야 한다. 만약 try ~ catch도, throws 도 작성하지 않는다면 에러가 발생한다. 

main 메서드 내부의 첫 줄에서는 파일을 대상으로 출력 스트림을 생성, 사용할 것이기에 FileOutputStream 클래스를 사용하였다. 이 클래스의 생성자에 파일명을 작성하면 해당 파일이 없을 때 새로 생성해준다. 해당 객체를 참조할 변수의 자료형은 좀 더 포괄적인 OutputStream 클래스 자료형으로 사용하였다. 그 후, 해당 참조 변수의 write() 내부에 int형 숫자를 작성함으로써, 해당 데이터를 출력 스트림으로 내보낸다. 그러면 해당 데이터는 앞서 생성된 텍스트 파일에 기록될 것이다. 참고로, write() 인자에 전달된 숫자는 ASCII 코드로 해석될 것이다. 그래서 67이란 숫자를 사용하였지만, 텍스트 파일에는 “C”라는 문자가 기록된 것이다. 

더 이상 파일에 내보낼 데이터가 없다면 close() 메서드를 통해 파일을 닫는다. 

## 입출력 스트림 예외 처리하기

이번에는 예외를 main 메서드 내부에서 try ~ catch문을 통해 직접 처리하는 코드이다. 

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;

class StringWriteWithTryCatch {
    public static void main(String[] args) {
        OutputStreamWriter textWriter = null;

        try {
            textWriter = new OutputStreamWriter(
                new FileOutputStream("IOStream/test2.txt"), "utf-8");
            textWriter.write("안녕하세요. 텍스트 파일에 저장된 텍스트입니다.");
            // close() 메서드 호출을 하지 않아 파일을 닫는 것을 깜빡했다.
        } catch (IOException e) {
            System.out.println("입출력 관련 예외 발생!");
            e.printStackTrace();
        }
        finally {
            // finally 구문을 통해 파일을 무조건 닫게끔 하고 있음.
            if (textWriter != null) {
                // close() 메서드를 호출할 때에도 예외가 발생할 수도 있다고 함.
                try {
                    textWriter.close();
                } catch (IOException e2) {
                    e2.printStackTrace();
                }
            }
        }
    }
}

```

이번 예제에서는 하나의 문자가 아닌 여러 글자들로 이루어진 문자열을 텍스트 파일에 쓰기 위해, 출력 스트림 객체를 생성하는 방법이 이전 예제와는 조금 달라졌다. 한글을 텍스트에 작성할 때 글자가 깨지지 않기 위해 utf-8 방식으로 인코딩하고자 한다. 이를 위해 보조 스트림인 OutputStreamWriter 스트림 클래스를 사용하였다. 해당 생성자의 첫 번째 인자로는 파일 출력 스트림 클래스 FileOutputStream의 인스턴스를 대입한다. 그리고 두 번째 인자로는 인코딩 방식인 “utf-8”을 문자열로 대입하였다. 그러면 이후 write() 메서드로 넘겨진 문자열이 utf-8 방식으로 인코딩되어 텍스트 파일에 저장될 것이고, 그러면 한글을 사용해도 글자가 깨져서 보이지 않을 것이다. 

한 편, 이러한 출력 스트림 생성 코드가 try 블록 내부에 존재한다. 블록 내부에 선언한 변수는 그 바깥에서 사용할 수 없으므로, 애초에 OutputStreamWriter 클래스형의 참조 변수를 try ~ catch 문 바깥에 먼저 선안하였다. 

그 후, IOException을 catch 문에서 다루도록 고안하였다. 그 후, finally 문에서 에러가 발생하여 열린 파일이 닫혀지지 않았을 때를 대비하기 위해 close() 메서드를 호출하도록 하고 있다. 

그런데 위 코드는 단지 입출력 예외를 처리하려고 했을 뿐인데 굉장히 복잡해졌다. 이러한 복잡성을 피하기 위해선 다음의 내용을 참조.

## try-with-resource 구문으로 예외 처리하기

try-with-resource 구문은 다음의 형태를 띤다.

```java
try (입출력 스트림 객체 생성) {
    // 입출력 스트림 관련 코드 작성.
} catch (IOException e) {
    // 입출력 예외 처리 코드.
}
```

try 바로 옆 괄호 안에 입출력 스트림을 생성하는 코드를 작성하면, 입출력 스트림이 생성될 뿐만 아니라, 이 입출력 스트림의 대상 파일을 이후에 자동으로 닫아준다. 따라서 이 경우, close() 메서드를 따로 호출할 필요가 없다. 

다음은 이 try-with-resource 구문을 통해 앞선 예제의 복잡한 코드를 최대한 간결하게 표현한 예제이다. 

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStreamWriter;

class ImprovedIOClose {
    public static void main(String[] args) {
        try (OutputStreamWriter output = new OutputStreamWriter(
            new FileOutputStream("IOStream/test3.txt"), "utf-8"
        )) {
            output.write("try ~ with ~ source를 적용한 텍스트");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

## 파일 대상 입력 스트림 생성

이번에는 앞선 예제들을 통해 문자열이 기록된 텍스트 파일로부터 이를 읽어오도록 해보겠다. 앞선 예제 3-1에서 생성한 test3.txt 파일을 읽어올 것이다. 해당 텍스트 파일에는 "try ~ with ~ source를 적용한 텍스트”라는 문자열이 있을 것이다. 

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;

class FileReadEx {
    public static void main(String[] args) {
        try(InputStreamReader readFile = new InputStreamReader(
            new FileInputStream("IOStream/test3.txt"), "utf-8")) {
            int ch;
            while(true) {
                ch = readFile.read();
                if(ch == -1) {
                    break;
                }
                System.out.print((char)ch);
            }
            System.out.println();  // 개행.
        } catch(IOException e) {
            System.out.println("예외 발생");
            e.printStackTrace();
        }
    }
}

```

```java
try ~ with ~ source를 적용한 텍스트
```

이번에는 파일로부터 텍스트 데이터를 읽어오는 것이기에, 입력 스트림 클래스인 FileInputStream와, 해당 텍스트를 “utf-8” 방식으로 디코딩하여 한글도 깨져서 보이지 않고 제대로 보이도록 하기 위해 보조 입력 스트림인 InputStreamReader을 사용하였다. 이후, try 구문 내에서 해당 파일 내 텍스트 데이터를 읽어오는 코드를 작성하였다. read() 메서드는 스트림 내에서 한 번에 한 글자씩만 읽어오는 메서드이다. 이 때 이를 int형으로 반환하기에, 문자로 읽기 위해서 char 자료형으로 변환시켜줘야 한다. 스트림에 더 이상 읽어올 데이터가 없다면 read() 메서드는 -1을 반환하며, 이를 이용하여 읽어올 데이터가 없으면 읽기 작업을 위한 while 반복문을 빠져나가도록 설계하였다. 

## 입출력 스트림을 이용하여 파일 복사하기

앞서 살펴본 내용들을 이용하면 하나의 파일 내부에 있는 내용을 다른 파일에 복사하는 기능도 구현해볼 수 있다. 다음이 그 예제이다. 

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.time.Duration;
import java.time.Instant;

class FileCopyEx1 {
    public static void main(String[] args) {
        String src = "IOStream/src/FileReadEx.java";
        String dest = "IOStream/test3.txt";

        try (InputStreamReader reader = new InputStreamReader(
                new FileInputStream(src), "utf-8");
            OutputStreamWriter writer = new OutputStreamWriter(
                new FileOutputStream(dest), "utf-8")
            ) {
                // 복사 작업 시간을 구하기 위한 용도. 
                // 시작 시간 기록.
                Instant timeStart = Instant.now();

                // 복사 작업.
                int data;
                while(true) {
                    data = reader.read();
                    if(data == -1) {
                        break;
                    }
                    writer.write(data);
                }

                Instant timeEnd = Instant.now();
                System.out.println("복사 시간 : " + 
                Duration.between(timeStart, timeEnd).toMillis() + "초");
        } catch(IOException e) {
            System.out.println("에러 발생");
            e.printStackTrace();
        }
    }
}

```

```java
복사 시간 : 1초
```

위 예제에서는 복사하는 데 걸리는 시간도 측정하기 위해 Instant, Duration 클래스도 활용하고 있다. 

위 예제는 입출력 스트림 내에서 데이터를 한 바이트씩 읽고 쓴 다음, 다시 스트림으로부터 한 바이트의 데이터를 얻어와 읽고 쓰는 작업을 반복하는 형식이다. 이렇게 입출력이 너무 많이 발생하면 작업 속도도 오래 걸린다. 그래서 대신, 스트림으로부터 데이터를 한 꺼번에 얻어오고 이를 사용하는 방식이 더 좋을 것이다. 이를 위해, 스트림으로부터 오는 데이터들을 한꺼번에 저장하는 임시 공간인 버퍼(buffer)를 이용하여 입출력 횟수, 작업 시간을 단축한다. 

다음은 파일 복사 예제를 버퍼를 이용하여 수행하는 예제 코드이다.

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.time.Duration;
import java.time.Instant;

class FileCopyEx2 {
    public static void main(String[] args) {
        String src = "IOStream/src/FileReadEx.java";
        String dest = "IOStream/test3.txt";

        try(InputStream reader = new FileInputStream(src);
            OutputStream writer = new FileOutputStream(dest)) {
            byte[] buf = new byte[1024]; // 1kb 크기의 버퍼
            int len;
            Instant timeStart = Instant.now();

            while(true) {
                len = reader.read(buf);
                if (len == -1) {
                    break;
                }
                writer.write(buf, 0, len);
            }

            Instant timeEnd = Instant.now();
            System.out.println("복사 시간 : " + 
            Duration.between(timeStart, timeEnd).toMillis() + "초");
        } catch(IOException e) {
            System.out.println("에러 발생");
            e.printStackTrace();
        }
    }
}

```

```java
복사 시간 : 0초
```

위 예제에서는 버퍼를 이용하여 복사 작업을 수행하기 위해, 1024 길이의 byte 배열 변수를 선언하였다. 이 변수가 버퍼 역할을 할 것이며, 한 번에 최대 1024 byte = 1KB의 데이터 용량을 받아서 입출력을 할  수 있게 하였다. 이렇게 버퍼를 이용할 때의 write() 메서드에 대입할 인자의 형태가 아까와는 달라진다. 첫 번째 인자에는 버퍼, 즉 바이트 배열을 대입한다. 두 번째 인자로는 해당 바이트 배열의 어느 위치부터 파일에 쓸 것인지 그 인덱스를 넘긴다. 그리고 세 번째 인자로는 두 번째 인자로 넘긴 인덱스로부터, 바이트 배열 내 어디까지를 파일에 쓸 것인지 그 길이를 넘기면 된다. 여기서는 바이트 배열의 처음부터 끝까지의 모든 데이터들을 파일에 작성할 것이기에 두 번째 인자는 0, 세 번째 인자는 바이트 배열의 전체 크기인 len을 대입하고 있다. 참고로, read(buf) 메서드에서는 인자로 넘긴 바이트 배열에 입출력 스트림으로부터 그 바이트 배열의 크기만큼의 데이터를 받아오고, 그 바이트 배열의 크기를 int형으로 반환하는 구조이다. 이를 len 변수에 할당하여 사용하는 방식이다. 

## 버퍼링 기능을 제공하는 보조 스트림 사용하기

방금 예제에서는 버퍼를 이용한 파일 복사를 위해, 버퍼 역할을 할 바이트 배열 변수를 선언하고 이를 이용하였다. 하지만 BufferedInputStream, BufferedOutputStream 보조 스트림을 이용하면 굳이 개발자가 버퍼를 위한 바이트 배열 변수를 선언하여 사용할 필요없이 버퍼를 이용할 수 있게 된다. 다음은 이 버퍼 보조 스트림을 이용하여 이전의 파일 복사 예제를 구현해본 에제이다. 

```java
import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.time.Duration;
import java.time.Instant;

class FileCopyWithBuffer {
    public static void main(String[] args) {
        String src = "IOStream/src/FileReadEx.java";
        String dest = "IOStream/test3.txt";

        try(BufferedInputStream reader = new BufferedInputStream(
            new FileInputStream(src));
            BufferedOutputStream writer = new BufferedOutputStream(
                new FileOutputStream(dest))
        ) {
            Instant timeStart = Instant.now();

            // buffer 역할을 할 byte[] 배열 변수를 선언할 필요가 없다.
            
            int data;
            while(true) {
                data = reader.read();
                if(data == -1) {
                    break;
                }
                writer.write(data);
            }

            Instant timeEnd = Instant.now();
            System.out.println("복사 시간 : " + 
            Duration.between(timeStart, timeEnd).toMillis() + "초");
        } catch(IOException e) {
            System.out.println("에러 발생");
            e.printStackTrace();
        }
    }
}

```

```java
복사 시간 : 0초
```

위 예제를 보면, Buffered로 시작되는 버퍼 보조 스트림을 이용하니, 개발자가 더 이상 버퍼용 바이트 배열을 선언하여 사용할 필요가 없게 되었다. 그리고 보조 스트림은 위 코드에서처럼 그 생성자에 사용할 기반 스트림을 인자로 넘기는 구조로 사용하는 것임을 알 수 있다 

> 복사한 파일의 내용이 적게 있어서 복사 시간에 차이가 거의 안나는 것처럼 보인다. 복사할 파일의 용량이 크면 아마 버퍼 유무의 차이가 클 것이다.
> 

## 문자 스트림

여태까지의 예제들에서는 FileInputStream 등의 바이트 스트림을 이용하여 텍스트 파일에 텍스트 데이터를 읽고 쓰는 방식이었다. 여기서 한글 텍스트도 제대로 쓰고 읽는 것을 가능케하기 위해, 보조 스트림을 사용하여 생성자에 인코딩 방식(”utf-8”)을 문자열 형태로 전달하는 방식을 사용하였다. 그런데 사실 자바에서는 문자 스트림도 따로 제공한다. 대표적으로 FileReader, FileWriter가 있다. char형 문자뿐만 아니라 문자열도 작성 가능하다. 그러나 이러한 문자 스트림은 그 자체로는 ‘utf-8’ 방식의 인코딩을 지정하여 사용할 수 없어 한글을 파일에 쓰고 읽을 때 글자가 깨지는 현상이 발생한다. “스택오버플로우”에 따르면[1], [2] ‘utf-8’ 인코딩 방식을 적용하여 파일을 읽고 쓰고자 한다면 문자 스트림보다는 앞선 에제들에서 사용해봤던 바이트 스트림과 보조 스트림을 혼합하여 사용하는 것을 추천하고 있다. 따라서 여기서는 FileReader, FileWriter라는 문자 스트림이 있다는 것만 언급하고 따로 예제로 살펴보지 않겠다. 

# 자바 객체 데이터를 입출력 스트림을 통해 읽고 쓰기

자바에서는 객체 데이터를 바이트 형태로 변환시킨 후에 외부 입출력 장치와 이를 전송, 교환할 수 있다고 한다. 이 때, 자바 내의 객체 데이터를 바이트 형태로 변환시키는 것을 직렬화(serialization), 그 반대 과정을 역직렬화(deserialization)라고 한다. 

이 직렬화를 위해, 자바에서 다음의 스트림 클래스들을 사용할 수 있다. 

- `ObjectInputStream(InputStream in)` : InputStream 객체를 생성자의 인자로 받는다.
- `ObjectOutputStream(OutputStream out)` : OutputStream 객체를 생성자의 인자로 받는다.

자바에서 특정 객체를 직렬화할 때에는 해당 객체 내에 private 접근 제한자로 선언한 멤버가 있다고 해도 이 멤버도 직렬화되어 외부 입출력 장치로 전달된다. 즉, private 내용도 외부로 유출되는 것이다. 입출력 장치를 통해 외부로 전달되어선 안되는 private 멤버를 가지고 있는 클래스와, 직렬화를 해도 되는 클래스 간 구분을 위해, 직렬화하고자 하는 클래스에는 직렬화 대상 클래스라고 표시를 해줘야 한다. 그래야 엉뚱한 클래스를 직렬화하지 않을 것이다. 자바에서는 java.io.Serializable 인터페이스를 이용하여 직렬화 대상 클래스가 이를 구현받도록 하면 된다. 해당 인터페이스는 그저 이를 구현하는 클래스가 직렬화를 하고자 하는 클래스라는 것을 표시하기 위한 용도이기에, 따로 구현할 추상 메서드를 보유하고 있지 않다. 이러한 인터페이스를 마커 인터페이스(marker interface)라 부른다. 

> 직렬화 대상 클래스에 java.io.Serializable 마커 인터페이스를 구현하도록 하지 않고 그대로 객체 스트림을 통해 직렬화를 시도하면 에러가 발생하니 반드시 직렬화 대상 클래스에 해당 마커 인터페이스를 구현받도록 해야 한다.
> 

다음은 사이트 유저의 닉네임과 고유 번호를 담는 유저 클래스를 정의한 후, 이를 직렬화하여 .bin 확장자 파일에 이진 파일 형태로 저장하는 예제이다. 

```java
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;
import java.io.Serializable;

class User12 implements Serializable {
    private String nickName;
    private int uniqueNumber;

    User12(String nickName, int uniqueNumber) {
        this.nickName = nickName;
        this.uniqueNumber = uniqueNumber;
    }

    String getNickName() {
        return this.nickName;
    }

    int getUniqueNumber() {
        return uniqueNumber;
    }

    void printProfile() {
        System.out.println("=== 사용자 정보 ===");
        System.out.println("닉네임 : " + this.nickName);
        System.out.println("고유번호 : " + this.uniqueNumber);
        System.out.println("====================");
    }
}

class SerializeObjectEx {
    public static void main(String[] args) {
        User12 foodSiteUser = new User12("bowWow123", 1523);
        User12 beautySiteUser = new User12("great230", 39);

        try(FileOutputStream fileout = new FileOutputStream("IOStream/obj-data.bin");
            ObjectOutputStream objoutStream = new ObjectOutputStream(fileout)) {
            objoutStream.writeObject(foodSiteUser);
            objoutStream.writeObject(beautySiteUser);
        } catch(IOException e) {
            System.out.println("=== 입출력 관련 예외 발생 ===");
            e.printStackTrace();
        }
    }
}

```

위 예제를 실행할 때, 예외가 발생하지 않으면 콘솔 창에 아무것도 출력되지 않는 대신, 지정한 곳에 .bin 확장자 파일이 생성된다. 해당 확장자를 가진 파일은 일반적인 텍스트 에디터로는 그 내용을 확인할 수 없다. 

위 코드에 따르면, ObjectOutputStream 객체의 writeObject() 메서드에 직렬화하고자 하는 객체를 인자로 전달하면 해당 객체가 직렬화되어 대상 파일에 저장된다. 

이번에는 .bin 파일에 저장된 객체 데이터를 역직렬화하여 자바 객체 데이터로 읽어오는 예제이다. 

```java
import java.io.FileInputStream;
import java.io.IOError;
import java.io.IOException;
import java.io.ObjectInputStream;

class DeserializeObjectEx {
    public static void main(String[] args) {
        try(FileInputStream filein = new FileInputStream("IOStream/obj-data.bin");
            ObjectInputStream objinStre = new ObjectInputStream(filein)) {
            User12 foodSiteUser = (User12)objinStre.readObject();
            System.out.println(foodSiteUser.getNickName());
            System.out.println(foodSiteUser.getUniqueNumber());
            foodSiteUser.printProfile();

            User12 beautySiteUser = (User12)objinStre.readObject();
            System.out.println(beautySiteUser.getNickName());
            System.out.println(beautySiteUser.getUniqueNumber());
            beautySiteUser.printProfile();
        } catch(IOException e) {
            System.out.println("=== 입출력 예외 발생 ===");
            e.printStackTrace();
        } catch(ClassNotFoundException e) {
            System.out.println("대상 클래스 발견 못함.");
            e.printStackTrace();
        }
    }
}

```

```java
bowWow123
1523
=== 사용자 정보 ===
닉네임 : bowWow123
고유번호 : 1523
====================
great230
39
=== 사용자 정보 ===
닉네임 : great230
고유번호 : 39
====================
```

외부 입출력 대상으로부터 자바 객체 데이터를 역직렬화하여 읽어들여오기 위해 ObjectInputStream 객체의 readObject() 메서드를 이용하면 해당 객체를 반환받을 수 있다. 이 때, 객체 데이터이므로, 강제 형변환이 필요하다. 이 객체에서 내부에 정의된 멤버 변수 및 메서드들을 그대로 이용할 수 있다. 

---
References

[1] [How to write a UTF-8 file with Java?](https://stackoverflow.com/questions/1001540/how-to-write-a-utf-8-file-with-java)

[2] [How to read/write a file in UTF-8 in Java?](https://stackoverflow.com/questions/13350676/how-to-read-write-a-file-in-utf-8-in-java)

[3] [[JAVA] 파일 입출력   / 파일 입출력 한글 깨짐 현상 "UTF-8"](https://blog.naver.com/software705/220587262406)

[4] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)