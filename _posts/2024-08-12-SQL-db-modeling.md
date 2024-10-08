---
title: "[SQL][DB] DB Modeling"
category: "SQL"
tag: ["sql", "dbms", "db", "database", "데이터베이스", "정규화", "폭포수"]
---

# 폭포수 모델 (Waterfall model)

DB를 비롯하여 소프트웨어를 제작할 때에는 그 규모가 크고 복잡할수록 설계가 먼저 진행되어야 한다. 이러한 소프트웨어 설계 방법 중 하나로 폭포수 모델이란 것이 존재한다. 각각의 단계가 마치 폭포수가 떨어지듯 순차적으로 진행되기에 붙여진 이름이다. 

해당 모델은 다음의 과정을 거친다.

- 요구사항 수집: 클라이언트로부터 요구사항을 수집한다.
- 요구사항 분석 : 수집한 요구사항들을 분석, 정리하는 과정. DB 설계의 경우 이 과정에서 어떤 것을 테이블의 필드로 정할지, 어떻게 테이블들을 구성할지 등을 구상한다. 즉, 이 과정에서 DB 설계가 이뤄진다. 이 과정에서 테이블 간 관계를 명확히 하기 위해 이를 그래프로 그리는 방법인 ERD(Entity Relation Diagram)를 사용하기도 한다. 
DB 설계의 경우 다음의 3단계 설계가 진행된다.
    - 개념적 설계 : 요구사항들을 토대로 DB 구조를 단순하고 대략적으로, 추상적으로 나타내는 것. 이 과정에서 DBMS에서 사용하는 개념들과 비슷하지만 사실 다른 개념을 사용하는 ERD를 이용하여 데이터 구조를 관계적으로 표현한다.
    - 논리적 설계 : 개념적 설계에서 표현한 데이터들을 컴퓨터가 이해, 처리할 수 있도록 변환하기 위해 DBMS에서 지원하는 논리적 구조로 변환하는 과정. 필드의 데이터 타입과 이 데이터 타입들 간의 관계로 표현되는 논리적 구조로 변환한다. RDBMS에서는 여기서 테이블을 설계한다.
    - 물리적 설계 : 논리적 구조로 표현된 데이터들을 물리적 구조의 데이터로 변환하는 과정. RDBMS의 경우, RDBMS에서 제공하는 여러 DB 개체들, 즉 테이블, 데이터 타입, 테이블들 간의 관계, 인덱스, 뷰 등을 이용하여 실제로 설계하는 과정이다.
- 소프트웨어 구현 (코딩) : 앞선 설계를 토대로 프로그램을 제작한다.
- 테스트(QA) : 코딩을 통해 제작된 프로그램이 의도대로 잘 동작하는지 테스트하는 과정. 보통 프로그램 제작자가 아닌 제 3자가 진행함. 회사에 따라 QA 부서가 따로 존재하기도 함.
- 납품(배포) : 테스트 성공 후 프로그램을 실제로 배포한다.
- 유지보수 : 배포한 프로그램을 유지 보수 한다.

이러한 폭포수 모델은 각 단계가 명확히 구분되어 있다는 특징을 가진다. 그런데 폭포에서 내려가기는 쉬워도 올라가는 건 어렵듯, 진행 과정에서 문제가 발생할 경우 그 이전 단계로 되돌아가기 어렵다는 단점이 있다.

프로젝트 규모가 크지 않은 경우에는 폭포수 모델보다는 애자일 방식이 주로 사용된다고 한다. 설계를 거치지 않고 바로 코딩으로 돌입하는 방식이다. 애자일에 대한 자세한 사항은 다음 글들을 참고.

[애자일 개발 방법론과 스크럼 프레임워크](https://f-lab.kr/insight/agile-development-and-scrum-framework)

[애자일 개발이란 무엇이며 왜 중요한가요? \| OpenText](https://www.opentext.com/ko-kr/what-is/agile-development)

# DB Modeling 관련 개념

DB 구현에 앞서 모델링을 하는 가장 큰 이유는 데이터의 중복성을 제거하기 위함이다. 

데이터 중복성 제거의 일환으로, 모든 데이터들을 보통 하나의 테이블에 한꺼번에 넣지 않고 여러 테이블로 나눠 저장한 후, 각 테이블들이 관계를 맺는 방식으로 데이터를 저장한다. 테이블들이 서로 관계를 맺기 위해서 두 테이블이 적어도 하나의 같은 필드를 가지며, 한 테이블에는 해당 필드에 기본 키를, 다른 테이블에는 해당 필드에 외래 키를 설정한다. 이전에 [데이터의 무결성과 제약조건](/sql/SQL-data-integrity-and-constraint/) 페이지에서 참조 무결성을 통해 두 테이블을 하나로 연결한 사례를 떠올리면 된다. 

이 때, 테이블들간의 관계를 다음과 같이 분류할 수 있다. 

- 일대일 관계 (1:1) : 두 테이블에서 공통된 필드에 공통된 데이터를 가지는 레코드가 하나일 경우이다. 이 관계에 있는 테이블들은 사실 하나로 합치는 게 더 나을 수도 있다.
- 일대다 관계(1:N) : 두 테이블에 공통의 필드가 존재하고, 하나의 테이블의 해당 필드의 데이터가 다른 테이블의 여러 데이터들로부터 참조되는 상황. 예를 들어 부모 테이블에는 “부서코드”란 필드가 있고 거기에 “10”이란 데이터가 있는 경우, 자식 테이블의 같은 필드에서 “10”이란 데이터가 둘 이상 나타나는 경우이다. 일대다 관계에서는 이처럼 한 쪽 테이블에 중복되는 데이터들이 존재하기에 이 두 테이블을 하나로 합치면 안된다. DB에서 가장 흔하게 보이는 관계이며, 가장 바람직한 관계이다.
- 다대다 관계(N:M) 두 테이블의 공통된 필드에서 두 테이블 모두 데이터가 중복되어 나타나는 경우, 앞서 들은 예시를 다시 들면, 한 쪽 테이블의 “부서코드” 필드에 “10”이란 데이터가 중복되어 나타나는데, 다른 테이블에서도 똑같이 중복되어 나타나는 구조이다. DB 설계에서 가장 안좋은 형태로, 다대다 관계는 일대다 관계로 변환시킬 수 있다.

## 정규화 (Normalization)

데이터 중복을 방지하기 위한 방법론으로, 이를 통해 무결성을 지키고 테이블 구성을 명확하게 할 수 있는 장점들이 있다. 정규화에는 학술적으로는 5단계까지 나뉘나 실무에서는 보통 3단계까지만 거친다고 한다. 

다음에 소개할 정규화 과정에는 공통적으로 테이블을 여러 개로 나누는 작업이 수반되는데, 그렇다고 해서 정규화가 무조건 테이블 분할을 의미하지는 않는다. 중복된 데이터 제거를 위해 어떤 경우에는 반대로 두 테이블을 하나로 합칠 수도 있다. 따라서 정규화를 무조건 “테이블 분할”이라 이해하지 말고 “데이터 중복을 방지하기 위한 방법”이라고 이해하는 것이 좋다. 

### 제 1정규화 (1NF)

하나의 필드에는 원자값, 즉 하나의 값만 가져야 한다. 

예를 들어 다음의 테이블이 존재할 때, 

| 이름 | 취미 | 고유번호 |
| --- | --- | --- |
| 김큐엘 | 여행, 독서 | 1 |
| 정디비 | 영화 | 2 |

위 테이블의 “취미” 필드에 “여행”, “독서”란 여러 값이 하나의 칸에 들어있는 것을 볼 수 있다. 이는 제 1정규화를 위배하므로 반드시 여러 칸으로 나누는 것이 좋다. 다음과 같이 말이다. 

| 이름 | 취미 | 고유번호 |
| --- | --- | --- |
| 김큐엘 | 여행 | 1 |
| 정디비 | 영화 | 2 |
| 김큐엘 | 독서 | 1 |

이렇게 여러 레코드로 나누는 것이 좋다. 참고로 위 테이블은 다음과 같이 일대다 관계 테이블로 나눠 더 보기 좋게 할 수 있다.

| 고유번호 | 이름 |
| --- | --- |
| 1 | 김큐엘 |
| 2 | 정디비 |

| 고유번호 | 취미 |
| --- | --- |
| 1 | 여행 |
| 2 | 영화 |
| 1 | 독서 |

### 제 2정규화 (2NF)

한 테이블에 기본 키가 여러 필드들을 하나로 묶어 적용된 복합 키 형태일 경우, 기본 키가 아닌 다른 모든 필드들은 기본 키가 젹용된 모든 필드에 의존적이어야만 한다. 이 때, 기본 키가 적용된 필드들 중 일부에만 의존적이어선 안되고, 모든 필드에 의존적이어야만 한다. 이 때, 다른 필드들이 기본 키가 적용된 모든 필드들과 의존적인 상태를 “완전 함수적 종속”, 반대로 일부 기본 키에만 의존적인 상태를 “부분 함수적 종속”이라 한다. 정리하면 제 2 정규화는 “완전 함수적 종속”을 만족시켜야 한다는 규칙이다. 

예를 들어 다음의 테이블이 있을 때, 

| 이름(PK) | 생년월일(PK) | 본관 | 전화번호 |
| --- | --- | --- | --- |
| 김큐엘 | 950101 | 가평 | 1111-2222 |
| 정디비 | 000203 | 경주 | 2222-3333 |
| 자바스 | 021211 | 해주 | 3333-4444 |

동명이인이 있을 수도 있고, 반대로 생년월일이 정말 우연히도 겹칠 수도 있어서 위와 같이 “이름” 필드와 “생년월일” 필드에 기본 키를 부여한 상황이다. 그런데 기본 키가 아닌 필드들 중 “본관” 필드는 “이름” 필드하고만 연관이 있지 “생년월일”하고는 전혀 연관이 없는 필드이다. 즉 “부분 함수적 종속” 관계에 있다. 이 테이블은 제 2 정규화를 만족하지 못한다. 

이를 해결하기 위해선 일부 기본키와 그 기본키에 부분적으로 종속된 키가 아닌 필드들을 따로 테이블로 분리하는 게 좋다. 위 테이블에서 “이름” - “본관” 필드를 다른 테이블로 빼는 게 좋다.

| 이름(PK) | 본관 |
| --- | --- |
| 김큐엘 | 가평 |
| 정디비 | 경주 |
| 자바스 | 해주 |

| 이름(PK) | 생년월일(PK) | 전화번호 |
| --- | --- | --- |
| 김큐엘 | 950101 | 1111-2222 |
| 정디비 | 000203 | 2222-3333 |
| 자바스 | 021211 | 3333-4444 |

위와 같이 서로 관련있는 필드들끼리 별도의 테이블로 분류하는 것이 좋다. 

### 제 3정규화

키가 설정되지 않은 필드는 마찬가지로 키가 설정되지 않은 다른 필드에 종속적이어선 안된다. 

예를 들어, 한 테이블 내에 여러 필드들 중 “주민번호”라는 필드와 “이름”이라는 필드가 있고, 이 둘의 필드 모두 키가 설정되어 있지 않을 때, 두 필드들은 사실 “주민번호”만 알아도 “이름”을 알 수 있으므로, 이 둘의 필드는 다른 테이블로 분리하여 일대다 관계를 설정하는 것이 좋다. 

다음의 테이블을 보자.

| 회원코드 | 구매서적명 | 서적 가격 |
| --- | --- | --- |
| 1 | “코딩 너도 할 수 있다!” | 10000 |
| 2 | “성공한 개발자의 비밀” | 15000 |
| 3 | “성공한 개발자의 비밀” | 15000 |
| 4 | “아무 일도 없었다” | 25000 |

이 테이블을 보면, 회원코드만 알면 그 회원이 어떤 서적을 구매했는지 알 수 있고, 어떤 서적의 이름을 알면 그 서적의 가격을 알 수 있는 구조이다. 다시 말해 “회원 코드”가 “구매서적명”을 결정하고 “구매서적명”이 “서적 가격”을 결정하는 구조이다. 그러면 결과적으로 “회원 코드”를 알면 “서적 가격”을 알게 되는 삼단 논리 형태의 구조를 띤다. 이렇게 A → B, B → C가 성립될 때 A → C가 성립되는 것을 “이행적 종속”이라 하며, 제 3 정규화는 이러한 이행적 종속을 제거하는 것을 의미한다. 

이러한 구조의 테이블은 A → B와 B → C로 연결된 두 테이블로 분할하는 것이 좋다. 위 테이블은 다음과 같이 분할할 수 있다. 

| 회원코드 | 구매서적명 |
| --- | --- |
| 1 | “코딩 너도 할 수 있다!” |
| 2 | “성공한 개발자의 비밀” |
| 3 | “성공한 개발자의 비밀” |
| 4 | “아무 일도 없었다” |

| 구매서적명 | 서적 가격 |
| --- | --- |
| “코딩 너도 할 수 있다!” | 10000 |
| “성공한 개발자의 비밀” | 15000 |
| “아무 일도 없었다” | 25000 |

만약 원래 테이블을 그대로 사용했다면, “구매서적명”의 데이터가 변경되면 “서적 가격”도 해당 서적의 가격으로 변경해야만 한다. 즉, 두 개의 필드의 데이터들을 일괄적으로 변경해야 하는 번거로움이 있다. 그러나 제 3정규화를 거치고 나면 하나의 테이블에서만 데이터를 변경하면 되어서 이점이 있다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 우재남, “혼자 공부하는 SQL”, (한빛미디어, 2021)

[3] DB 설계

[데이터베이스 설계(개념적 설계, 논리적 설계, 물리적 설계), 개체 집합, 관계 집합](https://nowes00.tistory.com/9)

[4] DB 설계

[[정보처리기사 필기 핵심] - 데이터베이스 설계 (개념적/ 논리적/ 물리적) & 문제](https://levelup-teddybear.tistory.com/9)

[5] DB 설계

[[정보처리기사]-데이터 모델(DB 개념적, 논리적(정규화), 물리적 설계), 스키마, 이상(Anomaly)](https://velog.io/@tjrdbfl123/정보처리기사-DB-개념적-논리적-물리적-설계)

[6] RDBMS에서의 테이블 관계

[[DB] 관계형 데이터베이스의 관계 (1:1, 1:N, N:M)](https://ittrue.tistory.com/201)

[7] 정규화

[정규화(Normalization) \| 👨🏻‍💻 Tech Interview](https://gyoogle.dev/blog/computer-science/data-base/Normalization.html)

[8] 정규화

[[Database] 정규화(Normalization) 쉽게 이해하기](https://mangkyu.tistory.com/110)