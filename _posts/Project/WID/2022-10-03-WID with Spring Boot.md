---
title: "[WID] WID with Spring Boot (작성중)"
excerpt: "Spring Boot으로 작성한 WID"

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
- 어플리케이션 실행 중 생성된 데이터를 저장하기 위해 데이터베이스가 필요한데, 여러 데이터베이스 중 무료로 사용할 수 있는 MariaDB를 설치합니다.
- MariaDB는 <https://mariadb.org/> 사이트를 통해서 운영체제에 맞는 버전을 다운로드 할 수 있습니다.
- 내려받은 MariaDB 설치 파일을 실행하고 라이센스에 동의하면 설치 경로에 대한 정보가 나옵니다.

![mariaDB 설치](/assets/images/WIDwithSpringBoot/mariaDB 설치.png){: width="49%", height="100%"}
![mariaDB 설치2](/assets/images/WIDwithSpringBoot/mariaDB 설치2.png){: width="49%", height="100%"}{: .align-right}

- 다음은 MariaDB에서 가장 중요한 계정인 root계정을 설정해줄 차례입니다.
- root계정을 외부에서도 접근해서 사용하려면 'Enable access from remote..'부분을 체크하면 됩니다.

![mariaDB 설치3](/assets/images/WIDwithSpringBoot/mariaDB 설치3.png){: width="59%", height="100%"}{: .align-center}

- 다음으로 해줄 설정은 운영체제에서 사용하는 이름과 외부 프로그램이 MariaDB와 연동할 때 사용하는 포트번호입니다.
- MariaDB는 기본적으로 3306포트를 이용해서 외부에서 들어오는 연결을 처리합니다.
- 현재 운영체제에서 이미 3306포트를 사용하고 있어 문제가 발생하면 다른 포트로 변경해주어야 합니다.

![mariaDB 설치4](/assets/images/WIDwithSpringBoot/mariaDB 설치4.png){: width="49%", height="100%"}
![mariaDB 설치5](/assets/images/WIDwithSpringBoot/mariaDB 설치5.png){: width="49%", height="100%"}{: .align-right}

- 정상적으로 설치가 완료되면 다음과 같은 화면을 볼 수 있습니다.

![mariaDB 설치6](/assets/images/WIDwithSpringBoot/mariaDB 설치6.png){: width="49%", height="100%"}
![mariaDB 설치7](/assets/images/WIDwithSpringBoot/mariaDB 설치7.png){: width="49%", height="100%"}{: .align-right}

## **데이터베이스 생성**
- 작성할 프로그램과 데이터베이스는 연결이 필요한데, 이 때 별도의 프로그램을 이용해 데이터베이스를 연결하고, 필요한 작업을 수행합니다.
- 흔히 SQL 에디터라고도 불리는데 여기서는 MaridDB 설치 시 자동으로 설치되는 HedidiSQL을 데이터베이스 연결에 필요한 프로그램으로 사용합니다.
- HedidiSQL을 실행하고 세션 관리자로 데이터베이스와 연결을 시도합니다. 연결에 사용되는 계정은 root계정입니다. MariaDB 설치 시 지정한 패스워드가 필요합니다.

![mariaDB 사용자 계정 추가](/assets/images/WIDwithSpringBoot/mariaDB 사용자 계정 추가.png){: width="49%", height="100%"}
![mariaDB 사용자 계정 추가2](/assets/images/WIDwithSpringBoot/mariaDB 사용자 계정 추가2.png){: width="49%", height="100%"}{: .align-right}

- 정상적으로 연결되면 아래와 같은 화면을 확인할 수 있습니다.

![mariaDB 사용자 계정 추가3](/assets/images/WIDwithSpringBoot/mariaDB 사용자 계정 추가3.png){: width="49%", height="100%"}{: .align-center}

- 데이터베이스 연결을 완료했으니, 실제 데이터베이스를 생성하도록 합니다.

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

