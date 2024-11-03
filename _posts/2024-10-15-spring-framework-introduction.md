---
title: "[Spring] Spring Framework 개요"
category: "Spring"
tag: ["Spring", "Java", "POJO", "IOC", "DI", "AOP"]
---

# 개요

스프링(Spring)은 자바 기반 애플리케이션 개발을 더 편리하게 해주는 엔터프라이즈급 오픈소스 경량급 애플리케이션 프레임워크이다. 

- 엔터프라이즈용인 스프링은 기업을 대상으로 하는 프레임워크이다. 따라서 실무에서 스프링이 자주 쓰인다고 한다. 
옛날 스프링이 등장하기 전 기업들은 비즈니스 로직을 구현하기 위해 따로 기술을 습득해야했다고 하는데, 그 기술이 매우 어려웠다고 한다. 그러나 스프링의 등장으로 인해 이제 기업은 순수히 비즈니스 로직 구현에만 집중할 수 있게되었다고 한다. 여기서 비즈니스 로직은 기업이 사용자의 요구사항 해결을 위해 제공하고자 하는 서비스를 코드로 구현한 것을 의미한다. 
실제로 스프링은 애플리케이션 개발 시 개발자가 오로지 비즈니스 로직 구현에만 집중하도록 하는 것을 목표로 하고 있다고 한다.
- 오픈소스이다. 즉, 모든 사용자들이 쉽게 접근할 수 있어 언제든지 이를 이용하여 애플리케이션을 개발할 수 있다. 심지어 스프링의 일부 코드를 수정하여 사용해도 된다고 한다. 그럼에도 특정 기업에서 관리하기 때문에 안정적 개발과 개선이 보장된다고 한다.
- 스프링은 수많은 모듈들과 방대한 코드로 구성되어 있다고 한다. 여기서의 경량급은 스프링의 규모를 이야기하는 것이 아니다. 여기서의 경량급이란 말은 스프링 이전 기술들에 비해 스프링을 사용하면 개발자가 작성하는 코드의 복잡성과 그 양이 상대적으로 적다는 것을 표현하기 위한 말이라고 한다.
- 프레임워크는 특정 목적을 수행하기 위해 미리 설계된 코드를 라이브러리 형태로 제공하는 것을 의미하며, 라이브러리는 개발자가 원하는 기능만 가져다가 사용할 수 있는 것에 비해 프레임워크는 프레임워크에서 정한 일련의 규칙을 따라 개발자가 코드를 작성해야만 하는 특징을 가진다. 즉, 라이브러리에 대해서는 개발자가 주도권을 가지고 있으나, 프레임워크는 프레임워크의 규칙을 따라서 코드를 작성해야 하기에 개발자가 아닌 프레임워크가 주도권을 가지고 있다는 것이 특징이다. 나중에 소개할 “제어의 역전”이란 개념도 스프링이 프레임워크이기에 나타나는 특징이다. 
스프링은 특정 목적을 수행하기 위한 프레임워크 중에서도 애플리케이션 프레임워크이다. 즉, 개발자가 애플리케이션을 좀 더 쉽게 개발하기 위한 프레임워크인 것이다.

스프링 공식 사이트는 다음을 참고.

[https://spring.io/](https://spring.io/)

# 스프링의 특징

객체 지향 프로그래밍을 공부하다보면 두 객체 간의 의존성이 생기는 것은 어쩔 수 없으나, 너무 의존성이 강하면 여러모로 안좋다고 하는 것을 들어봤을 것이다. 두 객체 간의 의존성이 너무 강하면 한 객체가 수정될 때 그 객체를 포함하는 다른 객체에서도 코드 수정을 해야 하는데, 프로젝트 규모가 커질수록 수정해야할 곳이 많아져 이는 매우 번거로운 프로그래밍이 된다. 즉, 유지보수성에 안좋다. 또한, 그렇게 해서 기존의 코드를 수정하다가 예상치 못한 오류, 버그를 발생시킬 수 있다는 위험도 있다. 따라서 객체 지향에서는 객체들 간의 의존성을 최소화하는 것이 좋다고 한다. 

스프링은 이러한 점을 특히나 강조한다. “의존성이란 것 자체를 이렇게나 싫어하나?” 싶을 정도로 객체들 간의 의존성을 최소화하려는 것이 스프링의 특징이다. 스프링에서는 이러한 객체 간의 의존성을 줄이기 위해 다음의 특징들을 가진다. 

- 의존성 주입 (Dependency Injection)
- 제어의 역전 (Inversion of Control)
- POJO (Plain Old Java Object)

또한 객체 지향에서의 장점 중 하나인 코드 재사용성을 살리기 위한 기술로 다음이 존재한다. 

- 관점 지향 프로그래밍 - AOP (Aspect Oriented Programming)

이 글에서는 위에서 언급한 특징들을 하나씩 살펴볼 것이다. 

## POJO (Plain Old Java Object)

Plain Old Java Object라는 것은 말 그대로 순수한 자바 객체를 의미한다. 즉, 스프링 프레임워크에서는 다른 라이브러리나 프레임워크에 의존적인 객체가 아닌, 순수히 자바로만 작성된 객체를 사용하는 것을 지향한다. 만약 외부 라이브러리의 객체에 의존적인 클래스를 작성한다면, 시간이 지나면서 계속 버전이 업데이트되는 라이브러리 특성 상 deprecated되는 기능들이 생길 수밖에 없다. 그러면 개발자가 작성한 객체가 더 이상 작동되지 않는 불상사가 발생할 수 있다. 게다가 만약 외부 라이브러리에서 특정 기능이 모두 전혀 다른 기능으로 바뀌면 이걸 사용하는 개발자 정의 객체에서도 기존 코드를 모조리 수정해야하는 상황도 발생할 수 있을 것이다. 이러한 문제점들의 원인도 사실 외부 라이브러리에서 제공하는 객체에 대한 의존성이 존재하기에 발생하는 현상이다. 스프링에서는 이러한 외부에서 제공하는 객체에 의한 의존성을 최소화하려고 하기에 POJO 프로그래밍을 지향한다. 

스프링에서는 이러한 POJO 지향을 위해 IOC, DI 등의 기술들을 이용한다. 

## 여러 객체 간의 의존성 - 합성(Composition)과 집합(Aggregation)

의존성 주입 및 제어의 역전에 대해 살펴보기 전에 합성(Composition)과 집합(Aggregation)에 대해 간단히 살펴보자. 

합성과 집합은 하나의 클래스 내부에서 다른 클래스의 객체를 포함하는 관계에 대한 내용이다. 합성과 집합 모두 하나의 클래스 내부에서 다른 클래스의 객체를 포함하여 내부적으로 사용한다는 점은 똑같으나 약간의 차이점이 있다. 

합성은 하나의 클래스 내부에 다른 클래스의 객체를 직접 생성하여 사용하는 방식이다. 

```java
class Dog {
	private String dogName;
		
	public Dog(String dogName) {
		this.dogName = dogName;
	}

    void speak() {
	     System.out.println("왈왈!");
    }
}

class Person {
    private Dog myDog = new Dog("바둑이");  // 합성.
    
    void myDogAction() {
	    myDog.speak();
    }
}
```

예제 1-1

반면 집합은 클래스 내부에서 다른 클래스의 객체를 직접 생성하는 것이 아니라 생성자 또는 메서드의 인자로 전달받는 방식이다. 

```java
class Dog {
	private String dogName;
		
	public Dog(String dogName) {
		this.dogName = dogName;
	}

    void speak() {
	    System.out.println("왈왈!");
    }
}

class OtherPerson {
    private Dog myDog;
    
    public OtherPerson(Dog dog) {
        this.myDog = dog;  // 집합
    }
    
    void myDogAction() {
	    myDog.speak();
    }
}
```

예제 1-2

합성의 특징은 다음과 같다.

- 하나의 클래스 내부에 다른 클래스의 객체를 생성하는 방식이기에 포함하는 객체는 포함되는 객체가 사라지면 제 기능을 할 수 없는 구조이다.
- 포함하는 객체가 소멸되면 포함된 객체도 소멸된다. 즉, 두 객체의 소멸주기를 공유하게 된다.
- 만약 클래스 내부에서 새로운 객체를 사용해야 한다면 해당 클래스의 내부 코드를 수정해줘야 한다. 이는 기능이 확장되거나 변경되는 상황에 유연하게 대처하지 못한다는 뜻이다.

```java
class Person {
    //private Dog myDog = new Dog("바둑이");  // 기존 코드를 삭제해야 한다.
    private Cat myCat = new Cat("야옹이");
    
    void myPetAction() {
	    //myDog.speak();
	    myCat.speak();
    }
}
```

예제 1-3

위와 같이 기존 코드를 삭제해야하는 경우를 방지하기 위해 인터페이스를 사용해도 문제는 여전히 존재한다. 

```java
public interface Pet {
	void speak();
}

class Dog implements Pet {
	private String petName;
		
	public Dog(String petName) {
		this.petName = petName;
	}
		
	@Override
    void speak() {
	    System.out.println("왈왈!");
    }
}

class Cat implements Pet {
	private String petName;
		
	public Cat(String petName) {
		this.petName = petName;
	}
		
	@Override
    void speak() {
	    System.out.println("야옹!");
    }
}

class Person {
    //private Pet myPet = new Dog("바둑이");
    private Pet myPet = new Cat("야옹이");
    
    void myPetAction() {
	    myPet.speak();
    }
}
```

예제 1-4.

즉, 기능의 확장 또는 변경에 따라 기존 코드도 수정해야 하기에 개방 폐쇠 원칙(OCP)을 어기게 된다. 이러면 유지보수가 힘들어진다. 이러한 문제가 발생한 이유가 두 객체 간의 결합도가 강하기 때문이다. 

반면, 집합의 특징은 다음과 같다. 

- 외부로부터 객체를 주입받는 형태이기에 두 객체는 어느 정도의 의존성은 갖더라도 그 정도가 약하다. 외부로부터 온 객체에 수정이 가해지거나 어떠한 버그, 오류가 생기더라도 포함하는 객체의 다른 일부 기능들은 정상 작동될 수 있다.
- 포함 관계에 놓인 두 객체의 소멸주기가 다르다. 메서드 내부로 주입된 객체는 해당 메서드의 기능이 다할 때 소멸될 수 있도록 할 수 있는데, 이 경우 포함하는 객체도 같이 소멸되는 방식은 아니기에 두 객체 간 소멸 주기가 다르다고 할 수 있다. 이는 두 객체가 어느 정도 독립성을 가진다는 것을 암시한다고 볼 수 있다.

집합은 인터페이스를 이용한 추상화 및 다형성과 결합될 때 더 유연한 코드를 작성할 수 있게 된다. 

```java
public interface Pet {
	void speak();
}

class Dog implements Pet {
	private String petName;
		
	public Dog(String petName) {
		this.petName = petName;
	}
		
	@Override
    void speak() {
	    System.out.println("왈왈!");
    }
}

class Cat implements Pet {
	private String petName;
		
	public Cat(String petName) {
		this.petName = petName;
	}
		
	@Override
    void speak() {
	    System.out.println("왈왈!");
    }
}

class OtherPerson {
    private Pet myPet;
    
    public OtherPerson(Pet myPet) {
        this.myPet = myPet
    }
    
    void myPetAction() {
	    myPet.speak();
    }
}

public class Main {
	public static void main(String[] args) {
	    Dog dog = new Dog("바둑이");
	    Cat cat = new Cat("야옹이");
	    
	    OtherPerson someone = new OtherPerson(dog);
	    someone.myPetAction();
	    
	    OtherPerson otherOne = new OtherPerson(cat);
	    otherone.myPetAction();
	}
}
```

예제 1-5. 

위 코드를 보면 dog 객체이든 cat 객체이든 상관없이 OtherPerson 클래스 내부의 코드에 수정 또는 추가 작업이 이뤄지지 않고 있음을 알 수 있다. 즉, OCP 원칙을 잘 따르고 있음을 알 수 있다. 이는 집합이 두 객체 간의 의존성이 상대적으로 낮기 때문이다. 

다음에 살펴볼 의존성 주입과 제어의 역전을 보면 알 수 있듯, spring은 집합을 활용하여 두 객체 간의 의존성을 최소화하여 유지보수성을 확보하는 데에 집중하고 있음을 알 수 있을 것이다. 

## 제어의 역전 (IoC, Inversion of Control)

앞서 합성 방식으로 작성된 클래스 내부를 보면 알 수 있듯, 개발자가 직접 객체를 new 키워드를 통해 생성하려고 하기에 의존성 문제가 발생한다. 이러한 이유로 스프링에서는 개발자가 직접 객체를 생성하게 해주지 않고, 스프링의 “스프링 컨테이너(Spring Container)” 또는 “IoC 컨테이너”에 위임한다. 이를 통해 스프링이 대신 객체를 생성하고 여러 객체들 간의 의존 관계도 대신 맺어주며, 생성된 객체의 생명 주기도 스프링이 대신 관리해준다. 

즉, 객체 생성 및 관리에 대한 제어권이 개발자가 아닌 스프링이라는 프레임워크에 주어졌기에 이를 제어의 역전이라고 부른다. 개발자에게 객체 생성 및 관리의 자유를 주면 그만큼 코드의 안정성이 낮아지므로 개발자의 자유를 제한하는 방식인 것이다. 

## 의존성 주입 (DI, Dependency Injection)

이러한 제어의 역전을 구현하는 방법으로 의존성 주입을 이용한다. 의존성 주입은 어떤 클래스 내부에 개발자가 직접 다른 객체를 생성하지 않고, 스프링 컨테이너가 대신 생성한 객체를 주입하는 형태이다. 이로 인해 클래스는 집합의 형태를 띈다. 

```java
@Component
class Person {
    private Pet myPet;
    
    @Autowired
    public Person(Pet myPet) {
        this.myPet = myPet;
    }
    
    void myPetAction() {
	    myPet.speak();
    }
}
```

예제 2-1

위 코드를 보면 Person 이라는 클래스는 집합의 형태를 띄고 있는 것을 알 수 있다. 이를 통해 외부에서 생성한 객체를 해당 클래스 내부로 주입하여 두 객체 간의 의존성을 낮추고 있다. 

한 편, 개발자가 객체를 직접 생성하지 못하기에 스프링이 대신 객체를 생성하여 의존성을 주입해야 하는데, 이 과정에서 annotation이 사용된다. 

`@Component` 어노테이션은 개발자가 작성한 클래스를 Java bean으로 등록하기 위한 어노테이션이다. 스프링은 개발자가 작성한 클래스를 bean으로 관리하기 때문이다. 

`@Autowired` 어노테이션은 의존성을 주입할 때 사용하는 어노테이션이다. 위 예제에서는 생성자를 통해 의존성을 주입하고 있다. 사실 의존성을 주입하는 방법은 크게 3가지가 있다.

- 생성자를 통한 의존성 주입.
- setter 메서드를 이용한 의존성 주입.
- 필드 선언을 통한 의존성 주입.

```java
// setter 메서드를 이용한 의존성 주입
@Component
class Person {
    private Pet myPet;
    
    @Autowired
    public void setMyPet(Pet myPet) {
        this.myPet = myPet;
    }
    
    void myPetAction() {
	    myPet.speak();
    }
}

// 필드 선언을 통한 의존성 주입
@Component
class Person {
	@Autowired
    private Pet myPet;
    
    void myPetAction() {
	    myPet.speak();
    }
}
```

예제 2-2

어노테이션에 대해서는 나중에 다른 페이지에서 자세히 살펴보겠다. 

## 관점 지향 프로그래밍 (AOP, Aspect Oriented Programming)

앱 개발 시 기능을 구현할 때 “핵심 기능(핵심 관심 사항)”과 “부가 기능(부가 관심 사항)”으로 관점을 나누고, 핵심 기능들 사이에서 공통적으로 존재하는 부가 기능들을 모듈화하여 개발하는 방식을 관점 지향 프로그래밍 (AOP)이라고 한다. 여기서 부가 기능은 주로 로그인(인증), 로깅(logging), 트랜잭션(transaction) 등이 될 수 있다. 핵심 기능은 주로 비즈니스 로직을 일컫는다. 

온라인 카페를 만든다고 가정해보자. 댓글 기능을 구현하는 모듈과 글 포스팅 기능을 구현하는 모듈이 존재할 때, 댓글을 달기 위해, 또는 글을 포스팅 하기 위해 모두 로그인 기능이 필요하다고 가정해보자. 그러면 댓글 기능 모듈과 글 포스팅 기능을 하는 각각의 모듈 내에 로그인 기능을 넣는 방법을 떠올릴 수 있겠다. 그러나 이러면 로그인 기능이 두 모듈에서 중복되어 나타난다는 문제점이 있다. 또한 하나의 모듈에 핵심 기능과 로그인이라는 부가 기능 이 두 기능이 섞여 있기에 단일 책임 원칙 (SRP)에도 위배된다. AOP는 이러한 부가 기능 코드의 중복을 제거하고 재사용성을 확보하기 위한 기술이라고 보면 되겠다. 

참고로 각각의 모듈에 흩어져 있는 공통의 기능을 “횡단 관심사(cross-cutting concern)”라고 한다. AOP는 이러한 횡단 관심사를 하나로 모듈화하여 코드의 재사용성을 확보하는 것을 목표로 한다. 

![그림 3-1. 각각의 모듈에 공통으로 존재하는 부가 기능들이 흩어져 있다. ](/images/2024-10-15/spring-framework-introduction/aop_ex1.png)

그림 3-1. 각각의 모듈에 공통으로 존재하는 부가 기능들이 흩어져 있다. 

![그림 3-2. AOP 방식으로 접근하면 각가의 모듈에 흩어져 있는 부가 기능들을 하나로 모듈화하여 부가 기능이 필요한 곳 어디든지 삽입하여 사용할 수 있다. ](/images/2024-10-15/spring-framework-introduction/aop_ex2.drawio.png)

그림 3-2. AOP 방식으로 접근하면 각가의 모듈에 흩어져 있는 부가 기능들을 하나로 모듈화하여 부가 기능이 필요한 곳 어디든지 삽입하여 사용할 수 있다. 

### AOP 관련 용어

Spring AOP에는 여러 용어들이 있다. 이를 정리해보겠다.

- Aspect
    - 여러 모듈에 공통으로 적용될 부가 관심 사항(횡단 관심사)을 의미.
    - 이 부가 관심 사항을 코드로 구현한 것이 Advice이다.
- Join point
    - 인스턴스 생성 시점, 메서드 호출 시점, 예외 발생 시점처럼 애플리케이션의 실행 흐름 중에서 Aspect를 적용할 수 있는 모든 특정 지점들을 의미한다.
    - 스프링에서 제공하는 AOP는 메서드 수준에서만 Aspect를 적용할 수 있다.
    - 핵심 관심 사항 전후로 부가 관심 사항을 추가할 수 있는데, 이 전후 지점이 join point가 되겠다.
- Advice
    - 특정 Join point에 삽입될 부가 기능을 구현한 코드
- Point Cut
    - advice가 적용될 특정 join point들의 묶음을 의미하며, advice가 적용될 위치 선별 기능이기도 하다. 보통 advice가 적용될 핵심 관심 사항 메서드가 여러 곳에 있으므로 실제 코드 상에서는 특정 지시자를 이용하여 advice가 적용될 여러 메서드들을 동시에 지정한다.
    - 스프링에서는 AOP를 메서드 수준에서만 적용 가능하기에 point cut 설정도 메서드 수준에서만 가능하다.
- Advisor
    - Advice와 Point Cut을 묶어 부르는 말. Advisor = Advice + Point Cut
- Weaving
    - 실제로 특정 Point cut에 advice를 삽입하는 것을 의미한다. 즉, 실제로 핵심 관심 사항 로직에 부가 관심 사항 로직을 삽입하는 행위를 일컫는다.
- Target
    - 부가 관심 사항을 적용할 핵심 관심 사항이 구현된 모듈을 의미. 스프링에서는 메서드 수준에서만 aspect가 적용되기에 여기서는 핵심 관심 사항이 구현된 메서드라 보면 되겠다.
    - AOP의 입장에서는 부가 관심 사항을 추가할 “핵심 관심 사항”이 타겟이 되므로 이름도 target으로 지은 것 같다.

![그림 4-1. AOP 관련 용어들을 다이어그램을 통해 정리해보았다. ](/images/2024-10-15/spring-framework-introduction/aop_terms.drawio.png)

그림 4-1. AOP 관련 용어들을 다이어그램을 통해 정리해보았다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] 황희정, “짧고 굵게 배우는 JSP 웹 프로그래밍과 스프링 프레임워크”, (한빛아카데미, 2024)

[4] [스프링과 스프링부트(Spring Boot)ㅣ정의, 특징, 사용 이유, 생성 방법](https://www.codestates.com/blog/content/스프링-스프링부트)

[5] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)

[6] Spring 공식 홈페이지

[Home](https://spring.io/)

[7] 집합과 합성

[UML - 클래스 다이어그램 고급 - 집합과 합성](https://ocwokocw.tistory.com/14)

[8] 객체의 결합도

[💠 객체의 결합도 & 응집도 의미와 단계 💯 총정리](https://inpa.tistory.com/entry/OOP-💠-객체의-결합도-응집도-의미와-단계-이해하기-쉽게-정리)

[9] Spring annotation

[[Spring] Annotation 정리](https://velog.io/@gillog/Spring-Annotation-정리)

[10] AOP

[[Spring] 스프링 AOP (Spring AOP) 총정리 : 개념, 프록시 기반 AOP, @AOP](https://engkimbs.tistory.com/entry/스프링AOP)

[11] AOP 용어 정리

[Spring AOP(Aspect Oriented Programming) 용어 정리 !](https://velog.io/@wnguswn7/Spring-AOPAspect-Oriented-Programming-용어-정리)