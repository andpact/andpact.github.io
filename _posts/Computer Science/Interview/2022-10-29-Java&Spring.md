---
title: "[Interview] Java & Spring"
excerpt: "Java & Spring 정리"

categories:
  - Interview
tags:
  - 

toc: true
toc_sticky: true
 
date: 2022-10-29
last_modified_at: 2022-10-31
---

개인적인 의견이 포함되어 있고, 잘못된 정보가 있을 수 있습니다.

## **자바의 메모리 영역**
- JVM에 의해 3가지로 관리되는 메모리 영역
- 메서드(Method) 영역 : static 변수, 전역 변수가 저장되는 공간
- 스택(Stack) 영역 : 지역 변수, 메서드 등이 저장되는 공간
- 힙(Heap) 영역 : new 연산자를 통해 동적 할당된 객체들이 저장되는 공간이자 가비지 컬렉터에 의해 관리되는 공간

## **자바 가상 머신(Java Virtual Machine)**
- 자바 프로그램을 운영체제에 관계 없이 실행하기 위한 소프트웨어적 하드웨어

<!-- ## **가비지 컬렉터(Garbage Collector)** -->

## **인터페이스(Interface)**
- 미완성된 메서드로만 이루어진 클래스이다. 따라서 단독으로 사용할 수 없고, 하위 클래스의 구현을 통해서만 사용 가능하다.
- 공통적인 기능을 인터페이스에 정의하고, 하위 클래스에서 변형하여 사용하기 위해 작성한다.
- 미완성 메서드가 하나이상 존재하는 클래스는 abstarct가 붙는다.

<!-- ## **메모리 상수풀 영역** -->

## **직렬화(Serialize)**
- 자바 프로그램 내부의 객체나 데이터를 자바 프로그램 외부에서 사용하기 위해 바이트 형태로 변환하는 것
- 반대의 경우를 역직렬화(Deserialize)라고 한다.

## **JDBC(Java DataBase Connectivity)**
- 자바 프로그램에서 데이터베이스에 접근할 수 있도록 하는 자바 API
- 데이터베이스에 쿼리나 업데이트를 실행하는 방법을 제공한다.

## **리플렉션**
- 컴파일러를 무시한 채 런타임 상황에서 메모리에 올라간 클래스나 메서드 등의 정의를 동적으로 찾아 조작할 수 있는 행위
- 동적인 언어가 가진 특징

## **Servlet과 JSP**
- 자바 프로그램에서 클라이언트의 요청을 받고 화면을 출력하는 동일한 기능을 수행한다.
- Servlet은 자바로 작성되므로 요청을 받는 것에 특화되어있고, JSP는 HTML을 사용하므로 출력에 특화되어 있다.

## **제네릭(Generic)**
- 클래스의 타입을 결정하지 않고, 객체 생성 시 유동적으로 타입을 정하기 위해 사용하는 것

## **박싱(Boxing)과 언박싱(Unboxing)**
- 박싱(Boxing) : 원시 데이터 타입을 Wrapper 클래스로 변환하는 것
- 언박싱(Unboxing) : Wrapper 클래스를 원시 데이터 타입으로 변환하는 것

## **데드락(Dead Lock)**
- 둘 이상의 스레드가 서로의 자원을 요청하여 프로세스가 진행되지 않고 멈춰있는 상태

## **상속과 컴포지션**
- 상속 : 하위 클래스가 상위 클래스를 확장해서 상위 클래스의 멤버를 사용하는 것(is 관계)
- 컴포지션 : 클래스가 다른 클래스를 내부로 포함시키는 것(has 관계)

## **다형성**
- 하나의 메서드나 클래스가 다양한 방식으로 동작하는 것

<!-- ## **스프링(Spring)** -->

<!-- ## **스프링부트(Spring Boot)** -->

## **MVC 패턴(Model, View, Controller)**
- 프로그램 디자인 방식 중 하나
- Model : 데이터베이스 관리
- View : 화면 출력
- Controller : Model과 View사이의 데이터 교환

## **MVC1, MVC2 패턴**
- MVC1 : 화면 출력과 데이터 교환을 모두 JSP에서 처리하는 구조
- MVC2 : 화면 출력은 JSP가 담당하고, 데이터 교환은 서블릿이 담당하는 구조

<!-- ## **스프링 MVC 구조 흐름** -->

<!-- ## **관점 지향 프로그래밍(Aspect Oriented Programming)** -->

## **의존성 주입(Dependency Injection)**
- 의존성이 높아 여러곳에서 사용되는 객체를 클래스에 삽입하여 사용하는 것

<!-- ## **스프링 필터랑 인터셉터** -->

## **IOC(Inversion of Control)**
- 인스턴스의 생성부터 소멸까지의 단계를 개발자가 아닌 컨테이너가 대신 관리해주는 것

## **DAO와 DTO**
- DAO : DB에 접근하기 위해 SQL을 실행하는 객체
- DTO : MVC 계층 간의 데이터 교환에 사용되는 객체 VO는 read only특징을 가진점이 다르다.