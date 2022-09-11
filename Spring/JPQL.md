# JPQL
* JPQL(Java Persistence Query Language)은 SQL을 추상화한 객체 지향 쿼리 언어다.
* SQL을 추상화 했기 때문에, 특정 데이터베이스에 의존하지 않는다.
* 순수 JDBC, MyBatis, SpringJdbcTemplate 으로는 피할 수 없는 SQL작성 반복을 대폭 줄여준다.
* 매우 복잡한 쿼리를 작성해야 하거나 특정 데이터베이스에 의존적인 기능이 필요하다면 SQL작성이 불가피하지만, 대부분의 쿼리는 JPQL을 통해 해결할 수 있다.
* JPQL의 가장 큰 장점은 **데이터베이스를 테이블이 아닌 엔티티로 쿼리할 수 있다**는 것이다.
* 그러나 자바 코드가 아닌 문자열로 작성되기 때문에, 복잡한 동적 쿼리를 작성하기 번거롭다.
    * 복잡한 동적 쿼리는 QueryDSL이라는 오픈소스 라이브러리를 사용해 해결할 수 있는데, 이 또한 결국 JPQL로 변환된다.
    * 그리고 JPQL은 데이터베이스 방언에 따라 알맞은 SQL로 변환된다.

## JPQL 규칙
```sql
select m from Member as m where m.age > 18
```
* 엔티티와 속성은 대소문자를 구별한다.
* JPQL 키워드 (select, from, where 등)는 대소문자를 구별하지 않는다.
* 별칭(m)은 필수이며, as키워드는 생략 가능하다.

* sql에 있는 COUNT, SUM 등의 집계 함수를 JPQL에서 사용할 수 있다.
    * 집계 함수는 보통 GROUP BY, HAVING과 같은 집합 기능 및 ORDER BY 같은 정렬 기능과 함께 쓰는 경우가 많다.
    *

## TypedQuery, Query
* TypedQuery는 반환 타입이 명확할 때 사용하고, Query는 반환 타입이 명확하지 않을 때 사용한다.
* TypedQuery는 결과 조회시 제네릭 타입을 반환하기 때문에 사용하기 편리하므로, 반환 타입이 명확하다면 TypedQuery를 사용하는 것이 좋다.
  ```sql
  TypedQuery<Member> query = em.createQuery("select m from Member m", Member.class);
  ```
* 아래 예시는 엔티티 대신 필드 두개를 프로젝션 했기 때문에 반환타입을 지정할 수 없다.
  ```sql
  Query query = em.createQuery("select m.username, m.age from Member m");
  ```
## 프로젝션
* 프로젝션은 select 절에서 조회할 대상을 지정하는 것이다.
* select 절에서 sql처럼 DISTINCT 키워드를 통해 중복을 제거할 수 있다.
* JPQL과 SQL의 DISTINCT는 약간의 차이가 있다.

### 조회 가능 대상
* select절에서 조회 가능한 대상은 총 3가지이다.

1. 스칼라 타입
    * 가장 기본적인 숫자, 문자 등 기본 데이터 타입을 조회할 수 있다.
      ```java
      String qlString = "select m.username from Member m"
      List<String> result = em.createQuery(qlString, String.class).getResultList();
      ```
2. 엔티티 타입
    * 엔티티 자체를 조회할 수 있다.
      ```java
      String qlString = "select m from Member m"
      List<Member> result = em.createQuery(qlString, Member.class).getResultList();
      ```
3. 임베디드 타입
    * 값 타입 중 하나인 임베디드 타입을 조회할 수 있다.
      ```java
      String qlString = "select m.address from Member m"
      List<Address> result = em.createQuery(qlString, Address.class).getResultList();
      ```


* 아래 예시와 같이 여러 값을 함께 조회하는 경우 받을 수 있는 타입들이 있다.
  ```sql
  select m.username, m.age from Member m
  ```
    1. Query 타입으로 조회
       ```java
       String qlString = "select m.username, m.age from Member m"
       List result = em.createQuery(qlString).getResultList();
       ```
        * 아무런 타입 정보를 알 수 없기 때문에 별로 도움이 되지 않는다.
    2. Object[] 타입으로 조회
       ```java
       String qlString = "select m.username, m.age from Member m"
       List<Object[]> result = em.createQuery(qlString).getResultList();
       ```
        * raw타입 컬렉션 대신 Object[] 타입이라도 주는 것이 좋다.
    3. new 명령어로 조회
       ```java
       String qlString = "select new jpql.jpql.UserDto(m.username, m.age) from Member m"
       List<MemberDto> result = em.createQuery(qlString, MemberDto.class).getResultList();
       ```
        * 조회 결과를 DTO로 바로 받을 수 있다.
        * DTO 클래스에 컬럼의 타입과 순서가 일치하는 생성자를 만들어야 하며 ,DTO클래스의 패키지 명까지 모두 적어줘야 한다.
        * 코드가 약간 길어지지만, 여러 값을 조회하는 방법 중에서는 제일 깔끔하다.

## 결과 조회
### getResultList()
```java
String qlString = "select m from Member m";
List<Member> result = em.createQuery(qlString,Member.class).getResultList();
```

* 결과가 하나 이상일 때 사용하며, 리스트를 반환한다.
* 결과가 없으면 빈 리스트를 반환한다.

### getSingleResult()
```java
String qlString = "select m from Member m";
Member result = em.createQuery(qlString,Member.class).getSingleResult();
```
* 결과가 하나일 때 사용하며, 단일 객체를 반환한다.
* 결과가 없으면 `NoResultException`이 발생한다.
* 결과가 두개 이상이면 `NonUniqueResultException`이 발생한다.

## 파라미터 바인딩
1. 이름 기준
```java
String qlString = "select m from Member m where m.username = :username";
em.createQuery(qlString, Member.class).setParameter("username", "kim");
```
2. 위치 기준
```java
String qlString = "select m from Member m where m.username = ?1";
em.createQuery(qlString, Member.class).setParameter(1, "kim"); 
```
* 위치 기준 파라미터 바인딩은 파라미터 추가 시 순서가 바뀔 수 있다.
* 이러한 문제로 이름 기준 파라미터 바인딩을 사용하는 것이 좋다.


## 페이징 API
* JPQL은 SQL을 추상화했으며, 그 장점이 가장 잘 드러나는 부분중 하나는 페이징 API이다.
* 특정 데이터베이스들은 페이징 쿼리를 짜기가 매우 번거롭지만, JPQL은 페이징을 매우 쉽게 짤 수 있도록 도와준다.
* `setFirstResult(int startPosition)` : 조회 시작 위치(0부터 시작)
* `setMaxResults(int maxResult)` 조회할 데이터 수
``` java
String qlString = "select m from Member m order by m.age desc";

List<Member> result = em.createQuery(qlString, Member.class)
                        .setFirstResult(10)
                        .setMaxResults(10)
                        .getResultList(); 
```

## 조인
* 조인은 관계형 데이터베이스의 가장 중요한 부분이라고 할 수 있다.
* 데이터베이스의 조인은 3가지로 분류할 수 있으며, JPQL에서도 모두 지원한다.
### 내부 조인(inner join)
```java
select m from Member m [inner] join m.team t
```
* 키워드 inner는 보통 생략하며, 위 예시같은 경우 조인 결과가 있는 Member만 조회한다.
### 외부 조인(outer join)
```java
select m from Member m left [outer] join m.team t
```
* 키워드 outer는 보통 생략하며, 조인결과가 없는 Member도 함께 조회한다.

### 세타 조인
```java
select count(m) from Member m, Team t where m.username = t.name
```

## ON절
* ON절을 JPQL에서도 사용할 수 있다.
* ON절은 크게 2가지 경우에 사용된다
### 조인 대상 필터링
* 내부 조인과 외부 조인은 기본키와 외래키를 조인 조건으로 삼는다.
```java
select m, t from Member m left join m.team t on t.name = 'teamA' 
```
* ON절을 활용하여 조인 조건을 추가해서, 조인해야 할 레코드를 줄임으로써 조인의 성능을 높일 수 있다.


### 연관관계 없는 엔티티 외부 조인
* ON절은 연관관계가 없는 엔티티를 외부 조인하는데 활용할 수 있다.
```java
select m, t from Member m left join on m.username = t.name 
```
* 위와같이 팀의 이름이 같은 레코드들을 외부조인할 수 있다.


## 서브 쿼리
* JPQL을 통해 서브쿼리를 할 수 있다.
* 나이가 평균보다 많은 회원을 조외하는 경우는 아래와 같다.
  ```sql 
  select m from Member m
  where m.age > (select avg(m2.age) from Member m2)
  ```
* 주문 건이 한 건 이상인 회원을 조회하는 경우는 아래와 같다.
  ```sql
  select m from Member m
  where (select count(o) from Order o where m = o.member) > 0
  ```

* 서브쿼리 앞에 `exists`, `all`, `any(some)`, `in` 을 붙일 수 있다.
    * `exists` : 서브쿼리의 결과가 존재하면 참을 반환하며, not exists를 통해 반환값을 뒤집을 수 있다.
    * `all` : 조건을 모두 만족하면 참을 반환한다.
    * `any(some)` : 조건을 하나라도 만족하면 참을 반환한다.
    * `in` : 서브쿼리의 결과 중 하나라도 같은 것이 존재하면 참을 반환하며, not in을 통해 반환값을 뒤집을 수 있다.

* teamA 소속인 회원을 조회하는경우 아래와 같다.
  ```sql
  select m from Member m
  where exists (select t from m.team t where t.name = 'teamA')
  ```

* 어느 한 팀에라도 소속된 회원을 조회하는 경우 아래와 같다.
  ```sql 
  select m from Member m
  where m.team = any(select t from Team t)
  ```

* JPQL의 서브쿼리는 where, having, select절에서 사용할 수 있지만 **from절에서는 사용할 수 없다.**
    * from절에 서브쿼리가 필요한 경우 조인으로 풀어야 한다.
    * 대부분의 서브쿼리는 조인으로 바꿀 수 있는 경우가 많다.

## 조건식 (CASE, COALESCE, NULLIF)
### 기본 CASE식
* sql처럼 case, when, then, else, end를 조합하여 조건별로 반환값을 다르게 정할 수 있다.
```java 
select
  case
       when m.age <= 10 then '어린이 요금'
       when m.age >= 60 then '경로 요금'
       else '일반 요금'
  end
from Member m 
```

### 단순 CASE식
* 자바의 switch문처럼 단순하게 값을 직접 비교할 수 있다.
```java
select
  case t.name
       when 'teamA' then '우승팀'
       when 'teamB' then '준우승팀'
       else '일반팀'
  end 
from Team t
```
### COALESCE
* coalesce문은 값을 하나씩 조회해서 null이 아닐 때 반환하며, null 대신 기본값을 지정하고 싶을 때 사용할 수 있다.
```java
select coalesce(m.username,'이름 없는 회원') from Member m
```

### NULLIF
* nullif는 두 값이 같으면 null을 반환하며, 다르면 첫 번째 값을 반환한다.
```java
select nullif(m.username, '관리자') from Member m
```

### 기본함수
* JPQL은 다양한 기본 함수들을 제공한다.
* 문자열을 다룰 때 사용하는 CONCAT, SUBSTRING, TRIM, LOWER, UPPER, LENGTH, LOCATE 함수를 제공한다.
* 또한 수학 함수인 ABS, SQRT, MOD를 제공한다.
* 그리고 JPQL은 sql에서는 불가능한 컬렉션을 표현할 수 있기 때문에 추가적으로 컬렉션을 다루는 SIZE, INDEX 함수를 제공한다.




___
참고

[인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍](https://www.inflearn.com/course/ORM-JPA-Basic/)