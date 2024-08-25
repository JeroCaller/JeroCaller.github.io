---
title: "[Java] super()와 부모 객체 생성"
category: "Java"
tag: ["java", "super", "inheritance", "object", "instance"]
---

자식 클래스의 생성자 메서드에 개발자가 super()라고 부모 클래스의 생성자 메서드 호출을 명시하지 않더라도 컴파일 과정에서 자동으로 삽입된다고 한다. 

super()라는 부모 생성자 호출문은 자식 생성자 내부에서 사용 시 반드시 첫 줄에 작성해야 한다. 

```java
class Parent {
	Parent() {
		System.out.println("Hi from Parent");
	}
}

class Child extends Parent {
	Child() {
		System.out.println("Hi from Child");
		super();  // 에러
		System.out.println("Hi from Child");
	}
}

public class Study1 {
	
	public static void main(String[] args) {
		Child c = new Child();
	}
}
```

예제 2-1

```java
Exception in thread "main" java.lang.Error: Unresolved compilation problem: 
	Constructor call must be the first statement in a constructor

	at Child.<init>(Study1.java:10)
	at Study1.main(Study1.java:18)
```

예제 2-1 실행결과 

한 편, 앞서 자식 생성자 메서드 내부에 super()와 같이 부모 생성자 메서드 호출문을 작성하지 않아도 컴파일 과정에서 자동으로 삽입되어 실행된다고 하였다. 다음의 코드에서 이를 확인할 수 있다. 

```java
class Parent {
	Parent() {
		System.out.println("Hi from Parent");
	}
}

class Child extends Parent {
	Child() {
		System.out.println("Hi from Child");
	}
}

public class Study1 {
	
	public static void main(String[] args) {
		Child c = new Child();
	}
	
}
```

예제 2-2

```java
Hi from Parent
Hi from Child
```

예제 2-2 실행결과

실행결과에 “Hi from Parent”가 찍혀있다. 이는 Child() 생성자 메서드 내부의 맨 첫 줄에 super() 코드가 삽입되었음을 알 수 있다. 

그런데 다시 생각해보자. 생성자가 호출된다는 것은 곧 인스턴스가 생성된다는 뜻이다. 그렇다는 것은, 자식 클래스에 대해서만 인스턴스를 생성했음에도 사실은 부모 인스턴스도 같이 생성된다는 것이다. 그것도 자식 인스턴스가 생성되기 전에 말이다. 이는 위 실행결과를 보면 인스턴스 생성 순서를 유추할 수 있다. 

즉, 자식 인스턴스를 생성하면 그 이전에 자동으로 부모 인스턴스도 생성되는 구조인 것이다. 

이와 연관지어 다음의 코드를 보자.

```java
Child c = new Child();
Parent p = c;
```

예제 2-3

Child 인스턴스를 생성했으니 당연히 Parent 인스턴스(이 인스턴스를 p1이라 하겠다)도 자동으로 힙 메모리 영역에 생성되었을 것이다. 그러면 두 번째 줄을 실행하면 p라는 참조변수는 그 자료형이 Parent이므로 p1 이라는 인스턴스를 가리키게 되는 걸까?

그건 아니다. 다음의 코드에서 이를 확인할 수 있다. 

```java
System.out.println(p == c);  // true
```

예제 2-4

즉, 참조변수 p는 Child 인스턴스를 가리키고 있다는 뜻이다. 위 예제 2-3에서는 두 번째 줄에서 Child 자료형인 참조 변수 c가 Parent 자료형으로 자동 형변환을 한 것이다. 즉, 참조 변수의 타입만 자동으로 변환된 것일뿐, 동일한 Child 인스턴스를 가리키는 건 변함없다는 뜻이다. 

---

References

[1] 신용권, “혼자 공부하는 자바”, (한빛미디어, 2024)