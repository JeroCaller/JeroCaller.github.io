---
layout: single
title:  "[Python-basic] 정적 언어와 동적 언어"
categories: "Python"
tag: ["Python", "basic"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 정적 언어와 동적 언어

2022년 11월 27일 

---

## 정적 언어와 동적 언어 (static language and dynamic language)

- 정적 언어

변수 선언 시 자료형을 직접 선언해줘야 한다. 이 변수가 메모리를 얼마나 쓰게끔 할 건지, 이를 어떻게 사용할 건지 등을 컴퓨터에 알려주기 위함

예) int: 해당 변수를 정수로 다룰 것임을 선언

이러한 자료형 선언을 통해 컴퓨터는 해당 정보를 바탕으로 프로그램을 컴파일(compile)한다. 이 때 low-level language으로 컴파일한다.

- low-level language: 컴퓨터 하드웨어에 특화되어 있고, 인간보다는 컴퓨터가 더 이해하기 쉽도록 만들어진 언어

자료형을 사람이 직접 선언해주면 컴퓨터가 더 빨리 작동한다.

이러한 언어들은 한 번 자료형을 선언하면 바꿀 수 없다. 따라서 정적 언어라 불린다.

- 동적 언어

스크립팅 언어(scripting language)라고도 한다.

변수의 자료형을 일일이 선언해줄 필요가 없다. 컴퓨터가 알아서 판단하기 때문이다. 

예) x = 12 ⇒ 컴퓨터는 자동으로 ‘12’를 정수로 인식한다 ⇒ 따라서 컴퓨터는 x라는 변수의 자료형을 정수형으로 자동으로 인식한다.

동적 언어는 컴파일 대신 인터프리터(interpreter)라 불리는 프로그램을 이용해 실행한다.

동적 언어는 정적 언어에 비해 느린 편이다.

동적 언어의 예)

1. Perl
2. Ruby
3. PHP: HTML과 코드를 쉽게 조합해준다. 그래서 웹 개발에 주로 쓰인다.

- 컴파일러 VS 인터프리터

컴파일러

특정 프로그래밍 언어로 쓰인 문서를 다른 프로그래밍으로 옮기는 언어 번역 프로그램.

실행 프로그램을 만들기 위해 고급 프로그래밍 언어(high-level programming language)를 저급 프로그래밍 언어(low-level programming language)로 번역할 때 쓰인다. 이 때 원래의 문서를 원시 코드 또는 소스 코드라 하고, 출력된 문서를 목적 코드라 한다. 즉, 원시 코드에서 목적 코드로 옮기는 과정을 컴파일(compile)이라 한다.

=> 한마디로 번역가이다. from high-level to low-level

사람이 이해할 수 있는 high-level 언어를 기계는 이해하기 어렵기에 중간에서 이를 기계어로 해석해줄 컴파일러가 필요한 것이다.

예) A: 난 한국어밖에 할 줄 몰라. 근데 저 미국인과 대화를 나누고 싶어

B: 난 영어밖에 몰라. 근데 난 저 한국인과 대화를 나누고 싶어

C: 난 한국어와 영어 둘 다 잘해, 내가 중간에서 통역해줄게 -> 컴파일러의 역할

컴파일러는 소스 코드 전체를 한번에 모두 기계어로 번역해준다.

컴파일러의 실행 시간은 인터프리터보다 빠르다. 컴파일러는 초기 컴파일 후 실행파일을 만들어 놓아서 다음 실행 시에는 그 실행파일을 실행하기만 하면 되기 때문.

컴파일러는 인터프리터보다 많은 메모리를 사용한다. 원시 프로그램 크기가 크면 클수록 상당한 시간이 걸린다.

---

인터프리터

고급 언어로 작성된 원시 코드를 한 번에 한 줄씩 읽어들여 실행하는 프로그램

컴파일러와 달리, 기계어로 번역하지 않고 바로 소스 코드를 실행한다. 그래서 중간에 컴파일 과정은 없다.

원시 프로그램의 크기에 상관없이 즉시 실행 가능

사용자와의 대화식 프로그래밍 가능 -> 교육용으로 쓰이기도 함

프로그램을 빠르게 테스트 하길 원할 때 쓰기 좋음.

---

Reference

[1] Bill Lubannovic, "Introducing Python", p 7~8 (O'REILLY, 2015)

[2] Wikipedia, "컴파일러", 

**[https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC)**

[3] jhur98, "컴파일러(compiler)와 인터프리터(interpreter)의 차이", 

**[https://velog.io/@jhur98/%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%ACcompiler%EC%99%80-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0interpreter%EC%9D%98-%EC%B0%A8%EC%9D%B4](https://velog.io/@jhur98/%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%ACcompiler%EC%99%80-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0interpreter%EC%9D%98-%EC%B0%A8%EC%9D%B4)**

[4] Wikipedia, "인터프리터", 

**[https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0](https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0)**