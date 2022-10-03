---
title: "[WID] WID with Spring Boot (작성중)"
excerpt: "Spring Boot으로 작성한 App WID"

categories:
  - WID
tags:
  - 

toc: true
toc_sticky: true
 
date: 2022-10-03
last_modified_at: 2022-10-03
---
## **MariaDB 설치**
![mariaDB 설치](/assets/images/App WID with Spring Boot/mariaDB 설치.png)
## **데이터베이스 생성**
- 데이터베이스 생성 이미지 추가

## **Spring Initializr을 이용한 프로젝트 생성**
- 이미지 추가!!

---
- **데이터베이스 관련 설정 추가**

스프링 부트가 자동 설정을 통해서 인식한 Spring Data JPA를 실행할 때, 데이터베이스를 찾을 수 있도록 application.properties파일에 다음과 같은 데이터베이스 설정을 추가합니다.
```
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://localhost:3306/webdb
spring.datasource.username=webuser
spring.datasource.password=webuser
```
---
- **Lombok을 테스트 환경에서도 사용**

프로젝트 생성 시 Lombok 라이브러리가 설치 되지만, 테스트 환경을 위한 준비는 되지 않았습니다.<br>
build.gradle 파일 내 dependencies 항목에 test 관련 설정을 추가합니다.
```
testCompileOnly 'org.projectlombok:lombok'
testAnnotationProcessor 'org.projectlombok:lombok'
```
---
- **DataSource 설정**

DB연결!!!

