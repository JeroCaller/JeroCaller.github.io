---
title: "[Spring] IOC, DI, Bean에 관한 Spring annotation 정리"
category: "Spring"
tag: ["Spring", "Java", "Annotations", "IOC", "DI", "Bean"]
---

# Bean 등록

## Component

`@Component` - 개발자가 직접 작성한 클래스를 스프링 컨테이너에 bean으로 등록해주는 annotation이다. 클래스 수준에서 붙여준다. 

이 때, `@Component` 라고만 작성하면 Bean id는 해당 클래스명의 camel case로 변경된 이름으로 적용된다. 만약 다른 이름을 사용하고자 한다면 `@Component(value = "myBean")` 과 같이 value 파라미터로 원하는 bean id명을 정하면 된다. 

```java
@Component
// bean id : thisIsMyBean으로 자동 변경됨
public class ThisIsMyBean {
    ...
}

@Component(value = "wow")
// bean id : wow로 지정됨. 
public class ThatOneAlsoMyBean {...}

// Main.java
public class Main {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context
			= new AnnotationConfigApplicationContext(Main.class);
				
		// bean 사용.
		context.getBean("thisIsMyBean", ThisIsMyBean.class);
		context.getBean("wow", ThatOneAlsoMyBean.class);
    }
}
```

## Service

`@Service` - `@Component` 와 그 기능은 같으나, 해당 클래스가 “비즈니스 로직”을 구현하는 클래스임을 명시하고자 할 때 쓰인다. 

## Repository

`@Repository` - `@Component` 와 그 기능은 같으나, 해당 클래스가 “DB 연동 로직”을 구현하는 클래스임을 명시하고자 할 때 쓰인다. 

## Bean

만약 외부 라이브러리에서 제공하는 클래스를 bean으로 등록하고자 한다면 대신 `@bean` 어노테이션을 사용한다. 이 때, 해당 객체를 반환하는 메서드 생성 후, 해당 메서드에 해당 어노테이션을 적용하면 된다. 해당 메서드를 포함하는 클래스에는 반드시 `@Configuration` 어노테이션을 적용해놔야 한다. 

# 의존성 주입

## Autowired

필드, setter method, 생성자 메서드에 bean 주입 시 사용되는 어노테이션이다. 주입하고자 하는 bean 객체의 Type에 따라 패키지 내 해당 클래스를 찾아 이의 인스턴스를 주입하는 방식이다. (type으로 매핑)

```java
// Pseudo code

@Compoenet
public class Wow {
		
	@Autowired // 필드 의존성 주입
    priavate OtherObj otherObj;
    
    @Autowired  // 생성자 의존성 주입
    public Wow(OtherObj otherObj) {
        this.otherObj = otherObj;
    }
    
    @Autoewired // setter 메서드 의존성 주입
    public void setOtherObj(OtherObj otherObj) {
        this.otherObj = otherObj;
    }
}
```

해당 어노테이션은 type으로 매핑하기에, 만약 패키지 내에 어떠한 인터페이스가 있고, 해당 인터페이스를 구현하는 클래스가 둘 이상일 경우, 인터페이스를 자료형으로 하는 필드에 `@Autowired` 어노테이션 하나만 적용하여 의존성을 주입하려고 시도하면 에러가 뜬다. 이 경우 `@Qualifier` 어노테이션도 같이 붙여야 한다.

## Qualifier

`@Qualifier` 어노테이션은 `@Autowired` 와 같이 사용된다. `@Autowired` 만으로는 똑같은 type의 클래스가 둘 이상일 경우 스프링 컨테이너는 어느 클래스의 bean을 사용해야할지 결정할 수 없다. 따라서 이 경우 `@Qualifier(value = "...")` 어노테이션을 이용하여 특정 Type의 bean을 지정하면 된다. 

다음은 직원 명단 데이터와 고객 명단 데이터를 각각의 Dao 클래스를 통해 가져오고, 이 둘의 Dao 클래스가 같은 인터페이스를 구현할 때 `@Autowired` 만 사용할 때의 문제점을 나타내는 예제이다.

```java
package pack.model;

import java.util.List;

public interface DaoInterface {
	List<String> selectAll();
}

```

DaoInterface.java

```java
package pack.model;

import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Repository;

@Repository
public class StaffDao implements DaoInterface {

	@Override
	public List<String> selectAll() {
		List<String> data = Arrays.asList(
			"직원-김큐엘",
			"직원-정디비",
			"직원-자바스"
		);
		
		return data;
	}
	
}

```

StaffDao.java

```java
package pack.model;

import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Repository;

@Repository
public class CustomerDao implements DaoInterface {

	@Override
	public List<String> selectAll() {
		List<String> data = Arrays.asList(
				"고객-나이썬",
				"고객-서부리",
				"고객-봄부트"
			);
			
		return data;
	}
	
}

```

CustomerDao.java

```java
package pack.business;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

import pack.model.DaoInterface;

@Service
public class BusinessLogic {
	
	@Autowired
	private DaoInterface daoInter;
	
	public void printData() {
		System.out.println("=== 사람 명단 ===");
		daoInter.selectAll().forEach(data -> {
			System.out.println(data);
		});
		System.out.println("=== 사람 명단 끝! ===");
	}
}

```

BusinessLogic.java

```java
package pack.business;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = {"pack.model", "pack.business"})
public class Main {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context
			= new AnnotationConfigApplicationContext(Main.class);
		
		BusinessLogic logic = context.getBean(
			"businessLogic", BusinessLogic.class
		);
		logic.printData();
	}
}

```

Main.java

Main.java를 실행하면 다음의 에러 메시지가 뜬다. 

```java
10월 18, 2024 5:44:48 오후 org.springframework.context.support.AbstractApplicationContext refresh
경고: Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'businessLogic': Unsatisfied dependency expressed through field 'daoInter': No qualifying bean of type 'pack.model.DaoInterface' available: expected single matching bean but found 2: customerDao,staffDao
Exception in thread "main" org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'businessLogic': Unsatisfied dependency expressed through field 'daoInter': No qualifying bean of type 'pack.model.DaoInterface' available: expected single matching bean but found 2: customerDao,staffDao
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.resolveFieldValue(AutowiredAnnotationBeanPostProcessor.java:787)
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.inject(AutowiredAnnotationBeanPostProcessor.java:767)
	at org.springframework.beans.factory.annotation.InjectionMetadata.inject(InjectionMetadata.java:145)
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor.postProcessProperties(AutowiredAnnotationBeanPostProcessor.java:508)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.populateBean(AbstractAutowireCapableBeanFactory.java:1421)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:599)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:522)
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:337)
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:234)
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:335)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:200)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:975)
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:962)
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:624)
	at org.springframework.context.annotation.AnnotationConfigApplicationContext.<init>(AnnotationConfigApplicationContext.java:93)
	at pack.business.Main.main(Main.java:12)
Caused by: org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type 'pack.model.DaoInterface' available: expected single matching bean but found 2: customerDao,staffDao
	at org.springframework.beans.factory.config.DependencyDescriptor.resolveNotUnique(DependencyDescriptor.java:218)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1420)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1353)
	at org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor$AutowiredFieldElement.resolveFieldValue(AutowiredAnnotationBeanPostProcessor.java:784)
	... 15 more

```

위 메시지를 자세히 보면 다음의 메시지가 있다. 

```java
expected single matching bean but found 2: customerDao,staffDao
```

즉, autowired 어노테이션은 bean 스캔 대상 패키지 전체에 대해 유일한 type의 클래스만을 사용할 수 있다. 

이를 해결하려면 `@Quailfier` 어노테이션을 다음과 같이 사용하면 된다.

```java
package pack.business;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

import pack.model.DaoInterface;

@Service
public class BusinessLogic {
	
	@Autowired
	@Qualifier(value = "customerDao")
	//@Qualifier(value = "staffDao")
	private DaoInterface daoInter;
	
	public void printData() {
		System.out.println("From " + daoInter.getClass().getName());
		System.out.println("=== 사람 명단 ===");
		daoInter.selectAll().forEach(data -> {
			System.out.println(data);
		});
		System.out.println("=== 사람 명단 끝! ===");
	}
}

```

BusinessLogic.java

```java
From pack.model.CustomerDao
=== 사람 명단 ===
고객-나이썬
고객-서부리
고객-봄부트
=== 사람 명단 끝! ===

```

출력 결과 1

```java
From pack.model.StaffDao
=== 사람 명단 ===
직원-김큐엘
직원-정디비
직원-자바스
=== 사람 명단 끝! ===

```

출력 결과2

## Resource

`@Resource` 어노테이션도 `@Autowired` , `@Qualifier` 와 마찬가지로 bean 의존성을 주입하는 어노테이션이다. 다만 type에 의한 매핑을 하는 `@Autowired` 와 달리 `@Resource` 어노테이션은 bean의 id 이름을 이용한다. `@Resource(name = "beanId")` 와 같이 사용하면 된다. 

해당 어노테이션을 사용하려면 pom.xml에 다음과 같이 디펜던시를 명시해야 한다.

```xml
<dependency>
  <groupId>javax.annotation</groupId>
  <artifactId>javax.annotation-api</artifactId>
  <version>1.3.2</version>
</dependency>
```

다음은 앞선 예제를 위 어노테이션으로 바꾼 모습이다.

```java
package pack.business;

import javax.annotation.Resource;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

import pack.model.DaoInterface;

@Service
public class BusinessLogic {
	
	//@Autowired
	//@Qualifier(value = "customerDao")
	//@Qualifier(value = "staffDao")
	//@Resource(name = "staffDao")
	@Resource(name = "customerDao")
	private DaoInterface daoInter;
	
	public void printData() {
		System.out.println("From " + daoInter.getClass().getName());
		System.out.println("=== 사람 명단 ===");
		daoInter.selectAll().forEach(data -> {
			System.out.println(data);
		});
		System.out.println("=== 사람 명단 끝! ===");
	}
}

```

BusinessLogic.java

```java
From pack.model.CustomerDao
=== 사람 명단 ===
고객-나이썬
고객-서부리
고객-봄부트
=== 사람 명단 끝! ===

```

Main.java 출력결과

## Value

`@Value` 어노테이션은 특정 bean의 필드 값을 입력하고자 하거나, `.properties` 설정 파일 내에 있는 값을 주입하고자 할 때 사용하는 어노테이션이다. 

먼저 다음과 같이 다른 bean의 필드값을 주입할 수 있다. 

```java
package pack.etc;

import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

@Component
public class SomeData {
	private String name = "김큐엘";
	private int myNum = 132;
	private List<String> myFavFoods = Arrays.asList("햄버거", "치킨");
	
	public String getName() {
		return name;
	}
	
	public int getMyNum() {
		return myNum;
	}

	public List<String> getMyFavFoods() {
		return myFavFoods;
	}
	
}

```

SomeData.java

```java
package pack.etc;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class UserData {
	
	// 필드 의존성 주입
	@Value("#{someData.name}")
	private String userName;
	
	private int userNum;
	private List<String> userFoods;
	
	public UserData() {}
	
	// 생성자 의존성 주입
	// 생성자 또는 setter 메서드 의존성 주입 방법 사용 시 
	// @Autowired 어노테이션과 같이 사용되어야 함.
	@Autowired
	public UserData(@Value("#{someData.myFavFoods}") List<String> foods) {
		this.userFoods = foods;
	}
	
	// setter 메서드 의존성 주입
	@Autowired
	public void setUserNum(@Value("#{someData.myNum}") int userNum) {
		this.userNum = userNum;
	}
	
	public void printUserData() {
		System.out.println("=== 유저 정보 ===");
		System.out.println("유저 이름: " + this.userName);
		System.out.println("유저 번호: " + this.userNum);
		System.out.println("유저가 좋아하는 음식 목록");
		
		int[] count = {0};
		this.userFoods.forEach(food -> {
			count[0]++;
			
			System.out.println(count[0] + ". " + food);
		});
	}
}

```

UserData.java

```java
package pack.etc;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = "pack.etc")
public class Main {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context
			= new AnnotationConfigApplicationContext(Main.class);
		
		UserData userData = context.getBean("userData", UserData.class);
		userData.printUserData();
	}
}

```

Main.java

```java
=== 유저 정보 ===
유저 이름: 김큐엘
유저 번호: 132
유저가 좋아하는 음식 목록
1. 햄버거
2. 치킨
```

Main.java 콘솔창 출력결과

`@Value` 어노테이션 매개변수에는 JSP에서 봤던 `$` 로 시작하는 EL 언어를 사용할 수 있고, 스프링 전용 EL인 `#` 으로 시작하는 spEL을 사용할 수도 있다. 단, 위 예제에서 볼 수 있듯, 다른 bean의 필드값을 사용할 때에는 spEl을 사용해야 에러가 뜨지 않으며, 반대로 아래에서 소개할 `.properties` 파일 사용 시에는 EL 태그를 사용하거나 spEl과 EL을 적절히 활용해야 한다. 

`@Value` 어노테이션은 properies 파일 내 설정 변수들을 가져와 값으로 주입할 때 유용하다. 다음은 DB 연동 정보가 적힌 `myconfig.properies` 파일로부터 정보들을 모두 읽어와 이를 출력하는 예제이다. 

```java
# properties 주석은 # 문자를 이용한다. 
# key 이름에 꼭 마침표(.)가 들어갈 필요는 없다.

admin.name=kimquel
db.name=mariadb
db.id=root
db.password=1111
```

myconfig.properies

```java
package pack.property;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

@Component
@PropertySource("classpath:myconfig.properties")
public class UseInfo {
	
	@Value("${admin.name}")
	private String adminName;
	
	// # : spEL 사용
	// spEL 내에서의 EL (# 내부에 $) 사용 시 
	// EL($)은 작은 따옴표로 감싸줘야한다. 
	@Value("#{'${db.name}'}")
	private String dbName;
	
	@Value("${db.id}")
	private String dbId;
	
	@Value("${db.password}")
	private int dbPassword;
	
	public void printDbInfo() {
		System.out.println("DB 정보");
		System.out.println("Admin name: " + adminName);
		System.out.println("DB name: " + dbName);
		System.out.println("DB ID: " + dbId);
		System.out.println("DB Password: " + dbPassword);
	}
}

```

UseInfo.java

위와 같이 `properies` 파일과 같은 외부 파일을 가져오려면 `@PropertySource` 어노테이션과 더불어 그 매개변수로 `"classpath:..."` 으로 시작하는 해당 파일 경로명을 작성해줘야 한다. 참고로 src/main/resouces 폴더를 기준으로 경로를 인식한다. 

```java
package pack.property;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = "pack.property")
public class DBMain {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context 
			= new AnnotationConfigApplicationContext(DBMain.class);
		UseInfo useInfo = context.getBean("useInfo", UseInfo.class);
		useInfo.printDbInfo();
	}
}

```

DBMain.java

```java
DB 정보
Admin name: kimquel
DB name: mariadb
DB ID: root
DB Password: 1111

```

DBMain.java 콘솔창 출력결과

위와 같은 원리를 이용하면 코드 상에서 보여지면 안되는 민감한 정보들을 properies 파일로 빼낸 다음 이를 `@Value` 어노테이션을 이용하여 가져오는 방식을 사용하면 되겠다. 그러면 만약 소스 코드를 깃허브에 업로드할 때, 민감한 정보가 들어있는 properties 파일은 .gitignore를 이용하여 깃허브에 업로드 되지 않도록 하기만 하면 민감한 정보를 감출 수 있게 된다. 

## Scope

`@Scope` 어노테이션은 특정 bean의 인스턴스를 스프링 컨테이너가 싱글톤으로 생성하게 할 지 아니면 일반적인 방법으로 생성하게 할지를 결정할 때 쓰는 어노테이션이다. “singleton”과 “prototype” 이 두 값 중 하나를 선택하면 되며, 이 어노테이션을 생략하면 “singleton”이 기본값으로 설정된다. `@Scope("singleton")` 과 같이 사용하면 된다.  

# Bean 스캔

## Configuration & ComponentScan

위 두 어노테이션을 main 메서드가 존재하는 클래스에 설정하면 패키지 내에 흩어져 있는 클래스들을 스캔하여 bean으로 등록한다.

`@ComponentScan` 의 `basePackages` 파라미터를 이용하여 bean 클래스들이 저장되어 있는 경로를 설정함으로써 스프링 컨테이너가 이들을 스캔할 수 있도록 할 수 있다. 

```java
package pack.business;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = {"pack.model", "pack.business"})
public class Main {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context
			= new AnnotationConfigApplicationContext(Main.class);
		
		BusinessLogic logic = context.getBean(
				"businessLogic", BusinessLogic.class
		);
		logic.printData();
	}
}

```

Main.java

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] [[Spring] Annotation 정리](https://velog.io/@gillog/Spring-Annotation-정리#component)

[4] resouce annotation

[[Java] Spring - @Resource 어노테이션을 이용한 의존 자동 설정](https://m.blog.naver.com/seek316/222157097469)

[5] value annotation 및 properies 파일 사용 방법

[[Spring] Annotation - @value](https://j-sik.tistory.com/109)

[6] value anno 생성자, setter 메서드 방식 사용 시 

[Spring @Value 사용방법(예제)](https://memo-the-day.tistory.com/27)

[7] value anno 생성자, setter 메서드 방식 사용 시 

[Spring @Value 사용방법(예제)](https://memo-the-day.tistory.com/27)

[8] properties 파일 이클립스에서 생성하는 방법. 

[[JAVA/Ecilpse] *.properties 파일 생성](https://velog.io/@soyeon/JAVA-Eclipse-properties-파일-생성)

[9] spEl 관련

[[Spring] SpEL - Spring Expression Language](https://atoz-develop.tistory.com/entry/Spring-SpEL-Spring-Expression-Language)