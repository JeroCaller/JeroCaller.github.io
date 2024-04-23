---
title: "[Java] vscode에서 자바 실행하는 법"
category: "Java"
tag: ["java", "자바", "vscode"]
---

이 문서에서는 JDK와 vscode가 깔려있다는 전제 하에 설명이 진행될 것이다. 

1. vscode를 키고, 확장탭에서 “Extension Pack for Java”를 설치한다. 
    
    ![20240304_051408_screenshot.png](/images/2024-03-04/2024-03-04-java-vscode%EC%97%90%EC%84%9C%20%EC%9E%90%EB%B0%94%20%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94%20%EB%B2%95-1.png)
    

1. vscode 메뉴의 [보기] → [명령 팔레트]를 클릭한다. 
    
    ![20240304_051408_screenshot (2).png](/images/2024-03-04/2024-03-04-java-vscode%EC%97%90%EC%84%9C%20%EC%9E%90%EB%B0%94%20%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94%20%EB%B2%95-2.png)
    

1. 그러면 아래 사진과 같이 vscode 창의 윗 부분에 무언가를 입력하는 창이 뜨는데, 거기에 Java: Create Java Project 를 입력하고 엔터키를 누른다. 
    
    ![20240304_051408_screenshot (3).png](/images/2024-03-04/2024-03-04-java-vscode%EC%97%90%EC%84%9C%20%EC%9E%90%EB%B0%94%20%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94%20%EB%B2%95-3.png)
    

1. 화면이 아래 사진과 같이 바뀌는데, 목록에서 “No build tools”를 클릭한다. 
    
    ![20240304_051408_screenshot (4).png](/images/2024-03-04/2024-03-04-java-vscode%EC%97%90%EC%84%9C%20%EC%9E%90%EB%B0%94%20%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94%20%EB%B2%95-4.png)
    

1. 이후, 프로젝트가 저장될 폴더를 지정한다. 아래 사진에서는 “java-study”라는 폴더 내에 프로젝트 폴더를 생성할 것이기에 해당 폴더를 택하였다. 
    
    ![20240304_051408_screenshot (5).png](/images/2024-03-04/2024-03-04-java-vscode%EC%97%90%EC%84%9C%20%EC%9E%90%EB%B0%94%20%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94%20%EB%B2%95-5.png)
    

1. 다시 vscode 창으로 돌아와서, 프로젝트 폴더명을 입력한다. 여기서는 JavaStudy2로 해보았다. 
    
    ![20240304_051408_screenshot (6).png](/images/2024-03-04/2024-03-04-java-vscode%EC%97%90%EC%84%9C%20%EC%9E%90%EB%B0%94%20%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94%20%EB%B2%95-6.png)
    

1. 아래 사진과 같이 방금 입력한 프로젝트명과 동일한 폴더와 함께, 그 안에 lib, src 폴더가 생성되고, src폴더 안에 App.java 파일이 생성되었다면 준비가 완료된 것이다. 기본으로 생성되는 App.java 파일에서 코드를 입력하고 F5키를 눌러 실행시켜보면 바로 실행된다. 다른 자바 파일을 생성하고자 한다면 src 폴더 안에 생성하면 된다. 
    
    ![20240304_051408_screenshot (7).png](/images/2024-03-04/2024-03-04-java-vscode%EC%97%90%EC%84%9C%20%EC%9E%90%EB%B0%94%20%EC%8B%A4%ED%96%89%ED%95%98%EB%8A%94%20%EB%B2%95-7.png)
    

---

References

[1] [Visual Studio Code / Java 개발 환경 설정하기](https://eating-coding.tistory.com/73)