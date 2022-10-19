# JPA
## JPA?
* 자바 진영의 ORM 기술 표준 인터페이스이다.
  * 객체는 객체대로 설계를 하고, 관계형 데이터베이스는 관계형 데이터베이스대로 설계를 하면, ORM(Object-Relational Mapping) 프레임워크가 중간에서 매핑을 해준다.
* JPA가 실제로 동작하는 것이 아니고, 이 인터페이스를 구현한 구현체가 동작을 한다. (대표적으로 Hibernate)
* 대표적인 개념으로는 연관관계 매핑, 영속성 컨텍스트가 있다.
### 연관관계 매핑
* 객체 지향 프로그래밍과 관계형 데이터베이스의 차이점로 개발자가 중간 중간 SQL문을 작성 및 변환을 해야한다.
* JPA와 같은 ORM 프레임워크는 객체와 관계형 데이터베이스를 매핑해주고 SQL을 대신 작성해준다.
  * 하지만 객체와 테이블을 정확하게 매핑하는 것이 중요하다.
### 영속성 컨텍스트
* 연관관계 매핑이 객체와 테이블의 매핑 정보를 구성하는 부분이라면, 영속성 컨텍스트는 JPA 내부 동작 방식을 이해하기 위해 필요한 부분이다.
* JPA를 사용하면 객체를 자바 컬렉션에 관리하는 것 처럼 편리하게 데이터베이스에 관리할 수 있다.
  * 객체를 조회하고 변경만 해도 변경 내용이 데이터베이스에 자동으로 반영된다.

## Spring Data JPA
* spring-data 프로젝트는 다양한 데이터 관리 솔루션을 동일한 방식의 인터페이스로 사용할 수 있게 해준다.
* spring-data-jpa는 spring-data의 인터페이스로 JPA를 다룰 수 있게 해주는 프로젝트이다.

### 쿼리 메서드 기능
#### 메서드 이름으로 쿼리를 생성
* 메서드 이름으로 쿼리를 생성해주는 기능이다.
```java
public interface UserRepository extends Repository<User, Long> {
  List<User> findByEmailAddressAndLastname(String emailAddress, String lastname);
}
```
```sql
select u from User u
where u.emailAddress = ?1
  and u.lastname = ?2
```
* 자세한 사용법은 아래 공식문서 내용들을 참고하면 된다.
* [Query Creation 공식문서](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.query-creation)
* [쿼리 메서드 주제 키워드](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#appendix.query.method.subject)
* [쿼리 메서드 조건자 키워드 및 수정자](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#appendix.query.method.predicate)
#### 메서드 이름으로 NamedQuery
* 메서드 이름으로 JPA Named 쿼리를 호출하는 기능이 있다.
* 쿼리에 이름을 부여해서 사용하는 방법이다.
* 어노테이션 또는 XML에 쿼리를 정의할 수 있다.
```java
@Entity
@NamedQuery(name = "User.findByEmailAddress",
        query = "select u from User u where u.emailAddress = ?1")
public class User {
}
```
* 자세한 사용법은 아래 공식문서를 참고하면 된다.
* [JPA Named Queiries 공식문서](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods.named-queries)
#### @Query 어노테이션으로 Repository Interface에 쿼리 정의
* `@Query` 어노테이션을 사용하여 실행할 메서드에 쿼리를 직접 작성할 수 있다. (이름 없는 NamedQuery 같은 느낌)
```java
public interface UserRepository extends JpaRepository<User, Long> {
  @Query("select u from User u where u.emailAddress = ?1")
  User findByEmailAddress(String emailAddress);
} 
```
* 정적 쿼리의 경우, 메서드 이름으로 쿼리를 생성하는 방법으로 사용하다가, 좀 더 복잡한 쿼리가 필요한 경우 해당 기능을 사용하면 된다.
  * 복잡한 동적 쿼리의 경우 queryDSL을 사용하면 된다. (추후 공부 예정)

## 장점 및 단점
### 장점
* 코딩량이 줄어든다.
* 도메인 클래스를 중요하게 다룬다.
* 비즈니스 로직에 대한 이해가 쉬워진다.
* 더 많은 테스트케이스 작성이 가능하다
* 유지보수하기 좋다.
* 데이터베이스를 유연하게 변경 가능하다

### 단점(주의사항)
* DataBase 설계에 대해 제대로 이해하고 있어야 한다.
* JPA에 대한 지식이 없는상태에서 사용하면 문제가 발생하기 쉽다.
* JPA는 학습 곡선이 매우 높다.

## 영속성 컨텍스트
* 엔티티를 영구 저장하는 환경이다.
* 논리적인 개념으로 눈에 보이진 않으며, 엔티티 매니저를 통해 영속성 컨텍스트에 접근할 수 있다.

### 엔티티의 생명주기
* 비영속(new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
  * 객체를 생성만 한 상태로, 영속성 컨텍스트와 전혀 관계가 없는 상태를 의미한다.
* 영속(managed) : 영속성 컨텍스트에 관리되는 상태
  * 객체를 생성하고 저장한 생타로, 영속성 컨텍스트에 의해 관리되는 상태를 의미한다.
* 준영속(detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태
  * 영속성 컨텍스트에 저장되었다가 분리된 상태를 의미하며, 준영속 상태의 엔티티는 영속성 컨텍스트가 제공하는 기능을 사용할 수 없다.
  * `em.detach(entity)` (특정 엔티티만 준영속 상태로), `em.clear()` (영속성 컨텍스트 초기화로 모든 엔티티 준영속 상태), `em.close()` (영속성 컨텍스트 삭제)와 같은 방법들로 준영속 상태로 만들 수 있다.
* 삭제(removed) : 삭제된 상태

### 영속성 컨텍스트의 장점
* 1차 캐시
  * 객체를 저장하고 조회할 경우 해당 데이터를 DB에서 바로 찾아보는 것이 아니라, 1차 캐시에서 조회하게 된다.
  * 만약 1차캐시에 존재하지 않는 데이터라면 DB에 접근하여 해당 데이터를 1차 캐시에 저장한 뒤 그 값을 반환하게 된다.
* 동일성 보장
  * 반복 가능한 읽기(REPEATABLE READ)등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 애플리케이션에서 제공한다
* 트랜잭션을 지원하는 쓰기 지연
  * 한번에 한번씩 DB에 SQL문이 전달되지 않고, 명령어들을 쓰기 지연 SQL 저장소에 저장해 놓은 뒤 커밋하는 순간 한번에 데이터베이스에 모든 SQL문을 날린다.
* 변경 감지
  * 객체를 수정하게 되면 개발자가 따로 업데이트 함수를 구현하고 호출하지 않아도 자동으로 데이터베이스에 있는 데이터가 갱신된다.
  * 예를들면, DB에서 엔티티를 조회한 후 해당 데이터를 수정하면 자동으로 데이터베이스에 있는 데이터가 갱신된다.
* 지연 로딩
  * 서비스가 커질수록 참조하는 객체가 많아지고, 객체가 가지는 데이터의 양이 많아진다.
  * DB로부터 참조하는 객체들의 데이터까지 한번에 가져오는 것은 부담이 커진다.
  * JPA는 참조하는 객체들의 데이터를 가져오는 시점을 정할 수 있는데(Fetch Type), LAZY 타입을 통해 이러한 성능이슈를 줄일 수 있다.

### 플러시
* 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영하는 것을 의미한다.
* 플러시가 발생하는 과정
  1. 변경 감지
  2. 수정된 엔티티 쓰기 지연 SQL 저장소에 등록
  3. 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송(등록, 수정, 삭제 쿼리)

* 영속성 컨텍스트를 플러시 하는 방법
    1. `em.flush()` : 직접 호출
    2. 트랜잭션 커밋 : 플러시 자동 호출
    3. JPQL 쿼리 실행 : 플러시 자동 호출


## 엔티티 매핑
### 객체와 테이블 매핑

#### @Entity
* @Entity가 붙은 클래스는 JPA가 관리한다.
* JPA를 사용해서 테이블과 매핑할 클래스는 `@Entity`를 꼭 붙여줘야 한다.
* 주의 사항
  * 기본 생성자 필수적이다.(파라미터가 없는 public 또는 protected 생성자)
  * final 클래스, enum, interface, inner클래스 사용이 불가능하다
  * 저장할 필드에 final을 사용할 수 없다.

* `@Enitiy` 속성
  * name : JPA에서 사용할 엔티티 이름을 지정할 수 있다.
  * 기본값은 클래스 이름을 그대로 사용하며, 같은 클래스 이름이 없으면 가급적 기본값을 사용한다.

#### @Table
* `@Table`은 엔티티와 매핑할 테이블을 지정할 때 사용한다.
* `@Table` 속성
  * name : 매핑할 테이블 이름
  * catalog : 데이터베이스 catalog 매핑
  * schema : 데이터베이스 schema 매핑
  * uniqueConstraints(DDL) : DDL 생성 시 유니크 제약 조건 생성

### 데이터베이스 스키마 자동 생성
* JPA를 사용하면 DDL(Data Definition Language)을 애플리케이션 실행 시점에 자동으로 생성할 수 있다.
  * 개발자가 테이블을 먼저 생성하고 객체를 구현할 필요 없이, **객체만 먼저 구현해도 JPA가 알아서 테이블을 만들어 준다.** (테이블 중심 -> 객체 중심)
  * JPA는 데이터베이스 dialect(방언)을 사용하여 데이터베이스에 맞는 적절한 DDL을 생성한다. (이렇게 생성된 DDL은 개발장비에서만 사용하고, 운영서버에서는 사용하지 않는것을 권장)

#### 데이터베이스 스키마 자동생성 속성(`hibernate.hbm2ddl.auto`)
* create : 기존 테이블 삭제 후 다시 생성(DROP + CREATE)
* create-drop : create와 같지만 종료 시점에 테이블 DROP
* update : 변경분만 반영(운영 DB에는 사용하면 안됨)
* validate : 엔티티와 테이블이 정상 매핑되었는지만 확인
* none: 사용하지 않음

#### 주의사항
* **운영장비에는 절대 create, create-drop, update를 사용하면 안된다.**
* 개발 초기 단예에는 create 또는 update로 local과 개발서버에 사용한다.
* 테스트 서버는 update 또는 validate를 사용해야 한다
* 스테이징과 운영 서버는 validate 또는 none로 사용해야 한다.

#### DDL 생성 기능
* `Column(nullable = true, length = 10)` 필수고, 10자 초과하면 안됨
* `Column(unique = true` 유니크 제약조건 추가

### 필드와 컬럼 매핑
#### @Column : 컬럼 매핑
* name : 필드와 매핑할 테이블의 컬럼 이름 (기본값은 객체의 필드 이름)
* insertable, updatable : 등록, 변경 가능 여부 (기본값은 True)
* nullable : null값의 허용 여부를 설정 (false로 설정하면 DDL 생성 시 not null 제약 조건이 붙음)
* unique : 한 컬럼에 유니크 제약 조건을 걸 때 사용
  * `unique`속성 보다는 `@Table`의 `uniqueConstraints` 속성을 주로 사용한다.
* columnDefinition : 데이터베이스 컬럼 정보를 직접 줄 수 있다.
* length : 문자 길이 제약 조건 설정 (String 에만 사용하며, 기본값은 255)
* percision, scale : BigDecimal, BigInteger 타입에서 사용 (소숫점 전체 자릿수와, 소수의 자릿수)
  * double, float타입에는 적용되지 않고, 아주 큰 숫자나 정밀한 소수를 다루어야 할 때만 사용한다.

#### @Temporal : 날짜 타입 매핑

* value
  * TemporalType.DATE : 날짜, 데이터베이스 data타입과 매핑
  * TemporalType.TIME : 시간, 데이터베이스 time 타입과 매핑
  * TemporalType.TIMESTAMP : 날짜와 시간, 데이터베이스 timestamp 타입과 매핑
    * LocalDate, LocalDateTime을 사용할 경우 생략 가능하다

#### @Enumerated : enum 타입 매핑
* value(기본값은 EnumType.ORDINAL)
    * EnumType.ORDINAL : enum 순서를 데이터베이스에 저장
    * EnumType.STRING : enum 이름을 데이터베이스에 저장
        * **기본값인 ORDINAL은 사용하지 않는것이 좋다.**(enum타입에 데이터가 추가되어도 테이블에서는 이것이 반영되지 않기 때문에 문제가 발생 함)

#### @Lob : BLOB, CLOB 매핑
* 지정할 수 있는 속성이 따로 없다.
* 매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑이다.
  * CLOB : String, char[], java.sql.CLOB
  * BLOB : byte[] java.sql.BLOB
#### @Transient : 특정 필드를 컬럼에 매핑하지 않음
* 데이터베이스에 저장, 조회가 안된다.
* 주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용한다.

### 기본 키 매핑
* 기본 키 매핑 어노테이션은 `@Id`, `@GeneratedValue`가 있다.
* 직접 할당의 경우 `@Id`만 사용하면 된다.
* 자동생성의 경우 `@GeneratedValue`도 붙여주면 된다.
* `@GeneratedValue`의 `strategy`속성
  * IDENTITY : 기본 키 생성을 데이터베이스에 위임한다.
  * SEQUENCE : 데이터베이스 시퀀스 오브젝트를 사용한다.
  * TABLE : 키 생성용 테이블을 사용하고, 모든 DB에서 사용한다
  * AUTO : 데이터베이스 방언에 따라 자동으로 지정해준다.

#### IDENTITY
* IDENTITY는 기본키 생성을 데이터베이스에 위임한다.
* jpa입장에서 엔티티를 영속상태에 만들기 위해 기본키를 먼저 알야아하고, 그렇기 때문에 `em.persist()` 시점에 쓰기지연을 하지 않고 바로 SQL문을 실행하여 데이터를 등록하고 식별자를 조회한다.
    * JPA는 보통 트랜잭션 커밋 시점에 INSERT SQL문을 실행한다.
    * AUTO_INCREMENT는 데이터베이스에 INSERT SQL을 실행한 이후에 ID 값을 알 수 있다.
* 주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용한다.

#### SEQUENCE
* SEQUENCE도 엔티티를 영속성 컨텍스트에 등록하기 위해 기본키를 알야아 하며, 그렇기 때문에 `em.persist()` 시점에 시퀀스를 조회하는 SQL문을 실행하여 시퀀스의 nextValue를 받아 영속성 컨텍스트에 등록한다.
* `@SequenceGenerator`가 필요하다.
  * name : 식별자 생성기 이름(필수)
  * sequenceName : 데이터베이스에 등록되어 있는 시퀀스 이름(기본값은 hibernate_sequence)
  * initialValue : DDL 생성 시에만 사용되며, 시퀀스 DDL을 생성할 때 처음 시작하는 수를 지정(기본값은 1)
  * allocationSize : 시퀀스 한 번 호출에 증가하는 수
    * 성능 최적화에 사용되며, 데이터베이스에 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값을 반드시 1로 설정해야 한다.(기본값은 50)
  * catalog, schema : 데이터베이스 catalog, schema 이름
* 오라클, PostgreSQL, DB2, H2에서 사용한다.

#### TABLE
* 키 생성 전용 테이블을 만들어서 데이터베이스 시퀀스를 흉내내는 전략이다.
  * 장점 : 모든 데이터베이스에 적용 가능
  * 단점 : 성능
* `@TableGenerator`가 필요하다
  * name : 식별자 생성기 이름(필수)
  * table : 키생성 테이블 명(기본값은 hibernate_sequences)
  * pkColumnName : 시퀀스 컬럼명(기본값은 sequence_name)
  * valueColumnName : 시퀀스 값 컬럼명(기본값은 next_val)
  * pkColumnValue : 키로 사용할 값 이름(기본값은 엔티티 이름)
  * initialValue : 초기 값, 마지막으로 생성된 값이 기준(기본값은 0)
  * allocationSize : 시퀀스 한 번 호출에 증가하는 수
    * 성능 최적화에 사용되며 기본값은 50
  * catalog, schema : 데이터베이스 catalog, schema 이름
  * uniqueConstraints : 유니크 제약 조건 지정

#### 권장하는 식별자 전략
* 기본키 제약조건은 null이 아니며, 유일해야 하고, 변하면 안된다.
* 위 조건을 항상 만족하는 자연키는 찾기 어려우므로 대체키를 사용하는게 좋다.(주민번호 등도 적절하지 않음)
* Long type + 대체키 + 키 생성 잔략을 사용하는것이 권장된다.


## 연관관계 매핑
* 객체와 테이블 연관관계의 차이를 이해해야 한다.
* **공통 예시는 team과 member가 있고, member는 하나의 team에만 소속될 수 있는 상황이라 가정**한다.

### 객체를 테이블에 맞추어 모델링
* team과 member는 일대다 연관관계를 갖으면서, 멤버쪽에 team_id가 외래키로 등록이 된다.
* 이 상황에서 member의 team을 조회하려면, member에 저장된 teamId를 이용해 맞는 팀을 조회해 올 수 밖에 없다. (객체 지향적인 방법이라고 할 수 없음)
* 객체를 테이블에 맞추어 데이터 중심으로 모델링 하면 협력관계를 만들 수 없다.
* **테이블은** 외래키로 조인을 사용해 연관된 테이블을 찾는다.
* **객체는** 참조를 사용해서 연관된 객체를 찾는다.

### 객체 지향 모델링
#### 단방향
* 객체 지향적으로 모델링 하기 위해서는 member안에 team이라는 객체가 들어있어야 한다. (참조를 바로 할 수 있음)

#### 양방향
* Team에 members라는 member들이 저장 될 List를 추가하여 역방향 조회가 가능하도록 만든다.

##### 객체의 양방향 연관관계
* 객체를 양방향으로 참조하려면 단방향 연관관계를 2개 만들어야 한다.
* 객체의 양방향 관계는 사실 양방향 관계가 아닌, 서로 다른 단방향 관계 2개인 것이다.

##### 테이블의 양방향 연관관계
* 테이블은 외래키 하나로 두 테이블의 연관관계를 관리한다.
* 위 예시에서는 MEMBER.TEAM_ID 외래키 하나로 양방향 연관관계를 갖는다. (양쪽으로 조인 가능함)

#### 연관관계의 주인
* 객체의 양방향 연관관계에서, 단방향 2개로 양방향으로 연결된 객체 둘 중 하나로 외래키를 관리해야 한다.
1. 객체의 두 관계 중 하나를 연관관계의 주인으로 지정한다.
2. 연관관계의 주인만이 외래키를 관리한다.(등록, 수정)
3. 주인이 아닌 쪽은 읽기만 가능하다.
4. 주인은 mappedBy 속성을 사용하지 않는다.
5. 주인이 아니면 mappedBy 속성으로 주인을 지정한다.
* 외래 키가 있는 곳을 주인으로 정하면 된다.

#### 양방향 연관관계 주의사항
* 순수 객체 상태를 고려하여 항상 양쪽에 값을 설정한다.
* 연관관계 편의 메서드를 생성한다. (뒤에서 다룰 예정)
* 양방향 매핑시에 무한 루프를 조심해야 한다.
  * toString(), lombok, JSON 생성 라이브러리

#### 양방향 연관관계 정리
* 단방향 매핑만으로도 이미 연관관계 매핑은 완료가 된다.
* 양방향 매핑은 반대 방향으로의 조회 기능이 추가된 것 뿐이다.
* 단방향 매핑을 잘 하고 양방향은 필요할 때 추가해도 된다.
* 비즈니스 로직을 기준으로 연관관계의 주인을 선택하면 안되고, 연관관계의 주인은 외래키의 위치를 기준으로 정해야 한다.

## 일대일(1:1) 매핑
* 일대일 관계는 주 테이블이나 대상 테이블 중 외래키를 넣을 테이블 선택이 가능하다.
* **공통 예시는 member와 locker가 있다고 가정**한다. (주 테이블 member, 대상 테이블 locker)
* member가 딱 하나의 locker를 가지고 있는 상황이고, 락커도 회원 한명만 할당 받을수 있는 상황으로, 이 둘의 관계는 일대일 관계이다.
### 일대일 주 테이블 외래키 단방향
* 멤버를 주 테이블로 보고, 주테이블 또는 대상테이블에 외래키를 저장할 수 있다.
* 유니크 제약 조건을 추가한 상태에서만 가능하다.
* 다대일(N:1) 단방향 관계 매핑과 JPA 어노테이션 `@OneToOne` 만 달라지고 방법은 거의 유사하다.

### 일대일 주 테이블에 외래키 양방향
* 다대일(N:1) 양방향 매핑처럼 외래키가 있는 곳이 연관관계의 주인이다.
* `@OneToOne` 어노테이션으로 일대일 단방향 관계를 매핑하고, `@JoinColumn을 넣어주면 된다. (우선 단방향 관계)
* 반대편에 mappedBy를 적용시켜주면 일대일 양방향 관계 매핑이 된다.
  * `mappedBy = "locker"`의 경우, member엔티티에 있는 locker필드와 매핑되었다는 것을 의미한다.
  * 이 경우 member필드는 읽기 전용 필드이다.

### 일대일 대상 테이블에 외래키 단방향
* 일대일관계에서 대상 테이블에 외래키를 저장하는 단방향 관계는 JPA에서 지원하지 않는다.

### 일대일 대상 테이블에 외래키 양방향
* 일대일 주 테이블에 외래키 양방향 매핑을 반대로 구현한 상태라고 생각하면 된다. (매핑 방법 같음)
* 주 테이블은 member테이블이지만, 외래키를 대상 테이블에서 관리하고 주 테이블의 locker필드는 읽기 전용이 된다.


### 외래키 주 테이블 vs 대상테이블
#### 주 테이블에 외래키
* 주 객체가 대상 객체의 참조를 가지는 것처럼, 주 테이블에 외래키를 두고 찾는 방식이다.
* 객체지향 개발자들이 선호하는 방식이고, JPA 매핑이 편리하다.
* 주 테이블만 조회해도 대상 테이블에 데이터가 있는지 확인이 가능하다. (장점)
* 값이 없으면 외래키에 NULL을 허용해야 한다. (DB 입장에서 치명적일 수 있음) (단점)

#### 대상 테이블에 외래키
* 대상 테이블에 외래키가 존재하는 상태이다.
* 전통적인 데이터베이스 개발자들이 선호하는 방식이다.
* 주 테이블과 대상 테이블을 일대일에서 일대다 관계로 변경할 때 테이블 구조를 유지할 수 있다. (테이블 구조를 유지하며 유지보수 용이) (장점))
* 코드상에서는 주로 주 테이블에서 대상 테이블을 많이 엑세스 하는데, 어쩔 수 없이 양방향 매핑을 해야 한다. (단점)
* JPA가 제공하는 기본 프록시 기능의 한계로, 지연 로딩으로 설정해도 항상 즉시 로딩이 된다. (단점)
  * JPA입장에서 일대일 관계의 주 테이블에 외래키를 저장하는 상황에서는, 주 테이블의 외래키로 대상 테이블의 ID가 있는지 없는지만 확인하면 된다.
  * 만약 있으면 프록시 객체를 넣어주고, 없으면 null을 넣으면 되며, 나중에 진짜 대상테이블에 엑세스 할 때 쿼리가 나간다.
  * 대상테이블에 외래키를 저장하면, DB의 주 테이블만 조회해서는 안된다. 대상 테이블에서 확인해야(쿼리문 실행) 알 수 있따.
  * 어차피 쿼리문이 실행되므로, 프록시를 만들 필요가 없다. (하이버네이트 구현체의 경우 지연로딩으로 설정해도 항상 즉시로딩 됨)

## 연관관계 편의 메서드
* 양방향 연관관계를 맺을 땐 양쪽 모두 관계를 맺어주어야 한다.
* JPA입장에서 보면 연관관계의 주인(외래키가 있는 곳)쪽에만 관계를 맺어주면 정상적으로 양쪽 모두에서 조회가 가능하다.
* 그러나 객체까지 고려한다면 양쪽 다 관계를 맺어야 한다.
* 객체의 양방향 연관관계는 양쪽 모두 관계를 맺어주어야 순수한 객체 상태에서도 정상적으로 동작하게 된다.
* 따라서 양방향 연관관계에서는 양쪽 객체 모두 신경써줘야 하고, **양쪽 모두의 관계를 맺어주는 것을 메서드**로 사용하는 것이 안전하다.
### 연관관계 편의 메서드 주의사항
* 연관관계 편의 메서드를 작성해야 할 때 주의해야 할 점이 있다.
* 아래 예시를 참고하면 되며, 아래 예시는 하나의 Team에 여러 Member가 속할 수 있다고 가정한다.
```java
public class Member{
    
    ...
  
    public void setTeam(Team team){
        if(this.team != null){  // Member에 기존 Team이 존재한다면
            this.team.getMembers().remove(this);    // 기존 Team의 Members에서 해당 Member를 제거하여 연관관계를 끊어줌
        }
        this.team = team;   // 멤버에 새로운 팀 설정
        if(!team.getMembers().contains(this)){    // 새로운 Team의 Members에 Member가 속해있지 않으면 
            team.addMember(this);   // 해당 Team에 Member를 추가해줌
        }
    }
}
```

```java
public class Team{
    
    ...
  
    public void addMember(Member member){
        this.members.add(member);   // 해당 팀의 Members에 새로운 멤버를 추가해줌
        if(member.getTeam != this){ // 만약 그 새로운 맴버의 팀이 해당 팀과 다르다면
            member.setTeam(this);   // 새로운 멤버의 팀은 해당 팀으로 설정
        }
    }
}

```

## JPA 순환참조
* 예시로 member는 post와 1:N 관계이고, member를 통해 post를 조회할 수 있고, post를 통해서도 member를 조회할 수 있도록 양방향 관계로 매핑을 한다.
* 이럴 경우 post Entity를 그대로 반환하게 되면 post가 참조하고있는 member가 조회되고, member가 다시 post를 참조하고 이게 계속 반복된다.
* 이러한 순환참조를 해결하기 위한 방법이 여러가지가 있다.

### @JsonIgnore
* JSON데이터에 해당 프로퍼티는 null로 들어간다. (데이터에 아예 포함시키지 않음)

### @JsonManagedReference, @JsonBackReference
* `@JsonManagedReference`를 연관관계 주인의 반대편에 붙인다.
* `@JsonBackReference`를 연관관계의 주인에 설정한다.

### 매핑 재 설정
* 양방향 매핑이 꼭 필요한지 생학해보고, 만약 양방향 매핑이 필요 없다면 단방향 매핑으로 설정을 하여 순환참조를 해결한다.

### DTO 객체를 만들어서 반환 👍
* JPA에서 순환참조가 발생하게 되는 원이는 양방향 매핑 때문이기도 하지만, 더 정확히는 Entity 자체를 응답으로 보내서 생기게 된다.
* Entity를 그대로 응답으로 보내게 되면 필요없는 정보까지 다 보내지게 된다.
* 그래서 DTO를 만들어 필요한 정보만 옮겨담아 응답으로 보내면 순환참조 문제는 애초에 생기지 않는다.

## Cascade(영속성 전이)
* 특정 엔티티를 영속 상태로 만들 때, 연관된 엔티티도 함께 영속 상태로 만들고 싶은 경우 사용한다.
* 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장하거나, 부모 엔티티를 삭제할 때 관련된 자식 엔티티도 함께 삭제하고 싶은 경우 사용한다.
* 지연/즉시 로딩과는 아무 관련이 없다.
* 프로젝트 규모가 커지는 경우, 연관관계가 하나일 경우에만 사용하는게 좋다.

### Cascade 속성
* ALL : 모두 적용으로, 웬만한 경우 ALL 사용
* PERSIST : 하위 엔티티까지 영속성 전달
* REMOVE : 연결된 하위 엔티티까지 제거
* MERGE : 하위 엔티티까지 병합
* REFRESH : 하위 엔티티까지 값 다시 읽어오기
* DETACH : 하위 엔티티까지 영속성 제거 
* 보통 ALL, PERSIST, REMOVE만 사용하고 대부분 ALL을 사용한다. 
### 고아 객체 삭제
* 고아 객체란 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 의미한다.
* 이렇게 연관관계가 끊어진 객체를 자동으로 삭제되게 할 수 있는 방법이 있는데, `orphanRemoval=true`이다.


## 즉시 로딩과 지연 로딩
* Member에 FK가 있고 Member와 Team이 다대일로 매핑되어 있다고 예시를 든다.
* Member에 대한 조회만 필요할 때 Team도 같이 조회하게 되면 아무리 연관관계가 맺어있다고 해도 손해이다.
* JPA에서는 이러한 문제를 지연로딩 `LAZY`를 사용해 프록시로 조회하는 방법으로 해결한다.
* 예를 들면 Member와 Team이 `@ManyToOne`으로 다대일로 설정되어 있고 해당 애너테이션에 `FetchType.LAZY`속성을 추가한다.
  ```java
  @ManyToOne(fetch = FetchType.LAZY)
  @JoinColumn(name = "TEAM_ID")
  private Team team;
  ```
  * 위와 같이 멤버클래스에 Team이 `FetchType.LAZY`로 설정되어 있으면 멤버를 조회시 Team은 프록시로 조회한다.

* 즉시 로딩의 경우 속성을 `EAGER`로 설정하면 되고, 조회 시 프록시를 사용하지 않는다. (실무에서는 대부분 지연로딩 사용)
* `@ManyToOne`, `@OneToOne`은 기본값이 즉시 로딩이고, `@OneToMany`, `@ManyToMany`는 기본값이 지연 로딩이다.

### 즉시 로딩 vs 지연 로딩
* 실무에서는 한번 조회할 때 다수의 테이블을 조인해서 가지고 오면 성능문제를 일으킨다.
* 실무에서는 가급적 지연 로딩만 사용한다.
  * 즉시 로딩을 적용하면 예상하지 못한 SQL이 발생
  * 즉시 로딩은 JPQL에서 N+1문제를 일으킨다.
  
    ```
    em.createQuery("select m from Member m", Member.class).getResultList()
    ```
    * select문에서 Member를 조회하지만, Member안에 Team이 FK로 있기 떄문에 Team도 조회하면서 총 2번의 쿼리가 출력된다.

* `@ManyToOne`, `@OneToOne`은 기본값이 즉시 로딩이고, `@OneToMany`, `@ManyToMany`는 기본값이 지연 로딩이다. 



___
참고

[인프런 김영한님의 자바 ORM 표준 JPA 프로그래밍](https://www.inflearn.com/course/ORM-JPA-Basic/)