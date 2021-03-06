# 1.1 IoC 컨테이너: 빈 팩토리와 어플리케이션 컨텍스트

스프링에선 오브젝트의 관리를 어플리케이션 코드 대신 IoC 컨테이너가 담당

오브젝트의 생성과 오브젝트 사이의 런타임 관계를 설정하는 DI 관점으로 볼 땐 IoC 컨테이너를 빈 팩토리라고 함

빈 팩토리에 여러 가지 컨테이너 기능을 추가한 것을 어플리케이션 컨텍스트라고 부름

ApplicationContext는 ListableBeanFactory와 HierachicalBeanFactory라는 두 개의 인터페이스를 상속

ApplicationContext 인터페이스를 구현한 클래스의 오브젝트를 스프링 컨테이너, IoC 컨테이너라고 부름

## 1.1.1 IoC 컨테이너를 이용해 어플리케이션 만들기

ApplicationContext를 구현한 오브젝트를 만든 뒤 POJO 클래스와 설정 메타정보가 필요함

### POJO 클래스

- 특정 기술과 스펙에서 독립적
- 의존관계에 있는 다른 POJO와 느슨한 결합(인터페이스)

### 설정 메타정보

스프링의 설정 메타정보는 BeanDefinition 인터페이스로 표현되는 추상 정보

이 정보를 담은 오브젝트를 사용해 IoC와 DI 작업 수행

BeanDefinitionReader를 통해 메타정보를 어떤 형식으로든 정의 가능

- 빈: 스프링 컨테이너가 관리하는 오브젝트

**빈 메타정보**

- 빈 아이디, 이름, 별칭
- 클래스 또는 클래스 이름
- 스코프
- 프로퍼티 값 또는 참조
- 생성자 파라미터 값 또는 참조
- 지연된 로딩 여부, 우선 빈 여부, 자동와이어링 여부 등

IoC 컨테이너는 메타정보를 읽어들인 뒤, 빈 오브젝트를 생성하고 프로퍼티나 생성자를 통해 의존 오브젝트를 주입

- 하나의 클래스를 여러 개의 빈으로 등록하는 이유
    - 빈마다 다른 설정을 지정해두고 사용하기 위해서
        
        *ex) 사용할 DB가 여러 개*
        

## 1.1.2 IoC 컨테이너의 종류와 사용 방법

### StaticApplicationContext

코드를 통해 빈 메타정보를 등록하기 위해 사용

학습 테스트를 제외하면 거의 사용되지 않음

### GenericApplicationContext

가장 일반적인 어플리케이션 컨텍스트

외부 리소스에 있는 빈 설정 메타정보를 리더를 통해 읽어들임

**대표적인 빈 메타정보 설정**

- XML 파일
- 자바 소스코드 어노테이션
- 자바 클래스

어플리케이션 컨텍스트를 직접 만들고 초기화할 일은 없지만 테스트 코드에서 자주 사용됨

@ContextConfiguration에 지정한 XML파일로 초기화돼서 테스트 내에서 사용

### GenericXmlApplicationContext

XmlBeanDefinitionReader 내장

### WebApplicationContext

웹 환경에서 사용할 때 필요한 기능이 추가된 어플리케이션 컨텍스트

웹에선 main() 메소드 역할을 하는 서블릿을 만들어두고, 

미리 어플리케이션 컨텍스트를 생성해둔 다음, 

요청이 서블릿으로 들어올 때마다 getBean()으로 필요한 빈을 가져와 정해진 메소드를 실행해준다.

자신이 만들어지고 동작하는 환경인 웹 모듈에 대한 정보에 접근 가능

**DispatcherServlet**

- 웹 환경에서 어플리케이션 컨텍스트 생성
- 설정 메타정보로 초기화
- 클라이언트로부터 들어오는 요청마다 적절한 빈을 찾아서 실행
- web.xml로 설정

## 1.1.3 IoC 컨테이너 계층구조

트리 모양 계층 구조를 만들 때 한 개 이상의 IoC 컨테이너를 만들어 두고 사용한다

### 부모 컨텍스트를 이용한 계층구조 효과

계층구조 안의 모든 컨텍스트는 각자 독립적인 설정정보를 이용해 빈 오브젝트를 만들고 관리

DI를 위해 빈을 찾을 땐 부모 어플리케이션 컨텍스트의 빈까지 모두 검색

어플리케이션 컨텍스트가 서로 공유 가능

## 1.1.4 웹 어플리케이션의 IoC 컨테이너 구성

**프론트 컨트롤러 패턴**

몇 개의 서블릿이 중앙집중식으로 모든 요청을 다 받아서 처리하는 방식

따라서 스프링 웹 어플리케이션에서 사용되는 서블릿의 숫자는 하나이거나 많아야 두셋 정도

**웹 어플리케이션 안에서 동작하는 IoC 컨테이너**

- 스프링 어플리케이션의 요청을 처리하는 서블릿 안
- 웹 어플리케이션 레벨

스프링 웹 어플리케이션에선 일반적으로 두 가지 방식을 모두 사용하므로 두 개의 컨테이너가 만들어 짐

### 웹 어플리케이션의 컨텍스트 계층구조

**루트 웹 어플리케이션 컨텍스트**

웹 어플리케이션 레벨에 등록되는 컨테이너

서블릿 레벨에 등록되는 컨테이너들의 무보 컨테이너

프론트 컨트롤러 역할을 하는 서블릿에는 독립적으로 어플리케이션 컨텍스트가 만들어짐

각 서블릿이 공유하는 빈들을 루트 어플리케이션 컨텍스트에 등록

웹 기술을 추가하고 싶은 경우가 아니면 일반적으론 프론트 컨트롤러는 하나만 사용

스프링의 웹 기술 외의 JSP, AJAX와 같은 기술을 사용한다면 계층 형태로 컨텍스트를 구분해두는 것이 바람직

서블릿 컨텍스트 빈은 루트 어플리케이션 컨텍스트 빈을 참조할 수 있지만 반대는 안됨

루트 컨텍스트에 정의된 빈은 이름이 같은 서블릿 컨텍스트의 빈이 존재하면 무시될 수 있음

AOP 설정은 다른 컨텍스트의 빈에는 영향을 미치지 않음

### 웹 어플리케이션 컨텍스트 구성 방법

- 서블릿 컨텍스트와 루트 어플리케이션 컨텍스트 계층 구조
    - 가장 많이 사용되는 기본적인 구성 방법
    - 스프링 웹 외에도 기타 웹 프레임 워크나 HTTP 요청을 통해 동작하는 각종 서비스를 함께 사용할 수 있다
- 루트 어플리케이션 컨텍스트 단일구조
    - 스프링 웹 기술을 사용하지 않고 서드파티 웹 프레임워크나 서비스 엔진만을 사용해서 프레젠테이션 계층을 만든다면 서블릿을 사용하지 않음
- 서블릿 컨텍스트 단일구조
    - 스프링 외의 프레임워크나 서비스 엔진에서 스프링의 빈을 이용하지 않는다면 루트 어플리케이션 컨텍스트 생략

### 루트 어플리케이션 컨텍스트 등록

서블릿의 이벤트 리스너를 이용해 루트 웹 어플리케이션 컨텍스트 등록

WEB-INF 폴더 내에 있는 applicationContext.xml 파일을 디폴트 설정파일로 사용

### 서블릿 어플리케이션 컨텍스트 등록

스프링의 웹 기능을 지원하는 프론트 컨트롤러 서블릿은 DispatcherServlet

# 1.2 IoC/DI를 위한 빈 설정 메타정보 작성

## 1.2.2 빈 등록 방법

### XML: <bean> 태그

가장 단순하고 많이 쓰이는 방법

내부 빈을 설정할 수도 있음

### XML: 네임스페이스와 전용 태그

어플리케이션 컨텍스트가 필요로 하는 정보도 오브젝트 형태로 만들어 주입시킨다

태그와 애트리뷰트를 일일이 기억하지 않아도 됨

전용 태그 하나로 동시에 여러 개의 빈을 만들 수 있음

ex) aop, jdbc

### 자동인식을 이용한 빈 등록: 스테레오타입 어노테이션과 빈 스캐너

**빈 스캐닝**

특정 어노테이션이 붙은 클래스를 자동으로 찾아서 빈으로 등록

**빈 스캐너**

스캐닝 작업을 담당하는 오브젝트

**스테레오타입 어노테이션**

디폴트 필터에 적용되는 어노테이션

### 자바 코드에 의한 빈 등록: @Configuration 클래스의 @Bean 메소드

코드를 이용해서 오브젝트를 생성하고 DI를 진행

@Configuration과 @Bean이 붙으면 스프링 컨테이너가 인식할 수 있는 빈 메타정보 겸 빈 오브젝트 팩토리가 됨

- 컴파일러나 IDE를 통한 타입 검증이 가능하다
- 자동완성과 같은 IDE 지원 기능을 최대한 이용할 수 있다
- 이해하기 쉽다
- 복잡한 빈 설정이나 초기화 작업을 손쉽게 적용할 수 있다
