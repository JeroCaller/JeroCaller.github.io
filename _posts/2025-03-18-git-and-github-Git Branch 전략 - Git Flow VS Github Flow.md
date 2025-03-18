---
title: "Git Branch 전략 - Git Flow VS Github Flow"
category: "Git & Github"
tag: ["Git", "Github", "Git Branch 전략", "Workflow", "Git Flow", "Github Flow", "Forking Flow"]
---

# Git Branch 전략 - Git Flow VS Github Flow

Git Branch 전략은 여러 개발자들이 하나의 저장소에서 협업할 때 저장소를 효과적으로 활용하기 위해 Git branch에 대한 규칙을 세워 workflow를 정의하는 것을 의미한다. 여러 명의 사람들과 협업하는 과정에서는 많은 Git 브랜치들이 생성되어 사용될 것이기에 관리를 위한 규칙이 필요한데 이를 위한 것이 바로 Git Branch 전략인 것이다. 

만약 이러한 Git Branch 전략을 세우지 않은 체 프로젝트를 진행한다면 아마 다음의 문제점들에 봉착하게 될 것이다. 

- 어떤 브랜치가 최신 브랜치인지 분별하기 어려워진다.
- 어떤 브랜치로부터 분기를 형성하여 개발을 해고, 또 어떤 브랜치로 병합해야할지 모호해진다.
- 버그를 발견하여 이를 급히 고치기 위한 hotfix는 어떤 브랜치를 기준으로 수정해야하는지에 대한 모호성.

따라서 Git Branch 전략을 세우면 위 문제점들을 명확히 하여 해결할 수 있음과 동시에 다음의 이점들도 얻을 수 있다. 

- 여러 개발자들이 각자 브랜치를 담당하여 개발을 진행하기에 서로에게 영향을 끼치지 않고 독립적으로 개발을 수행할 수 있다.
- 특정 기능 개발을 위한 브랜치를 따로 생성하여 개발을 진행하면 이 기능에 대한 리뷰를 할 수 있고, 이 기능을 실제로 배포하기 위해 사용될 메인 브랜치에 merge해도 될지 결정할 수 있다. 만약 부적합하다면 해당 기능 브랜치를 merge하지 않고 버리기만 해도 된다. 그런데 만약 이렇게 브랜치를 따로 분리하지 않고 매번 실제 배포를 위한 메인 브랜치에 커밋(commit)해버리면 나중에는 불필요하다고 판별된 기능을 삭제하기가 어려워질 것이다.
- 어떤 Git Branch 전략을 택하느냐에 따라 이번에 배포할 기능에 대한 테스트와 다음 버전에서 새롭게 추가할 기능 개발을 별도로 두어 서로 독립적으로 진행시킬 수 있어 일을 더 효율적으로 진행할 수 있다.
- 전체적인 프로젝트 진행 사항을 브랜치 및 커밋 이력을 통해 쉽게 파악할 수 있을 것이다.

Git Branch 전략에는 다양하게 있다고 하나 대표적으로는 두 가지가 잘 알려져 있다고 한다. Git Flow와 Github Flow이다. 이 글에서는 이 두 전략에 대해 각각 살펴보고 이 둘의 차이점에 대해서도 살펴보고자 한다. 

# Git Flow 전략

Git Flow 전략에 대해선 다음의 그림이 잘 알려져 있다. Git Flow 전략의 핵심이 모두 들어있는 그림이기에 잘 들여다보는 것이 좋겠다. 

![참고 그림 1-1. Git flow를 설명하는 브랜치들의 시간에 따른 모습. 출처: [https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog](https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog)](https://devocean.sk.com/editorImg/2023/12/15/6c564810665399f6549ed2bffc7e763c7e39f5fab128a3442ddeb44ee6593c04)

참고 그림 1-1. Git flow를 설명하는 브랜치들의 시간에 따른 모습. 출처: [https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog](https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog)

Git Flow 전략에서는 main(master)과 develop 브랜치를 메인 브랜치로 하며, 나머지 feature, release, hot fix를 보조 브랜치로 활용하여 총 다섯 종류의 브랜치를 사용한다. 메인 브랜치의 경우 개발의 처음부터 끝까지 유지되는 브랜치이고, 그 외 보조 브랜치들은 대부분 1회성으로, 그 목적을 다하면 더 이상 사용하지 않는다는 특성을 지닌다. 

Git Flow에서 사용하는 다섯 브랜치들에 대해서는 어떤 브랜치로부터 끌어와서 사용할 지, 그리고 어떤 브랜치로 merge할지에 대한 구분이 있다. 예를 들어 master 브랜치에 feature 브랜치를 바로 merge 할 수 없다. release 브랜치의 경우, 배포 가능한 수준의 기능 개발이 이뤄지면 develop 브랜치에서 가져와 QA, 테스트를 진행하는 용도의 브랜치인데, 테스트가 완료되어 배포해도 될 수준에 이르면 main 브랜치로 merge하며, 테스트 진행 후 버그를 발견하여 고친 이력이 있을 경우 최신화를 위해 develop에도 merge하는 과정을 거친다. 즉 각각의 브랜치들에는 접근할 수 있는 브랜치와 그럴 수 없는 브랜치가 마치 “계층”처럼 존재하여 무분별한 merge 및 브랜치 분기를 막아준다. 

각각의 브랜치들의 역할 및 그 외 정보들에 대해선 다음과 같다. 

- main(master) : 라이브 서버에 실제로 배포되는 브랜치이며, 출시된 제품들의 버전을 관리한다.
- develop : 다음 출시 버전을 위해 개발하기 위한 용도의 브랜치. main보다는 이 브랜치가 개발 시 더 많이 사용된다. 맨 처음 프로젝트 초기에는 main 브랜치로부터 분기하여 생성한다.
- hotfix : 출시된 제품에서 버그를 발견한 경우 이를 고치기 위한 브랜치. main 브랜치에서 발견한 버그를 고치기 위해 main 브랜치로부터 끌어와 hotfix 브랜치를 생성한 후 해당 브랜치에서 버그 수정 작업을 진행한다. 버그 수정이 완료된 뒤에는 master와 develop 이 둘 모두에 merge하여 업데이트한다. main 브랜치에서 버그를 발견했다는 것은 develop 브랜치에서도 해당 버그를 발견하지 못했다는 뜻이기 때문에 develop 브랜치에도 merge하여 업데이트 하는 것이다. 해당 브랜치 특성 상 hotfix 브랜치는 개발보다는 유지보수의 성격에 더 가깝다고 말할 수 있겠다.
- feature : 새로운 기능을 개발하는 브랜치로, develop 브랜치에서 끌어와 새로운 기능을 독립적인 브랜치에서 진행한다. 새로운 기능 개발이 완료되면 다시 develop 브랜치에 merge 되는데, 일반적으로는 PR(Pull Request)을 하여 해당 기능에 대한 리뷰를 다른 협업자로부터 받은 후에 이를 승인받으면 그 때서야 merge하는 과정을 거친다. 여러 개발자들이 각자의 새로운 기능 개발을 담당하게 되면 각자의 feature 브랜치를 develop으로부터 분기, 생성시켜 각자의 브랜치에서 개발하면 된다. 각 브랜치의 이름은 기존에 존재하는 main(master), develop, hotfix, release를 제외한 모든 이름으로 자유롭게 설정 가능하다. 보통 “feature/login” 또는 “feature-login”과 같은 형태로 지을 수 있다고 한다.
- release : develop 브랜치에서 이제 다음 버전 출시를 위한 기능 개발들이 모두 이루어져 해당 버전 출시를 해도 되겠다는 판단 또는 협의가 나오면 이를 배포하기 전에 먼저 QA 및 테스트를 거치기 위한 브랜치이다. 이를 위해 develop 브랜치로부터 분기하여 생성하며, 이 release 브랜치에서 버그를 발견하여 수정하고자 하는 경우 그대로 release 브랜치 내에서 커밋하면 된다. 이 때, 새로운 커밋 이력이 생긴 것이므로 이 경우에는 최신화를 위해 develop 브랜치에 merge 시킨다. 그래야 버그가 수정된 상태의 작업물을 develop 브랜치에서도 받아 후속 작업을 이어갈 수 있기 때문이다. 한 편 이 브랜치에서 모든 테스트가 끝나면 이제 실제 배포가 가능한 상태이므로 이를 main 브랜치로 merge한다.

# Github Flow 전략

Github Flow 전략은 main(master) 브랜치와 feature라는 보조 브랜치만을 사용하는 git 브랜치 전략이다. 다음은 이 전략을 잘 묘사한 그림이다. 

![참고 그림 1-2. Github Flow 전략을 묘사한 그림. 출처: [https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog](https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog)](https://devocean.sk.com/editorImg/2023/12/15/0d231fe21e58bd49c9367c990287f6f7d68d0fb8a37afd650992bfc91523cb8a)

참고 그림 1-2. Github Flow 전략을 묘사한 그림. 출처: [https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog](https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog)

이 경우 main 브랜치는 기존 Git Flow와 마찬가지로 실제 배포 용도로 사용되는 브랜치가 되지만, Git Flow에서의 release, hotfix 등의 별도의 브랜치 없이 모두 feature 브랜치로 운영된다는 차이점이 있다. Github Flow에서도 마찬가지로 새로운 기능 개발이나 테스트, 버그 수정 등의 새로운 개발 사항들이 생긴다면 main 브랜치로부터 분기하여 새로운 feature 브랜치를 생성하여 거기서 개발하면 되겠다. 

feature 브랜치에서 개발이 완료된 경우, main 브랜치로 바로 merge하기 전에 먼저 PR을 열어 자신이 개발한 코드에 대해 다른 이들로부터 리뷰를 받는다. 이 전략에서는 실제 서버에 배포되는 main 브랜치로 바로 merge하는 방식이기에 Git Flow에 비해 더 상세하고 신중한 리뷰가 이뤄질 것이다. 여기서 리뷰를 끝마치고 merge해도 된다는 판단이 나오면 그때서야 main 브랜치에 merge를 진행하게 된다. 

이 전략에서는 Git Flow와는 달리 더 적은 수의 브랜치를 가지고 운영하므로, 기능 개발, 테스트 등의 작업에 대한 브랜치 이름 및 커밋 메시지를 더 명확하고, 구체적으로, 또 체계적으로 작성하는 것이 중요하다. 그래야 나중에 문제가 생겼을 때 커밋 이력을 추적하기에 용이하기 때문이다. (그렇다고 해서 Git Flow에서는 브랜치 이름 및 커밋 메시지를 대충 작성해도 된다는 것은 아니다. 다만 상대적으로 Github Flow에서는 그 특성상 더 신경써야 한다는 것이다) 

이처럼 Github Flow는 그 특성 상 실제 배포를 위한 main 브랜치로의 merge가 자주 일어나기에 CI, CD 등의 배포 자동화 도구를 이용하여 merge되는 즉시 배포가 자동으로 실행도록 설정한다고 한다. 

이 Github Flow에서는 hotfix를 비롯한 버그나 가장 작은 기능에 대해서는 별도로 구분하지 않는다. 이러한것들도 결국 개발자가 해야하는 일이라는 관점으로 바라보기 때문이다. 그래서 별도의 브랜치를 두지 않고 feature라는 보조 브랜치에서 모두 해결하는 것이다. 

# Git Flow VS Github Flow

위에서 살펴본 두 전략의 각각의 특징들 및 차이점을 정리하면 다음과 같다. 

- Git Flow는 상대적으로 많은 브랜치를 사용하고 그 과정도 복잡하다는 특징이 있다. 이는 그만큼 엄격한 브랜치 관리를 위해서이다. 따라서 Git Flow는 주로 대규모 프로젝트에서 사용하거나 명확한 배포 절차가 필요한 경우, 각각의 역할을 분리하는 것이 중요한 경우, 또는 1개월 이상의 긴 호흡으로 개발하며 테스트 등의 작업들도 할 수 있는 경우에 적합하다고 한다.
- Github Flow는 비교적 단순한 브랜치 전략을 사용하며, 수시로 main 브랜치에 merge되어 빈번한 배포가 이뤄지기에 자주, 또는 빠르게 배포해야할 필요가 있는 프로젝트에 적합하며, 사용하는 브랜치 수가 적고 간단하기에 소규모 프로젝트에 적합하다고 한다.
- Git Flow는 더 체계적으로 Git 브랜치들을 관리할 수 있다는 장점이 있지만, 그만큼 복잡한 시스템을 가지고 있고, 배포 주기도 상대적으로 느리기 때문에, 소규모 프로젝트나 배포 주기가 빠르게 이뤄져야 하는 경우에는 그에 대한 유연성이 떨어져 부적합할 수 있다.
- GitHub Flow는 그 특성 상 기능을 개발하고 빠르게 배포할 수 있다는 장점이 있지만, 별도의 검증, 테스트를 위한 브랜치를 따로 두어 운영하지는 않기에, 빠른 배포보다는 안정성 등이 중시될 때에는 부적합할 수도 있다. (그런데 그렇다고 해서 테스트 자체를 아예 하지 않는다는 건 아니다. 그저 main 브랜치로부터 따로 분기시켜 테스트를 위한 브랜치를 만들어 테스트 작업을 수행하면 그만이다) 팀의 규모가 너무 크거나 프로젝트가 복잡할 때, 그래서 좀 더 엄격하고 체계적인 관리가 필요할 때에는 Git Flow를 사용하는 것이 더 좋을 수도 있다.

물론 “이러이러한 상황에서는 무조건 이 전략을 사용하세요!”와 같이 엄격히 어떠한 규칙이 정해져 있는 건 아니기에 상황에 따라 적합하다고 여기는 전략을 선택하여 사용하면 되겠다. 또는 각 전략들의 장점만 모아 사용하거나 또는 일부 절차를 생략하는 전략을 사용할 수도 있겠다. 예를 들어 개인 프로젝트를 하는데 소규모 프로젝트인 경우, Github Flow를 채택했을 때 PR을 하는 것은 사실상 의미가 없을 것이다. 코드를 개발하는 것도, 그 코드를 리뷰, 검증하는 것도 모두 개인 개발자 혼자서 다하는 구조이기 때문이다. 따라서 이와 같이 상황에 따라 유연하게 전략을 선택하여 적용하는 것이 중요하다고 볼 수 있겠다. 

# 번외 - Forking Flow

이 외에도 여러 Git 브랜치 전략들 중 하나로 Forking Flow라는 것이 있다. 앞선 두 전략에서는 하나의 중앙 원격 저장소에서 작업한다는 전제가 깔려있지만, Forking Flow 전략은 각각의 개발자들이 중앙 원격 저장소를 fork(복제)하여 자신의 개인 원격 저장소로 fork하여 작업한다는 차이를 가지고 있다. 

보통 중앙 원격 저장소와 개인 원격 저장소를 구분하기 위해, 중앙 원격 저장소는 upstream, 개인 원격 저장소는 origin이라고 관례적으로 부른다(이건 관례이지 강제 사항은 아니기에 필요하면 다른 이름을 부여해도 된다).

이 전략에서는 각각의 개발자들은 각자의 작업물을 바로 upstream에 push하는 것이 아니라, origin, 즉 자신의 원격 저장소에 먼저 push한다. 그 후 upstream 보유자에게 PR을 요청하여 병합(merge)을 요청하고, upstream 보유자 또는 프로젝트 관리자들이 이를 리뷰하여 upstream에 병합하는 과정을 거친다. 

주의할 점은, 개발자가 로컬 저장소에서 main 브랜치로부터 새로운 feature 브랜치를 따서 새로운 기능을 개발한 경우, 로컬 저장소의 main 브랜치에 바로 병합하는 것이 아니라, feature 브랜치 그대로 자신의 원격 저장소(origin)에 push 한 후에 중앙 원격 저장소에 병합 요청을 PR로 보낸다는 것이다. 앞서 살펴본 전략들을 포함하여 이 세 전략 모두 공통점은 개별의 개발자가 자신의 작업물을 바로 main 브랜치에 병합해선 안된다는 것이다. 그 전에 반드시 다른 협업자들로부터 리뷰, 피드백을 받고 난 뒤 merge해도 괜찮다는 승인을 받고 나서야 main 브랜치에 병합하는 것이다. 

이러한 Forking Flow는 중앙 원격 저장소와 개인 원격 저장소를 분리하여 협업한다는 특성 때문에 보통 오픈 소스 프로젝트에서 많이 사용한다고 한다. 또한 브랜치 중심으로 작업하는 것에 비해서는 조금 더 안전한 방식이기에 대규모 팀에서도 사용하기 적합하다고 한다. 

---

References

[1] [[GIT] 📈 깃 브랜치 전략 정리 - Github Flow / Git Flow](https://inpa.tistory.com/entry/GIT-%E2%9A%A1%EF%B8%8F-github-flow-git-flow-%F0%9F%93%88-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EC%A0%84%EB%9E%B5)

[2] [Git Branch 전략 비교 - Git Flow vs GitHub Flow](https://devocean.sk.com/blog/techBoardDetail.do?ID=165571&boardType=techBlog)

[3] git flow 전략에서 사용되는 5가지 브랜치들에 대한 설명

[[GitHub] Git 브랜치의 종류 및 사용법 (5가지) - Heee's Development Blog](https://gmlwjd9405.github.io/2018/05/11/types-of-git-branch.html)

[4] git flow 전략

[[GitHub] GitHub로 협업하는 방법[3] - Gitflow Workflow - Heee's Development Blog](https://gmlwjd9405.github.io/2018/05/12/how-to-collaborate-on-GitHub-3.html)

[5] forking workflow

[[GitHub] GitHub로 협업하는 방법[2] - Forking Workflow - Heee's Development Blog](https://gmlwjd9405.github.io/2017/10/28/how-to-collaborate-on-GitHub-2.html)

[6] feature branch workflow - github flow인 것 같음.

[[GitHub] GitHub로 협업하는 방법[1] - Feature Branch Workflow - Heee's Development Blog](https://gmlwjd9405.github.io/2017/10/27/how-to-collaborate-on-GitHub-1.html)

[7] git flow, github flow 및 구체적인 사용법 등

[사례로 이해하는 GitHub Flow](https://www.heropy.dev/p/6hdJi6)

[8] 참고) 깃허브 기반 1인 워크플로우 사례

[[GitHub]  깃허브로 1인 워크플로우 만들기](https://dev-dmsgk.tistory.com/16)

[9] [[적용기] Branch 전략 적용 후기(Git Flow, Github Flow)](https://sjevie.tistory.com/entry/%EC%A0%81%EC%9A%A9%EA%B8%B0-%EC%84%9C%EB%A1%9C-%EB%8B%A4%EB%A5%B8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EC%97%90-%EB%8B%A4%EB%A5%B8-Branch-%EC%A0%84%EB%9E%B5-%EC%A0%81%EC%9A%A9-%ED%9B%84%EA%B8%B0Git-Flow-Github-Flow)