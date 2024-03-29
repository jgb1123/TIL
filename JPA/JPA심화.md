# JPA 심화
## API 설계 기본 원칙
### 입력을 엔티티가 아닌 DTO로 받기
* 입력값을 엔티티로 그대로 받으면, 다양한 문제가 생긴다.
  * 엔티티에 `@NotEmpty`와 같은 API검증 요구사항이 들어가서 유지보수가 힘들어진다.
  * 엔티티는 여러 API에서 참조되는데, 한 엔티티에 여러 API를 위한 모든 요구사항을 담기 어렵다.
  * 엔티티가 변경되면 API 스펙이 변한다.
* 따라서 API 요청 스펙에 맞는 별도의 DTO를 만들어 사용자입력을 바인딩하여 받는 것이 좋다.
### 응답을 엔티티가 아닌 DTO로 반환하기
* 엔티티를 직접 응답값으로 사용하면 다음과 같은 문제가 생긴다.
  * 엔티티의 모든 값이 노출되기 때문에, API에서는 스펙에 맞는 값만 응답하는 것이 좋다.
  * 응답 스펙을 맞추려면 `@JsonIgnore`와 같은 View로직이 엔티티에 들어와야 한다.
  * 엔티티는 여러 API에서 참조되는데, 한 엔티티에서 여러 API를 위한 View로직을 담기 어렵다.
  * 엔티티가 변경되면 API 스펙이 변한다.
  * 컬렉션의 경우 직접 반환하면 API 스펙을 변경하기 어려워지므로, 별도의 클래스로 감싸서 응답하는 것이 좋다.
### 결론
* DTO를 사용하여 엔티티를 외부에 노출하지 않는 것이 좋다.
* 사용자 입력을 받을때와 응답값을 반환할 때 모두 엔티티 대신 별도의 DTO를 사용하는 것이 좋다.

## X대일 연관관계 성능 최적화
* X대일 연관관계는 즉시 로딩으로 설정하면 항상 테이블에서 조인이 일어나서 성능 튜닝이 어려워지므로 지연 로딩으로 설정하는 것이 좋다.
* 기본값이 즉시 로딩이기 때문에, 직접 지연 로딩으로 수정해줘야 한다.

### 페치조인을 통한 N+1 문제 해결
* X대일 연관관계에서는 지연로딩으로 설정 하면 엔티티는 초기화되지 않은 프록시로 조회된다.
* 하지만 DTO로 변환하는 과정에서 프록시를 초기화하기 위해 추가 쿼리가 나가게 된다. ([이전 N+1 문제 참고](https://github.com/jgb1123/TIL/blob/main/Spring/JPQL.md))
* 페치 조인을 통해 해당 N+1 문제를 해결할 수 있다. 
  * 페치 조인은 엔티티에 설정한 글로벌 로딩 전략을 무시하고 연관된 엔티티를 즉시 로딩한다.
  * 즉시 로딩하기 때문에 프록시가 아닌 실제 엔티티를 조회한다.
  * 프록시가 아니기 때문에 DTO 변환과정에서 프록시를 초기화하면서 발생하는 N+1 문제 또한 발생하지 않는다.
#### 페치조인의 한계
##### paging
* 페치 조인을 통해 N+1을 개선할 순 있지만, 막상 Page를 반환하는 쿼리를 작성해보면 에러가 발생한다.
* A와 B가 일대다 관계이고, A의 데이터가 2개, B의 데이터가 10개면 A를 기준으로 B데이터를 가져오면 총 20개의 row가 생긴다.
* 이 때 A를 기준으로 페이징을 하고 싶은데, JPA입장에서는 어떤 기준을 가지고 Paging을 해야하는지 알 수 없다.
* 따라서 페이징이 안된다.
##### 페치조인 paging 해결 방법
* X대일 관계는 그냥 페치조인을 사용하면 된다.
* X대다 관계에서는 지연로딩과 batch size로 해결할 수 있다.
  * 지연로딩만 사용하면 n+1문제가 발생해 쿼리를 여러번 수행하지만, 이를 방지하기 위해 batch size를 사용하면 된다.
  * 전역적으로 적용하려면 properties 파일에 `spring.jpa.properties.hibernate.default_batch_fetch_size=100`를 추가하면 된다.
  * Field나 Class에 적용하려면 `@BatchSize(size = 100)` 애너테이션을 사용하면 된다.
  * 해당 설정을 통해 size를 100으로 설정해놓으면, 하위 엔티티를 가져올 때 한번에 상위 엔티티 ID를 지정한 숫자만큼 in Query로 가져온다.
* 추가로 X대다 관계에서 `@Fetch(FetchMode.SUBSELECT)`를 통해서도 해결할 수 있다.
  * `@BatchSize`와 비슷하지만, `@BatchSize`의 경우 사이즈 갯수 제한을 임의로 두어서 사용자가 최적화된 사이즈를 적용하게 도와주고, 이 애너테이션은 전부 다 가져오도록 해준다.
  * `@BatchSize`는 size가 100이면 유저 만명 중 100명의 userId에 대해서만 검색을 하지만, 이 애너테이션은 만명 모든 유저를 select를 하게 된다.
* querydsl을 사용하면 이와같은 고민을 할 필요가 없다.
  * 동적쿼리나 페이징 모두 처리가 가능해진다.

### DTO 직접 조회
* 대부분의 X대일 조회 성능은 페치 조인을 통해 최적화한다.
* DTO 직접 조회 방법은 특정한 화면에 딱 맞는 API를 제공해야 할 때 사용하는 특수한 방법이다.
* 데이터베이스에서 조회한 엔티티를 즉시 DTO로 매핑하면 된다.
#### 구현 방법
* 조회한 데이터를 DTO로 즉시 매핑하기 위해서는 **DTO 클래스에 데이터의 타입과 순서가 일치하는 생성자**를 만들어야 한다.
* 또한 클래스의 패키지명까지 적어줘야 하기 때문에 조금 번거로울 수 있다.
* 이 경우 페치조인이 아닌 일반 조인을 사용할 수 있고, 페치 조인의 경우 연관된 엔티티의 모든 데이터를 조회하기 때문에 네트워크 전송량이 상대적으로 많아진다.
* DTO 직접 조회 방법을 이용하면 즉시 DTO로 매핑하기 때문에 필요한 데이터만 선택적으로 조회할 수 있다.
* 현대에는 네트워크 대역폭이 넉넉하기 때문에 성능 향상은 생각보다 미비하긴 하다.
#### 장단점
1. 장점
   * 딱 필요한 데이터만 조회하기 때문에 성능이 향상된다.(성능 향상이 미비하긴 함)
2. 단점
   * DTO는 API스펙이기 때문에 데이터를 직접 DTO로 매핑하면 쿼리의 재사용성이 떨어진다.
   * API 스펙이 데이터 접근 계층에 들어가게 된다. (데이터 접근 계층이 프레젠테이션 계층에 의존하는 상황이 발생)

