---
title: "[Forklog][Trouble Shooting][Kakao API] 음식점 DB 저장 시 똑같은 데이터 반복 문제"
category: "Team Project"
tag: ["Team Project", "Forklog", "Trouble Shooting", "Kakao API", "API"]
---

실제 문서 작성일: 2024년 12월 29일

![1.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%9D%8C%EC%8B%9D%EC%A0%90%20DB%20%EC%A0%80%EC%9E%A5%20%EC%8B%9C%20%EB%98%91%EA%B0%99%EC%9D%80%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EB%B0%98%EB%B3%B5%20%EB%AC%B8%EC%A0%9C/1.png)

API 호출 후 DB에 음식점 정보 저장 시 같은 음식점 정보들이 반복하여 저장되는 문제 발생.

kakao API로 테스트한 결과, 애초에 kakao API에서 특정 페이지 번호 이상 넘어가면 아무리 페이지 번호를 증가시켜도 똑같은 음식점 결과가 나온 것을 확인. 즉, 코드 상의 문제가 아니라 kakao API 자체 문제로 판명됨.

따라서 300개와 같이 처음부터 대용량으로 가져오지 말고 조금씩 가져와서 중복 데이터를 최대한 방지하는 것이 좋을 것으로 보임. 

파악 결과, 애초에 kakao api에서 전체 문서들 중 총 45개의 데이터만 제공하는 것으로 파악됨. 한 페이지에 15개의 데이터를 보여주니 사실상 3페이지까지만 제대로 된 데이터를 제공하는 것이고, 4페이지부터는 1~3페이지 데이터를 반복적으로 보여주기만 하는 것으로 보임. 

![2.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%9D%8C%EC%8B%9D%EC%A0%90%20DB%20%EC%A0%80%EC%9E%A5%20%EC%8B%9C%20%EB%98%91%EA%B0%99%EC%9D%80%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EB%B0%98%EB%B3%B5%20%EB%AC%B8%EC%A0%9C/2.png)

![3.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-kakao-api-%EC%9D%8C%EC%8B%9D%EC%A0%90%20DB%20%EC%A0%80%EC%9E%A5%20%EC%8B%9C%20%EB%98%91%EA%B0%99%EC%9D%80%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EB%B0%98%EB%B3%B5%20%EB%AC%B8%EC%A0%9C/3.png)

이는 특정 지역 당 음식점을 최대 45개밖에 가져올 수 없다는 뜻이다. 이로 인해, 지역 이름을 바꿔가면서 지역 당 45개씩 DB에 저장하는 수밖에 없을 것으로 보임.