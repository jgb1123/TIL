# Spring Security
## Spring Security?
* Spring Framework 기반 애플리케이션의 인증(Authentication)과 인가(Authorization) 기능을 가진 프레임워크다.
* 모든 자바 애플리케이션에 적용 가능하지만 웹 애플리케이션에서 많이 쓰이고 있다.
* 스프링 시큐리티는 인증과 인가에 대한 부분을 Servlet Filter기반으로 처리하고 있다.(SpringMVC와 같은 특정 웹 기술과의 의존성은 없음)
  > Filter vs Interceptor
  > * Filter는 DispatcherServlet 전에 적용되므로 가장 먼저 URL요청을 받는다
  > * Interceptor는 DispatcherServlet과 Controller 사이에 위치하기 때문에 Filter와의 적용 시기의 차이가 있다.
* 애플리케이션의 보안 절차를 일일이 구축하고 관리하는 것은 굉장히 힘든 일이지만, Spring Security는 이미 잘 구성된 체계적인 보안 흐름과 그에 포함된 다양한 모듈을 제공해준다.
  * 개발자가 직접 제로베이스에서 보안 로직을 작성하지 않아도 체계화된 보안 프로세스를 적용할 수 있다.
* 스프링의 대부분 프로젝트들처럼 확장성을 고려한 프레임워크 이므로, 다양한 기능들을 손쉽게 추가하고 변경할 수 있다.
* Java8 이상에서만 사용할 수 있다.
> 용어 정리
> 
> 접근 주체(Principal)
> * 보호된 리소스에 접근하는 대상이다.
> 
> 인증(Authentication)
> * 특정 리소스에 접근하려고 하는 사용자가 누구인지 확인할 때 사용한다.
> * 주체의 신원을 증명하는 과정이다.
>   * 주체가 인증을 위해 신원증명정보를 제시한다.
>   * 주체가 유저일 경우 신원증명정보는 패스워드다.
> 
> 인가(Authorization)
> * 인증을 마친 유저에게 권한을 부여하여 애플리케이션의 특정 리소스에 접근할 수 있게 허가하는 과정이다.
> * 인가는 반드시 인증 과정 이후에 수행되어야 하며, 권한은 롤 형태로 부여하는게 일반적이다.
> 
> 권한
> * 인증된 주체가 애플리케이션의 동작을 수행할 수 있도록 허락 되었는지 확인한다.
> * 권한 승인이 필요한 부분으로 접근하려면 인증 과정을 통해 주체가 증명되어야 한다.
> * 권한 부여 영역
>   * 웹 요청 권한
>   * 메서드 호출 및 도메인 인스턴스에 대한 접근 관한


## Spring Security의 기능
* 모든 요청에 대해서 인증을 요구한다
* username/password를 가진 사용자가 양식 기반 인증을 제공해준다.
* BCrypt로 암호 저장소를 보호해준다.
* 사용자의 로그아웃 기능을 제공해준다.
* CSRF(Cross-Site Request Forgery)공격을 방지한다.
* Session Fixation(세션 고정 공격)을 보호한다.
* 보안 헤더 통합
  * 보안 요청을 위한 Http Strict Transport Security
  * X-Content-Type-Options 통합
  * 캐시제어
  * X-XSS-Protection 통합
  * 클릭재킹을 방지하는 X-Frame-Options 통합
* Servlet API 메서드와의 통합

## Filter
* 스프링 시큐리티는 Servlet Filter기반으로 인증과 인가에 대한 부분을 처리하고 있다.
* 클라이언트가 서버로 요청을 하게 되면 먼저 Servlet Filter를 거치게 된다.
* Filter를 모두 거치고 난 후 `DispatcherServlet`과 같은 Servlet에서 요청이 처리된다.

### FilterChain
* `FilterChain`은 의미 그대로 여러개의 Filter들이 사슬처럼 연결되어 연쇄적으로 동작한다.
* 하나의 서블릿은 단일요청을 처리하지만 필터는 체인을 형성하여 실제 요청을 순서대로 수행한다.
* Filter의 순서는 2가지 방법으로 지정 가능한데, `@Order`애너테이션이나 `Ordered`를 구현하는 방법과, `FilterRegistrationBean`을 이용해 순서를 설정하여 필터를 등록하는 방법이 있다.


### DelegatingFilterProxy
* 우선 사용자가 처음 요청을 하면 서블릿 필터를 거치게 된다.
* 스프링 시큐리티에서 사용하는 필터들은 스프링 빈으로 등록이 되어있고, 이 빈들은 서블릿 필터에서는 사용이 불가능 하다. (스프링 컨테이너, 스프링 컨테이너)
* 그래서 이 스프링 시큐리티 필터들을 수행해 줄 필터가 필요한데 그 역할을 `DelegatingFilterProxy`가 한다.
* `DelegatingFilterproxy`는 서블릿 컨테이너에 등록되어 있다.
* `DelegatingFilterproxy`에서는 `ApplicationContext`에서 `FilterChainproxy`라는 빈을 찾아 보안처리를 위임한다.

### FilterChainProxy
* `springSecurityFilterChain`이름으로 생성되는 스프링 빈이다.
* `DelegatingFilterProxy`로 부터 요청을 위임 받고 실제 보안 처리를 한다.
* 스프링 시큐리티가 초기화될 때 관리할 필터들을 결정한다. (기본필터 + 설정한 추가필터)
* 필터를 순서대로 호출해서 사용자 요청을 각각의 필터에 전달한다
* 마지막 필터까지 예외가 발생하지 않으면 인증에 성공한다.

## Spring Security 사용 방법
* Seuciryty 관련 Config 클래스에 `@EnableWebSecurity`를 적용하면 웹 보안 활성화를 위한 여러 클래스들을 import해준다. (SpringSecurityFilterChain에 등록되며, 스프링 시큐리티를 사용)
* filterChain 메서드를 생성하여 빈에 등록하며, 여기서 인자로 사용되는 `HttpSecurity`클래스가 있고, 이 클래스에서 인증과 관련된 API들이 제공된다.
* HttpSecurity 클래스를 http 라는 이름의 인자로 받아와 제공되는 메서드들을 실행한다.
``` java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.csrf().disable();   // form태그로만 요청이 가능해지고 postman등의 요청이 불가능해 진다.
				http.headers().frameOptions().disable(); // h2연결을 위해 필요한 세팅

        http.authorizeRequests()
                .antMatchers("/user/**").authenticated()    // URL중 이 패턴을 포함하는 경우에 대해서는 인증된 사용자만 접근 가능
                .antMatchers("/manager/**").access("hasRole('ROLE_ADMIN') or hasRole('ROLE_MANAGER')")  // SpEL표현식에 의한 결과에 따라 접근 가능
                .antMatchers("/admin/**").access("hasRole('ROLE_ADMIN')")
                .anyRequest().permitAll()   // 어떤 요청이던지 인증이 되어야 한다.
                .and()
                .formLogin()  // formLogin방식을 사용
                .loginPage("/login")   // 로그인 URL
        return http.build();
    }

}
```
## Form Login
* Form Login은 Spring Security에서 제공하는 인증 방식이다.
1. Client에서 Get방식으로 특정 URL을 요청했을 때, URL이 인증이 필요한 경우 Server는 로그인 페이지로 리다이렉트 된다.
2. Client는 로그인 페이지에서 username/password를 입력하여 Post방식으로 인증 시도한다.
3. Server에서는 username/password가 Server에서는 Session과 토큰을 생성하고 저장한다.
4. Client에서 접속하려던 URL에 접근 요청 시 세션에 저장된 인증 토큰으로 접근할 수 있게 되며, 세션에 인증토큰이 있는 동안은 해당 사용자가 인증된 사용자라 판단하여 인증을 유지하게 된다.

### Form Login 사용 방법
```java
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin()
       .loginPage(“/login.html")    // 로그인 페이지
       .defaultSuccessUrl("/home)   // 로그인 성공 후 이동될 페이지
       .failureUrl(＂/login.html?error=true“)    // 로그인 실패 후 이동될 페이지
       .usernameParameter("username")   // 아이디 파라미터명 설정
       .passwordParameter(“password”)   // 패스워드 파라미터명 설정
       .loginProcessingUrl(“/login")    // 로그인 Form URL
       .successHandler(loginSuccessHandler())   // 로그인 성공 후 핸들러
       .failureHandler(loginFailureHandler())   // 로그인 실패 후 핸들러
}
```

### Form Login 인증 필터 (UsernamePasswordAuthenticationFilter)
* `UsernamePasswordAuthenticationFilter`는 로그인 인증처리를 담당하고 인증처리에 관련된 요청을 처리하는 필터이다.
1. 사용자가 요청한 정보를 확인하여 요청정보 URL을 확인함(일치하면 다음단계, 일치하지 않으면 다음 필터로 진행(chain.doFilter))
2. `Authentication`에서 실제 인증처리를 하게 되며, 로그인 페이지에서 입력한 Username과 Password를 담은 인증 객체(Authentication)를 생성하여 `AuthenticationManager`에 넘긴다. 
3. `AuthenticationManager`는 내부적으로 `AuthenticationProvider`에게 인증 처리를 위임하게 된다.
   * 해당 Provider는 인증처리를 담당하는 클래스이다.
   * 인증에 실패할경우 `AuthenticationException` 예외를 반환하며 `UsernamePasswordAuthenticationFilter`로 돌아가서 예외처리를 수행한다.
   * 인증에 성공하면 `Authentication` 객체를 생성하여 인증 결과 유저 객체(User)와 유저 권한 정보 객체(Authorities)를 담아 `AuthenticationManager`에게 반환한다
4. 최종 `Authentication` 객체는 `SecurityContext`에 저장된다.
5. `SecurityContext`에 저장된 후에는 `SuccessHandler`를 호출하여 실행하게 된다.
6. 이후 `SecurityContextHolder.getContext().getAuthentication()`를 통해 인증 객체를 꺼내서 쓸 수 있다.