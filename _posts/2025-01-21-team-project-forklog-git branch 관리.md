---
title: "[Forklog] Git Branch 관리"
category: "Team Project"
tag: ["Team Project", "Forklog", "Git", "Github", "Fork", "Branch", "Merge"]
---


# 브랜치 생성 및 merge시 주의사항

프로젝트의 최종 결과물이 담길 main 브랜치로부터 각자 새로운 브랜치를 생성하여 새로운 기능이나 버그, 수정 등의 작업을 하고 이를 main 브랜치에 합칠 것이다. 여기서 내가 멘토링을 듣기 전까지는 몰랐던 사실 하나를 정리하고자 한다. 

아마도 내 기억이 맞다면, main 브랜치로부터 새로운 브랜치를 생성한 후, 그 곳에서 새로운 기능 개발한 것을 main 브랜치에 합친 후에, 그 브랜치를 그대로 계속 사용했던 걸로 기억한다. 그런데 이렇게 하면 merge 충돌이 날 수 있고, 자칫 잘못하면 브랜치들이 꼬일 수 있다고 멘토님께서 말씀하셨다. 

![그림 1-1. 브랜치 분할 및 병합 방법의 예시. ](/images/2025-01-21/team-project-forklog-git%20branch%20%EA%B4%80%EB%A6%AC/1.png)

그림 1-1. 브랜치 분할 및 병합 방법의 예시. 

그래서 새로 생성한 브랜치는 한 번 main 브랜치에 merge하면 그 브랜치는 더 이상 사용하면 안된다. 또 다른 기능을 추가하고자 하거나 버그 수정 등의 작업을 하고자 하면 위 그림 1-1의 오른쪽 그림처럼 아예 새로운 브랜치를 또 따로 생성하여 거기서 작업해야 한다고 한다. A라는 기능을 “Branch-1”이란 브랜치 위에서 작업하고 이를 main 브랜치에 merge한 후, 이 후에 해당 기능에 수정 사항이 생겼다면 “Branch-2”와 같은 아예 새로운 브랜치를 새로 생성하여 거기서 작업해야 한다는 것이다. 즉, main 브랜치를 제외한 나머지 브랜치들은 일회용인 셈이다. 위 그림 1-1의 왼쪽과 같이 이미 main 브랜치에 merge한 브랜치를 계속 끌고 사용할 때 여기서 merge 충돌이나 의도치 않은 문제를 겪을 수 있다고 한다. 

이 방법으로 바꾸고 나니 확실히 이전과는 다르게 merge 충돌이 확연히 줄어들었다는 느낌을 받았다. 그리고 “브랜치 일회용” 방식을 사용하니 각 브랜치들은 하나의 작업 요소만을 갖기에 해당 브랜치에서 어떤 작업이 일어났는지 추적하기 쉬워질거라 생각된다. 이것도 어떻게 보면 객체 지향 프로그래밍에서의 SRP(Single Responsibility Principle, 단일 책임 원칙)과도 흡사하기 때문이다. 

# Fork를 이용하여 협업하기

예전에는 하나의 repository 내에서 각자 브랜치를 생성하여 작업한 후 이를 main 브랜치에 merge했다면, 이후부터는 깃허브 repository를 각자의 계정으로 fork한 후 작업해보는 방식으로 바꿔보았다. 그렇게 했더니 확실히 브랜치 기반 협업에서는 merge 충돌이나 실수들이 많아서 협업이 어려웠는데, fork 방식으로 하니 훨씬 더 수월해지고 심적으로도 실수에 대한 부담감이 줄어들었다. 

fork를 이용한 협업 방식은 다음과 같은 과정으로 흘러갔다. 

1. 팀장의 github 원격 repository를 기점으로 한다. 이 곳에서 각자 팀원들이 작업한 모든 결과물들이 하나로 합쳐져 하나의 프로젝트를 형성할 것이다. 
2. fork해 온 내 repository에 대해, 이를 pull하여 최신화한 로컬 repository 내에서, main 브랜치로부터 새로운 브랜치를 생성하여 거기서 새로운 작업을 한다. 
3. 새로운 작업을 모두 마쳤다면 다시 main 브랜치로 checkout한 상태에서, github의 내 repository에서 “Contribute”, “Sync fork” 버튼 왼쪽의 상태 메시지를 본다. “This branch is 6 commits behind …”와 같이 “behind”란 말이 있다면 원본 repository에 새로운 커밋 사항들이 업데이트되었다는 뜻이므로 “Sync fork” 버튼 클릭 후 “Update branch” 버튼을 클릭한다. 그러면 원본 repository로부터 새로운 사항들을 업데이트 받을 수 있다. 
    
    ![그림 1-2.](/images/2025-01-21/team-project-forklog-git%20branch%20%EA%B4%80%EB%A6%AC/2.png)
    
    그림 1-2.
    
4. 이를 내 로컬 repository의 main 브랜치에 있는 상태에서 pull받고 merge한다. 로컬 repository의 내역도 최신화하는 것이다. 
5. main 브랜치에서 앞서 만든 브랜치를 merge한다. 
6. 로컬 repository에 있던 main 브랜치를 내 github repository에 push한다. 
7. github의 내 repository에서 “Contribute” 버튼 (위 그림 1-2 참고)을 눌러 원본 repository에 Pull Request (PR)를 한다. (참고로 이 상태가 되면 아까와 달리 “This branch is n commits ahead” 식의 메시지가 뜬다)
8. 이전에 팀장의 초대로 collaborator로 선정되었다면 merge도 스스로 할 수 있다. 따라서 PR 후 바로 merge하면 내 작업물이 원본 repository에 반영되는 것이다. 

![그림 1-3. Fork를 이용한 협업방식 흐름도.](/images/2025-01-21/team-project-forklog-git%20branch%20%EA%B4%80%EB%A6%AC/3.png)

그림 1-3. Fork를 이용한 협업방식 흐름도.

![그림 1-4. Fork를 이용하여 협업 시 생성된 git branch 그래프.](/images/2025-01-21/team-project-forklog-git%20branch%20%EA%B4%80%EB%A6%AC/4.png)

그림 1-4. Fork를 이용하여 협업 시 생성된 git branch 그래프.

이렇게 fork 방식을 이용하니 브랜치 방식보다 훨씬 더 편한하기도 했고, 똑같은 프로젝트가 내 github 계정에도 있으니 완성된 프로젝트를 팀원들끼리 나눠 갖기에도 매우 쉬웠다. 

---

References

[1] 에이콘아카데미(강남) 멘토링 강의