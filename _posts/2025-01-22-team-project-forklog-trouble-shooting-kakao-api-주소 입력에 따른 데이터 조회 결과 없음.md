---
title: "[Forklog][Trouble Shooting][kakao API] 주소 입력에 따른 데이터 조회 결과 없음."
category: "Team Project"
tag: ["Team Project", "Forklog", "Trouble Shooting", "API", "Kakao API"]
---

실제 문서 작성일: 2024년 12월 22일

주소의 도로명 주소까지 입력한 채로 kakao api의 [키워드로 장소 검색하기] 실행 시 검색 결과가 나오지 않음. 

![1.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%A3%BC%EC%86%8C%20%EC%9E%85%EB%A0%A5%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%A1%B0%ED%9A%8C%20%EA%B2%B0%EA%B3%BC%20%EC%97%86%EC%9D%8C/1.png)

![2.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%A3%BC%EC%86%8C%20%EC%9E%85%EB%A0%A5%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%A1%B0%ED%9A%8C%20%EA%B2%B0%EA%B3%BC%20%EC%97%86%EC%9D%8C/2.png)

도로명까지 입력 시 kakao api 자체 검색 결과에 나오지 않기에 발생한 문제였음.

![3.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%A3%BC%EC%86%8C%20%EC%9E%85%EB%A0%A5%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%A1%B0%ED%9A%8C%20%EA%B2%B0%EA%B3%BC%20%EC%97%86%EC%9D%8C/3.png)

![4.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%A3%BC%EC%86%8C%20%EC%9E%85%EB%A0%A5%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%A1%B0%ED%9A%8C%20%EA%B2%B0%EA%B3%BC%20%EC%97%86%EC%9D%8C/4.png)

도로명만 제거하면, 즉 주소의 대분류, 중분류까지 입력하면 조회 결과가 나온다.

![5.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%A3%BC%EC%86%8C%20%EC%9E%85%EB%A0%A5%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%A1%B0%ED%9A%8C%20%EA%B2%B0%EA%B3%BC%20%EC%97%86%EC%9D%8C/5.png)

![6.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%A3%BC%EC%86%8C%20%EC%9E%85%EB%A0%A5%EC%97%90%20%EB%94%B0%EB%A5%B8%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%A1%B0%ED%9A%8C%20%EA%B2%B0%EA%B3%BC%20%EC%97%86%EC%9D%8C/6.png)