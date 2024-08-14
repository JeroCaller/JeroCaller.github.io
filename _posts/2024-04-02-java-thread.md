---
title: "[Java] 스레드 (Thread)"
category: "Java"
tag: ["java", "자바", "스레드", "thread"]
---

# 스레드 개념

- 프로세스(process) : 운영체제에서 실행 중인 프로그램 및 애플리케이션.
- 멀티태스킹(multi-tasking) : 둘 이상의 프로세스 또는 작업을 동시에 처리하는 것.

윈도우, 맥OS, 리눅스 등의 현대 운영체제에서는 여러 개의 프로그램, 즉 여러 개의 프로세스를 동시에 실행시키는 멀티태스킹이 가능하다. 예를 들면 계산기로 계산을 하면서 동시에 웹 브라우저에서 검색을 할 수 있고, 동시에 메모장을 켜서 메모를 할 수 있는 것이다. 

각 프로세스는 자신만의 메모리 및 자원을 할당받아 사용한다. 그래서 한 프로세스가 다른 프로세스의 메모리 및 자원에 간섭할 수 없다. 즉, 프로세스는 다른 프로세스에 대해 독립적이다. 

- 스레드(thread) : 하나의 프로세스 안에 존재하는 하나의 실행 흐름.

운영체제 내에서 여러 개의 프로세스를 동시에 실행할 수 있듯, 프로세스 내에서도 여러 개의 스레드를 가져 동시에 각자의 작업을 수행하도록 할 수 있다. 프로세스 자체가 어떠한 작업을 수행하기 위해 존재하는 것이므로, 하나의 프로세스에는 적어도 하나의 스레드가 존재한다. 

스레드도 마찬가지로, 프로세스 내에서 각자 자신만의 자원을 가져 독립적으로 실행된다. 

자바로 만든 앱의 경우, JVM에서 동작한다. 이 때, JVM은 운영체제 위에서 동작한다. 그리고 자바 앱을 둘 이상 동시에 실행시킬 경우, 각각의 앱에 각자의 JVM을 가져 그 위에서 앱을 실행시킬 수 있다. 즉, 하나의 JVM은 하나의 앱을 실행시킬 수 있다. 그리고 하나의 앱 내부에서 여러 개의 스레드를 가질 수 있어 동시에 여러 작업을 진행시킬 수 있다. 

자바로 코드를 작성하고 이를 실행시킬 때에도 사실 적어도 하나의 스레드가 존재하여 특정 작업을 실행하는 방식이었다. 다음의 코드를 보겠다.

```java
class MainThread {
    public static void main(String[] args) {
        String threadName = Thread.currentThread().getName();
        System.out.println("현재 스레드 이름 : " + threadName);
    }
}
```

```java
현재 스레드 이름 : main
```

위 코드를 실행할 때, main이라는 이름의 하나의 스레드로 작업을 수행하는 것이었다. 

이 페이지에서는 개발자가 자바에서 직접 스레드를 생성하고, 그 스레드가 특정 작업을 처리하도록 하는 방법에 대해 살펴보겠다. 이를 알게되면 하나의 프로그램 내부에서 둘 이상의 스레드를 생성시켜 여러 작업들을 동시에 수행하게 할 수 있을 것이다. 이러한 스레드의 특성을 이용하면 여러 작업들을 처리할 때 하나의 작업을 모두 처리해야만 다음 작업을 처리할 수 있었던 기존 방식을 사용할 때 걸리는 시간을 단축시킬 수 있을 것으로 기대된다.

# 자바에서 스레드 생성 및 실행하기

자바에서 스레드를 생성 및 작성할 수 있는 방법에는 2가지가 있다. 

- Thread 클래스를 상속받아 run() 메서드를 오버라이딩하여 실행시키고자 하는 작업을 코드로 작성하는 방법.
- Runnable 인터페이스의 run() 메서드를 구현하는 방법.

참고로 Thread, Runnable 모두 java.lang 패키지에 포함되어 있어 따로 import 할 필요가 없다. 

다음은 Thread 클래스를 상속받아 스레드를 생성 및 실행시키는 예제이다.

```java
class MyThread extends Thread {
    public int factorial(int n) {
        if (n < 0) {
            // 음수의 팩토리얼은 이 메서드에서는 구할 수 없음.
            return -1;
        }
        return (n == 1 || n == 0) ? 1 : n * factorial(n-1);
    }

    @Override
    public void run() {
        int total = factorial(5);
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName + " : " + total);
    }
}

class ThreadInheritance {
    public static void main(String[] args) {
        MyThread mt = new MyThread();
        String mainThread = Thread.currentThread().getName();
        mt.start();
        System.out.println("main : " + mainThread);
    }
}

```

```java
main : main
Thread-0 : 120
```

스레드를 사용하기 위해선 우선 스레드 내에서 실행시키기를 원하는 코드를 run() 메서드를 오버라이딩하여 그 내부에 작성한다. 위 예제에서는 정수를 받으면 그에 대한 팩토리얼을 구하는 메서드를 따로 정의한 후, run() 메서드 내부에서 이를 호출하는 형식으로 사용하고 있다. 이 스레드를 실행시킬 때에는 우선 Thread 클래스를 상속받는 자식 클래스를 인스턴스화한 후, 해당 객체의 start() 메서드를 호출한다. 여기서 주의할 것은, run() 메서드를 직접 호출하는 것이 아니라는 것이다. run() 메서드를 정의하면 이후 스레드 실행 시 start() 메서드를 대신 호출하여 run() 메서드가 실행되도록 하는 방식이라고 한다. 

위 코드의 main 메서드 내부를 보면, 개발자가 정의한 MyThread() 클래스의 스레드의 실행을 명령하는 start() 코드가 먼저 작성되어 있다. 코드 순서로만 보면, 해당 스레드의 작업이 모두 끝난 후에 그 다음 코드가 실행되어야 할 것으로 생각할 수 있다. 이 논리에 따르면 출력결과는 다음과 같이 나왔어야 했다. 

```java
Thread-0 : 120
main : main
```

하지만, 위 예제 코드에서는 main 스레드와 MyThread 클래스로 실행되는 스레드 이렇게 두 스레드가 동시에 실행되는 방식이다. 이 때, MyThread 스레드의 작업 완료 시간이 더 오래 걸리면 상대적으로 더 빨리 작업이 끝나는 다른 스레드의 결과물이 먼저 보일 수 밖에 없다. 하나의 스레드의 작업이 끝난 후에 다른 스레드의 작업이 시작되는 순차적 방식이 아닌, 동시에 여러 스레드들이 각자의 작업을 처리하는 개념이기 때문이다. 그러므로 스레드를 다루는 코드에서는 코드 순서에 맞게 반드시 여러 개의 스레드들의 작업도 그 순서대로 완료된다는 보장이 없음을 알아두는 것이 추후 스레드를 다루는 코드 작성 시 헷갈리지 않을 것이다. 

메인 메서드의 실행이 끝나면 프로그램 자체도 종료된다. 따라서, 메인 메서드 내 코드들이 끝까지 실행되었더라도 중간에 다른 스레드가 작업 중이라면 메인 메서드의 종료는 지연된다. 

이번에는 Runnable 인터페이스를 이용하여 스레드를 생성 및 실행하는 예제이다. 

```java
class MyThread2 implements Runnable {
    public long factorial(int n) {
        if (n < 0) {
            // 음수의 팩토리얼은 이 메서드에서는 구할 수 없음.
            return -1;
        }
        long total = 1;
        for(int i = 2; i <= n; i++) {
            total *= i;
        }
        return total;
    }
    
    @Override
    public void run() {
        long total = factorial(5);
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName + " : " + total);
    }
}

class ThreadWithRunnable {
    public static void main(String[] args) {
        Thread mt = new Thread(new MyThread2());
        String mainThread = Thread.currentThread().getName();
        mt.start();
        System.out.println("main : " + mainThread);
    }
}

```

```java
main : main
Thread-0 : 120
```

Runnable 인터페이스를 이용해서도 스레드를 생성 및 실행시킬 수 있다. 다만, 이전의 Thread 클래스 상속 방식과 다른 점은, 스레드 클래스를 인스턴스화할 때, Thread 클래스의 인스턴스의 생성자에 Runnable 인터페이스를 구현하는 클래스의 인스턴스를 인자로 대입하여 Thread 객체를 생성한 후, 이 객체를 이용하여 스레드를 실행해야한다는 점이다. 

한 편, 스레드를 구현하는 클래스 내부에 run() 메서드만 존재한다면 이를 람다식으로 구현하여 코드를 더 간결하게 할 수도 있다. 

```java
class RunnableWithLambda {
    public static void main(String[] args) {
        Runnable factorialTask = () -> {
            long total = 1;
            long n = 5;
            for(long i = 2; i <= n; i++) {
                total *= i;
            }
            String threadName = Thread.currentThread().getName();
            System.out.println(threadName + " : " + total);
        };

        Thread mt = new Thread(factorialTask);
        String mainThread = Thread.currentThread().getName();

        mt.start();
        System.out.println("main : " + mainThread);
    }
}

```

```java
main : main
Thread-0 : 120
```

# 여러 스레드 동시 실행

다음은 여러 개의 스레드들이 동시에 실행된다는 것을 확인하기 위한 예제이다. 두 스레드를 생성 및 실행시키는 예제이다. 

```java
class TwoTasks {
    public static void main(String[] args) {
        Runnable thread1 = () -> {
            String threadName = Thread.currentThread().getName();
            int n = 10;
            for(int i = 0; i < n; i++) {
                System.out.printf("From %s : %d\n", threadName, i);
            }
        };

        Runnable thread2 = () -> {
            String threadName = Thread.currentThread().getName();
            int n = 10;
            for(int i = n; i > 0; i--) {
                System.out.printf("From %s : %d\n", threadName, i);
            }
        };

        Thread t1 = new Thread(thread1);
        Thread t2 = new Thread(thread2);

        t1.start();
        t2.start();
    }
}

```

```java
From Thread-0 : 0
From Thread-0 : 1
From Thread-1 : 10
From Thread-1 : 9
From Thread-0 : 2
From Thread-0 : 3
From Thread-0 : 4
From Thread-1 : 8
From Thread-1 : 7
From Thread-0 : 5
From Thread-0 : 6
From Thread-0 : 7
From Thread-1 : 6
From Thread-1 : 5
From Thread-0 : 8
From Thread-0 : 9
From Thread-1 : 4
From Thread-1 : 3
From Thread-1 : 2
From Thread-1 : 1
```

한 스레드의 작업이 끝나야 다른 스레드가 작업을 할 수 있는 순차적 방식이 아니라, 여러 스레드가 동시에 작업을 실행하는 구조이기에 위 예제의 실행결과처럼 두 스레드 간 출력 결과가 서로 섞이는 것을 볼 수 있다. 

# 스레드 동기화

각 스레드는 각자의 메모리 영역을 할당받아 독립적이라고 하였다. 하지만 프로세스 내 스레드들은 공유 자원을 사용할 수 있다. 자바에서 static으로 선언된 전역 변수가 그 예이다. 스레드들은 이 공유 자원에 접근하여 값을 변경시키고 그 변경된 값을 다시 저장하도록 할 수 있다. 그러나 이 경우 문제가 발생할 수 있다. 둘 이상의 스레드가 동시에 공유 자원에 접근하여 그 값을 변경하려고 할 때이다. 

예를 들어, A 스레드가 1로 초기화되어 있는 전역 변수의 값을 가져와 그 값을 1 증가시키는 연산을 하였다고 가정해보자. 이 과정에서 해당 스레드는 해당 변수값을 토대로 연산을 하기 위해선 우선 전역 변수값을 불러와 CPU에서 연산 작업을 수행해야 한다. 연산이 끝나면 변경된 값을 다시 전역 변수에 저장하는 형태이다. 그런데, A 스레드가 해당 전역 변수의 값을 가져와 CPU에 연산을 실행시키는 도중에 B 스레드가 해당 전역 변수에 접근한다고 해보자. 그러면 여전히 전역 변수값은 1인 상태로 B 스레드에 그 값을 넘기는 것이다. 이 때 B 스레드에서 1을 감소시키는 연산을 한다고 해보자. 그리고 그 사이에 A 스레드에서의 연산 작업이 끝나 전역 변수값이 2로 변경되었다고 해보자. 이 사이에 B 스레드는 그제서야 연산 작업이 끝났고, 그 결과값인 0이 나왔다. 이를 뒤늦게 전역 변수값에 갱신시켰다. 그 결과, 최종적으로 전역 변수에 0이 저장된다. 원래대로라면 초기값 1에 대해 1 + 1 - 1 = 1이 되어야 한다. 그런데 두 스레드가 동시에 공유 자원에 접근, 변경을 하다보니 의도치 않은 데이터가 저장되는 것이다. 

이렇게 여러 스레드들이 공유 자원에 접근해야 하는 경우, 아무런 조치를 취하지 않으면 여러 스레드들이 동시에 공유 자원에 접근하여 그 데이터가 원치 않은 방향으로 오염될 수 있으며, 이는 버그의 원인이 될 수 있다. 이를 방지하기 위한 방법으로 동기화(synchronization)를 사용한다. 동기화는 여러 스레드들이 공유 자원에 동시에 접근하도록 두는 것이 아니라, 하나의 스레드가 공유 자원에 접근하여 사용하고 있으면 다른 스레드들은 이 작업이 끝날 때까지 기다리도록 한 다음, 해당 스레드가 공유 자원의 사용을 마치면 그 다음 스레드가 접근 가능하게 하도록 하는 순차적 방식을 적용시키는 것이다. 공중화장실의 한 칸은 한 번에 단 한 사람만 사용할 수 있는 것과 같은 이치이다. 

자바에서는 스레드 동기화를 위해 다음의 두 가지 방법들을 고려해볼 수 있다. 

- 메서드 동기화 : 메서드 선언부에서, 반환 타입명 앞에 synchronized 키워드를 삽입한다. 그러면 해당 메서드를 동기화하여 사용할 수 있게 된다.

```java
public synchronized int someMethod() {}
```

- 블록 동기화 : 동기화하고자 하는 블록에 synchronized 키워드를 작성하는 방법. 이 방법의 경우, synchronized 키워드 뒤의 괄호 안에는 여러 스레드들이 같이 사용할 공유 데이터를 작성한다. 메서드 내의 일부 코드만 동기화하고자 할 때 사용할 수 있는 방법.

```java
public int someMethod() {
    synchronized (공유 객체) {
        // 동기화하고자 하는 코드 작성.
    }
}
```

다음은 동기화를 하지 않았을 때, 두 스레드가 하나의 전역 변수의 값을 1씩 증감시키려고 하는 예제이다. 

```java
public class ThreadsUsingCommonData {
    public static int hp = 1;

    public static void main(String[] args) throws InterruptedException {
        int endLimit = 100000;

        Runnable heal = () -> {
            for(int i = 0; i < endLimit; i++) {
                hp++;
            }
        };
        
        Runnable attack = () -> {
            for(int i = 0; i < endLimit; i++) {
                hp--;
            }
        };

        Thread healThread = new Thread(heal);
        Thread attackThread = new Thread(attack);

        healThread.start();
        attackThread.start();

        // Thread의 종료를 기다린다. 
        // 이로 인해 그 뒤의 코드는 thread들이 종료되어야 실행될 수 있다.
        healThread.join();
        attackThread.join();

        System.out.println(hp);
    }
}

```

```java
3861
```

처음에 전역 변수인 hp의 값은 1이다. 그리고 두 스레드 heal, attack은 총 100000번의 반복문을 돌아 각각 hp의 값을 1씩 증가, 감소시키는 작업을 내포하고 있다. 이 경우, 원래 의도대로라면 1 + 100000 - 100000 = 1로, 전역 변수의 최종값은 그대로 1이 되어야 한다. 그러나 앞서 얘기했던 문제점에 의해 전혀 엉뚱한 결과가 나온 것을 볼 수 있다. 

다음은 이러한 문제를 해결하기 위해 앞서 소개한 동기화로 문제를 해결하는 예제이다. 

```java
public class syncTwoThreads {
    public static int hp = 1;

    public static synchronized void doHeal() {
        hp++;
    }

    public static synchronized void doAttack() {
        hp--;
    }

    public static void main(String[] args) throws InterruptedException {
        int endLimit = 100000;

        Runnable heal = () -> {
            for(int i = 0; i < endLimit; i++) {
                doHeal();
            }
        };

        Runnable attack = () -> {
            for(int i = 0; i < endLimit; i++) {
                doAttack();
            }
        };

        Thread healThread = new Thread(heal);
        Thread attackThread = new Thread(attack);

        healThread.start();
        attackThread.start();

        // Thread의 종료를 기다린다. 
        // 이로 인해 그 뒤의 코드는 thread들이 종료되어야 실행될 수 있다.
        healThread.join();
        attackThread.join();

        System.out.println(hp);
    }
}

```

```java
1
```

이전 예제와 달리, 이번에는 예상했던 결과값대로 나왔다. 

동기화할 때 한 가지 문제점은, 하나의 공유 자원에 대해 여러 스레드들이 차례대로 한 번에 한 스레드씩만 접근하게 할 경우, 이 스레드들이 너무 많으면 병목 현상이 발생하여 전체적인 프로그램 실행 속도가 느려질 수도 있다. 

# ReentrantLock : 원하는 구간만 명시적으로 동기화하기

java.util.concurrent.locks의 ReentrantLock 클래스를 이용하면 synchronized 키워드와 똑같은 동기화 효과를 내면서도, 원하는 구간에만 명시적으로 동기화를 하겠다는 표시도 할 수 있다. 

- `ReentrantLock.lock()` : 동기화 시작점 지정.
- `ReentrantLock.unlock()` : 동기화 마지막 시점 지정.

다음은 이전 예제를 ReentrantLock 클래스로 구현해본 예제이다. 

```java
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockEx {
    public static ReentrantLock threadLock = new ReentrantLock();
    public static int hp = 1;

    public static void doHeal() {
        threadLock.lock();
        hp++;
        threadLock.unlock();
    }

    public static void doAttack() {
        threadLock.lock();
        hp--;
        threadLock.unlock();
    }

    public static void main(String[] args) throws InterruptedException {
        int endLimit = 100000;

        Runnable heal = () -> {
            for(int i = 0; i < endLimit; i++) {
                doHeal();
            }
        };

        Runnable attack = () -> {
            for(int i = 0; i < endLimit; i++) {
                doAttack();
            }
        };

        Thread healThread = new Thread(heal);
        Thread attackThread = new Thread(attack);

        healThread.start();
        attackThread.start();

        // Thread의 종료를 기다린다. 
        // 이로 인해 그 뒤의 코드는 thread들이 종료되어야 실행될 수 있다.
        healThread.join();
        attackThread.join();

        System.out.println(hp);
    }
}

```

```java
1
```

# 스레드 풀 (Thread pool)

스레드 수가 많아질 수록 스레드의 생성 및 삭제 작업은 생각보다 CPU, 메모리 등의 자원들에 많은 부담을 주며, 실행 시간 또한 늦추는 원인이 된다고 한다. 또한, 앞서 언급했듯, 각 스레드들은 각자의 메모리 영역을 차지하고 이를 사용하기에, 스레드들을 무한정 생성할 수 없다. 

이러한 스레드의 자원 소모 및 성능 문제를 해결하려면, 최대한 같은 스레드를 재활용하도록 그 스레드 수를 제한하고, 그 스레드들의 생성, 소멸 작업을 최소화하는 것이 좋을 것이다. 또한, 동시에 실행할 스레드의 수도 제한시키는 것이 좋다. 

이를 위한 방법으로 스레드 풀이라는 방법이 있다. 이는 제한적인 개수의 스레드들을 미리 생성시켜놓고 실행할 작업들을 각각의 스레드에 할당시켜 병렬적으로 작업들을 실행한 후, 스레드가 작업을 끝내면 그 스레드를 삭제시키지 않고 다음 작업을 할당받을 수 있도록 대기시키는 방법이다. 

![사진 1. 스레드 풀 개념도. [1]](https://upload.wikimedia.org/wikipedia/commons/thumb/0/0c/Thread_pool.svg/600px-Thread_pool.svg.png)

사진 1. 스레드 풀 개념도. [1]

자바에서는 스레드 풀 사용 시 이에 사용되는 스레드들을 JVM에 맡겨 관리하도록 한다. 그리고 이러한 스레드 풀을 java.util.concurrent 패키지의 ExecutorService, Executors 클래스를 통해 사용할 수 있다. 

자바에서 사용할 수 있는 스레드 풀 유형에는 다음이 있다. 

- newSingleThreadExecutor : 스레드 풀 내에 하나의 스레드만 생성 및 유지시킨다. 스레드가 하나 뿐이기에, 하나의 작업이 완료되어야 그 다음 작업을 실행할 수 있는 구조이다. 둘 이상의 스레드들을 동시에 실행시키는 방법이 아니므로, 동기화를 고려할 필요가 없다.
- newFixedThreadPool : 스레드 풀 내부에 생성 및 유지시킬 스레드들의 수를 지정하여 해당 개수만큼의 스레드들을 생성, 유지시킨다. 작업 중이지 않은 스레드는 삭제되지 않고 그대로 유지하며 다음에 올 수도 있는 작업을 위해 대기한다.
- newCachedThreadPool : 스레드가 일정 시간동안 어떠한 적업도 하지 않으면 그 스레드를 삭제하여 스레드 수를 작업 수에 맞춰 유연하게 관리하는 방법이다.

위 스레드 풀들을 사용하는 형태는 다음과 같다. 

```java
// 스레드 풀 변수 선언 및 초기화.
// 스레드 개수를 지정할 수 있는 경우, 생성자에 스레드 수를 지정한다. 
ExecutorService 스레드풀변수명 = Executors.newFixedThreadPool(3);

스레드풀변수명.submit(thread);  // 스레드 풀에 작업 전달.

// 스레드 풀 및 내부의 스레드 전체 삭제.
// 더 이상 스레드 풀로 할 작업이 없을 때 호출.
스레드풀변수명.shutdown();
```

다음은 단 2개의 스레드만 생성, 유지시키는 스레드 풀을 생성한 후, 3개의 작업을 해당 스레드 풀에 전달하여 처리하도록 하는 예제이다. 이 때, 각 3개의 작업은 그 처리 시간이 각각 다르다. 

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

class ThreadPoolEx {
    public static int getTotal(int n) {
        int total = 0;
        for(int i = 1; i <= n; i++) {
            total += i;
        }
        return total;
    }

    public static void main(String[] args) {
        Runnable shortTask = () -> {
            String threadName = Thread.currentThread().getName();
            int total = getTotal(10);
            System.out.println(threadName + " : " + total);
        };

        Runnable midTask = () -> {
            String threadName = Thread.currentThread().getName();
            int total = getTotal(1000);
            System.out.println(threadName + " : " + total);
        };

        Runnable longTask = () -> {
            String threadName = Thread.currentThread().getName();
            int total = getTotal(10000);
            System.out.println(threadName + " : " + total);
        };

        ExecutorService threadPool = Executors.newFixedThreadPool(2);
        threadPool.submit(longTask);
        threadPool.submit(shortTask);
        threadPool.submit(midTask);

        threadPool.shutdown();
    }
}

```

```java
pool-1-thread-2 : 55
pool-1-thread-2 : 500500
pool-1-thread-1 : 50005000
```

위 예제에서는 실행 시간이 가장 짧은 shortTask, 중간 정도로 걸리는 midTask, 가장 오래 걸리는 longTask 이렇게 3개의 작업이 존재한다. 그리고 이를 스레드가 단 2개 뿐인 스레드 풀에 전달하여 처리하도록 하고 있다. 스레드풀에 전달된 작업들은 longTask → shortTask → midTask 순이다. 

처음 longTask, shortTask 이 두 작업은 스레드 풀에 들어가 각각의 스레드에 전달되어 실행된다. 그 뒤에 올 midTask 작업은 이미 스레드 풀의 모든 스레드가 각자의 작업을 처리하고 있기에 대기할 수 밖에 없는 상황이다. 맨 처음 전달된 longTask는 그 처리 시간이 오래 걸려 계속 실행되는 도중에, shortTask는 상대적으로 그 처리 시간이 짧아 먼저 작업이 끝나게 된다. 그래서 위 출력결과에서 shortTask 결과가 먼저 뜬 것이다. 이러면 shortTask를 처리 완료한 스레드는 이제 다음에 처리할 midTask를 받아들여 이를 실행시킨다. 위 출력결과를 보면 thread-2라는 이름의 동일한 스레드가 두 개의 결과를 출력하였는데, 이는 같은 스레드가 shortTask 처리 후 midTask 작업도 처리했음을 보여주는 증거이다. 그런데 midTask 작업은 longTask보다 더 짧은 시간에 처리되어서 먼저 그 결과가 출력되었다. 그 후에 마지막으로 longTask의 처리 결과가 출력되었다. 

# Callable, Future : 스레드 실행 결과값을 반환받고 싶을 때

여태까지 스레드는 실행만 할 뿐, 그 결과값을 반환하지는 않았다. 만약 스레드 실행의 결과값도 반환받고 싶다면, java.util.concurrent 패키지의 Callable<T>와 Future 인터페이스를 이용하면 된다. 

먼저 예제부터 살펴보겠다. 다음 예제는 앞선 예제와 동일하나, 스레드 실행 결과값을 반환받아 이를 따로 출력하는 예제이다. 

```java
import java.util.Arrays;
import java.util.List;
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

class CallableAndFuture {
    public static int getTotal(int n) {
        int total = 0;
        for(int i = 1; i <= n; i++) {
            total += i;
        }
        return total;
    }

    public static void main(String[] args) 
    throws InterruptedException, ExecutionException {
        Callable<Integer> shortTask = () -> {
            String threadName = Thread.currentThread().getName();
            int total = getTotal(10);
            System.out.println(threadName + " : " + total);
            return total;
        };

        Callable<Integer> midTask = () -> {
            String threadName = Thread.currentThread().getName();
            int total = getTotal(10000);
            System.out.println(threadName + " : " + total);
            return total;
        };

        Callable<Integer> longTask = () -> {
            String threadName = Thread.currentThread().getName();
            int total = getTotal(100000);
            System.out.println(threadName + " : " + total);
            return total;
        };

        ExecutorService threadPool = Executors.newFixedThreadPool(2);
        Future<Integer> longFuture = threadPool.submit(longTask);
        Future<Integer> shortFuture = threadPool.submit(shortTask);
        Future<Integer> midFuture = threadPool.submit(midTask);

        List<Integer> resultList = Arrays.asList(
            shortFuture.get(), midFuture.get(), longFuture.get()
        );
        System.out.println(resultList);

        threadPool.shutdown();
    }
}

```

```java
pool-1-thread-2 : 55
pool-1-thread-2 : 50005000
pool-1-thread-1 : 705082704
[55, 50005000, 705082704]
```

위 예제에서, Runnable 대신 Callable<T>를 사용하였다. 여기서 제네릭 인자는 스레드의 반환값의 자료형을 지정할 때 쓰인다. 위 예제에서는 반환값의 자료형이 int라서 Integer를 제네릭 인자로 지정하였다. 

Callable<T> 인터페이스로 구현된 작업들을 스레드 풀에 전달할 때, submit() 메서드는 해당 작업의 반환값을 Future<T> 자료형으로 반환한다. 위 예제에서는 이를 Future<Integer> 자료형 변수에 할당받고 있다. 

작업의 반환값을 얻으려면 스레드 풀로부터 얻은 Future 객체의 .get() 메서드를 호출하여 그 값을 얻을 수 있다. 위 예제에서는 3개의 작업으로부터 얻은 결과값을 실행시간 순으로 List 컬렉션 객체에 할당하고 이를 출력하였다. 

이렇게 스레드 실행 후의 반환값을 얻으려면 Callable<T>, Future<T> 인터페이스와, 스레드 풀을 구현하는 ExecutorService 인터페이스, Executors 클래스 이 모두를 묶음으로 사용해야 얻을 수 있음을 알 수 있다. (스레드 풀이 아닌 다른 방법으로 스레드 실행값을 얻을 수 있는지는 모르겠음)

# 컬렉션 객체 동기화

## 컬렉션 객체도 thread-safety 하지 않다

컬렉션 객체도 스레드에 안전하지 않다. 즉, 여러 스레드가 동시에 컬렉션 객체에 접근하여 해당 자원을 변경시키면 그 자원은 의도치 않은 방향으로 데이터가 오염될 수 있다. 

리스트 객체를 예로 들면, 이전 예제들처럼 리스트 객체에 접근하여 요소를 추가 또는 삭제하는 작업의 메서드들을 동기화시켜도, 리스트 객체 자체에도 동기화를 따로 시키지 않으면 여전히 스레드-안전하지 않아, 리스트 데이터가 오염된다. 

다음은 이를 보여주는 예제로, 전역 객체인 List에 대해, 요소를 하나 추가하는 메서드와 반대로 요소를 하나 삭제하는 메서드 이 각각의 메서드를 수행하는 두 개의 스레드들에 관한 예제이다. 공유 자원에 접근하는 두 메서드에 synchronized 키워드가 쓰여져 동기화가 되어있다는 점에 주목하자.

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

class AsyncCollectionObj2 {
    public static List<Integer> myList = new ArrayList<>();

    public static void fillList(List li, int num, int liSize) {
        li.clear();
        for(int i = 0; i < liSize; i++) {
            li.add(num);
        }
    }

    public static synchronized void appendOne() {
        myList.add(1);
    }

    public static synchronized void removeOne() {
        myList.remove(myList.size()-1);
    }

    public static void main(String[] args) throws InterruptedException {
        int endLimit = 100;

        fillList(myList, 1, 3);
        System.out.println(myList);
        System.out.println(myList.size());

        Runnable add = () -> {
            for(int i = 0; i < endLimit; i++) {
                appendOne();
            }
        };

        Runnable remove = () -> {
            for(int i = 0; i < endLimit; i++) {
                removeOne();
            }
        };

        ExecutorService threadPool = Executors.newFixedThreadPool(2);
        threadPool.submit(add);
        threadPool.submit(remove);

        threadPool.shutdown();

        // 스레드 풀 내 모든 작업들이 다 완료될 때까지 그 뒤의 코드를 실행시키지 않고 대기.
        threadPool.awaitTermination(100, TimeUnit.SECONDS);

        System.out.println(myList);
        System.out.println(myList.size());
    }
}

```

```java
[1, 1, 1]
3
[1, 1, 1]
3
```

```java
[1, 1, 1]
3
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
100
```

위 예제에서, myList는 처음에 모두 값이 1인 요소가 3개 저장된다. 이후, 해당 리스트에 요소를 100개 추가하는 add 작업을 진행하는 스레드와, 해당 리스트에서 요소를 100개 삭제하는 remove 작업을 진행하는 스레드 이 두 스레드가 동시에 실행된다. 그 결과, 원래대로라면 해당 리스트의 요소 개수가 3 + 100 - 100 = 3이어야 한다. 그런데 해당 코드를 매번 실행할 때마다 그 결과값이 달라진다. 이는 아무리 공유 자원에 접근하는 “기능”에 동기화를 하더라도, 컬렉션 객체 자체에도 동기화를 하지 않으면 여전히 스레드-안전하지 않다는 것을 보여준다. 따라서 컬랙션 객체 자체에도 멀티 스레드 환경에서 안전하게 다뤄지도록 동기화가 필요하다. 

참고) `awaitTermination(timeout, unit)` : 스레드 풀 내에서 모든 작업이 완료될 때까지 이 뒤의 코드를 실행하지 않고 기다리게 하는 메서드. join() 과 동일한 역할을 하는 것 같다. 첫 번째 인자로는 스레드 풀 내 모든 작업이 끝날 때까지 대기할 시간을, 두 번째 인자는 첫 번째 인자로 전달된 시간의 단위를 대입한다. 예를 들어, `awaitTermination(100, TimeUnit.SECONDS)` 이면, 스레드 풀 내 모든 작업이 100초 내에 완료될 때까지 기다린다. 

## Collections.synchronized 를 이용하여 동기화

컬렉션 객체 자체에도 동기화를 시키기 위해서, java.util.Collections 클래스의 synchronized로 시작하는 메서드를 이용하면 된다. 

| 컬렉션 객체 | 메서드 |
| --- | --- |
| List | synchronizedList(List list) |
| Set | synchronizedSet(Set set) |
| Map<K, V> | synchronizedMap(Map<K, V> map> |

사용 형태는 대략 다음과 같다.

```java
import java.util.Collections;
// ...
List<Integer> myList = Collections.synchronizedList(new ArrayList<>());
```

> 문제점 발생) `Collections.synchronizedList()` 를 이용하여 앞선 예제 8-1에서의 myList를 동기화시킨 후, 다른 코드는 바꾸지 않고 그대로 실행해보았다. 그런데, 아직도 여러 번 실행하면 그 중 몇 번은 3이 아닌 엉뚱한 결과가 나온다. 더 이상한 것은, appendOne(), removeOne() 메서드의 동기화를 해제하면 오히려 더 엉뚱한 결과가 나오는 빈도가 줄어드는 현상을 발견하였다. 하지만 어찌됐건 두 경우 모두 같은 코드를 여러 번 실행헀을 때 엉뚱한 결과가 가끔씩 나온다. 왜 이런 결과가 나오는지 이유를 알 수 없다.
> 

## Concurrent : 컬렉션 객체 요소 병렬 처리

앞서 살펴본 `Collections.synchronized~()` 메서드를 이용하여 컬렉션 객체를 동기화시키는 경우, 컬렉션 객체 내 모든 요소가 동기화된다. 그런데 만약 스레드들이 모든 요소가 아닌 일부 요소에만 접근할 경우에는 굳이 모든 요소가 잠길 필요가 없을 것이다. 이 경우, 컬렉션 내부의 모든 요소들을 한꺼번에 잠그는 방식이 아닌, 특정 요소에 스레드가 접근할 때에만 다른 스레드가 접근하지 못하도록 부분적으로 잠그는 방식이 더 유연하게 동기화를 하면서 처리 속도도 개선시킬 수 있을 것이다. 

자바에서는 java.util.concurrent 패키지에서 ConcurrentHashMap, ConcurrentQueue 등을 제공한다. 

```java
Map<K, V> map = new ConcurrentHashMap<>();  // 변수 선언 및 초기화 방법.
```

다음은 `Collections.synchronizedMap()`을 이용하는 방법과, ConcurrentHashMap() 을 이용하는 방법의 실행 시간을 비교해보는 예제이다. 

```java
import java.time.Duration;
import java.time.Instant;
import java.util.Collections;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

class ConcurrentPerformance {
    public static Map<String, Integer> syncMap = 
        Collections.synchronizedMap(new HashMap<>());
    public static Map<String, Integer> concMap =
        new ConcurrentHashMap<>();
    
    public static void timeTest(Map<String, Integer> mapObj) 
        throws InterruptedException 
    {
        String threadName = Thread.currentThread().getName();
        int repeat = 10000;
        int threadNum = 3;

        System.out.println(threadName + " 스레드 실행 시작.");
        Instant timeStart = Instant.now();

        Runnable insertData = () -> {
            for(int i = 0; i < repeat; i++) {
                mapObj.put(String.valueOf(i), i);
            }
        };

        ExecutorService threadPool = Executors.newFixedThreadPool(threadNum);
        for(int i = 0; i < threadNum; i++) {
            threadPool.submit(insertData);
        }

        threadPool.shutdown();
        threadPool.awaitTermination(100, TimeUnit.SECONDS);

        Instant timeEnd = Instant.now();
        System.out.println("작업 끝. 걸린 시간 : " + Duration.between(timeStart, timeEnd).toMillis());
    }

    public static void main(String[] args) throws InterruptedException {
        timeTest(syncMap);
        timeTest(concMap);
    }
}

```

```java
main 스레드 실행 시작.
작업 끝. 걸린 시간 : 22
main 스레드 실행 시작.
작업 끝. 걸린 시간 : 10
```

확실히 ConcurrentHashMap을 이용하여 컬렉션 요소 단위로 동기화를 하는 것이 작업 실행 시간을 더 단축시켜준다는 것을 확인할 수 있다. 

---
References

[1] 스레드 풀1

[Thread pool](https://en.wikipedia.org/wiki/Thread_pool)

[2] 스레드 풀2

[[JAVA] 쓰레드 풀(Thread Pool): 개념, 장점, 사용 방법, 코드 예시 (feat. Baeldung)](https://engineerinsight.tistory.com/197)

[3] 스레드 풀3

[Thread Pool 이해하기 \| 허원철의 개발 블로그](https://heowc.dev/en/2018/02/08/thread-pool/)

[4] 이재환, “이재환의 자바 프로그래밍 입문”, (골든래빗, 2021)