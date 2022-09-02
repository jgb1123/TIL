# Spring REST Docs
## REST API
* REST Docs에 대한 정리 전에 간단히 REST API에 대해 다시 정리하려고 한다.
* REST에 대한 설명은 [Network 공부](https://github.com/jgb1123/TIL/blob/main/Network/Network.md) 끝부분에 정리되어 있다.
### REST API?
* REST API는 SPA(Single Page Application) 방식으로, 프론트엔드에서 백엔드의 데이터를 가져올 때 가장 많이 사용되는 리소스 처리 방석이다.
* REST API는 `URI로 리소스를 요청`하여 특정 형태로 `표현(Representation)`하며, `HTTP Method`를 적극적으로 활용하여 `행위(Verb)`를 나타낸다.
* 예시로, `GET/members`의 경우 모든 member의 정보를 응답으로 달라는 뜻이 된다.
* REST API의 요청으로 나오는 응답은 대체로 JSON으로 표현되므로, 범용적이며 읽기 쉽다.
* REST API는 무상태 환경에서 동작하는 것을 전제로 한다. (JWT, OAuth2와 같은 토큰 인증이 사용되며, 상태 유지를 위한 세션은 사용하지 않음)
### RESTful API
* REST API를 설계할 때 강제되는 표준은 없지만, 보다 RESTful하게 설계하는 방법은 있다.
* REST API는 기본적으로 URI로 리소스를 표현해야 한다.
* 또한 리소스에 대한 행위는 HTTP Method를 사용한다.
### URI 규칙
1. URI의 마지막이 `/` 로 끝나서는 안된다.
2. 리소스간 계층적 관계를 나타내기 위해 구분자 `/`를 사용한다.
3. URI의 가독성을 높이기 위해, `_`대신 `-` 를 사용한다.
4. 동사 보다는 명사, 대문자 보다는 소문자를 사용한다.
5. 파일 확장자를 URI에 포함시키지 않는다.
6. URI의 단어는 복수형으로 사용한다. (단순 유지 규칙)
7. URI에 행위를 포함하지 않는다. (delete 등)

## Spring REST Docs
* 개발을 하다보면 API 문서를 제공해야 할 일이 많은데, 이 API 문서를 위키나 문서에 직접 정리하게 되면 코드와의 동기화를 맞추기가 힘들다.
* Spring REST Docs란 테스트코드를 기반으로 자동으로 API문서를 작성할수 있게 도와주는 프레임워크로, Test가 통과되어야 문서가 작성이 된다.
* API Spec이 변경되거나 추가/삭제 되는 부분에 대해 항상 테스트 코드를 수정하게 되고, 이 코드 기반으로 API 문서가 업데이트 되기 때문에 코드와 API문서간의 동기화 문제가 해결된다.

### Spring Rest Docs vs Swagger
* Java 기반의 애플리케이션에서는 Swagger라는 API 문서 자동화 오픈소스를 많이 사용했었다.
* Swagger의 단점은 API문서를 만들기 위해 많은 애너테이션들이 코드에 추가되어야 하므로 코드가 굉장히 지저분해지며, 코드와 동기화가 안될 수 있다.
* Swagger의 장점은 Swagger는 API를 테스트 해 볼 수 있는 화면을 제공하고, Spring Rest Docs에 비해 적용하기 쉽다.
* Spring REST Docs는 더 깔끔한 문서를 만들 수 있으므로, API 문서 제공용으로는 Spinrg REST Docs가 더 적합할 수 있다.

사용법 ToDo

___
참고

https://pronist.dev/146

https://dzone.com/articles/7-rules-for-rest-api-uri-design-1

https://techblog.woowahan.com/2597/