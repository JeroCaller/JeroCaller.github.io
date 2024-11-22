---
title: "[DevelOpinion] 성능에 대한 고민 - pool 구상"
category: "DevelOpinion"
tag: ["DevelOpinion", "pool", "디벨로피니언"]
---

DevelOpinion 디벨로피니언. 개발에 대한 저의 생각과 의견을 내보는 글의 성격을 DevelOpinion 디벨로피니언이라고 지어봤습니다. 

어제 학원에서 강사님께서 내신 문제를 풀어보았다. 시간상 다 풀지는 못했고, 백엔드 단(스프링 부트를 사용하였다)만 완성시키고, 프론트엔드는 이제서 리액트에서 리덕스 설정 코드, 그러니까 reducer와 action을 어떻게 설정해야하는지 고민하는 단계에 들어선 게 전부다. (사실 redux의 개념 자체는 적어도 강사님께서 알려주신 기초적인 개념만 보면 이해하기 어려운 것은 아니지만, 실질적으로 코드 상에서 어떻게 설정하고 사용하는지 그 순서와 action과 reducer간의 결합 방법, store 설정 방법, 그리고 이를 실질적으로 클라이언트 쪽에서 이를 어떻게 호출해야하는지에 대한 것이 아직도 헷갈린다) 다른 분들 중에서는 이미 나보다 더 빨리 진도를 나가신 분들이 계셨다. 불과 몇 달 전의 나였다면 “난 왜 이리 문제를 늦게 푸는거지?” 라며 불안해했을 지도 모르지만, 적어도 오늘은 그런 불안함은 들지 않았다. 며칠, 아니 어쩌면 몇 주 전부터 난 새로 든 생각이 있었기 때문이다. 

“구현이 다가 아니다”

물론 구현 난이도가 높으면 코드 가독성, 성능, 효율성 등을 따질 겨를도 없이 코드가 dirty하더라도 구현 자체가 가능한지를 먼저 따지면서 코딩을 하는 게 우선일 것이다. 하지만 구현 난이도가 쉽건, 아니면 구현 난이도가 어려움에도 구현에 성공했건 간에 구현만 하는 게 다가 아니라는 생각이다. 코드를 얼마나 가독성 있게 작성하였느냐. 프로젝트 전반에 걸쳐 중복되는 패턴은 없는지, 있다면 어떻게 그 중복을 없앨 것인지, 그리고 성능에 대한 고려도 하였는지, 혹시 너무 비효율적으로 동작하도록 코드를 짠 건 아닌지 이런 것들을 생각하는 것도 중요하다고 느끼고 있다. 

오늘 푼 문제 중 백엔드 단에서 서비스 클래스를 작성하고 있을 때였다. CRUD 기능을 하는 각각의 메서드들을 구현하고 있었다. RESTFul System 중점으로 개발해야했기에 DB 작업으로 나온 결과물을 JSON으로 응답하도록 코드를 구성해야헀다. 그렇다보니 아무래도 DB 작업으로 나온 데이터들을 JSON 형태로 내보내도록 하기 위해 최종적으로는 Map 타입을 사용할 수 밖에 없었다. 그런데 insert, update, delete에 해당하는 각각의 메서드들을 작성하고 보니 모두 공통적으로 JSON 데이터 구성을 위한 HashMap 객체를 각 메서드의 최상단에서 생성하고 있는 패턴을 발견하게 되었다. 이걸 보고 이런 생각을 하였다. 

“실무였다면 이러한 메서드들이 정말 쉼없이 호출될 텐데, 그 때마다 매번 HashMap 객체를 생성해야 한다고? 이건 메모리 입장에서는 너무 부담스러운 비용아닌가?”

실제로 이와 비슷한 문제에 대해 JDBC에서는 DBCP라는 개념으로, Thread에서는 Thread Pool이라는 방법으로 대응하고 있다. 이 개념들의 공통점은 매번 필요할 때마다 객체들을 생성했다가 삭제하는 행위 자체가 메모리에 부담을 주는 행위이니 객체를 재활용하자는 철학을 가지고 있다. 이러한 생각에 미치자 나는 아예 HashMap 객체를 최초로 한 번만 생성하고, 이를 필요할 때마다 재활용하자는 생각을 하게 되었다. 그래서 나는 아예 HashMap 객체를 딱 한 번만 생성하여 재활용하는 싱글톤 패턴을 적용하여 조금이나마 메모리 부하를 줄이고자 해보았다. 물론 강사님께서 내신 문제는 대규모 데이터가 들어온다든가 어떠한 연산이 정말로 컴퓨터에 부하를 줄 정도의 문제는 아니었지만, 그럼에도 나는 지금 내가 풀고 있는 문제가 정말 실무에 상용화될 제품이다, 생각하고 이러한 고민을 해보았다. 

원래 서비스단의 코드가 이러했다면

```java
public Map<String, Object> insertOne(ExerciseRequest request) {
  Map<String, Object> response = new HashMap();
		
  //... 생략
}

public Map<String, Object> updateOne(ExerciseRequest request) {
  Map<String, Object> response = new HashMap();
		
  // ...
}

public Map<String, Object> deleteOne(Long targetId) {
  Map<String, Object> response = new HashMap();
		
  // ...
}
```

다음과 같이 싱글톤 패턴의 별도의 클래스를 정의하고

```java
package pack.model.service;

import java.util.HashMap;
import java.util.Map;

import org.springframework.stereotype.Service;

@Service
public class ServiceHelper {
	
  /**
    * 반복되어 사용되는 JSON 출력용 Map 타입 객체를 매번 생성하여 메모리에 부하를 주지 않도록 
   * 똑같은 Map 객체를 재사용하도록 한다.
   */
  public static class ResponseMap {
    private static final Map<String, Object> responseMap = new HashMap<String, Object>();
		
    public static Map<String, Object> getClearedResponseMap() {
      responseMap.clear();
      return responseMap;
    }
}
	
  // 그 외 다른 성격의 헬퍼 메서드들 작성.

}
```

그 다음 서비스단 코드를 다음과 같이 바꿨다.

```java
public Map<String, Object> insertOne(ExerciseRequest request) {
  Map<String, Object> response = ServiceHelper.ResponseMap.getClearedResponseMap();
		
  // ...
}

public Map<String, Object> updateOne(ExerciseRequest request) {
  Map<String, Object> response = ServiceHelper.ResponseMap.getClearedResponseMap();
		
  // ...
}

public Map<String, Object> deleteOne(Long targetId) {
  Map<String, Object> response = ServiceHelper.ResponseMap.getClearedResponseMap();
		
  // ...
}
```

그러면 적어도 위 메서드들이 자주 사용될 때 HashMap 객체를 매번 생성, 소멸시키지 않고 단 하나의 객체를 재활용하니 메모리 비용 문제에 대한 솔루션이 되지 않을까, 생각했다. 

그런게 막상 되돌아보니 저렇게 코드를 짠 건 조금 멍청한 생각이었다. 다음의 두 가지 이유에서였다. 

1. 어차피 스프링 프레임워크에서는 하나의 클래스 내부에 다른 객체를 의존성으로 주입할 때 주입되는 객체는 기본적으로 singleton으로 생성된다. 그래서 굳이 저렇게 별도의 싱글톤 클래스를 정의할 필요가 없었다. 
2. 정말 실무를 생각해서 고려한 것이라면 위 코드는 동시 접속자가 생기게 되어 동시적으로 메서드 호출을 하려고 하면 문제가 발생할 수 있다. 사용자가 두 명만 되어도 이 둘이 동시에 한 명은 insert 관련 기능을, 또 한 명은 update 관련 기능을 요청했을 때 두 메서드 모두 단 하나의 HashMap 객체를 “동시”에 사용해야 하는데 이 때 충돌이 일어날 수 있다. 즉 thread-safety하지 않다. 

두 번째 문제는 기존의 싱글톤 패턴에서 조금 더 확장해서 아예 Pool을 구현하면 해결되지 않을까, 란 생각이 든다. 즉, 단 하나의 HashMap 객체만을 생성하여 운용하는 게 아니라, 일정 개수의 객체들을 생성한 후 이들을 재활용하는 것이다. 물론 이럼에도 thread-safety가 지켜지도록 비동기적 접근을 동기적 접근으로 바꾸도록 해야 하는 작업은 필요할 것이다. 

그래서 지금은 이를 구현할 대략적인 방법으로 다음과 같이 구상하고 있다. 실제 실행되는지는 아직 해보지 않았으므로 사실상 의사 코드(pseudo code)라 봐주면 되겠다.

```java
@Component
public class ResponseMapPool {
	private final Map<Integer, Map<String, Object>> responseMapPool = new HashMap<Integer, Map<String, Object>>();
		
	private Integer poolSize = 10;  // 기본값을 10으로 설정
		
	private List<Boolean> occupiedStatus = new ArrayList<Boolean>(); // 특정 해시맵 객체가 이미 사용되고 있는지 기록 용도. 사용되고 있지 않다면 false로 기록.
		
	// 사용자가 원하는 pool의 사이즈를 결정할 수 있게끔 한다. 
	public ResponseMapPool(Integer poolSize) {
		this.poolSize = poolSize;
				
		// respones Map 객체들을 생성하여 pool에 저장. 
		for (int i = 0; i < this.poolSize; i++) {
			responseMapPool.put(i, new HashMap<String, Object>());
			occupiedStatus.add(false);
		}
	}
		
	public synchronized Map<String, Object> getResponseMap() {
		for (int i = 0; i < poolSize; i++) {
			if (!occupiedStatus.get(Integer.valueOf(i))) {
				occupiedStatus.set(i, true);
				return responseMapPool.get(i);
			}
	    }
			
		// 여기까지 왔다는 것은 모든 HashMap들이 사용되고 있다는 뜻이다. 
		// 따라서 이 경우 어쩔 수 없이 특정 HashMap 객체를 사용 가능 상태로 강제로 바꿔 
		// 리턴하도록 고안하였다.
		occupiedStatus.set(0, true);
		Map<String, Object> target = responseMapPool.get(0);
		target.clear();
		return target;
	}
		
	public synchronized void close(Map<String, Object> willBeReturnedResponseMap) { 
		    
		for (int i = 0; i < poolSize; i++) {
			// 참조가 같으면 두 객체는 같은 객체로 판별
		    if (responseMapPool.get(i) == willBeReturnedResponseMap) {
		      occupiedStatus.set(i, false);
		      break;
		    }
        }
		    
		// 설령 for문을 통해 pool 내에 해당하는 객체를 찾지 못하는 
		// 예외가 발생하더라도 close 메서드에 들어온 이상 
		// 무조건 responseMap 객체 내부의 데이터들을 초기화한다. 
		willBeReturnedResponseMap.clear();
	}
		
}
```

사실 위 코드가 제대로 동작만 한다면 이왕이면 이 pooling 기법을 사용하는 것이 더 좋지 않을까, 란 생각이 든다. 

그런데 설령 위 코드가 정말로 작동은 한다 하더라도 실제로 메모리에 부담을 덜 준다든지 성능 최적화에 도움을 준다는 걸 어떻게 실험해야 증명할 수 있을지는 현재로서는 전혀 감이 잡히지 않는다. 

아무튼 나는 요새 코드를 작성할 때 사소해보이는 것일지라도 이렇게 매번 “더 좋은 코드로 작성할 순 없었을까?”를 고민하며 이런저런 실험을 하려고 노력한다. 이젠 “구현만 되면 끝”이란 생각은 벗어나야할 단계이지 않나, 란 생각도 들어서이다. 

사실 기회가 생긴다면, 내가 예전에 독학으로 공부했던 자료구조나 알고리즘을 팀플이든 내 개인 플젝이든 간에 한 번 적용해보고 싶기는 하다. 그래서 구현의 문제 뿐만 아니라 성능의 문제도 이 두마리 토끼를 잡아보고 싶다. 그런데 아직까지는 이를 적용할 기회가 잘 나오지 않아서 아쉬운 상황이다.