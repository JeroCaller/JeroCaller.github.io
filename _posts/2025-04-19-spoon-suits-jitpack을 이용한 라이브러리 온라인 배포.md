---
title: "[Spoon Suits] Jitpack을 이용한 라이브러리 온라인 배포"
category: "Personal Project"
tag: ["Personal Project", "Library", "Spring Boot", "Spoon Suits", "Maven", "Jitpack", "Gradle", "Github", "Tag", "Release"]
---


저번 글 “[[Spoon Suits] 스프링부트 기반 라이브러리 제작 후 로컬 maven 저장소에 배포하기](/personal%20project/spoon-suits-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%EA%B8%B0%EB%B0%98-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EC%A0%9C%EC%9E%91-%ED%9B%84-maven-%EB%A1%9C%EC%BB%AC-%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0/)” ****에서는 직접 만든 스프링부트 라이브러리를 로컬 기기에서 배포 및 사용하는 방법에 대해 다뤄보았다. 이번 글에서는 이 라이브러리를 전 세계 어디서든, 또는 언제든지 사용할 수 있도록 온라인으로 배포하기 위해 필자가 겪고 배운 과정들을 적어보고자 한다. 

필자가 완성한 Spoon Suits 라이브러리의 Github repository는 다음을 참고. 

[https://github.com/JeroCaller/Spoon-Suits](https://github.com/JeroCaller/Spoon-Suits)

# 라이브러리 온라인 배포를 위한 도구 선택

사실 이전 글에서도 아주 잠깐 언급하기도 했고, 이 글에서도 언급할 사이트인 Jitpack을 이용한 방법 외에도 다른 방법으로 온라인 배포가 가능하다고 한다. 필자가 조사해본 라이브러리 온라인 배포 방법에는 다음이 있었다 (어쩌면 이것 말고도 다른 방법들도 더 많이 있을지 모른다).

- Maven Repository 사이트를 이용한 배포
- Jitpack 사이트를 이용한 배포
- Github packages를 이용한 배포
- Nexus Repository를 이용한 배포
    - 이 방법은 위의 다른 항목들과 달리 회사의 사내에서만 사용할 수 있다고 한다. 그래서 엄연히 말하면 온라인 배포용은 아니라고 볼 수 있다. 후에 조사해보니 외부에서의 접근을 막아야하거나 어려운 상황일 때 유용하게 사용된다고 한다. 회사 내에서 실제로 사용한다는 후기들이 많았다. 필자는 이를 사용해본적은 없지만 이야기들만 들어보면 개인용으로도 사용할 수 있지 않을까, 라는 생각도 든다. 그런데 개인용이면 그냥 이전 글에서 언급했던 maven local repository를 이용하여 로컬 기기로 배포하는 방법을 사용하는 게 더 간단할지도 모른다.

이 중에서 배포가 가장 간단하고 빠른 것으로 Jitpack이 알려져 있어 필자도 이 사이트를 온라인 배포 도구로 채택하였다. 필자가 만든 라이브러리도 만드는 데 의도치 않은 시간들이 많이 사용되어서 그렇지, 사실 거창한게 아니고 스프링부트 프로젝트 만들 때 쉽고 간단하게 사용하려고 만든거라 온라인 배포에서도 어떤 거창하거나 복잡한 작업이 전혀 필요없었다. 아니, 간단한 배포 및 빠른 사용을 위해서는 오히려 거창하거나 복잡한 과정을 거치는 것은 피하는 게 좋다고 생각했다. 한 편, 필자가 만든 라이브러리는 솔직히 말하면 혼자서 사용할 계획으로 만들긴 했지만, private으로 감춰야할 정도로 어떤 민감한 정보가 있거나 보안적인 이슈가 있는 것도 아니었고, 꼭 내 라이브러리를 사용하는 다른 사용자가 없더라도 내가 기기에 구애받지 않고 언제 어디서든 사용할 수 있다면 그것만으로도 큰 장점이 아닐까, 란 생각을 가지고 있었다.

필자도 외부 라이브러리 사용을 위해 종종 검색 용도로 사용하는 “[Maven Repository](https://mvnrepository.com/)” 사이트를 통해서도 라이브러리 배포가 가능하다고는 하나 조사 결과 그 과정이 꽤 복잡하다고 한다. 또한 Github packages는 말 그대로 Github라는 사이트에서 제공하는 것이기에 Github에 익숙한 사람이라면 Github의 생태계에 놓여있기에 친숙할 것이라는 장점도 있으나, 이를 사용하기 위해 발급받아야 하는 토큰이 노출되고, 이를 방지하기 위해 private repository로 바꿔야 하는데, 여기서 비용이 발생한다는 단점이 있다는 주장도 있었다. Nexus의 경우 일단 온라인 배포도 아닐 뿐더러, 배포 사이트에 대해 처음 조사하였을 땐 그 존재 자체도 몰랐던 사이트였다. 이 글을 쓰고 있는 시점에서 어떠한 사이트인지 그 개요만 잠깐 본 수준이다. 이러한 이유들로 인해 결국 필자가 생각하는 라이브러리 배포의 목적 및 주어진 상황에 가장 잘 부합한다고 여겨졌던 Jitpack을 선택하였다. 

# Jitpack을 이용한 라이브러리 배포

Jitpack은 자바 및 안드로이드 프로젝트 배포를 위한 사이트로, Commit이나 Github tag 및 Release를 기반으로 배포하는 방식이라 앞서 언급했던 타 배포 사이트들에 비해 훨씬 배포 과정이 간단한 편이다. 그럼에도 필자는 역시 이 Jitpack을 처음 접하기도 하였고, 필자의 상황이 일반적인 상황과는 조금 달랐기에 Jitpack을 이용한 배포 과정에서도 애를 좀 먹기도 했다. 

## 배포를 위해 필요한 프로젝트 내에서의 설정

### 멀티 모듈 프로젝트에서 Gradle을 이용한 배포 설정

필자가 만든 라이브러리 소스 코드가 담긴 repository에는 하나의 프로젝트 수준의 폴더만 있는 것이 아니였다. 이 라이브러리를 테스트할 용도의 프로젝트 폴더도 같이 있었다. 즉, 하나의 프로젝트 폴더를 모듈(module)이라 할 때 필자는 single module 구조가 아니라 multiple modules의 구조였다. 

> 멀티 프로젝트(multi-project)란 용어도 있지만 Jitpack 공식 문서에서는 module이라고 표현한다. 따라서 이 글에서는 이 두 용어를 섞어 사용하겠다.
> 

```
/spoonsuits   // root
    /spoonsuits  // 라이브러리 프로젝트 폴더
    /spoonsuits-local-test  // spoonsuits 라이브러리의 테스트를 위한 프로젝트 폴더
```

코드 1-1. Spoon Suits 라이브러리 폴더 구조. 참고로 두 폴더 내부의 최상위 레벨에 모두 각각의 build.gradle 파일들이 존재한다. 

그러다보니 이 상태로 Jitpack에 배포하니 배포에 실패했었다. 이후 추가적인 조사와 시도 끝에 이 문제를 해결할 수 있었다. 이 문제의 원인은 리포지토리 최상단에 별도로 build.gradle 및 settings.gradle을 작성하지 않았고, 이 build.gradle을 실행시켜 만들어진 gradle/wrapper 폴더 및 그 내부의 내용물이 존재하지 않았기에 발생한 문제였다. 즉, 다음과 같은 구조로 바꿨어야 했었다.

```
/spoonsuits   // root
    /spoonsuits 
    /spoonsuits-local-test
    build.gradle
    settings.gradle
    /gradle  // build.gradle 실행 시 생성되는 폴더 및 그 내용물들
        /wrapper
            gradle-wrapper.jar
            gradle-wrapper.properties
```

코드 1-2. 수정 후의 라이브러리 repository 구조.

배포에 실패했던 원인을 자세히 살펴보면 다음과 같다. 1차적으로 마주치는 원인은 다음과 같다.

```
ERROR: Gradle wrapper not found. Please add. Using default gradle to build.

grep: gradle/wrapper/gradle-wrapper.properties: No such file or directory
Generating Gradle wrapper v 6.7.1
```

코드 1-3. Jitpack 배포에서 실패했을 때 필자가 마주친 로그. 

위 메시지는 필자가 Jitpack 배포에 실패했을 때 마주친 로그이다. 참고로 위와 같은 배포 로그는 Jitpack 사이트에서 볼 수 있다. 어쨌거나 위 메시지를 보면 gradle wrapper에 해당하는 디렉토리 및 관련 파일들을 Jitpack이 찾지 못했기에 배포에 실패한 것이다. 이러한 디렉토리 및 파일들을 생성하려면 build.gradle 파일이 라이브러리 repository 내 최상단에도 존재해야 했다. 아니면 테스트 폴더는 제외하고 라이브러리 폴더 내 내용들로만 repository를 채우든가 해야하는데, 이 경우 필자가 따로 Github에서 별도의 repository를 파고 거기에 테스트 폴더를 넣든가 해야 한다. 하지만 이렇게 별도로 분리하면 하나의 리포지토리만 관리하면 될 것을 두 개씩이나 관리해야하고, 사용자 입장에서도 소스 코드의 구조를 제대로 파악하기 힘들 것 같아서 하나의 리포지토리로 합쳐서 관리하기로 한 것이다. 더군다나 테스트 폴더만 있는 것도 아니었고 라이브러리 아이콘을 저장하기 위한 폴더, README 파일 등 다른 파일 및 폴더들도 있어야 했다. 따라서 이 해결책은 근본적인 해결책이 아니었다. 

이러한 이유로 최상단에서도 build.gradle 및 settings.gradle 파일을 만들었다. Jitpack 공식 Github의 repository 소스 코드 및 공식 문서를 참고하여 필자는 각각 다음과 같이 작성하였다. 

```java
// 라이브러리 폴더만 배포하기 위한 설정.
include 'spoonsuits'
```

코드 1-4. settings.gradle

```java
allprojects {
    version = "1.0.0"
    group = 'com.jerocaller.libs.spoonsuits'
}

subprojects {
    apply plugin: 'java'
    apply plugin: 'maven-publish'

    sourceCompatibility = '21' // java 21
    targetCompatibility = '21'

    java {
        withSourcesJar()
        withJavadocJar()
    }

}
```

코드 1-5. build.gradle. [Jitpack 공식 Github repository](https://github.com/jitpack/gradle-modular)를 참고하여 필자의 방식대로 일부 수정하였다. 

settings.gradle에서는 하위 모듈들의 존재들을 인식하고 관리할 수 있도록 하기 위한 코드가 들어가 있다. 여기서는 라이브러리 모듈에 해당하는 “spoonsuits”만 배포할 것이고, 테스트 폴더는 배포 대상이 아니었기에 ‘spoonsuits’ 폴더에 대해서만 포함시켰다. 이로 인해 Jitpack에서는 “spoonsuits”라는 폴더만 인식하여 이 폴더만 배포하게 된다. 만약 여러 모듈들이 있고 이 모듈들을 모두 인식하게끔 하려면 각 줄마다 `include '모듈명'` 과 같은 형식으로 작성하면 된다. 

build.gradle 파일 내부에는 크게 “allprojects” 부분과 “subprojects” 부분이 있다. allprojects는 최상위(root) 프로젝트의 build.gradle를 포함한 모든 하위 프로젝트들의 build.gradle를 제어한다. 달리 말하면 root project를 포함한 모든 서브 프로젝트들까지 전부 다 제어의 대상이 된다는 것이다. subprojects는 여기서 root project만 제외한 모든 서브 프로젝트들을 대상으로 한다.  그래서 사실 제어 범위만 한 끗 차이로 다를 뿐 그 외에는 별 다른 차이점이 없다고 한다. 

이 사실을 염두해두고 위 코드 1-5를 보면, 루트 프로젝트를 포함한 모든 프로젝트들의 버전 및 그룹들을 “allprojects” 부분에서 통일된 데이터로 적용하도록 하였으며, “subprojects”에서는 모든 하위 프로젝트들에 공통적으로 적용할 플러그인, 자바 버전, 소스 코드 및 javadoc을 담은 jar 파일 생성을 하도록 설정하였다. 

이후에는 이 build.gradle 파일을 한 번 실행시킨다. 그러면 앞서 언급했던 `/gradle/wrapper` 폴더 및 그 내부의 특정 파일들이 생성된다. 이 폴더 및 그 내부에 있는 파일들을 Jitpack이 읽어 배포할 수 있는 원리이다. 

> Jitpack 공식 문서에 따르면 Jitpack에서도 개발자가 만든 라이브러리의 javadoc 페이지를 게시할 수 있게 해주는데, 이를 위해선 javadoc.jar라는 파일을 생성할 수 있게 해줘야 한다. 이를 위해 `withJavadocJar()` 코드를 삽입한 것이다.
> 

한 편 이러한 gradle의 특징들을 이용하면, 하나의 루트 프로젝트 안에 분야별로 여러 서브 모듈들로 나눠 배포할 수도 있을 것이다. 이 경우 사용자는 해당 라이브러리의 모든 모듈들을 사용하도록 할 수도 있고, 아니면 필요한 모듈만 골라 사용할 수 있게 된다. 사용자 입장에서 Jitpack에 배포된 라이브러리를 사용하는 방법에 대해서는 후술할 예정. 

### 라이브러리 내 Gradle 설정

한 편 라이브러리 모듈 내 build.gradle에서는 Jitpack 배포를 위해 다음과 같은 설정을 하였다. 

```java
plugins {
	//  
	id 'maven-publish'  // 추가 #1
}

// 추가 #2
publishing {
	publications {
		maven(MavenPublication) {
			// Publication only contains dependencies and/or constraints without a version. You should add minimal version information, publish resolved versions
			// 아래 versionMapping을 더해주지 않으면 위와 같은 에러가 뜸.
			versionMapping {
				usage('java-api') {
					fromResolutionOf('runtimeClasspath')
				}
				usage('java-runtime') {
					fromResolutionResult()
				}
			}

			groupId = 'com.jerocaller.libs.spoonsuits'
			artifactId = 'spoonsuits'
			version = "1.0.0"

			from components.java
		}
	}
}
```

코드 2-1. build.gradle

Jitpack 공식 문서에 따르면, Gradle을 이용하여 배포 시 maven이나 maven-publish 둘 중 하나의 플러그인을 사용해야 한다고 한다. 위 코드에서는 후자를 택해 #1과 같이 삽입하였다. 사실 이 부분은 앞선 코드 1-5에서 해당 플러그인을 모든 하위 프로젝트들에 대해 적용되도록 하였기에 불필요할 수도 있다. 여기서는 여러 개의 프로젝트들이 있는 구조가 아니라 단일 프로젝트 구조인 경우를 가정하고 작성한 것이라 이렇게 된 것이다. 한 편, 위 코드 2-1의 #2와 같은 코드 블록을 추가하면 된다. 사실 #1 부분과 더불어 이 부분은 지난 글에서처럼 Maven local repository에 배포하기 위해 이미 설정한 코드라 필자의 입장에서는 딱히 추가할 것이 없었다. 그래서 위 코드는 단일 프로젝트로 Jitpack에 바로 배포하고자 할 때 참고하기 위해 이렇게 남긴다. 

### jitpack.yml 파일을 이용한 배포 설정

한 편, Jitpack은 라이브러리 배포 시 기본적으로 OpenJDK Java 8 버전으로 배포를 시도한다고 한다. 그런데 필자의 자버 버전은 21이였다. 그래서 이 상태 그대로 배포하면 이로 인한 에러가 발생하면서 배포에 실패할 것이다. 따라서 필자는 라이브러리 리포지토리의 최상단에 jitpack.yml 파일을 생성한 뒤, 다음과 같은 내용을 작성하였다.

```yaml
jdk:
  - openjdk21
```

코드 3-1. jitpack.yml

위 코드에서는 “jdk” 프로퍼티를 이용하여 “openjdk21”로 배포하도록 설정하였다. “jdk” 외에도 “before-install”을 통해 라이브러리 설치 전의 작업을 수행하도록 설정할 수도 있고, “install”을 통해 어떻게 라이브러리를 설치할 것인지도 명령어를 작성할 수 있다. 참고로 여기서 사용되는 명령어들은 리눅스를 기준으로 한다(Jitpack 배포 로그를 자세히 보면 Linux 환경에서 배포되었다는 로그를 볼 수 있다). 이에 대한 자세한 사항은 Jitpack 공식 문서의 일부인 [https://docs.jitpack.io/building/#custom-commands](https://docs.jitpack.io/building/#custom-commands) 사이트 참고. 

## Github Tag & Release 생성으로 버전 부여한 후 Jitpack에서 배포하기

라이브러리 소스 코드가 담긴 Repository 내에 앞서 언급한 설정들을 모두 마치면 이번에는 Github repository에서 작업을 해줘야한다. 현재 main 브랜치에 버전 번호가 부여된 태그를 추가하고, 그로 인해 생성되는 Release를 토대로 Jitpack에 배포할 수 있게 된다. 사실 로컬에서도 Git에서 특정 커밋에 Tag를 부여한 뒤, origin으로 push해도 똑같이 할 수 있지만 여기서는 Github repository에서 직접 tag를 부여하여 release 해볼 것이다. 

![사진 1-1.](/images/2025-04-19/spoon-suits-jitpack%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EB%B0%B0%ED%8F%AC/1.png)

사진 1-1.

먼저 위 사진처럼 라이브러리가 담긴 Github repo 화면으로 이동한다. 그러면 우측에 “Release” 항목이 있다. 지금은 이미 릴리즈한 상태라 위 사진과 같은 상황이지만, 처음에 아무것도 릴리즈하지 않은 상태라면 “Create a new release” 라는 하이퍼링크가 존재한다. 이를 클릭한다. 

![사진 1-2.](/images/2025-04-19/spoon-suits-jitpack%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EB%B0%B0%ED%8F%AC/2.png)

사진 1-2.

그러면 위와 같은 화면으로 이동할 것이다. “Target: main” 왼쪽에 “v1.0.0”이라는 버전명과 함께 태그 아이콘이 그려진 드롭박스가 있다. 이 버전명은 필자가 작성한 것으로, 해당 버튼을 클릭하여 “v1.0.0”이라 적고 해당 태그를 생성한 후의 모습이다. 아래에는 release 타이틀 및 해당 릴리즈에 대한 설명을 적을 수 있는 공간이 있다. 필자는 위 사진과 같이 작성한 후 아래 “publish release” 버튼을 눌러 릴리즈를 생성하였다. 

![사진 1-3. ](/images/2025-04-19/spoon-suits-jitpack%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EB%B0%B0%ED%8F%AC/3.png)

사진 1-3. 

그러면 위 사진과 같이 현재 만든 버전의 릴리즈가 최상위에 위치하게 되며, 만약 여러 번의 릴리즈를 한 경우 위 사진처럼 목록으로 뜨게 된다. 이렇게 Github에서 tag를 부여하고 release까지 하게 되었다. 참고로 이 상태에서 로컬 repo로 pull을 받으면 아래 사진과 같이 태그 내역이 고스란히 남게 된다.

![사진 1-4. Github에서 여러 번 release한 후 로컬 repo로 pull 받은 후의 모습. 여러 군데에서 버전명이 담긴 태그들이 보인다. ](/images/2025-04-19/spoon-suits-jitpack%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EB%B0%B0%ED%8F%AC/4.png)

사진 1-4. Github에서 여러 번 release한 후 로컬 repo로 pull 받은 후의 모습. 여러 군데에서 버전명이 담긴 태그들이 보인다. 

Jitpack은 Github release를 기준으로 버전별로 라이브러리가 배포되기에 위와 같은 작업들을 진행한 것이다. 위 과정까지 모두 완료하였다면 이제 [jitpack.io](http://jitpack.io) 사이트로 이동한다. 

![사진 1-5. jitpack 사이트 첫 화면](/images/2025-04-19/spoon-suits-jitpack%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EB%B0%B0%ED%8F%AC/5.png)

사진 1-5. jitpack 사이트 첫 화면

위 사진과 같이 해당 사이트로 이동하면 가운데에 입력란이 뜨는데, 여기에 배포할 Github repository 주소를 넣은 후, “Look up” 버튼을 클릭한다. 

![사진 1-6. ](/images/2025-04-19/spoon-suits-jitpack%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EB%B0%B0%ED%8F%AC/6.png)

사진 1-6. 

필자의 경우 몇 번의 수정이 있었기에 위와 같이 여러 버전들이 나타나있다. 배포하고자 하는 항목의 오른편에 있는 “Get it”을 클릭하면 배포가 시작된다. 잠시 기다리면 “Log” 쪽에 위 사진과 같이 문서 모양의 아이콘이 뜨게 된다. 이 때 문서 아이콘이 빨간색이면 배포에 실패했다는 뜻이고, 초록색이면 배포에 성공했다는 뜻이다. 해당 문서 아이콘을 누르면 배포 로그를 자세히 볼 수 있다. 

# Jitpack으로 배포한 라이브러리 사용법

이제 온라인 상에서 배포된 라이브러리를 자신의 프로젝트 내에서 사용해봐야할 차례이다. 사실 앞선 사진 1-6의 화면에서 아래로 스크롤 내리기만 하면 다음의 사진을 볼 수 있다. 

![사진 2-1. ](/images/2025-04-19/spoon-suits-jitpack%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%98%A8%EB%9D%BC%EC%9D%B8%20%EB%B0%B0%ED%8F%AC/7.png)

사진 2-1. 

위 절차대로 따라하면 된다. 사실 실제로는 다음과 같이 간소화할 수 있다. 

```java
repositories {
	// ...
	maven { url "https://jitpack.io"  }
}

dependencies {
    // com.github.JeroCaller:Spoon-Suits:v<버전>
    implementation 'com.github.JeroCaller:Spoon-Suits:v1.0.3'
}
```

코드 3-1. build.gradle

위와 같이 사용자의 프로젝트 내 build.gradle 파일 내에 위와 같은 내용을 입력하고 해당 파일을 재실행하면 해당 라이브러리를 자동으로 자신의 프로젝트에 다운로드 받아 사용할 수 있게 된다. 

만약 여러 개의 프로젝트들이 하위 레벨에 존재하는 구조일 경우에는 다음과 같이 특정 모듈만을 골라 사용할수도 있다고 한다. 

```java
implementation 'com.github.User.Repo:Module:Tag'

// 예시. 만약 Cookie라는 서브 모듈이 존재하며 이 모듈만 사용하고자 하는 경우
implementation 'com.github.JeroCaller.Spoon-Suits:Cookie:v1.0.3'
```

코드 3-2. 멀티 모듈 구조의 라이브러리에서 특정 모듈만을 사용하고자 할 때의 build.gradle

물론 멀티 모듈 구조의 라이브러리에서 모든 모듈들을 가져와 사용할 수 있으며, 이를 위해선 코드 3-1과 같이 작성하면 된다. 

한 편 필자가 만든 라이브러리 repo 내 패키지 구조는 테스트 폴더의 존재로 인해 멀티 모듈 구조로 보이지만, 사실 테스트 폴더는 배포용이 전혀 아니었고, 라이브러리 폴더만 배포하도록 설정하였기에 사실상 싱글 모듈 구조나 다를바가 없다. 그래서 코드 3-2와 같이 하면 해당 라이브러리를 불러올 수 없고, 코드 3-1과 같이 해야 불러올 수 있게 된다. 

> Jitpack에서 멀티 모듈 배포 기능을 허용하기에 이를 이용하여 필자도 하나의 모듈에 모인 각 분야의 기능들을 별도의 모듈들로 분리하여 배포할 수 있을 것이다. 처음에는 이 사실을 몰라 일단 하나의 모듈 내부에서 모든 분야의 기능들을 한꺼번에 몰아 넣었는데, 특정 모듈만 선택하여 사용할 수 있도록 하고자 한다면 멀티 모듈 구조로 리팩토링하는 것도 고려해볼만한 점일 것 같다.
> 

# 글을 마치며…

이렇게 하여 스프링부트 기반 라이브러리를 제작하여 Jitpack을 이용하여 온라인 상에 배포하고 사용하는 과정에 대해 살펴보았다. 그런데 이 과정에서 매번 코드의 수정 및 새로운 기능을 추가할 때마다 Github에서 새로운 버전의 태그를 일일이 부여하고 일일이 change log 작성과 함께 릴리즈하는 것은 은근 번거로운 작업이다. 다행히도, CI / CD의 도구들 중 하나인 Github Actions를 이용하면 이러한 과정조차 자동화할 수 있다. 다음 글에서는 Github Actions를 이용하여 Github tag & release를 자동화하는 방법에 대해 살펴보겠다. 이와 더불어 CI, CD 및 Github Actions에서 사용하는 여러 개념들에 대해서도 살펴보고자 한다. 

---

References

[1] jitpack 공식 사이트

[JitPack \| Publish JVM and Android libraries](https://jitpack.io/)

[2] jitpack 공식 docs

[:: Documentation for JitPack.io](https://docs.jitpack.io/)

[3] jitpack 공식 repository - 멀티 프로젝트일 경우 배포 방법 예시

[https://github.com/jitpack/gradle-modular](https://github.com/jitpack/gradle-modular)

[4] [스프링부트 공유라이브러리 만들고 jitpack으로 배포하기 - 1편](https://ssdragon.tistory.com/167)

[5] [스프링부트 공유라이브러리 만들고 jitpack으로 배포하기 - 2편](https://ssdragon.tistory.com/168)

[6] [Jitpack & github releases를 사용한 라이브러리 배포](https://swmobenz.tistory.com/42)

[7] [What is the difference between allprojects and subprojects](https://stackoverflow.com/questions/12077083/what-is-the-difference-between-allprojects-and-subprojects)

[8] gradle - multi project

[gradle 정리 - multiple project](https://netframework.tistory.com/entry/gradle-%EC%A0%95%EB%A6%AC-multiple-project)

[9] gradle - multi project

[[Gradle] 프로젝트 수준의 Gradle에서 모듈 수준의 Gradle 제어하기](https://kotlinworld.com/324)