---
title: "[Java] Singleton (싱글톤)"
category: "Java"
tag: ["java", "singleton", "싱글톤", "design pattern", "디자인 패턴", "private", "static"]
---

전체 프로그램에서 단 하나의 인스턴스만 생성할 수 있도록 하는 방법을 싱글톤(Singleton)이라 한다. 

자바에서 싱글톤을 만들기 위해선 우선 클래스 내부에서 자기 자신의 객체를 하나 생성하여 이를 static 변수에 저장한 뒤, 싱글톤을 정의하는 클래스 외부에서 해당 클래스를 인스턴스화할 수 없게 만들어야 한다. 이 과정에서 static과 private 키워드가 사용된다. 

다음은 자바에서 싱글톤을 구현한 예제이다.

```java
class Singleton {
	
	/*
	 * private를 붙여 외부에서 해당 필드 변수에 
	 * 직접 접근하여 값을 변경하지 못하도록 방지.
	 */
	private static Singleton sglt = new Singleton();
	
	/*
	 * 기본 생성자.
	 * private로 설정함으로써 외부에서 해당 클래스로부터
	 * 인스턴스를 생성하지 못하게 한다. 
	 */
	private Singleton() {}
	
	/*
	 * 싱글톤 인스턴스를 얻으려면 다음의 메서드를 호출하여 사용해야 함.
	 * sglt 변수에 대한 getter 메서드.
	 */
	static Singleton getInstance() {
		return sglt;
	}
	
	void sayHello() {
		System.out.println("Hi from Singleton!");
	}
}

public class MySingleton {
	public static void main(String[] args) {
		// 외부에서 싱글톤 객체 생성 시도 => 에러.
		// Singleton sg = new Singleton();
		
		// 싱글톤 인스턴스를 얻는 유일한 방법.
		Singleton sg1 = Singleton.getInstance();
		Singleton sg2 = Singleton.getInstance();
		
		sg1.sayHello();
		sg2.sayHello();
		
		// 서로 같은 객체를 참조하는가? 
		// 싱글톤이므로 둘 다 같은 객체를 참조해야 함.
		System.out.println(sg1 == sg2);  // true
	}
}

```

예제 1-1

```java
Hi from Singleton!
Hi from Singleton!
true
```

예제 1-1 실행결과

싱글톤은 외부에서 클래스를 생성할 수 없게끔 고안되어야 한다고 하였다. 따라서 싱글톤 인스턴스를 반환할 메서드 getInstance()는 static으로 정의될 수밖에 없다. 그래야 클래스를 인스턴스화하지 않고 바로 클래스명에서 해당 메서드에 접근할 수 있기 때문이다. `Singleton.getInstance()` 와 같이 말이다. 그리고 이 static 메서드 내부에서 싱글톤 인스턴스를 참조하는 변수 sglt를 사용하기에 해당 변수도 static으로 선언되어야 하는 것이다. 

---

References

[1] 신용권, “혼자 공부하는 자바”, (한빛미디어, 2024)