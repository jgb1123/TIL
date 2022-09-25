# Spring MVC 심화
## 예외처리
### MVC 예외 처리
* 서블릿이 제공하는 기능만으로 MVC 예외처리를 하기는 상당히 복잡하다.
  * `WebServerFactoryCustomizer<ConfigurableWebServerFactory>` 를 구현해서 예외, 오류 종류에 따라 `ErrorPage`에 경로를 추가하고, 예외 처리용 컨트롤러를 만들어서 경로에 매핑된 View를 반환해야 한다.
* 하지만 스프링 부트에서는 발생한 예외에 대해 `/error`라는 기본 경로로 `ErrorPage`를 자동으로 등록해준다.
* 서블릿 밖으로 예외가 나거거나, 에러가 호출된 경우 모든 오류에 대해 `/error` 를 호출하게 되며, `/error` 경로를 처리하는 `BasicErrorController`도 자동으로 등록한다.
* 스프링 부트가 예외 처리 경로와, 경로를 처리하는 컨트롤러를 자동으로 등록해주기 때문에 개발자는 오류 페이지만 등록하면 된다.
  * 오류페이지는 정적 HTML로 써도 되고, 뷰 템플릿을 써서 동적으로 표현해도 된다.
  * 알맞은 경로에 오류페이지 파일만 만들어두면 스프링이 알맞은 파일을 선택해서 사용한다.

### API 예외 처리
* HTML 페이지는 각 오류에 맞는 오류페이지를 만들어 오류화면을 보여주면 된다.
* 하지만 API는 각 오류 상황에 맞는 오류 응답 스펙을 정하고 JSON으로 데이터를 보내줘야 한다.
  * API마다 각각의 컨트롤러나 예외마다 서로 다른 응답 결과를 출력해야 할 수도 있다.(`BasicErrorController`를 확장하면 JSON 메시지도 변경할 수 있지만, `BasicErrorController`만으론 부족함)
  * 매우 세밀하며 더 복잡하다.
* 정리하자면, HTML 오류 페이지와 API 오류 메세지는 다른 수준의 개념이다.

### @ExceptionResolver, @ControllerAdvice
* 스프링부트에서는 API 예외처리를 보다 쉽게 할 수 있는 방법을 제공한다.
* 스프링부트가 기본으로 제공하는 `ExceptionResolver`를 사용하는 것이다.
* `ExceptionResolver`들은 `HandlerExceptionResolverComposite`에 아래와 같은 순서로 등록된다.
  1. ExceptionHandlerExceptionResolver
  2. ResponseStatusExceptionResolver
  3. DefaultHandlerExceptionResolver

* 가장 많이 사용하는 것은 `ExceptionHandlerExceptionResolver`이다.
#### ExceptionHandlerExceptionResolver
* `@ExceptionHandler` 애너테이션을 사용하면 API 예외처리가 매우 간단해진다.
* `@RequestMapping` 컨트롤러를 작성하는 것과 같이 명확하게 예외 처리를 할 수 있다.
* 이 `@ExceptionHandler`를 처리하는 것이 바로 `ExceptionHandlerExceptionResolver`이다.
#### @ExceptionHandler
* `@ExceptionHandler` 애너테이션을 통해 해당 컨트롤러에서 처리하고 싶은 예외를 지정하면 된다.
* `@ExceptionHandler({AException.class, BException.class})`처럼 다양한 예외를 한번에 처리할 수도 있다.
  * 처리하고 싶은 예외를 생략하면 메서드 파라미터 예외를 처리한다.

```java
@ResponseStatus(HttpStatus.BAD_REQUEST)
@ExceptionHandler(IllegalArgumentException.class)
public ErrorResult illegalExHandler(IllegalArgumentException e) {
    log.error("[exceptionHandler] ex", e);
    return new ErrorResult("BAD", e.getMessage());
}
```

* 오류 코드를 좀 더 동적으로 설정하고싶으면 응답으로 ResponseEntity를 사용하면 된다.
  * `@ExceptionHandler`는 스프링의 컨트롤러와 매우 유사하며, 마치 스프링의 컨트롤러 처럼 다양한 파라미터와 응답을 지정할 수 있다.
* 하지만 `@ExceptionHandler`는 이러한 예외 처리 코드가 컨트롤러에 섞여있어야 된다는 단점이 있다.
* 예외 처리 코드를 분리하기 위해 사용되는 것이 `@ControllerAdvice`와 `@RestControllerAdvice`이다.

#### @ControllerAdvice, @RestControllerAdvice
* 예외 처리 코드를 분리하기 위해 사용되는 컨트롤러이다.
* 둘의 차이는 `@Controller`와 `@RestController`처럼 `@ResponseBody`의 유무 차이이다.
* 사용 방법은 각 컨트롤러에서 `@ExceptionHandler` 애너테이션이 붙은 메서드들을 해당 애너테이션(`@ControllerAdvice`, `@RestControllerAdvice`)을 붙인 클래스로 옮기기만 하면 된다.
* 해당 애너테이션을 사용해 대상으로 지정한 컨트롤러에 `@ExceptionHandler`, `@InitBinder`기능을 부여해주며, **대상을 지정하지 않으면 모든 컨트롤러에 적용**된다. 
  ```java
  @ControllerAdvice(annotations = RestController.class)
  public class ExampleControllerAdvice1 {}
  ```
  ```java
  @ControllerAdvice(assignableTypes = {ControllerInterface.class,AbstractController.class})
  public class ExampleControllerAdvice2 {}
  ```
### 예외처리 정리
* 예외처리를 하는 방법은 다양하지만, 가장 편리하고 명확한 방법은 있다.
  * MVC의 경우 스프링 부트가 제공하는 기본설정을 사용하면 된다.
  * API의 경우 `@ExceptionHandler`와 `@ControllerAdvice`를 사용하면 된다.
* 하지만 단순히 기능만 사용하면 어떤 과정을 통해 처리되는지 이해할 수 없기 때문에, 그 과정을 이해하고 있는 것이 중요하다.

___
참고

[인프런 김영한님의 Spring MVC 2편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2/dashboard)