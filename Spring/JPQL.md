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

## 기본함수
* JPQL은 다양한 기본 함수들을 제공한다.
* 문자열을 다룰 때 사용하는 CONCAT, SUBSTRING, TRIM, LOWER, UPPER, LENGTH, LOCATE 함수를 제공한다.
* 또한 수학 함수인 ABS, SQRT, MOD를 제공한다.
* 그리고 JPQL은 sql에서는 불가능한 컬렉션을 표현할 수 있기 때문에 추가적으로 컬렉션을 다루는 SIZE, INDEX 함수를 제공한다.


## 경로 표현식
* 모든 객체는 서로 참조를 통해 그래프처럼 연결되어있다. (자바에서 `.`을 통해 연결된 객체로 이동할 수 있음)
* 엔티티도 객체이기 때문에 마찬가지이고, 엔티티들은 연관관계를 통해 객체 그래프를 이룬다.
* JPQL에서도 경로 표현식을 통해 객체 그래프를 탐색할 수 있다.
* 경로 표현식은 자바에서 `.`을 통해 연관된 객체로 이동하듯이 JPQL에서 객체(엔티티) 그래프를 탐색하는 문법이다. (주로 select, where절에서 사용)

### 상태 필드 (state field)
* 문자열이나 숫자처럼 단순히 값을 저장하는 필드로, 단순 값이기 때문에 더이상 탐색이 불가능하다.

### 단일 값 연관 필드
* `@OneToOne`, `@ManyToOne`을통해 연관관계를 맺은 필드로, 탐색 결과는 엔티티이다.
* 탐색결과는 엔티티이기 때문에 추가 탐색이 가능하다. (묵시적 내부 조인이 발생함)

### 컬렉션 값 연관 필드
* `@OneToMany`, `@ManyToMany` 를 통해 연관관계를 맺은 필드로, 탐색 결과는 컬렉션이다.
* 탐색결과가 컬렉션이기 때문에 추가 탐색이 불가능하다. (묵시적 내부 조인이 발생함)
    * 컬렉션을 from절에서 명시적 조인하고, 별칭을 붙이면 추가탐색이 가능 하긴 하지만 권장하지 않는 방법이다.


## 묵시적 조인, 명시적 조인
* 연관된 엔티티를 경로 탐색하면 엔티티, 컬렉션에 관계 없이 묵시적 내부 조인이 발생한다.
* 묵시적 내부조인은 JPQL에는 조인이 명시되어있지 않지만 SQL에서는 조인이 생기는 것을 의미한다.
* 연관된 엔티티는 다른 테이블에 저장되어 있기 때문에 당연히 조인을 통해 가져와야 한다.
* **묵시적 조인은 항상 내부조인이 되는 한계**가 있고, JPQL에 명시되어 있지 않은 조인이 SQL에 생기기 때문에 **쿼리 튜닝이 힘들어진다.**
* JPQL은 변환되는 SQL과 최대한 모양을 비슷하게 맞춰주는 것이 유지보수하기에 좋다.
* **묵시적 조인 대신 명시적 조인을 사용하는 것이 좋다.**
  * 명시적 조인을 사용하면 JPQL이 변환되는 SQL과 모양이 비슷해지며, 외부 조인 또한 가능하다.

```java
select m.username // 상태 필드
from Member m
  join m.team t // 단일 값 연관 필드 명시적 조인
  join m.orders o // 컬렉션 값 연관 필드 명시적 조인
where t.name = '팀A'

```

## N+1 문제
* N+1 문제는 1번의 쿼리 이후 연관된 엔티티 N개의 데이터를 찾기 위해 최대 N번의 추가쿼리가 나가는 문제이다.
* N+1 문제는 성능에 막대한 불이익을 주기 때문에 반드시 해결해야 한다.

### N+1문제가 발생하는 경우
1. 다대일 관계를 즉시 로딩으로 설정한 경우 (기본값)

    ```java
    em.createQuery("select m from Member m").getResultList();
    ```
   * JPQL은 SQL 그대로 번역되며, 연관된 엔티티를 고려하지 않는다.
   * 따라서 위 예시는 Team테이블의 데이터를 가져오지 않는다.
   * 위 예시는 Member에서 Team을 즉시로딩으로 설정했는데, 문제는 여기서 발생한다.
   * 처음 쿼리로 10명의 Member가 조회되면, 연관된 Team 프록시를 초기화하기 위해 최대 10번의 추가 쿼리가 발생한다.


2. 다대일 관계를 지연 로딩으로 설정한 경우
    ```java
    List<Member> members = em.createQuery("select m from Member m", Member.class).getResultList();

    for (Member member : members) {
    member.getTeam().getName(); // 프록시 초기화
    } 
    ```
    * 위 예시는 Member에서 Team을 지연 로딩으로 설정했기 때문에 Team엔티티는 초기화 되지 않은 프록시가 된다.
    * 바로 N번의 추가 쿼리가 나가지는 않지만, Member 엔티티들이 Team의 데이터를 요구하면 프록시를 초기화하기 위해 최대 N번의 추가 쿼리가 나가게 된다.
    * N+1 문제는 즉시 로딩, 지연 로딩 상관 없이 발생한다.

3. 일대다 관계를 지연 로딩으로 설정한 경우 (기본값)
    ```java
    Team team = em.createQuery("select t from Team t where t.name = 'team A'", Team.class).getSingleResult();

    for (Member member : team.getMembers()) {
    member.getUsername(); // 프록시 초기화
    }
    ```
    * N+1 문제는 컬렉션에서도 발생한다.
    * 일대다 연관관계는 기본값이 지연로딩이기 때문에 만약 Team에 연관된 Member가 10명이라면 프록시 초기화를 위해 10번의 추가 쿼리가 나간다.
* N+1 문제를 해결하는 방법은 추후 공부 예정이다.

## 페치 조인
* 페치 조인을 사용하면 연관된 엔티티나 컬렉션을 SQL 한 번으로 조회한다.
* 연관된 엔티티까지 프록시가 아닌 실제 엔티티로 조회하기 때문에 N+1 문제가 발생하지 않는다.
* 페치 조인은 SQL의 조인 종류가 아니며, 성능 최적화를 위해 사용하는 JPQL의 기능이다.

### 일반 조인
* JPQL
```java
select m
from Member m
join m.team
```
* SQL 번역
```sql
SELECT m.*
FROM member m
INNER JOIN team t ON m.team_id=t.id
```

* 위와 같이 Member에서 일대다 연관관계를 가지는 Team을 일반 조인 후 번역된 SQL을 보면 연관된 team 테이블의 데이터는 조회하지 않는다.

### 엔티티 페치 조인
```java
select m
from Member m
join fetch m.team
```
* SQL 번역
```sql
SELECT m.*, t.*
FROM member m
INNER JOIN team t ON m.team_id=t.id
```
* 위와 같이 Member에서 다대일 연관관계를 가지는 Team을 페치 조인 후 번역된 SQL을 보면 연관된 team 테이블의 데이터도 함께 조회한다.
* join 키워드 다음에 fetch 키워드를 넣으면 페치 조인이 된다.
### 컬렉션 페치 조인
```java
select distinct t
from Team t
join fetch t.members
where t.name = 'teamA'
```
* SQL 번역
```sql
SELECT DISTINCT t.*, m*
FROM team t
INNER JOIN member m ON t.id = m.team_id
WHERE t.name = 'teamA'
```
* 위와 같이 Team에서 일대다 연관관계를 가지는 Member를 페치 조인 후 번역된 SQL을 보면 Member 테이블의 데이터도 함께 조회한다.
* 일대다 관계를 조인하면 데이터가 굉장히 많아지므로, `DISTINCT`를 통해 중복을 제거하는 것이 좋다.
  * SQL의 `DISTINCT`는 조회된 데이터가 완전히 일치하는 경우 중복을 제거하지만, JPQL의 `DISTINCT`는 여기에 추가로 엔티티 중복도 제거해준다.

### 페치 조인의 한계
* 페치 조인으로 모든 문제를 해결할 순 없고, 한계가 있다.
* 페치 조인 대상에 별칭을 줄 때 주의해야 한다.
  * [관련 좋은 질문](https://www.inflearn.com/questions/15876)
* 둘 이상의 컬렉션을 페치 조인 할 수 없다.
  * 컬렉션을 조인하면 데이터가 굉장히 많아지기 때문에 컬렉션 패치 조인은 최대 한 개 까지 가능하다.
* 컬렉션을 페치 조인하면 페이징 API(`setFirstResult`, `setMaxResults`)를 적용할 수 없다.
  * 컬렉션을 조인하면 데이터가 굉장히 많아지기 때문에 페이징을 해도 의도한 결과가 나오지 않는다.
  * 하이버네이트 구현체는 컬렉션을 페치 조인 한 뒤 페이징하면 경고 로그를 남기고 데이터베이스가 아닌 메모리에서 페이징한다.
  * 성능에 매우 큰 손실이 생길 수 있으므로 **절대 사용하면 안된다.**

## 벌크 연산
* 벌크 연산은 한 번의 쿼리로 여러 엔티티를 변경하는 것이다.
* 예를 들어 회원 100명의 나이를 모두 증가시켜야 하는 경우 JPA의 변경감지를 통해 처리하면 총 100번의 UPDATE 쿼리가 필요하다.
* 벌크 연산을 이용하면 한 번의 쿼리로 해결할 수 있다.

```java
String qlString = "update Member m " +
                  "set m.age = m.age + 1";

int resultCount = em.createQuery(qlString).executeUpdate();
```
### 벌크 연산의 특징
* `executeUpdate()`는 영향받은 엔티티 수를 반환한다.
* JPA 표준에서 UPDATE, DELETE를 지원한다.
* 하이버네이트에서 INSERT를 지원한다.
* 영속성 컨텍스트를 무시하고 데이터베이스에 직접 쿼리하기 때문에 벌크 연산을 가장 먼저 실행한 뒤 영속성 컨텍스트를 초기화 해야한다.



___
참고

[인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍](https://www.inflearn.com/course/ORM-JPA-Basic/)