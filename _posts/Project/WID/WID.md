---
title: "[WID] WID"
excerpt: "WID"

categories:
  - WID
tags:
  - 

toc: 
toc_sticky: 
 
date: 2022-10-03
last_modified_at: 2023-02-06
---
# **WID**
**What I Did** or **What I'll Do**

# **Request - Response Table**
|URL|Method|기능|
|:---:|:---:|:---:|
|/quest/|POST|Quest 등록|
|/quest/{qno}|GET|단일 Quest 조회|
|/quest/list|GET|전체 Quest 조회|
|/quest/{qno}|PUT|Quest 수정|
|/quest/{qno}|DELETE|Quest 삭제|
|/record/|POST|Record 등록|
|/record/{rno}|GET|단일 Record 조회|
|/record/list|GET|전체 Record 조회|
|/record/{rno}|PUT|Record 수정|
|/record/{rno}|DELETE|Record 삭제|

<!-- # **Wire Frame** -->

# **History**
**2023.02.05**
Quest CRUD 기능 작성
- Quest : 해야 할 일 혹은 계획을 의미
- DB 생성과 Entity 작성
  - qno(Entity 번호)
  - user(사용자)
  - title(제목)
  - dueDate(기한)
  - finished(완료 여부)
- Repository 작성
  1. Spring API인 JpaRepository를 상속받아 CRUD메서드를 사용
- Service 객체 작성
  1. ModerMapper을 주입받고, Controller에서 넘겨받은 DTO를 Mapper를 통해 Entity로 변환
  2. Repository를 주입 받고, 변환된 Entity를 통해 Repository의 CURD메서드를 실행
- Controller 작성
  1. Service 객체를 주입 받고, Request로부터 DTO를 받아 Service 객체의 CURD메서드를 실행
- DTO 작성
  - qno(DTO 번호)
  - user(사용자)
  - title(제목)
  - dueDate(기한)
  - finished(완료 여부)

Quest CRUD 기능 Test 코드 작성
  - Repository, Service 객체, Controller의 Test 코드 작성
    1. Repository, Service, Controller를 주입 받고, 임의의 DTO, Entity 생성 혹은 기존의 객체를 사용하여 Repository, Service, Controller의 CURD 메서드를 실행하여 테스트 진행

<!-- Build a Quest CRUD function
- Quest: Something to do or a plan
- Create DB and Build Entity
  - qno(entity number)
  - user
  - title
  - due date
  - finished
- Build Repository
  1. Execute CRUD method from inherited JpaRepository which is Spring API
- Build Service object
  1. DTO passed from Controller is converted to Entity through injected Mapper
  2. Execute CURD method from injected Repository with converted Entity
- Build Controller
   1. execute CURD method from injected service object with received DTO from request
- Build DTO
  - qno(DTO number)
  - user
  - title
  - dueDate
  - finished

Build Quest CRUD function test code
  - Build Repository, Service object, and Controller test code
    1. Execute Repository, Service, and Controller's CURD method to test from injected Repository, Service, and Controller, with creating arbitrary DTO, Entity, or using object existing -->

**2023.02.06**
Record CRUD 기능 작성
- Record : 과거에 했던 일, 시간을 어떻게 보냈는지 의미
- DB 생성과 Entity 작성
  - rno(Entity 번호)
  - user(사용자)
  - title(제목)
  - date(날짜)
  - start(시작 시간)
  - finish(종료 시간)
- Repository 작성
  1. Spring API인 JpaRepository를 상속받아 CRUD메서드를 사용
- Service 객체 작성
  1. ModerMapper을 주입받고, Controller에서 넘겨받은 DTO를 Mapper를 통해 Entity로 변환
  2. Repository를 주입 받고, 변환된 Entity를 통해 Repository의 CURD메서드를 실행
- Controller 작성
  1. Service 객체를 주입 받고, Request로부터 DTO를 받아 Service 객체의 CURD메서드를 실행
- DTO 작성
  - rno(DTO 번호)
  - user(사용자)
  - title(제목)
  - date(날짜)
  - start(시작 시간)
  - finish(종료 시간)

Record CRUD 기능 Test 코드 작성
  - Repository, Service 객체, Controller의 Test 코드 작성
    1. Repository, Service, Controller를 주입 받고, 임의의 DTO, Entity 생성 혹은 기존의 객체를 사용하여 Repository, Service, Controller의 CURD 메서드를 실행하여 테스트 진행

Build a Record CRUD function
- Record: What you did in the past, how you spent your time
- Create DB and Build Entity
  - rno(entity number)
  - user
  - title
  - date
  - start(time)
  - finish(time)
- Build Repository
  1. Execute CRUD method from inherited JpaRepository which is Spring API
- Build Service object
  1. DTO passed from Controller is converted to Entity through injected Mapper
  2. Execute CURD method from injected Repository with converted Entity
- Build Controller
  1. execute CURD method from injected service object with received DTO from request
- Build DTO
  - rno(DTO number)
  - user
  - title
  - date
  - start(time)
  - finish(time)

Build Record CRUD function test code
  - Build Repository, Service object, and Controller test code
  1. Execute Repository, Service, and Controller's CURD method to test from injected Repository, Service, and Controller, with creating arbitrary DTO, Entity, or using object existing