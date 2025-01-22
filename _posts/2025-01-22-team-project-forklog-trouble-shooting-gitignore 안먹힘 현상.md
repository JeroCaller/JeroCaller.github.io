---
title: "[Forklog][Trouble Shooting] gitignore 안먹힘 현상"
category: "Team Project"
tag: ["Team Project", "Forklog", "Trouble Shooting", "Gitignore", "gitignore"]
---

실제 문서 작성일: 2024년 12월 26일

특정 파일, 폴더를 gitignore 파일에 포함시켰음에도 반영되지 않을 때가 있다. 이는 git의 캐시가 계속 남아있어 발생하는 것일 수 있다. 

이 경우 git bash창을 열어 다음의 명령어를 입력한다.

```java

git rm -r --cached .  // 모든 캐시 파일 삭제
git add .  // 캐시 삭제 시 언스테이징되는데, 이를 다시 스테이징
git commit -m "fixed untracked files"  // 커밋 메시지와 함께 커밋
```

그러면 gitignore가 바로 적용될 것이다. 

---

References

[1] [[Github] .gitignore 파일이 바로 적용이 안될때 해결방법 : git 캐시 삭제](https://adjh54.tistory.com/376)

[2] [.gitignore가 작동하지 않을때 대처법](https://jojoldu.tistory.com/307)