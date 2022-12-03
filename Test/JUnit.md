# JUnit
## JUnit?
* 자바 프로그래밍 언어용 단위 테스트 프레임워크이다.
* 애너테이션 기반 테스트를 지원한다.
* 단정문(Assert)를 통해 테스트 케이스의 기대값에 대해 수행 결과를 확인할 수 있다.
* JUnit5는 런타임 시 Java8 이상이 필요하며, JUnit을 사용하려면 Gradle 4.7 이상이어야 한다.
* JUnit의 경우 Spring boot initializer에서 Spring-Web을 dependencies를 하게 되면 자동적으로 추가가 된다.
  * SpringBoot 2.2.0이전에는 JUnit4가 기본으로 설정되어있었고, 그 이후 버전부터는 JUnit5가 기본으로 설정되어 있다.
* JUnit5는 크게 Jupiter, Platform, Vintage 모듈로 구성되어 있다.

### Jupiter
* Test Engine의 실제 구현체는 별도의 모듈인데, 그 모듈 중 하나가 jupiter-engine이다.
* 이 모듈은 jupiter-api를 사용하여 작성한 테스트 코드를 발견하고 실행한다.
* Jupiter API는 JUnit5에 새롭게 추가된 테스트 코드용 API로, 개발자는 Jupiter API를 사용해 테스트코드를 작성할 수 있다.

### Platform
* test를 발견하고 테스트 계획을 생성하는 TestEngin API를 가지고 있다.
* TestEngine을 통해서 테스트를 발견하고 실행하고 결과를 보고한다.
* 각종 IDE 연동을 보조하는 역할을 한다.

### Vintage
* Test Engin의 구현체로 JUnit 3, 4에 사용된다.
* JUnit 3, 4 버전으로 작성된 테스트 코드를 실행할 때는 vintage-engine 모듈을 사용한다.

## JUnit 애너테이션
### @Test
* 테스트용 메서드임을 선언하는 애너테이션이다.

### @BeforeEach
* 각 테스트 메서드가 시작되기 전에 실행되어야 하는 메서드에 사용한다

### @AfterEach
* 각 테스트 메서드가 시작된 후 실행되어야 하는 메서드에 사용한다.

### @BeforeAll
* 현재 테스트 클래스 시작 전에 실행되어야 하는 메서드에 사용한다. (Static 처리 필요)

### @AfterAll
* 현재 테스트 클래스 종료 후에 실행되어야 하는 메서드에 사용한다. (Static 처리 필요)

### @SpringBootTest
* 통합 테스트 용도로 사용된다.
* @SpringBootApplication을 찾아가 하위의 모든 Bean을 스캔하여 로드한다.
* 그 다음 Test용 Application Context를 만들어 Bean을 추가하고, MockBean을 찾아 교체한다.

### @ExtendWith
* JUnit4에서 @RunWith로 사용되던 애너테이션이 ExtendWith로 변경되었다.
* @ExtendWith는 메인으로 실행될 Class를 지정할 수 있다.
* @SpringBootTest는 기본적으로 @ExtendWith가 추가되어 있다.

### @WebMvcTest(Class명.class)
* ()안에 작성된 클래스만 실제로 로드하여 테스트를 진행한다.
* 매개변수를 지정해주지 않으면 @Controller, @ResController, @RestControllerAdvice 등 컨트롤러와 연관된 Bean이 모두 로드된다.
* 스프링의 모든 Bean을 로드하는 @SpringBootTest대신 컨트롤러 관련 코드만 테스트할 경우 사용한다.

### @Autowired (about MockMvc)
* Controller의 API를 테스트하는 용도인 MockMvc 객체를 주입받는다.
  * MockMvc 클래스는 REST API 테스트를 할 수 있는 클래스이다.
* perform() 메서드를 활용하여 컨트롤러의 동작을 확인할 수 있다.
  * .andExpect(), .andDo(), andReturn() 등의 메서드를 같이 활용한다.

### @MockBean
* 테스트할 클래스에서 주입 받고 있는 객체에 대해 가짜 객체를 생성해주는 애너테이션이다.
* 해당 객체는 실제 행위를 하지 않고, given() 메서드를 활용하여 가짜 객체의 동작에 대해 정의하여 사용할 수 있다.

### @AutoConfigureMockMvc
* spring.test.mockmvc의 설정을 로드하면서 MockMvc의 의존성을 자동으로 주입한다.
* 일반적으로는 유닛테스트 시 MockMvc를 사용하게 되면 사용한다.

### @Import
* 필요한 클래스들을 Configuration으로 만들어 사용할 수 있다.
* Configuration Component 클래스도 의존성을 설정할 수 있다.
* Import된 클래스는 주입(@Autowired)으로 사용 가능하다.

## 통합 테스트
* 통합 테스트는 여러 기능을 조합하여 전체 비즈니스 로직이 제대로 동작하는지 확인하는 것을 의미한다.
* 통합 테스트의 경우 @SpringBootTest를 사용하여 진행한다.
  * @SpringBootTest는 @SpringBootApplication을 찾아가 모든 Bean을 로드하게 된다.
  * 이 방법을 대규모 프로젝트에서 사용할 경우, 테스트를 실행할 때마다 모든 빈을 스캔하고 로드하는 작업이 반복되어 테스트가 무거워진다.

## 단위 테스트
* 프로젝트에 필요한 모든 기능에 대한 테스트를 각각 진행하는 것을 의미한다.

### F.I.R.S.T 원칙
* Fast : 테스트 코드의 실행은 빠르게 진행되어야 한다.
* Independent : 독립적인 테스트가 가능해야 한다.
* Repeatable : 테스트는 매번 같은 결과를 만들어야 한다.
* Self-Validating : 테스트는 그 자체로 실행하여 결과를 확인할 수 있어야 한다.
* Timely : 단위 테스트는 비즈니스 코드가 완성되기 전에 구성하고 테스트가 가능해야 한다. (TDD의 원칙을 담고 있음)


___
참고

[어라운드 허브 스튜디오 - 테스트 코드 적용하기](https://www.youtube.com/watch?v=SFVWo0Z5Ppo)