---
title: "[WID] WID"
excerpt: "WID about time"

categories:
  - WID
tags:
  - 

toc: true
toc_sticky: true
 
date: 2022-10-03
last_modified_at: 2022-11-25
---
# **What**
**WID의 의미**
- 필자의 첫 개인 프로젝트
- 과거의 시간을 기록하고 미래의 시간을 계획하기 위한 어플리케이션
- WID는 **What I did** 혹은 **What I'll do**의 준말

# **Why**
**기획 배경**
- 2022년 3월 초, 공무원 시험 준비를 제쳐둔 채 프로그래밍에 관심이 생겨 공부를 시작하였다. 그리고 어떤 프로그램를 만들면 좋을까 고민을 하던 중, 가장 가까운 소비자인 나 자신이 무엇이 필요한지 생각해 보았다. 그 때 떠오른 것이 공무원 시험 준비 중에 '내가 잘하고 있는 것인가?'라는 의구심이 들었던 경험이었다. 이러한 의문을 해결하려면 쏟은 노력을 시각적으로 표현하고, 눈으로 확인할 수 있도록 하는 것이 좋겠다는 생각이 들었다. 여기에서 말하는 노력은 '오늘 몇 페이지를 공부했는가?' 혹은 '어떤 개념을 학습했는가?'가 아닌 '시간을 얼마나 투자했는가?'를 의미한다. 이렇게 정의한 이유는 노력이라는 것을 최대한 간결하고, 직관적으로 표현하고 싶었고, 당장 어제의 것이 아니라 최근 일주일, 한달, 길면 일년까지도 내가 쏟은 노력을 확인하고 싶었는데, 이를 가장 효율적으로 확인할 수 있는 시간은 시간이라 판단하였다. 위와 같은 이유로 내가 시간을 어떻게 보냈는지를 확인하는 프록램을 만들고 싶었다.

# **How**
**동작원리**
<!-- - 프레임워크, 라이브러리, 서블릿 등등 자세하게 -->
- 입력 폼으로 부터 어떤 활동의 제목과 시작 시점, 종료 시점에 관한 정보를 받아  DB에 저장하고, 리스트 화면에서 해당 활동에 대한 정보를 표현한다.

<!-- **와이어 프레임**
- ㅇ

**플로우 차트**
- ㅇ -->

# **When**
**개발 기간**
- 2022.10.18 ~

# **Who**
- **andpact.github.io**
- 서비스 도메인 주소

<!-- # **Where**
- d -->

<!-- **2022.10.18**
- 프로젝트 생성
- 프로젝트 경로 재설정 및 Tomcat 재시작 최소화 설정 [Tomcat - Configuration]
- 로그 한글 깨짐 해결 [help - Edit Custom VM Options] 메뉴에 설정추가
- TimeTableListController(Get) 및 TimeTableRegisterController(Post) 작성
- list.jsp 및 register.jsp 작성
- TimeTableDTO(tableNumber, date, title, startTime, endTime) 작성

**2022.10.20**
- 데이터베이스 연결 및 생성
- TimeTableService(listAll, get) 작성
- TimeTableReadController(Get) 작성
- read.jsp 작성
- DB연결 테스트 코드 작성
- lombok 및 HikariCP 라이브러리 추가
- TimeTableVO(tableNumber, date, title, startTime, endTime) 작성
- HikariCP 테스트 코드 작성
- TimeTableDAO(insert) 및 테스트 코드 작성
- ConnectionUtil 작성

**2022.10.21**
- TimeTableDAO(selectAll, selectOne, deleteOne, UpdateOne) 및 테스트 코드 작성

**2022.10.22**
- ModelMapper 라이브러리 추가
- MapperUtil 작성
- TimeTableService(register) 및 테스트 코드 작성

**2022.10.23**
- Log4j2, 테스트 환경용 lombok, jstl 라이브러리 추가
- log4j2.xml 파일 생성

**2022.10.27**
- list.jsp 작성
- DB(duration column) 추가
- TimeTableDAO(게시물 등록, 게시물 전체 조회, 게시물 조회, 게시물 수정) 및 테스트 코드 수정
- TimeTableDTO(duration 추가) 수정
- TimeTableVO(duration 추가) 수정

**2022.10.29**
- TimeTableListController(게시물 전체 조회) 날짜별 활동 당 총 시간 계산 기능 추가

**2022.11.01**
- TimeTableService(게시물 수정, 삭제) 작성

**2022.11.02**
- TimeTableDAO(게시물 범위 조회) 작성
- TimeTableService(게시물 범위 조회) 작성
- modify.jsp(게시물 수정) 작성
- TimeTableModifyController(게시물 수정) 작성
- TimeTableRemoveController(게시물 삭제) 작성

**2022.11.03**
- read.jsp(게시물 조회) 및 TimeTableReadController(게시물 조회) 삭제

**2022.11.04**
- login.jsp 및 LoginController(Get, Post) 작성
- LogoutController(Post) 작성
- LoginCheckFilter 작성
- UTF8Filter 작성
- DB(member Table) 생성
- MemberVO 작성
- MemberDAO(Get) 작성
- MemberDTO 작성
- MemberService 작성 -->


# **History**
**2022.11.04**<br>
<로그인 구현>
- 로그인 화면(임시)과 DB에 멤버 테이블(mname, mid, mpw 열)을 만들었다.
- ID와 PW를 입력하여 DB에 일치하는 ID와 PW가 존재하는지 확인하고, 존재하면 리스트 페이지로 이동, 그렇지 않으면 로그인 오류 메시지를 로그인화면에 출력한다.
- 아직 회원가입을 구현하지 않았기 때문에 임시로 로그인 테이블에 임시로 ID와 PW를 삽입하여 진행하였다.

**2022.11.08**<br>
<당일 게시물 CRUD 구현>
- 하루 00시 ~ 24시 안에서 시작되고 끝나는 활동의 게시물에 대한 CRUD를 구현하였다.
- 게시물 등록과 수정을 위한 페이지 각각 생성하였음. 게시물 등록과 수정에 필요한 정보는 제목, 시작날짜, 종료날짜, 시작시간, 종료시간이다.
- 게시물 조회는 로그인시 자동으로 할 수 있고, 게시물 삭제는 리스트 화면에서 삭제 버튼 클릭으로 진행하도록 구현하였다. 그리고 소요시간은 서블릿 객체에서 자동으로 계산되도록 하였다.
- DB에 게시물 테이블(mid, title, startDate, finishDate, start, finish, duration 열) 생성하였다.
- 게시물 등록 후에는 자동으로 리스트 화면으로 전환된다.

<이틀 게시물 CRUD 구현>
- 이틀에 걸쳐 진행되는 활동에 CRUD를 구현하였다.
- 해당 게시물은 DB에 2개의 데이터를 만든다. 이는 나중에 시간표를 그래픽으로 표현할 때 2개의 데이터가 필요하기 때문이다.

**2022.11.9**<br>
<회원 가입 구현>
- 회원 가입을 위한 페이지(임시)를 생성하였고, 닉네임과 ID, PW를 입력 시 DB의 멤버 테이블에 해당 내용이 저장되고 로그인을 할 수 있다.

**2022.11.10**<br>
<본인 게시물만 열람>
- 게시물 관련 테이블이 하나이기 때문에, 리스트 화면에서 전체 사용자의 게시물이 노출되지만 본인의 게시물만 보여야 하므로, 로그인 시 본인의 게시물만 보이도록 하였다.
- 로그인 시 사용자의 ID를 세션에 저장하고, 저장된 ID에 해당하는 데이터만 추출하도록 쿼리를 작성하여 리스트 페이지에서 본인의 게시물만 열람할 수 있게 하였다.

**2022.11.12**<br>
<클릭 등록 구현>
- 기존에는 데이터를 일일이 입력하여 게시물을 등록하였는데, 제목만 입력하고 시작 버튼을 누르면 시작시간이 기록되고, 종료 버튼 클릭 시 DB에 데이터가 추가되어 게시물을 등록하도록 구현하였다.

**2022.11.22**<br>
<임시 화면 구성>
- 로그인 화면 : ID, PW 입력창, 로그인, 회원가입 버튼 생성
- 회원가입 화면 : ID, PW, 이름 입력창, 가입 완료 버튼 생성
- 게시물 조회 화면 : 날짜 별 활동 소요시간 표시
- 게시물 등록 화면 : 제목, 시작날짜와 시간, 종료 날짜와 시간 입력창, 등록완료 버튼 생성
- 게시물 변경 화면 : 제목, 시작과 종료시간 입력창 생성

**2022.11.24**<br>
<클릭 입력 등록 방식 변경 및 등록 제한>
- 기존 방식 : Start 버튼 클릭 시 시작 시점을 세션에 저장하고, Finish 버튼 클릭 시 컨트롤러에서 세션의 정보와 종료 시점의 정보를 계산해 등록한다.
- 새로운 방식 : Start 버튼 클릭 시 입력 폼에 시작 시점을 기록하고, Finish 버튼 클릭 시 동일한 방식으로 입력 폼에 종료 시점을 기록 후, Registr 버튼 클릭해 등록한다.
- 시작 날짜가 종료 날짜 이후이거나, 시작 날짜와 종료 날짜가 같지만 시작 시간이 종료 시간 이후면 등록이 되지 않도록 컨트롤러를 수정하였다.