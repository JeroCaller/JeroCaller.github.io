---
title: "[Forklog][Trouble Shooting] 이클립스에서 .properties 한글 깨짐 방지"
category: "Team Project"
tag: ["Team Project", "Forklog", "Trouble Shooting", "propeties", "Eclipse"]
---

# Trouble-Shooting이란?

트러블 슈팅은 어떤 문제 발생 시 각종 수단, 수법을 동원하여 그 원인을 찾아 해결하는 작업을 의미한다. 소프트웨어 제작을 위한 프로젝트에서는 종종 개발 도중 또는 개발 후 소프트웨어 실행 도중에 에러 및 버그를 만나게 될 것인데, 이들의 원인을 규명하고 해결하는 과정을 문서화해두면 나중에 똑같은 문제가 발생했을 때 그 문서의 지시대로만 하면 해결되기에 편리하다. 또한 원인 해결 과정을 문서화하면 몰랐던 부분을 알게 된 것들을 기록하는 행위이기에 실력 향상에도 도움이 될 거라 생각한다. 이러한 이유로, 초라하지만 팀 프로젝트 당시 작성했던 트러블 슈팅 문서를 블로그에 기록해보겠다. 

실제 문서 작성일: 2024-12-19

# Eclipse에서 .properties 파일 한글 깨짐 해결법

이클립스에서 `.properties` 확장자 파일 내에서 한글이 작성되지 않을 때 팁. 이클립스 상단 메뉴의 “Window” ⇒ Preferences 클릭. 그 후 다음의 사진 참고

![캡처.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-%EC%9D%B4%ED%81%B4%EB%A6%BD%EC%8A%A4%EC%97%90%EC%84%9C%20.properties%20%ED%95%9C%EA%B8%80%20%EA%B9%A8%EC%A7%90%20%EB%B0%A9%EC%A7%80/1.png)

바로 적용되지 않으면 현재 켜져 있는 `.properties` 확장자 파일을 껐다 킨다. 그러면 이제 .properties 파일 내부에서 한글을 작성할 수 있게 된다.  

---

References

[1] 트러블 슈팅

[Trouble Shooting 트러블 슈팅이란?](https://velog.io/@lgsgst5613/Trouble-Shooting-%ED%8A%B8%EB%9F%AC%EB%B8%94-%EC%8A%88%ED%8C%85)

[2] 트러블 슈팅

[프론트엔드에서 트러블 슈팅하기](https://solo5star.dev/posts/56/)

[3] 트러블 슈팅

[프론트엔드에서 트러블 슈팅하기](https://solo5star.tistory.com/56)

[4] 트러블 슈팅

[트러블슈팅(troubleshooting)에 대해 알아보겠습니다.](https://feccle.tistory.com/92)

[5] 이클립스 .properties 파일 한글 깨짐 해결법

[Eclipse - .properties 파일 한글 깨짐 해결 방법](https://pabeba.tistory.com/239)