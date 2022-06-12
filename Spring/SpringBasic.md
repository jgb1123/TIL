# SpringBasic

## Spring project 생성
### project 생성
* 스프링 부트는 스프링을 쉽게 사용할 수 있도록 필요한 설정들이 모두 세팅되어져 있는 것을 말한다. 
* 스프링 부트 스타터 사이트인 https://start.spring.io 에서 프로젝트를 생성한다.
* Project에는 Maven Project와 Gradle Project가 있는데, 과거 프로젝트들은 Maven으로 남아있는게 있으나, **요즘은 Gradle을 이용**하는 추세이다.
* Project Metadata에 Group에는 보통 기업 도메인명을 적고, Artifact는 빌드되어 나오는 결과물을 말한다. (프로젝트 명)
* Dependencies란에선 어떤 라이브러리를 쓸 건지 결정하면 된다.
    * 웹 프로젝트를 만들 것이므로, Spring Web 선택
    * html을 만들어주는 템플릿 엔진으로 Thymeleaf를 선택
* generate를 클릭하면 zip파일이 다운되는데, 압출을 풀고 해당 파일을 IntelliJ로 실행하면 준비 완료이다.

### .gitignore 파일
* git에는 소스코드만 올라갈 수 있도록 스프링 부트에서 자동적으로 생성해준다.

### gradle 폴더
* gradle wrapper와 같이 gradle과 관련된 파일들이 있다.
>gradle
>
> groovy를 이용한 빌드 자동화 시스템이다. 소프트웨어 개발자가 반복적으로 해야하는 코딩을 잘 짜여진 프로세스를 통해 자동으로 실행하며 믿을 수 있는 결과물로 생산해 낼 수 있는 방법이다.
> 1) 빠른 기간동안 계속해서 늘어나는 라이브러리를 추가한다.
> 2) 프로젝트를 진행하면서 라이브러리의 버전을 쉽게 동기화할 수 있다.
### src 폴더
* src에 main과 test가 있는데, main에는 JAVA와 resources가 있다.
  * JAVA에 실제 패키지들과 소스파일들이 있다
  * resources에는 xml, html, properties 등이 들어간다.
  * test에는 test코드들과 관련된 소스들이 들어가 있다. (test code가 요즘 개발 트렌드에서 매우 중요하다)
* gradle과 관련되서 gradle을 쓰는 폴더가 있다.

### build.gradle 파일
* 스프링 부트들이 나오면서 설정파일들까지 모두 제공되는데, build.gradle파일은 지금은 gradle버전을 설정하고 라이브러리들을 가져온다 정도로만 알고 있으면 된다.
* dependencies에서 설정했던 라이브러리들을 볼 수 있고, 테스트파일로는 Junit5가 기본적으로 들어간다.
* 이러한 라이브러리들을 mavenCentral을 통해 특정 사이트에서 다운받도록 되어있다.

## 기본적인 라이브러리
* Gradle은 의존관계가 있는 라이브러리들을 함께 다운로드한다.
* 아래는 아주 기본적인 라이브러리들이다.
#### 스프링 부트 라이브러리
* spring-boot-starter-web
  * spring-boot-starter-tomcat : 톰캣(웹서버)
  * spring-webmvc : 스프링 웹 MVC
* spring-boot-starter-thymeleaf : 타임리프 템플릿 엔진(View)
* spring-boot-start(공통) : 스프링 부트 + 스프링 코어 + 로깅
  * spring-boot
    * spring-core
  * spring-boot-starter-logging
    * logback, slf4j

#### 테스트 라이브러리
* spring-boot-starter-test
  * junit : 테스트 프레임워크
  * mockito : 목 라이브러리
  * assertj : 테스트 코드를 좀 더 편하게 작성하도록 도와주는 라이브러리
  * spring-test : 스프링 통합 테스트 지원