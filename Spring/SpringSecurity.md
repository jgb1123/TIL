# Spring Security
## Spring Security?
* Spring Framework 기반 애플리케이션의 인증(Authentication)과 인가(Authorization) 기능을 가진 프레임워크다.
* 모든 자바 애플리케이션에 적용 가능하지만 웹 애플리케이션에서 많이 쓰이고 있다.
* 스프링 인터셉터, 필터 기반의 보안 기능을 구현하는 것보다, 스프링 시큐리티를 통해 구현하는 것을 권장하고 있다.
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

## Spring Security를 사용해야 하는 이유
* 애플리케이션의 보안 절차를 일일이 구축하고 관리하는 것은 굉장히 힘든 일이다.
* Spring Security는 이미 잘 구성된 체계적인 보안 흐름과 그에 포함된 다양한 모듈을 제공해준다.
* 따라서 개발자가 직접 제로베이스에서 보안 로직을 작성하지 않아도 체계화된 보안 프로세스를 적용할 수 있다.
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