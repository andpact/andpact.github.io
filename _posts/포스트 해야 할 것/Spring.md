---
title: "[Shorts] Spring"
excerpt: "Spring 정리"

categories:
  - Shorts
tags:
  - 

toc: 
toc_sticky:
 
date: 2022-12-16
last_modified_at: 2023-01-16
---

<!-- 개인적인 의견이 포함되어 있고, 잘못된 정보가 있을 수 있습니다. -->

## **Spring Framework**
- IoC개념의 도입을 통해 비즈니스 로직에 더욱 집중하도록 만들어주는 프레임워크

## **IoC(Inversion of Control)**
- 자신이 필요한(의존하는) 객체를 선언했을 때, 스프링이 해당 객체를 생성하여 할당(주입)해주는 것(= Dependency Injection)

## **Dependency**
- 의존성 그 자체를 의미하거나, 중요도가 높아 의존성 주입에 사용되는 객체(collaborator)를 의미

## **BeanFactory**
- IoC Container를 구성하기 위한 기능을 제공하며 실제로 Bean을 생성 및 소멸시키는 기능을 제공하는 인터페이스

## **ApplicationContext**
- BeanFactory인터페이스를 구현한 인터페이스로서 스프링에서 IoC Container역할을 담당

## **IoC Container**
- Metadata를 통해 Bean을 생성하고 구성하고 조합하여 관리하는 역할

## **Bean**
- 어플리케이션의 중추적인 역할을 담당하여 IoC 컨테이너에 의해 생성, 구성, 관리되는 객체

## **Metadata**
- Bean과 그들 사이의 의존성을 나타내며, IoC Container에 의해 사용된는 정보

## **Domain Object**
- 내가 프로그래밍 하고자 하는 분야를 분석한 결과로 나오는 객체
- ex) Member, Post...

<!-- ## **Element, Attribute, Property**
- Element : 꺽쇠 괄호(<,>)로 표현한 문서 내 요소
- Attribute : Element의 특징을 나타내는 속성
- Property : 클래스 내의 변수 -->

## **BeanDefinition**
- Metadata내의 Bean을 정의한 정보를 토대로 IoC Container내에 Bean마다 생성되는 객체

## **Factory Bean**
- IoC Container 내에서 생성자가 아닌, 인스턴트 혹은 스태틱 메서드로 생성 되는 Bean

## **Autowiring**
- Bean들의 의존성에 의한 관계를 자동으로 연결시켜주는 기능

## **Bean Scope**
- Bean이 생성될 수 있는 개수나 상황에 조건을 두어 Bean이 생성되는데 제한을 두는 것

## **Callback**
- 특정 기능을 수행한 후 자동으로 실행되는 기능

## **InitializingBean**
- BeanFactory에 의해서 Bean이 생성된 후 자동 실행되는 Callback 기능을 수행하기 위해 구현하는 인터페이스

## **DisposableBean**
- Bean이 소멸된 후 자동 실행되는 Callback 기능을 수행하기 위해 구현하는 인터페이스

## **Lifecycle**
- 객체의 생성과 소멸 자체를 의미하거나, Bean의 생성과 소멸을 통제하는 메서드를 사용하기 위해 구현하는 인터페이스

## **LifecycleProcessor**
- IoC Container의 생성과 소멸을 위한 메서드를 제공하는 인터페이스

## **ApplicationContextAware**
- Bean 내부에서 ApplicationContext의 멤버를 접근하고 싶을 때 구현하는 인터페이스

## **BeanNameAware**
- Bean 내부에서 IoC Container 내에 정의된 해당 Bean의 이름에 접근하기 위해 구현하는 인터페이스

## **BeanPostProcessor**
- Bean의 생성 전과 후에 호출되는 Callback 메서드를 정의하기 위하여 구현하는 인터페이스

## **BeanFactoryPostProcessor**
- BeanFactoryPostProcessor을 제외한 어떠한 Bean도 생성되기 전에 Metadata를 읽어들이고 변경하기 위해 구현하는 인터페이스

## **FactoryBean**
- Bean을 생성하는 BeanFactory의 멤버를 접근하기 위하여 구현하는 인터페이스

## **@Autowired**
- 생성자, 필드, Setter 등에 표시하여 IoC Container에 의한 Autowiring을 받기 위한 애너테이션

## **@Bean**
- 메서드가 컨테이너에 의해 관리되는 빈을 생성하는 것을 나타내는 애너테이션

## **@Order**
- 애너테이션 처리된 배열이나 리스트 내 객체의 정렬순서를 정의하는 애너테이션

## **@Primary**
- 빈으로 설정된 동일한 타입의 객체가 여러개 정의되어 있고, 그 중 하나의 빈만 사용되는 경우에 우선적으로 사용될 빈을 나타내는 애너테이션

## **@DependsOn**
- 현재의 빈 객체가 의존하고 있는 객체를 나타내는 애너테이션

## **@Qualifier**
- 빈으로 설정된 동일한 타입의 객체가 여러개 정의되어 있고, 그 중 사용되어야 하는 하나의 빈을 특정하기 위해 필드나 파라미터에 표시하는 애너테이션

## **@Target(Java)**
- 애너테이션을 적용할 수 있는 부분(애너테이션, 필드, 지역 변수, 메서드, 패키지, 모듈, 파라미터, 타입, 타입 파라미터)을 나타내는 애너테이션

## **@Retention(Java)**
- 애너테이션이 얼마나 지속될 것인지를 나타내는 애너테이션

## **@Value**
- 필드 혹은 메서드/생성자 파라미터에 사용되어, SpEL 표현식이나 외부에 있는 속성을 기반으로 의존성 주입을 위해 사용되는 애너테이션

## **@PostConstruct(Jakarta)**
- 의존성 주입이 완료된 후 실행되어야 하는, 의존성이 높은 객체에 정의된 기능을 나타내는 애너테이션

## **@PreDestroy(Jakarta)**
- 의존성이 높은 객체가 삭제되기 전에 실행되어야 하는, 의존성이 높은 객체에 정의된 기능을 나타내는 애너테이션

## **@Configuration**
- Bean Definition을 생성하기 위해 @Bean 애너테이션 처리된 메서드를 포함하고 있는, 메타데이터를 포함하는 클래스를 나타내는 애너테이션

## **@Component**
- 애너테이션 기반 구성이나 경로 스캐닝을 사용할 때 자동으로 감지되는 Component(Bean) 객체임을 나타내는 애너테이션

## **@Repository**
- 애너테이션 처리된 클래스가 Repository 객체임을 나타내는, @Component 애너테이션으로부터 전문화된 애너테이션

## **@Service**
- 애너테이션 처리된 클래스가 Service 객체임을 나타내는, @Component 애너테이션으로부터 전문화된 애너테이션

## **@Controller**
- 애너테이션 처리된 클래스가 Controller 객체임을 나타내는, @Component 애너테이션으로부터 전문화된 애너테이션

## **@RestController**
- @Controller와 @ResponseBody의 의미를 모두 가진 애너테이션

## **@ResponseBody**
- Web의 응답을 담당하는 객체와 메서드의 반환값이 연결됨을 나타내는 애너테이션

## **@ComponentScan**
- @Configuration 애너테이션 처리된 클래스들과 함께 사용되어 해당 클래스들에 필요한 Component 스캐닝을 지시하는 애너테이션

## **@Scope**
- @Component 애너테이션 처리된 클래스의 인스턴스나 @Bean 애너테이션 처리된 메서드로부터 반환되는 Bean의 Scpoe의 이름을 나타내는 애너테이션

## **@Inject(Jakarta)**
- 필드, 메서드, 생성자 요소에 사용되어 의존성 주입이 필요함을 나타내는 애너테이션

## **@Named(Jakarta)**
- @Inject 애너테이션에 의해 의존성으로 주입되는 타입이 동일한 객체를 구별하기 위해 사용되고, @Component 애너테이션 대용으로 사용할 수 있는 애너테이션

## **@ManagedBean**
- @Component 애너테이션 대용으로 사용할 수 있는 애너테이션

## **@Description**
- Bean 객체에 대한 설명을 추가하기 위한 애너테이션

## **@Import**
- 메타데이터를 나타내는 @Configuration 애너테이션 처리가 된 클래스를 Import하기 위해 사용하는 애너테이션

## **@Conditional**
- @Configuration 애너테이션 처리된 클래스나 @Bean 애너테이션 처리된 메서드 등을 조건적으로 생성하여 이용하기 위해 사용하는 애너테이션

## **@ImportResource**
- 메타데이터를 가진 자원(파일)을 Import하기 위해 사용하는 애너테이션

## **Environment**
- Profile과 Properties라는 두 가지 측면을 모델링하며, 어플리케이션이 작동하는 환경을 나타내는 인터페이스

## **Profile**
- 해당 Profile이 활성화 되었을 때만 컨테이너에 등록되는 논리적인 Bean Definition 그룹

## **Properties**
- 키와 값으로 선언된 다양한 내 외부 소스의 특징들

## **@Profile**
- 해당 객체가 특정 Profile이 활성화 되어야 생성될 수 있다는 것을 나타내는 애너테이션

## **PropertySource**
- Properties를 담고 있는 클래스

## **@PropertySource**
- Environment에 PropertySource를 추가하기 위한 메커니즘을 제공하는 애너테이션

## **LoadTimeWeaver**
- 스프링 속 여러 클래스들이 JVM 속에 존재하는 것처럼 해당 클래스들을 변형하기 위한 인터페이스

## **@EnableLoadTimeWeaving**
- 빈의 LoadTimeWeaver를 활성화 시키기 위한 어노테이션

## **ContextLoader**
- Root Application Context를 생성하기 위한 실제 작업을 수행하는 클래스

## **ServletContext(Jakarta)**
- 서블릿이 서블릿 컨테이너와 소통하기 위한 메서드를 제공하는 인터페이스

## **Resource**
- Resource(파일, 객체 등)과 소통하기 위한 기능을 제공하는 인터페이스

## **ResourceLoader**
- Resource 인스턴스를 얻기 위해 사용하는 인터페이스

<!-- @Lazy -->

## **Validation**
- 프로그램으로 들어오는 데이터를 검증하여 위험을 줄이는 기술

## **Data Binding**
- 사용자의 데이터(User Input)와 개발자의 데이터(Domain Model)를 동기화하는 기술

## **Validator**
- 프로그램 내의 객체를 검증하기 위한 인터페이스

## **DataBinder**
- 사용자의 데이터를 이용하여, 개발자의 데이터가 되는 타겟 객체의 필드를 설정하기 위한 인터페이스

## **BeanWrapper**
- 빈을 다루기 위한 인터페이스

## **Errors**
- 특정 객체에의 Validation과 Data-binding에 대한 정보를 저장하고, 그것을 표현하기 위한 인터페이스

## **JavaBean**
- 인수가 없는 생성자를 가지며, Setter와 Getter 메서드의 명명 규칙이 존재하는 클래스

## **PropertyEditor(Java)**
- 문자열로 받은 입력데이터를 다른 타입의 객체로 변환하는 등, 객체의 타입을 변환하기 위한 인터페이스

## **PropertyEditorRegistrar**
- 커스텀 PropertyEditor을 등록하기 위한 인터페이스

## **Converter**
- PropertyEditor의 대안으로 사용될 수 있는, 객체의 타입 변환을 위해 사용되는 인터페이스

## **ConverterFactory**
- 한 객체로 부터 여러 타입으로의 형변환이 필요할 때, 원하는 타입을 위한 Converter를 반환하기 위한 인터페이스

## **ConversionService**
- 타입 변환을 위한 API를 정의한 인터페이스

## **Formatter**
- 클라이언트 사이드로부터의 문자열 변환하고 출력하기 위한 인터페이스

## **@NumberFormat**
- 필드나 메서드의 파라미터가 숫자 형태여야함을 나타내는 애너테이션

## **@DateTimeFormat**
- 필드나 메서드의 파라미터가 날짜나 시간 형태여야함을 나타내는 애너테이션

## **FormatterRegistry**
- Formatter를 추가하기 위한 인터페이스

## **FormatterRegistrar**
- 추가된 Formatter를 등록하기 위한 인터페이스

## **@Constraint**
- Custom Constraint 애너테이션을 만들기 위한 애너테이션

## **Aspect-oriented Programming (AOP)**
- 여러 타입이나 객체 간의 공통 작업을 모듈화한 프로그래밍 방식으로 객체 지향 프로그래밍을 보완하는 개념

## **Aspect**
- 여러 타입이나 객체 간의 공통 작업을 모듈화한 개념, 스프링에서 클래스로 표현된다.

## **Join point**
- 프로그램 내의 특정 메서드의 실행 지점

## **Advice**
- 특정 Join point에서 Aspect에 의해 수행되는 기능

## **Pointcut**
- 특정 조건에 의해 필터링된 Join point

## **Introduction**
- Advice가 실행되는 객체에 추가적인 메서드나 필드를 선언하는 것

## **Target object**
- Advice가 실행되는 객체. Advised object라고도 한다.

## **AOP proxy**
- Advice를 수행하기 위해 AOP 프레임워크에서 생성되는 객체

## **Weaving**
- Target object를 생성하기위해 다른 어플리케이션 타입이나 객체와 Aspect들을 연결하는 것

## **@EnableAspectJAutoProxy**
- @Configuration 클래스에 사용되어, @Aspect Component를 다루기 위한 애너테이션

## **@Aspect(AspectJ)**
- Spring AOP를 구성하기 위해 자동으로 감지되는 Aspect를 구성하기 위한 애너테이션

## **@Pointcut**
- Pointcut에 해당하는 메서드를 만들기 위한 애너테이션

## **@Before(AspectJ)**
- @Aspect 클래스 내에서 Before advice를 만들기 위한 애너테이션

## **@AfterReturning(AspectJ)**
- @Aspect 클래스 내에서 After Returning Advice를 만들기 위한 애너테이션

## **@AfterThrowing(AspectJ)**
- @Aspect 클래스 내에서 After Throwing Advice를 만들기 위한 애너테이션

## **@After**
- @Aspect 클래스 내에서 After (Finally) Advice를 만들기 위한 애너테이션

## **@Around**
- @Aspect 클래스 내에서 Around Advice를 만들기 위한 애너테이션

## **@Configurable**
- 해당 클래스가 스프링 기반 구성으로 적합한 것을 나타내는 애너테이션

## **@Nullable**
- 특정 상황에 애너테이션 처리된 객체가 Null이 될 수 있음을 나타내는 애너테이션

## **@NonNull**
- 특정 상황에 애너테이션 처리된 객체가 Null이 될 수 없음을 나타내는 애너테이션

## **@NonNullApi**
- 애너테이션 처리된 파라미터나 반환값이 해당 패키지 내에서 Null이 될 수 없음을 나타내는 애너테이션

## **@NonNullFields**
- 애너테이션 처리된 필드가 해당 패키지 내에서 Null이 될 수 없음을 나타내는 애너테이션

## **DataBufferFactory**
- DataBuffer을 할당(생성)하고, 관리하기 위한 기능을 제공하는 인터페이스

## **DataBuffer**
- 바이트 단위로 데이터를 저장할 수 있는 기능을 제공하는 인터페이스

## **PooledDataBuffer**
- DataBuffer가 데이터 Pool을 공유하도록 하는 기능을 제공하는 인터페이스

## **DataBufferUtils**
- DataBuffer에 사용하기 위한 활용성 메서드를 제공하는 인터페이스

<!-- ## **Ahead of Time Optimizations**
-

## **ApplicationContextAotGenerator**
-  -->

<!-- ## **Global Transactions**
- 여러개의 데이터 관리 자원(프레임워크 등)을 사용하는 데이터 관리 방식

## **Local Transactions**
- 하나의 특정 데이터 관리 자원(프레임워크 등)을 사용하는 데이터 관리 방식

## **TransactionManager**
- TransactionManager 구현을 위한 인터페이스

## **PlatformTransactionManager**
- 필수 Transaction 구조를 위한 중심 인터페이스

## **ReactiveTransactionManager**
- 반응형 Transaction 구조를 위한 중심 인터페이스

## **TransactionDefinition**
- Transaction 속성을 정의하는 인터페이스

## **TransactionStatus**
- Transaction의 상태를 나타내기 위한 인터페이스

## **HibernateTransactionManager**
- 하나의 Hibernate SessionFactory를 생성하기 위해 PlatformTransactionManager를 구현한 클래스

## **LocalSessionFactoryBean**
- Hibernate SessionFactory를 생성하기 위한 FactoryBean 인터페이스 -->

## **Data Access Object (DAO)**
- 여러 데이터 접근 기술에 동일한 방식으로 작동하는 데이터 접근을 위한 객체

## **Spring Web MVC**
- 서블릿 API를 기반으로 구축된 오리지널 웹 프레임워크

<!-- ## **Servlet container**
-  -->

## **DispatcherServlet**
- 요청을 처리하는 여러 컨트롤러를 하나로 묶어놓은 하나의 컨트롤러, 해당 컨트롤러 역할을 하는 클래스 

## **ServletContext**
- 컨트롤러, 화면 처리, 웹 관련 빈들을 포함하고 있는 공간

## **@RequestMapping**
- 요청을 처리하는 클래스나 메서드임을 나타내는 애너테이션

## **@GetMapping**
- Get 요청을 처리하는 메서드임을 나타내는 애너테이션

## **@PostMapping**
- Post 요청을 처리하는 메서드임을 나타내는 애너테이션

## **@PutMapping**
- Put 요청을 처리하는 메서드임을 나타내는 애너테이션

## **@DeleteMapping**
- Delete 요청을 처리하는 메서드임을 나타내는 애너테이션

## **@PatchMapping**
- Patch 요청을 처리하는 메서드임을 나타내는 애너테이션

## **@ModelAttribute**
- 메서드 파라미터나 메서드 반환값을 웹 뷰에 나타난 모델 속성과 묶기 위한 애너테이션

## **WebDataBinder**
- 웹 요청 파라미터의 데이터를 자바 빈 객체에 묶기 위한 DataBinder

<!-- ## **** -->









<!-- ## **브라우저(Browser)**
- 화면을 통해 서버에 요청을 보내고, 응답을 받아 화면에 표시하는 클라리언트 프로그램
- 사용자와 서버간의 상호작용(요청과 응답)을 쉽고 편리하게 하기 위해서 사용한다.
- URL로 요청을 보내고 요청에 대한 응답이 화면으로 표시된다.

## **WAS(Web application server)**
- 고정된 데이터를 전달하는 웹 서버와 달리 매번 새로운 데이터를 전달할 수 있는 서버
- 새로운 데이터를 만들어 내서 사용자와 상호작용하기 위해서 사용한다.
- 브라우저를 통해서 프로그램과 상호작용을 하고, 이로 인해 생성된 데이터를 화면으로 표시한다.

## **요청(Request)**
- 브라우저를 통해서 사용자가 서버에 데이터를 요구하는 것
- 사용자가 데이터를 조회하거나 특정 작업을 수행하기 위해 사용한다.
- 조회를 위해 GET방식을 사용하고, 작업을 수해하기 위해 POST방식을 사용한다.

## **응답(Response)**
- 사용자의 요청에 따라 서버가 데이터를 전달하는 것
- 원하는 데이터를 사용자에게 전달하기 위해 사용한다.
- 요청에 따라 정적데이터 혹은 동적데이터를 전달한다.

## **HTTP(Hyper text transfer protocol)**
- 클라이언트의 요청과 서버의 응답 사이의 데이터 교환 약속
- 데이터 교환 방식을 통일하기 위해서 사용한다.
- 요청과 응답을 묶어서 HEADER과 BODY라는 틀을 사용해서 처리한다.

## **서블릿(Servlet)**
- 서버에서 동적으로 요청과 응답을 처리할 수 있는 API를 정의한 것
- 자바 프로그래밍에서 사용자와 상호작용 하기 위해서 서블릿을 사용한다.
- 서블릿 컨테이너(서버)를 통해 서블릿 인스턴스를 서블릿 저장소인 서블릿 컨텍스트에 생성하여 사용자의 요청을 받고 서버의 응답을 줄 수 있다.

## **JSP(Java server pages)**
- 서블릿과 동일하게 동적으로 데이터를 구성하는 기술
- HTML 코드를 그대로 사용하기 때문에 화면을 구성하는 용도로 사용한다.
- JSP파일이 결국 서블릿 코드로 변환되어서 컴파일 되며, 서블릿과 같은 방식으로 사용된다.

## **쿼리 스트링(Query string)**
- 서버에게 어떤 값을 전달하기 위해서 브라우저의 주소창에 사용하는 것
- 사용자가 서버에 데이터와 함게 요청을 해야 할 경우에 쿼리 스트링을 사용한다.
- URL뒤에 물음표와 파라미터를 전달하여 사용한다.

## **MVC구조(Model-View-Controller)**
- 프로그램을 데이터 처리, 데이터 출력, 데이터 가공으로 계층화한 구조
- JSP가 출력에 적합하고, 서블릿이 가공에 적합하기 때문에 책임과 역학을 구분하기 위해 MVC구조를 사용한다.
- 사용자의 요청을 서블릿이 받아서 데이터를 가공하고, JSP에 데이터를 넘겨서 출력한다.

## **PRG패턴(Post-Redirect-Get)**
- POST방식 처리 후 GET방식으로 화면을 출력하는 패턴
- POST방식 이후에 결과를 확인하기 위해서 GET을 호출하는 PRG패턴을 사용한다.
- 서블릿에서 POST방식을 처리하고, 브라우저에게 호출해야하는 주소를 전달한 뒤, 브라우저는 받은 주소를 GET방식으로 호출한다.

## **모델(Model)**
- MVC구조에서 View와 Controller의 기능을 뺀 나머지 부분
- 요청과 응답 사이의 데이터 처리을 위해 모델 개념을 사용한다.
- DTO와 서비스 객체를 구성하여 모델의 역할을 수행한다.

## **DTO(Data transfet object)**
- MVC구조에서 계층간 전달되는 데이터를 나타내는 객체
- View와 Controller에 데이터를 전달하기 위해 DTO를 사용한다.
- 전달하고자 하는 데이터를 DTO로 묶어서 서비스 객체를 통해 각 계층을 이동하면서 사용한다.

## **서비스 객체**
- MVC구조에서 데이터 처리에 사용되는 객체
- DTO를 컨트롤러로 보내기 위한 데이터 가공에 서비스 객체가 사용된다.
- 컨트롤러에서 서비스 객체를 호출하여 데이터를 전달받고 JSP로 데이터를 넘겨준다.

## **DDL(Data definition language)**
- DB에 테이블을 생성하는 SQL
- 테이블을 생성하지 않으면 데이터를 저장할 수 없기 때문에 DDL을 이용하여 테이블을 생성한다.
- DDL에 해당하는 SQL을 사용하여 열과 행으로 이루어진 테이블을 생성하고 데이터를 저장한다.

## **DML(Data manipulation language)**
- DB의 데이터를 조작하는 SQL
- 테이블에 변경사항이 있을 때 DML을 사용하여 데이터를 조작한다.
- Insert, Update, Delete등의 키워드와 함께 사용한다.

## **쿼리(Query)**
- DB의 데이터를 조회할 때 사용하는 SQL
- 테이블에서 조회할 데이터가 있을 경우 쿼리를 사용한다.
- select 키워드와 함께 사용한다.

## **DAO(Data access object)**
- DB의 접근을 위해 SQL을 실행하는 객체, JDBC프로그램
- DB의 데이터를 조작하기 위해서 DAO를 사용한다.
- 클래스 내부에 SQL을 작성해서 사용한다.

## **VO(Value object)**
- DB의 한 행을 자바의 객체로 표현한 것
- DB의 데이터를 자바 프로그램 내에서 사용하기 위해서 VO로 만들어서 사용한다.
- 데이터를 클래스로 정의하여 프로그램 내에서 사용할 수 있도록 한다.

## **쿠키(Cookie)**
- 브라우저와 서버가 주고 받는 문자열로 만들어진 데이터이며 키와 값으로 이루어져 있다.
- 요청과 응답이 이루어지면 이에 대한 정보가 남지 않기 때문에, 정보를 남기기 위해서 쿠키를 사용한다.
- 쿠키를 브라우저에 저장하거나, 파일 형태로 컴퓨터 상에 저장하여 필요할 때 사용한다.

## **세션 저장소(Session Repository)**
- 서버에서 발행하는 쿠키(개발자가 만든 쿠키가 아닌)를 브라우저 상에 저장하기 위한 공간이며 키와 값으로 이루어져 있다.
- 요청과 응답과 관계 없이 브라우저가 실행되는 동안 필요한 정보를 저장하기 위해 세션 저장소를 사용한다.
- 서버에서 세션 저장소(Key)를 생성하고, 서블릿이나 JSP에서 객체를 세션 컨텍스트(Value)에 저장하여 사용한다.

## **필터(Filter)**
- 요청이 컨트롤러에 도착하기 전에 필터링 하는 역할
- 컨트롤러 마다 필터 역할을 하는 기능을 작성할 수 없으니, 한 번에 묶어서 필터에 작성한다.
- 필터에 해당하는 클래스를 작성하고, 경로를 설정해 사용한다.

## **EL 스코프(Scope)**
- JSP 내부에서 외부의 데이터를 참조하는 EL이 데이터를 참조하는 범위
  - Parameter scope : URL의 쿼리 스트링으로 넘어온 값
  - Page scope : JSP에서 EL을 통해 <c:set>으로 저장한 변수
  - Request scope : HttpServletRequest에 setAttribute()로 저장한 변수
  - Session scope : HttpSession을 이용해서 setAttribt()로 저정한 변수
  - Application scope : ServletContext를 이용해서 setAttribute()로 저장한 변수

## **스프링(Spring)**
- 의존성 주입 개념을 적용할 수 있는 프레임워크

## **의존성 주입(Dependency injection)**
- 스프링이 객체 지향 구조를 설계할 때 사용하는 개념으로 의존성(필요도)이 높은 객체의 관리(생성, 활용)를 스프링이 대신 해주는 것이고 필드 주입과 생성자 주입방식이 있다.
- 
- 스프링 설정 파일(root-context.xml)이나, 별도의 클래스를 작성하여 의존성 주입을 할 수 있다.

## **빈(Bean)**
- 스프링이 의존성 주입을 위해 관리하는 의존성이 높은 객체
- 
- 스프링 설정 파일을 읽어서 어플리케이션 컨텍스트가 생성되고 빈이 저장된다.


## **어플리케이션 컨텍스트(Application context)**
- 스프링의 빈이 저장되어 관리되는 공간
- 
- 스프링 설정 파일을 읽어서 어플리케이션 컨텍스트가 생성되고 빈이 저장된다.

## **스프링 MVC**
- 기존 MVC 구조에 다양한 기능을 접목시킨 것
  - Front-Controller 패턴
  - 애너테이션 활용(@Controller, @Request mapping, @Get mapping, @Post mapping..)
  - 컨트롤러 내 메서드의 파라미터 자동 수집과 변환
- 이전보다 코드가 짧아져 개발 시간을 단축시킬 수 있다.
- 

## **Ajax(Asynchronous javaScript and XML)**
- URL을 사용하지 않고 서버와 데이터를 주고 받는 방식
- 화면의 변화 없이 서버와 통신이 필요할 때(자동완성 등) 사용한다.
- 

## **JSON(Javascript object notation)**
- JS에서 객체를 키와 값의 형태로 표현하는 문자열
- 클라이언트에서 서버로부터 정보를 일관된 형태로 받기 위해 JSON을 사용한다.

## **REST(Representational state transfer)**
- 클라이언트와 서버가 어떻게 데이터를 주고받을지에 관한 가이드, 방식
- 원하는 자원(URL)과 작업(Get, Post...)을 나누어 효율을 높이고 통일하기 위해 REST 방식을 사용한다.

## **** -->