---
title: "[CSS] 스타일 상속"
category: "CSS"
tag: ["web", "js", "javascript"]
---

부모 요소와 그 자식 요소가 있을 때, 부모 요소에 스타일이 지정되어있으면, 그 스타일은 자식 요소에게 상속한다. 이 말은, 부모 요소에 설정된 스타일이 그대로 자식 요소에게도 설정된다는 것이다. 단, 별도의 스타일이 따로 지정되어 있는 자식 요소에 대해서는 부모 요소의 스타일을 상속받지 않고 따로 지정된 그 스타일을 따른다. 

하지만 모든 스타일 속성들이 상속되는 것은 아니다. 상속이 되는 것은 주로 글자와 관련된 것들이며, 이마저도 상속되지 않는 것들이 있다고 한다. 

특정 스타일 속성의 상속 여부를 확인하려면 크롬 기준 개발자 도구에서 확인할 수 있다. 개발자 도구의 좌상단에 있는 커서 모양 아이콘을 클릭하면 웹 화면의 특정 요소나 HTML 코드의 특정 요소를 클릭하여 해당 요소에 대해서만 자세히 살펴볼 수 있다. 그 후, HTML 코드 밑 “Styles” 태그를 클릭하면 해당 요소에 설정된 스타일 규칙들을 살펴볼 수 있다. 여기서 만약 부모 요소에 설정된 스타일 속성이 자식 요소에는 안 보인다면 그건 상속되지 않는 속성이라는 것이다. 

한 편, block요소에는 스타일 상속이 되지 않고, inline 속성에만 상속이 적용된다. 

스타일 상속이 되지 않는 스타일 속성들에 대해 강제적으로 상속되도록 만들 수 있다. 이는 스타일 속성값으로 inherit을 넣으면 된다. 예) `span {height: inherit}` 

# 느낀 점

최근 느끼는 거지만, 내가 독학할 때는 몰랐던 세부 지식들을 은근 많이 알게 되는 것 같다. 

---

References 

[1] 에이콘아카데미 강남 강의