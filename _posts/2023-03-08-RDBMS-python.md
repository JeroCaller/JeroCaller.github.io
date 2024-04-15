---
layout: single
title:  "[Python][RDBMS] 관계형 데이터베이스와 파이썬 개요"
categories: ["Python"]
tag: ["Python", "관계형 데이터베이스", "데이터베이스", "RDBMS", 'DB', 'Database']
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

관계형 데이터베이스는 다음의 특징을 가진다.

- 여러 명의 유저들이 동시에 데이터에 접근할 수 있다.
- 여러 유저들에 의해 생길 수 있는 원치 않는 데이터 변형을 막아준다.
- 데이터를 저장하고 검색할 수 있는 효율적인 방법을 제공한다.
- 데이터는 스키마(schema)로부터 정의되고, 제약조건(constraint)에 의해 제한된다.
- 선언형 (declarative) 쿼리 언어로 SQL (Structured Query Language)를 사용한다.

참고) 선언형 VS 명령형 (declarative VS imperative)

선언형 프로그래밍은 프로그램이 무엇을 할 지를 알려주는 것이고, 명령형 프로그래밍은 프로그램이 작업을 어떻게 할 것인지를 알려주는 것이다. 일종의 what VS how로 귀결될 수 있다. 명령형에서는 어떤 작업을 하기 위한 과정들을 일일이 다 알려줘야 한다. 반면 선언형은 그저 어떤 작업을 할 것인지만 알려주고, 그 작업을 하는 일련의 과정들은 자동으로 처리되는 방식이다. 

“관계형”이라 불리는 이유는, 서로 다른 종류의 데이터들을 표(table)로 표현하여 그들간의 관계를 보여주기 때문이다. 

관계형 데이터베이스의 테이블 중 하나 또는 그 이상의 열(column)이 기본 키(primary key)가 된다. 해당 키는 테이블에서 유일해야 한다. 이 키는 테이블에 똑같은 데이터를 중복되게 추가되는 것을 막아준다. 해당 키는 쿼리 작업 시 빠른 검색을 위한 인덱싱(index)에 쓰인다. 인덱스는 책의 목차 같은 것이라서 특정 행(row)을 더 빨리 찾을 수 있게 해준다. 

디렉토리 안에 파일이 들어가듯이, 테이블은 데이터베이스(database)안에 포함된다. 

> database라는 말은 여러 의미에서 쓰인다. 그래서 서버라는 의미로 쓰일 때는 database server, 테이블을 담는 상위 개념의 의미에서는 database, 데이터 그 자체를 가리킬 때는 data라고 부른다.
> 

key가 걸려 있지 않은 열을 이용하여 특정 행을 찾고자 할 때에는 보조 인덱스(secondary index)를 해당 열에 정의하고, 그렇지 않으면 테이블 스캔을 통해 전체 데이터를 다 뒤진다. 

각 테이블들은 서로 외래키(foreign key)로 연결될 수 있다. 그러면 특정 열들은 해당 키로 인해 제약이 걸리게 된다. 

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [Declarative vs imperative](https://dev.to/ruizb/declarative-vs-imperative-4a7l)