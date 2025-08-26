---
title: "[Spring Boot] Spring boot에서 제공하는 스케줄링 사용해보기"
category: "Spring"
tag: ["Spring", "Spring Boot", "Scheduling", "스케줄링", "schedule", "Quartz"]
---

저번 글인 “[[Spring Boot] 스케줄링과 Quartz](/spring/spring-boot-scheduling-and-quartz/)”에서는 자바 및 스프링 진영에서의 스케줄링 작업을 위한 도구 중 하나로 Quartz에 대해 소개하였다. 그리고 이와 비교하여 스프링 부트 자체에서도 제공하는 스케줄링 기능이 있다고 하였는데, 이번 글에서는 이에 대해서 간단하게 다뤄보고자 한다. 

# Spring Boot에서 기본으로 제공하는 스케줄링 기능 사용해보기

웹 앱 제작을 위한 Spring Boot 프로젝트를 처음 만들 때 다음의 의존성은 반드시 포함하게 된다. 

```java
implementation 'org.springframework.boot:spring-boot-starter-web'
```

코드 1-1. build.gradle의 의존성 중 하나.

이 글에서 다룰 Spring Boot 제공 스케줄링 기능도 이 의존성에 포함되어 있다. 그래서 Quartz를 다룰 때 해당 라이브러리에 대한 의존성을 별도로 build.gradle에 주입해야 했지만, 여기서는 별도로 해야 하는 작업이 없다. 

스케줄링 기능 사용을 위해 먼저 웹 앱의 진입점인 `main()` 메서드를 가지는 `~Application` 클래스에 다음과 같이 `@EnableScheduling` 어노테이션을 적용해야 한다. 

```java
package com.jerocaller.SpringBootScheduling;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

/**
 * <p>Note</p>
 * <p>
 *     스프링부트에서 기본으로 제공하는 스케줄링 기능 사용을 위해 아래 코드와 같이
 *     {@code @EnableScheduling} 어노테이션을 <code>main()</code> 메서드를 가진
 *     클래스에 적용해야한다.
 * </p>
 */
@SpringBootApplication
@EnableScheduling
public class SpringBootSchedulingApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootSchedulingApplication.class, args);
	}

}

```

코드 1-2. 

참고로 Javadoc 문서에 따르면 `@Configuration` 어노테이션이 적용된 클래스에 대신 적용해도 똑같이 스케줄링 기능을 사용할 수 있다고 한다. 

이 후, 스케줄링 작업을 정의할 클래스를 하나 선언한다. 지금 스프링 기반으로 코드를 작성하고 있기 때문에 해당 클래스는 반드시 `@Component` 와 같은 어노테이션을 이용하여 스프링 빈으로 정의해줘야 한다. 그리고 스케줄링 작업은 해당 클래스 내 메서드 단위로 작성하고, 해당 메서드에 `@Scheduled` 어노테이션을 적용한다. 

```java
package com.jerocaller.SpringBootScheduling.components;

import lombok.extern.slf4j.Slf4j;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

/**
 * <p>Note</p>
 * <p>
 *     {@code @Scheduled} 이용 시 이 어노테이션이 적용된 메서드를 가지고 있는 클래스는
 *     반드시 스프링 빈으로 등록되어야 한다. 따라서 아래 클래스의 경우 {@code @Component}
 *     어노테이션을 부여하였다.
 * </p>
 * <p>
 *     fixedDelay vs fixedRate 비교 테스트 시 둘 중 하나는 {@code @Scheduled} 어노테이션을
 *     주석 처리하여 둘 중 한 메서드만 스케줄링이 되도록 하여 정확한 비교를 가능하게 해야한다.
 * </p>
 * <p>
 *     {@code @Scheduled} 어노테이션이 부여된 메서드는 다음의 조건들을 반드시 지켜야만 한다.
 *     <ol>
 *         <li>스케줄러 메서드는 리턴 타입이 void여야 한다.</li>
 *         <li>스케줄러 메서드에는 매개변수를 정의할 수 없다.</li>
 *     </ol>
 * </p>
 */
@Component
@Slf4j
public class MyScheduler {

    /**
     * <p>
     *     fixedDelay는 이전 작업의 종료 시점부터 정의된 ms 시간만큼 지연된 후
     *     해당 작업을 실행하도록 할 때 쓰인다. 즉, 작업 수행시간을 포함하여 주기적인
     *     시간 간격으로 작업을 반복 실행할 때 쓰인다.
     * </p>
     * <p>
     *     fixedDelay로 정의한 시간보다 작업 수행시간이 더 길어질 경우,
     *     작업 수행 시간이 얼마냐 걸렸느냐에 상관없이 이전 작업이 끝난 후
     *     fixedDelay만큼 지난 후에야 다음 작업이 수행된다.
     *     예) fixedDelay = 1000이고 작업 수행 시간이 2000ms(2초)일 때,
     *     이전 작업이 끝나고 나서 1000ms 뒤에 다음 작업이 수행된다.
     * </p>
     * <p>
     *     즉, 달리 말해 fixedDelay는 이전 작업 수행 시간에 영향을 받지 않고
     *     지정된 시간만큼 지연된 뒤에 실행됨을 보장한다.
     * </p>
     *
     * @throws InterruptedException
     */
    @Scheduled(fixedDelay = 1000)
    public void schedulingOne() throws InterruptedException {
        log.info("첫 번째 작업 게시");
        Thread.sleep(2000);  // 이 작업이 2초 걸린다고 가정.
        log.info("첫 번째 작업 끝");
    }

    /**
     * <p>
     *     fixedRate는 이전 작업의 시작 시점으로부터 정의된 ms 시간만큼 지연된 뒤
     *     해당 작업을 다시 실행하도록 할 때 쓰인다.
     * </p>
     * <p>
     *     만약 fixedRate로 지정한 시간보다 작업 수행 시간이 더 길어질 경우,
     *     fixedRate로 지정한 시간 간격은 무시되고 이전 작업 수행 종료 직후에
     *     바로 다음 작업이 수행된다.
     * </p>
     * <p>
     *     예) fixedRate = 1000ms 이고, 작업 수행 시간이 2000ms이면
     *     이전 작업이 2초 걸려 수행을 완료한 뒤, 1000ms를 기다리지 않고
     *     바로 다음 작업이 수행된다.
     * </p>
     * <p>
     *     달리 말하면 fixedRate는 이전 작업 수행 시간에 영향을 받아
     *     만약 작업 수행 시간이 지정된 시간 간격을 넘어가면 지정된 시간 만큼
     *     기다리지 않고 바로 다음 작업을 수행한다. 따라서 주기적인 실행이 보장되지 않는다.
     * </p>
     *
     * @throws InterruptedException
     */
    @Scheduled(fixedRate = 1000)
    public void schedulingTwo() throws InterruptedException {
        log.info("두 번째 작업 개시");
        Thread.sleep(2000);
        log.info("두 번째 작업 끝");
    }
}

```

코드 1-3. 

위와 같이 `@Scheduled` 어노테이션이 적용된 메서드는 앱 실행 시 스케줄링이 시작된다. 

단, `@Scheduled` 어노테이션을 적용할 메서드는 다음의 사항들을 반드시 지켜야 한다. 

1. 스케줄러 메서드는 아무것도 반환하지 않아야 한다. 즉, void 여야 한다. 
2. 매개 변수를 가질 수 없다. 

그리고 `@Scheduled` 어노테이션에는 다음의 속성값들을 지정해줄 수 있다. 

- `fixedDelay` - 이전 작업의 종료 시점부터 지정된 ms(밀리 초)만큼 지난 뒤에 다음 작업이 수행된다.
- `fixedRate` - 이전 작업의 시작 시각으로부터 지정된 ms만큼 지난 뒤 다음 작업이 수행된다.
- `initialDelay` - `fixedDelay` 또는 `fixedRate` 가 적용된 메서드의 첫 작업이 시작되기 전 지연시킬 시간(ms 단위). 처음에만 적용된다.
- `cron` - 스프링 (또는 Quartz) 방식 cron 표현식을 이용하여 특정 시각마다 스케줄링할 때 사용. “[[Spring Boot] 스케줄링과 Quartz - 참고) cron 표현식을 적용한 Trigger 인스턴스 생성하기](/spring/spring-boot-scheduling-and-quartz/#%EC%B0%B8%EA%B3%A0-1)” 참고.

## fixedDelay와 fixedRate라는 두 지연 시간의 차이점

위 코드 1-3에서는 두 개의 스케줄러 메서드에서 각각 `fixedDelay` 와 `fixedRate` 이 두 속성이 사용되었다. 이 각각의 속성들을 확인하기 위해 이번에는 두 번째 메서드의 `@Scheduled` 어노테이션을 주석 처리하여 `schedulingOne()` 메서드만 동작하도록 하였다. 

![사진 1-1. 작업 시간 2초,  `fixedDelay` 로 지정한 지연 시간 1초로 설정한 메서드를 스케줄링한 결과.](/images/2025-08-26/spring-boot%EC%97%90%EC%84%9C%20%EC%A0%9C%EA%B3%B5%ED%95%98%EB%8A%94%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/1.png)

사진 1-1. 작업 시간 2초,  `fixedDelay` 로 지정한 지연 시간 1초로 설정한 메서드를 스케줄링한 결과.

해당 메서드에는 `Thread.sleep(2000);` 코드를 통해 2초의 작업 시간이 걸린다고 가정하였다. 그리고 지연 시간은 `@Scheduled(fixedDelay = 1000)` 로 잡았다. 이를 실행하여 콘솔창을 보니 위 사진에서도 볼 수 있듯, 작업이 실행되는 시간 자체는 2초가 걸리고, 작업이 끝난 뒤 1초 뒤에 다음 작업이 실행되는 것을 볼 수 있다. 

이번에는 반대로 첫 번째 메서드의 `@Scheduled` 을 주석 처리하고, 두 번째 메서드에서 주석 처리한 것을 해제한 후 실행해보겠다. 해당 메서드는 첫 번째 메서드와 동일하게 작업 시간 2초에 지연 시간이 1초로 똑같다. 다만 `fixedDelay` 가 아닌 `fixedRate` 를 사용했다는 점이 차이점이다. 

![사진 1-2. 작업 시간 2초, `fixedRate` 로 지정한 지연 시간 1초로 설정한 메서드를 스케줄링한 결과.](/images/2025-08-26/spring-boot%EC%97%90%EC%84%9C%20%EC%A0%9C%EA%B3%B5%ED%95%98%EB%8A%94%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/2.png)

사진 1-2. 작업 시간 2초, `fixedRate` 로 지정한 지연 시간 1초로 설정한 메서드를 스케줄링한 결과.

위 사진 결과를 보면 알겠지만, 앞선 첫 번째 메서드 스케줄링과는 시간적으로 차이가 있다는 것을 알 수 있다. 작업 시간 자체는 2초로 일정하지만, 작업이 끝나자마자 바로 다음 작업이 시작되는 것을 볼 수 있다. 즉, `fixedRate` 로 지연 시간을 1초로 지정했지만 이 지연 시간이 제대로 적용되지 않는다는 것이다. 

이러한 현상은 앞서 잠시 언급 했듯, `fixedDelay` 와 `fixedRate` 의 작동 원리에 차이가 있기 때문에 이러한 현상이 발생한다. `fixedDelay` 로 지정한 지연 시간은 이전 작업의 시간이 얼마나 걸리든 상관없이 이전 작업이 종료한 시각부터 지연 시간을 적용하기 때문에 지연 시간이 항상 적용되는 것이 보장된다. 그러나 `fixedRate`는 이전 작업의 “시작” 시점부터 지연 시간을 적용한다. 그래서 지정한 지연 시간보다 작업 시간이 더 길어지는 경우 이미 이전 작업이 시작될 때부터 지연 시간이 적용되었기 때문에 이전 작업이 끝나면 바로 다음 작업이 실행되는 것이다. 따라서 이전 작업의 작업 시간이 얼마나 걸리든 상관없이 지연 시간이 반드시 보장되어야 할 때에는 `fixedDelay`를 사용해야 하는 것에 주의해야 한다. 

## 스케줄러 설정 - 싱글 스레드에서 멀티 스레드로 설정하기

이번에는 앞선 코드 1-3에서 두 메서드에 적용된 모든 `@Scheduled` 어노테이션의 주석을 해제하여 실행해보았다. 

![사진 2-1. 두 스케줄러 메서드를 모두 실행했을 때의 결과. 하나의 작업이 지정된 지연 시간을 꽤 많이 초과하였다. ](/images/2025-08-26/spring-boot%EC%97%90%EC%84%9C%20%EC%A0%9C%EA%B3%B5%ED%95%98%EB%8A%94%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/3.png)

사진 2-1. 두 스케줄러 메서드를 모두 실행했을 때의 결과. 하나의 작업이 지정된 지연 시간을 꽤 많이 초과하였다. 

위 사진에서 볼 수 있듯, 하나의 작업이 지정된 지연 시간인 1초를 훨씬 넘긴 약 10초 후에 다시 실행되는 이상한 현상을 목격할 수 있다. 그 중간에는 다른 작업 하나만 계속 스케줄링되는 것을 볼 수 있다. 

이러한 현상이 발생하는 이유는 스프링에서 제공하는 스케줄러는 기본적으로 싱글 스레드 기반으로 동작하기 때문이다. 위 사진 결과를 미루어보아, 실행 시 싱글 스레드다 보니 특정 기간 동안 하나의 작업만 편향적으로 계속 싱글 스레드에 할당되어 실행된 것이다. 이러한 현상을 방지하기 위해서는 멀티 스레드 환경으로 바꿔줄 필요가 있다. 다음은 관련 설정 클래스를 새로 정의한 코드이다. 

```java
package com.jerocaller.SpringBootScheduling.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.SchedulingConfigurer;
import org.springframework.scheduling.concurrent.ThreadPoolTaskScheduler;
import org.springframework.scheduling.config.ScheduledTaskRegistrar;

/**
 * <p>
 *     스케줄러를 관리하는 스레드 설정 클래스.
 * </p>
 * <p>
 *     SpringBoot 제공 scheduler는 기본적으로 싱글 스레드 기반으로 동작한다.
 *     이로 인해 둘 이상의 스케줄러들이 등록되어 작동할 때 지정한 시간 간격대로
 *     수행되지 않고 엉뚱한 시간 간격으로 진행될 수 있다.
 *     이를 방지하기 위해 멀티 스레드로 설정한다.
 * </p>
 */
@Configuration
public class SchedulerConfig implements SchedulingConfigurer {

    // 다른 클래스에서도 사용할 수 있도록 static으로 선언함.
    private final static int POOL_SIZE = 5;

    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        final ThreadPoolTaskScheduler threadPoolTaskScheduler = new ThreadPoolTaskScheduler();

        threadPoolTaskScheduler.setPoolSize(POOL_SIZE);
        threadPoolTaskScheduler.setThreadNamePrefix("config-scheduler");
        threadPoolTaskScheduler.initialize();

        taskRegistrar.setTaskScheduler(threadPoolTaskScheduler);
    }
}

```

코드 2-1. 스케줄링 설정을 통해 스케줄링에 사용될 스레드를 멀티 스레드 환경으로 설정하고 있다. 

스케줄러 관련 설정을 위해서는 `@Configuration` 이 적용된 클래스가 `SchedulingConfigurer` 인터페이스의 `configureTasks()` 메서드를 구현하도록 해준다. 위 코드에서는 스레드 개수를 5개로 설정하고 이를 스케줄러에 적용하는 모습이다. 이제 이 상태에서 다시 앱을 실행해보면 다음의 결과를 얻는다. 

![사진 2-2. 스케줄러 멀티 스레드로 설정 후 스케줄링 실행 모습](/images/2025-08-26/spring-boot%EC%97%90%EC%84%9C%20%EC%A0%9C%EA%B3%B5%ED%95%98%EB%8A%94%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81%20%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0/4.png)

사진 2-2. 스케줄러 멀티 스레드로 설정 후 스케줄링 실행 모습

위 사진 결과를 자세히 보면 맨 처음 두 작업이 동시에 시작된 것을 볼 수 있다. 이를 통해 기존 싱글 스레드에서는 두 작업이 동기적으로 실행되었던 반면 이번에는 두 작업이 비동기적으로 실행되었음을 알 수 있다. 또한 두 작업이 각각 지정된 지연 시간대로 지연되며 다음 작업이 실행되는 정상적인 모습을 볼 수 있다. 이를 통해 추측해볼 수 있는 점은, 스케줄링할 메서드들이 N개가 있을 때, 각 메서드들의 지연 시간이 보장되도록 하려면, 즉 모든 스케줄러 메서드가 비동기적으로 동작할 필요가 있다면 적어도 N개 사이즈를 가지는 스레드풀을 설정해주는 것이 좋다는 것이다. 

# 글을 마치며…

지난 시간에 다뤄본 Quartz와 비교해보면 사용법이나 구조 자체는 이 글에서 다룬 스프링부트 기본 제공 스케줄러가 더 다루기 쉬운 것 같다. 하지만 Quartz와 비교해봤을 때 다음의 단점들이 눈에 띄었다. 

- 애플리케이션 실행을 중단하지 않고도 동적으로 스케줄링하는 작업에 새 데이터를 추가한다든가 동적으로 새로운 트리거를 적용시킨다든가 할 수 없다. 즉, 설정 파일이라든가 API 등 애플리케이션 외부에서 들어오는 스케줄링에 관해 동적으로 변경시킬 수 있는 조건들을 받아 그에 맞게 스케줄링을 변경하기 힘들다.
- Job과 Trigger의 분리가 되어 있지 않아 Job에 대한 유연한 스케줄링이 힘들다.
- DB에 스케줄링 관련 정보를 저장할 수 있어 애플리케이션이 중단 및 재시작이 되어도 스케줄링을 이어서 할 수 있었던 quartz와 달리 DB에 스케줄링 관련 정보를 저장할 수 있는 기능이 없어 스케줄링 정보의 영속화 이점을 얻을 수 없다.

이러한 점들을 미루어보았을 때, 역시 다른 사람들의 말대로 간단한 스케줄링 작업에는 스프링 부트에서 기본으로 제공하는 스케줄링 기능을 사용하는 것이 좋고, 조금만 복잡해져도 Quartz를 사용하는 것이 더 편하다는 것을 직감할 수 있었다. 

아무튼 이 글이 실제로 마주치는 문제의 성격에 따라 어떤 스케줄링 도구를 사용해야 좋을지 판단할 수 있는 기초적인 참고 자료가 되었기를 바란다. 

---

References

[1] 스프링부트 제공 스케줄러 사용법

[스프링부트 Scheduler 정해진 시간마다 동작 시키는법](https://devpad.tistory.com/133)

[2] 스프링부트 제공 스케줄러 사용법 및 quartz와의 차이점

[[spring] spring scheduler vs quartz scheduler](https://wouldyou.tistory.com/114)

[3] 스프링부트 제공 스케줄러 사용 및 fixedDelay vs fixedRate

[Spring Boot - 스케줄러 사용해보기 1. FixedDelay vs FixedRate](https://seolin.tistory.com/123)

[4] Spring.io - scheduling

[Task Execution and Scheduling :: Spring Framework](https://docs.spring.io/spring-framework/reference/integration/scheduling.html)