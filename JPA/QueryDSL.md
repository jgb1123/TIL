# QueryDSL
## QueryDSL?
* JPA에는 쿼리 메서드의 기능과 @Query를 통해 많은 기능을 이용할 수 있다.
* 하지만 고정된 형태의 값을 가진다는 한계가 있다.
* 즉, 단순한 검색조건을 만들어야 하는 상황에서는 JPA만으로 충분히 사용 가능하지만, 복잡한 동적 쿼리가 필요할 경우엔 한계가 생긴다.
* 이때 QueryDSL을 사용하면 복잡한 동적쿼리들도 처리할 수 있게 된다.
* QueryDSL은 오픈소스 프로젝트로, JPQL을 Java코드로 작성할 수 있도록 하는 라이브러리이다.
* QueryDSL은 select, from, where등 쿼리 작성에 필요한 키워드를 메서드 형식으로 제공한다.

## Gradle에서 Querydsl 설정
* build.gradle파일에 querydsl 설정을 추가해야 한다.
```groovy
// queryDsl version 정보 추가
buildscript {
  ext {
    queryDslVersion = "5.0.0"
  }
}

...
        
plugins{
  ...
  // querydsl plugins 추가
  id"com.ewerk.gradle.plugins.querydsl"version"1.0.10"
  ...
}

...

dependencies {
  ...
  // querydsl dependencies 추가
  implementation "com.querydsl:querydsl-jpa:${queryDslVersion}"
  implementation "com.querydsl:querydsl-apt:${queryDslVersion}"
  ...
}

...

// querydsl에서 사용할 경로 설정
def querydslDir = "$buildDir/generated/querydsl"

// JPA 사용 여부와 사용할 경로를 설정
querydsl {
  jpa = true
  querydslSourcesDir = querydslDir
}

// build 시 사용할 sourceSet 추가
sourceSets {
  main.java.srcDir querydslDir
}

// querydsl 컴파일시 사용할 옵션 설정
compileQuerydsl{
  options.annotationProcessorPath = configurations.querydsl
}

// querydsl 이 compileClassPath 를 상속하도록 설정
configurations {
  compileOnly {
    extendsFrom annotationProcessor
  }
  querydsl.extendsFrom compileClasspath
}
```
* gradle 설정이 완료되면 `Gradle Tasks` -> `compileQuerydsl`을 실행한다.
* 빌드에 성공하면 `build/generated/querydsl` 경로에 Project Entity들의 QClass가 생성된 것을 확인할 수 있다.

## 기본 문법

### 기본 Q-Type
* Q 클래스 인스턴스를 사용하는 2가지 방법이 있다.
```java
QMember qMember = new QMember("m");
```
   * 별칭 직접 지정
```java
QMember qMember = QmMember.member;
```
   * 기본 인스턴스 사용
* Qmember.member를 static import해서 사용하는 것이 좋다.

### 검색 조건 쿼리
* JPQL이 제공하는 모든 검색조건(where 절)
```java
member.username.eq("member") // username = 'member'
member.username.ne("member") // username != 'member'
member.username.eq("member").not() // username != 'member'
member.username.isNotNull() // username is not null
member.age.in(10, 20) // age in (10, 20)
member.age.notIn(10, 20) // age not in (10, 20)
member.age.between(10, 20) // age between 10 and 20
member.age.goe(30) // age >= 30
member.age.gt(30) // age > 30
member.age.loe(30) // age <= 30
member.age.lt(30) // age < 30
member.username.like("김%") // username like "김%"
member.username.contain("철") // username like "%철%"
member.username.startsWith("장") // username like "장%"
```
* where()에 파라미터로 검색조건를 추가하면 and조건이 추가된다.
* 또한 검색 조건들은 where() 뒤에 .and() 와 .or()를 사용해 메서드 체인으로 연결할 수 있다.

### 결과 조회 쿼리
* fetch() : 리스트를 조회, 결과가 없으면 빈 리스트를 반환한다.
* fetchOne() : 단 건 조회, 결과가 없으면 null, 결과가 둘 이상이면 예외발생
* fetchFirst() : 첫번째로 발견되는 결과 조회, limit(1).fetchOne()와 같다.
* fetchResult() : 페이징을 포함한 결과 조회
* fetchCount() : count 쿼리로 변경해서 count 수 조회가능

### 정렬
* orderBy()를 사용해 정렬할 수 있다.
* desc()를 이용해 내림차순으로, acs()를 이용해 오름차순으로 정렬할 수 있다.
* nullLast()나 nullFisrt()로 null 데이터에 순서를 부여할 수 있다.

### 페이징
```java
QueryResults<Member> queryResults = queryFactory
        .selectFrom(member)
        .orderBy(member.username.desc())
        .offset(1)
        .limit(2)
        .fetchResults();

queryResults.getTotal(); // 전체 데이터 수
queryResults.getLimit(); // 몇개로 제한할 지
queryResults.getOffset(); // 몇 번째부터
queryResults.getResults().size(); // 페이징 결과의 데이터 수
```
* fetchResult()를 사용할 경우 count쿼리가 먼저 나가고 select쿼리가 나간다.
* 실무에서는 페이징쿼리를 작성할 때 데이터를 조회하는 쿼리는 여러 테이블을 조인해서 하지만, count자체는 그럴필요가 없는 경우도 많다.
* fetchResult()를 사용하면 자동화된 count쿼리가 나가므로 성능상 안좋을 수 있으므로, 따로 count쿼리를 별도로 내는게 좋다.

### 집합
#### 일반적인 집합 함수
```java
List<Tuple> result = queryFactory
        .select(
            member.count(), // 멤버 수
            member.age.sum(), // 멤버 나이의 합
            member.age.avg(), // 멤버 나이의 평균
            member.age.max(), // 멤버 나이의 최댓값
            member.age.min() // 멤버 나이의 최솟값
        )
        .from(member)
        .fetch();
// Tuple
Tuple tuple = result.get(0);
tuple.get(member.count());
tuple.get(member.age.avg());
```
* tuple은 querydsl에서 제공하는 자료구조로, select에서 원하는 데이터 타입이 여러개면 tuple로 결과를 조회할 수 있다.
* 실무에서는 tuple보다는 dto로 가져오도록 많이 사용한다.

#### groupBy()
```java
List<Tuple> result = queryFactory
        .select(team.name, member.age.avg()
        .from(member)
        .join(member.team, team)
        .groupBy(team.name)
        .fetch();
```

#### having()
```java
List<Tuple> result = queryFactory
        .select(team.name, member.age.avg()
        .from(member)
        .join(member.team, team)
        .groupBy(team.name)
        .having(member.age.avg().gt(15))
        .fetch();
```

### 조인
#### 기본 조인
* 첫번째 파라미터에 조인 대상을 지정하고, 두번내 파라미터에 별칭을 사용할 Q타입 클래스를 지정하면 된다.
```java
Lisr<Member> result = queryFactory
        .selectFrom(member)
        .join(member.team, team)
        .where(team.name.eq("A팀"))
        .fetch();
```
* 조인 이후에 on을 넣어서 대상 지정을 넣을 수 있다.
* 연관관계가 없어도 조안을 할 수 잇는 세타조인도 가능하다.
  * 세타조인은 연관관계가 없이 데이터를 다 가져온 다음 조인하는 방식이다.
  * from에 여러 엔티티를 가지고와서 세타조인을 한다.

#### on절
* on절을 활용한 조인은 대상을 필터링해주고, 연관관계가 없는 엔티티는 외부조린을 활용할 수 있다.
  * on절은 join할 데이터를 필터해주는 반면, where절은 join을 하고 나서 데이터를 필터링하기위해 사용된다.
  * on절이 where절보다 더 실행된다.
```java
List<Tuple> result = queryFactory
        .select(member, team)
        .from(member)
        .leftJoin(member.team, team)
        .on(team.name.eq("A팀")
        .fetch();
```
* on절을 사용할 때 inner join을 사용하면 where과 결과적으로 동일하다.
* 연관관계가 없는 엔티티를 외부 조인할 경우 on절이 쓰인다.
  * 연관관계가 있는 경우 `leftJoin(member.team, team)`을 통해 member가 가진 FK를 통해 조인한다.
  * 연관관계가 없는 경우 `leftJoin(team).on()`과 같이 team에 조인을 하는데 이 조건을 on절에 담기게 해서 조인한다.

#### 페치조인
* 페치조인은 JPA 사용 시 성능 최적화를 위해 사용하는 JPQL의 기능이다.
```java
Member findMember = queryFactory
        .selectFrom(QMember.member)
        .join(member.team, team).fetchJoin()
        .where(QMember.member.username.eq("member1"))
        .fetchOne();
```

#### 서브쿼리
* 서브쿼리는 select문 안에 다시 select문이 기술된 형태의 쿼리이며, 안에 있는 결과를 밖에서 받아 처리하는 구조이다.
* 단일 select문으로 조건식을 만들기 복잡할 경우나, 완전히 다른 테이블에서 값을 조회해서 메인 쿼리로 이용하고자 할 때 사용한다.
* 서브쿼리는 select절이나 where절에서만 가능하다.
```java
// 나이가 가장많은 회원 조회
Member findMember = queryFactory
        .selectFrom(member)
        .where(member.age.eq(
            JPAExpressions
                 .select(qMember.age.max())
                 .from(qMember)
        ))
        .fetchOne();
```

### case문
* case문은 select, 조건절(where), order by에서 사용이 가능하다.

```java
List<String> result = queryFactory
        .select(member.age // 일반적인 case문
            .when(10).then("10살")
            .when(20).then("20살")
            .otherwise("기타"))
        .from(member)
        .fetch();
```

```java
List<String> result = queryFactory
        .select(new CaseBuilder() // 조금 복잡한 case문
            .when(member.age.between(10, 19).then("10대")
            .when(member.age.between(20, 29).then("20대")
            .otherwise("기타"))
        .from(member)
        .fetch();
```

### 상수, 문자 더하기
#### 상수
* 상수가 필요하면 Expressions.constant()를 사용하면 된다.
```java
List<Tuple> result = queryFactory
        .select(member.username, constant("A"))
        .from(member)
        .fetch();
```

#### 문자 더하기
* 문자를 더하려면 concat을 이용하면 편하다.
```java
String result = queryFactory
        .select(member.username.concat("_").concat(member.age.stringValue()))
        .from(member)
        .where(member.username.eq("member1"))
        .fetchOne();
```

## 중급 문법
### 프로젝션
* select 구문에 어떤 필드를 가져올지 지정하는 것을 프로젝션이라고 한다.
* 사용자 이름만 반환하는 경우에는 프로젝션 대상이 사용자 이름 1개이고, 1개이기 때문에 타입을 명확하게 지정할 수 있다.
* 하지만 프로젝션 대상이 여러개인 경우 Tuple이나 DTO로 조회해야 한다.

#### 프로젝션 대상 하나
```java
List<String> result = queryFactor
        .select(member.username)
        .from(member)
        .fetch();
```

#### Tuple
* 프로젝션이 여러개인 결과 값을 처리할 수 있도록 Querydsl에서는 Tuple클래스를 제공한다.
```java
List<Tuple> result = queryFactory
        .select(member.username, member.age)
        .from(member)
        .orderBy(member.age.asc())
        .fetch();
```

#### DTO로 조회
* DTO클래스로 결과값을 받는 방법은 프로퍼티, 필드, 생성자 접근 방법이 있다.
##### 프로퍼티 접근 방법
```java
List<MemberDto> result = queryFactory
        .select(Projections.bean(
                MemberDto.class, 
                member.username, 
                member.age
        ))
        .from(member)
        .fetch();
```
* Querydsl은 MemberDto 객체를 기본 생성자로 생성하고 값을 Setter로 생성한다.

##### 필드 접근 방법
```java
List<MemberDto> result = queryFactory
        .select(Projections.fields(
                MemberDto.class,
                member.username,
                member.age
        ))
        .from(member)
        .fetch();
```
* Getter, Setter없이 바로 필드에 접근해서 값을 설정한다.

##### 생성자 접근 방법
```java
List<MemberDto> result = queryFactory
        .select(Projections.constructor(
                MemberDto.class,
                member.username,
                member.age
        ))
        .from(member)
        .fetch();
```
* MemberDto 클래스의 생성자로 값을 설정한다.

#### @QueryProjection
* DTO 생성자 위에 해당 애너테이션을 붙이고, 컴파일 결과로 QDto파일이 생성된다.
* DTO 클래스 생성자에 `@QueryProjection` 애너테이션을 붙이고, `compileQuerydsl`을 실행하면 `QMemberDto`클래스를 얻을 수 있다.
```java
public class MemberDto {
    private String username;
    private int age;
    
    public MemberDto() {
        
    }
    
    @QueryProjection
    public MemberDto(String username, int age) {
        this.username = username;
        this.age = age;
    }
}
```

```java
List<MemberDto> result = queryFactory
        .select(new QMemberDto(member.username, member.age))
        .from(member)
        .fetch();
```
* 이 방식은 컴파일러로 타입을 체크할 수 있는 가장 안전한 방법이지만, DTO에 QueryDSL 애너테이션을 유지해야 하는 점과 DTO까지 Q파일을 생성해야 하는 단점이 있다.

### 동적 쿼리
* 복잡한 조건에 따른 동적 쿼리를 만들기 위해서는 BooleanBuilder와 where 두가지 방식을 사용할 수 있다.

#### BooleanBuilder
* 전달 받은 개수만큼 BooleanBuilder를 사용해 or, and 구문을 생성한다.
```java
private List<Member> searchMember1(String usernameParam, Integer ageParam) {
    BooleanBuilder builder = new BooleanBuilder();
    if(usernameParam != null) {
        builder.and(member.username.eq(usernameParam));
    }

    if(ageParam != null) {
        builder.and(member.age.eq(ageParam));
    }
    
    return queryFactory
        .selectFrom(member)
        .where(builder)
        .fetch();
}
```

```java
List<Member> result = searchMember1(usernameParam, ageParam);
```

#### where
```java
private List<Member> searchMember2(String usernameCond, Integer ageCond) {
    return queryFactory
        .selectFrom(member)
        .where(usernameEq(usernameCond), ageEq(ageCond))
        .fetch();
}

private Predicate usernameEq(String usernameCond) {
    if(usernameCond == null) return null;
    return member.username.eq(usernameCond);
}

private Predicate ageEq(Integer ageCond) {
    if(ageCond == null) return null;
    return member.age.eq(ageCond);
}
```

```java
List<Member> result = searchMember2(usernameParam, ageParam);
```
* usernameEq() 메서드가 null을 리턴하게 되면 where()에 null값이 들어가게 되는데, 이것은 무시가 된다. 그러므로 동적 쿼리가 될 수 있다.
* 쿼리 자체의 가독성이 더 높아지고 코드가 더 깔끔하게 나오므로, 실무에서 더 많이 사용한다.
  * BooleanBuilder는 객체를 또 봐야한다.

### 수정, 삭제 벌크 연산
* 쿼리 한번으로 대량의 데이터를 수정하는 방식을 벌크연산이라고 한다.
#### 데이터 수정
```java
long count = queryFactory
        .update(member)
        .set(member.username, "비회원")
        .where(member.age.lt(30))
        .execute();

em.flush();
em.clear();
```

* 데이터 수정 쿼리를 위와같이 실행하게 되면 영속성 컨텍스트를 무시하고 바로 DB에 쿼리를 날린다.
* 따라서 컨텍스트와 DB간에 데이터 불일치가 생기므로, 영속성 컨텍스트 내용을 초기화해서 DB와 일치시켜야 한다.

#### 데이터 삭제
```java
long count = queryFactory
        .delete(member)
        .where(member.age.lt(20))
        .execute();
```

### SQL function 호출
```java
String result = queryFactory
        .select(Expressions.stringTemplate("function('replace', {0}, {1}, {2})", // replace 사용
                member.username, "member", "M"))
        .from(member)
        .fetchFirst();
```
* SQL function은 JPA와 같이 Dialect에 등록된 내용만 호출할 수 있다.


### 함수 정리
* `JPAExpressions` 서브쿼리에 사용함
* `Expressions` 상수, SQL function 사용함
* `Projections` 프로젝션을 DTO로 반환할 때 사용함
* `ExpressionUtils` 서브쿼리에 별칭을 줄 때 사용함
* `BooleanBuilder` 동적 쿼리의 파라미터에 사용함
* `BooleanExpression` where 다중 파라미터 시 메서드의 반환 값

## 순수 JPA와 Querydsl
### 순수 JPA 레파지토리와 Querydsl
* JpaQueryFactory를 사용하면 된다.

```java
@Repository
public class MemberJpaRepository {

    private final EntityManager em;
    private final JPAQueryFactory queryFactory;
    
    // queryFactory를 bean으로 올려서 사용해도 되고, EntityManager를 받아 새롭게 queryFactory를 만들어도 됨
    // queryFactory를 bean으로 올려서 쓰면 lombok의 @RequiredArgsConstructor를 편하게 사용할 수 있음 
    // 하지만 JPAQueryFactory를 외부에서 받아야 하기 때문에 테스트코드를 작성할 때 불편할 수 있음
    public MemberJpaRepository(EntityManager em) {
        this.em = em;
        this.queryFactory = new JPAQueryFactory(em);
    }

    public void save(Member member) {
        em.persist(member);
    }

    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
    }

    public List<Member> findAll() { // 순수 JPA
        return em.createQuery("select m from Member m", Member.class)
                .getResultList();
    }
    
    public List<Member> findAll_Querydsl() { // Querydsl 적용
        return queryFactory
                .selectFrom(member)
                .fetch();
    }

    public List<Member> findByUsername(String username) { // 순수 JPA
        return em.createQuery("select m from Member m where m.username =:username", Member.class)
                .setParameter("username", username)
                .getResultList();
    }
    
    public List<Member> findByUsername_Querydsl(String username) {   // Querydsl 적용
        return queryFactory
                    .selectFrom(member)
                    .where(member.username.eq(username))
                    .fetch();
    }
}
```
* JpaQueryFactory는 EntityManager를 이용해 생성해도 되고, 빈으로 등록 후 주입받아도 된다.
* 스프링이 주입해서 사용하는 EntityManager는 프록싱해서 주입해준 가짜 EntityManager이므로, 동시성 문제에 대해 아무 문제 없다.

### 동적 쿼리와 성능 최적화 조회 (Builder)
* 동적 쿼리를 사용하기 위한 조건들을 Builder로 만들고 이를 한번에 Dto로 가져오도록 하여 성능을 최적화하는 방법이다.
```java
public List<MemberTeamDto> searchByBuilder(MemberSearchCondition condition) {
    BooleanBuilder builder = new BooleanBuilder();

    if(hasText(condition.getUsername())) { // StringUtils.hasText() null체크도 있지만 ""으로 들어올 수도 있기 때문에 hasText() 사용
        builder.and(member.username.eq(condition.getUsername()));
    }
        
    if(hasText(condition.getTeamName())) {
        builder.and(team.name.eq(condition.getTeamName()));
    }

    if(condition.getAgeGoe() != null) {
        builder.and(member.age.goe(condition.getAgeGoe()));
    }

    if(condition.getAgeLoe() != null) {
        builder.and(member.age.loe(condition.getAgeLoe()));
    }

    return queryFactory
        .select(new QMemberTeamDto(
                member.id.as("memberId"),
                member.username,
                member.age,
                team.id.as("teamId"),
                team.name.as("teamName")
        ))
        .from(member)
        .leftJoin(member.team, team)
        .where(builder)
        .fetch();
}
```
* 위 예시에 추가로 limit이나 페이징 쿼리 또는 기본 조건을 추가해 대량의 데이터를 가지고 오지 않도록 설계가 필요하다.

### 동적 쿼리와 성능 최적화 조회 (where)
```java
public List<MemberTeamDto> searchByWhere(MemberSearchCondition condition) {
    return queryFactory
        .select(new QMemberTeamDto(
                member.id.as("memberId"),
                member.username,
                member.age,
                team.id.as("teamId"),
                team.name.as("teamName")
        ))
        .from(member)
        .leftJoin(member.team, team)
        .where(
                usernameEq(condition.getUsername()),
                teamNameEq(condition.getTeamName()),
                ageGoe(condition.getAgeGoe()),
                ageLoe(condition.getAgeLoe())
        )
        .fetch();
}

private BooleanExpression usernameEq(String username) {
    return hasText(username) ? member.username.eq(username) : null;
}

private BooleanExpression teamNameEq(String teamName) {
    return hasText(teamName) ? team.name.eq(teamName) : null;
}

private BooleanExpression ageGoe(Integer ageGoe) {
    return ageGoe != null ? member.age.goe(ageGoe) : null;
}

private BooleanExpression ageLoe(Integer ageLoe) {
    return ageLoe != null ? member.age.loe(ageLoe) : null;
}
```
* Builder를 사용한 것 보다 where절을 파라미터 형식으로 조건을 거는게 가독성이 확실히 좋다.
* Intellij를 사용 시 where절 파라미터를 자동 생성으로 만들 때 Predicate타입을 리턴하도록 하는데, BooleanExpression으로 바꾸는게 더 좋다.
  * BooleanExpression도 Predicate를 상속받고 있고, 같은 BooleanExpression끼리 조합할 수 있어서 활용성이 더 좋다.
  * 따라서 Projection이 달라져도 재사용하는게 충분히 가능하다.

## 스프링 데이터 JPA와 Querydsl
### 사용자 정의 레파지토리
* JpaRepository를 상속한 스프링 데이터 JPA 레파지토리인 MemberRepository 생성
```java
public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom{
    List<Member> findByUsername(String username);
}
```

* 복잡한 쿼리를 담당한 MemberRepositoryCustom 인터페이스 생성
```java
public interface MemberRepositoryCustom {
    List<MemberTeamDto> search(MemberSearchCondition condition);
}
```

* 실제 구현을 담당한 MemberRepositoryImpl 클래스 생성
```java
public class MemberRepositoryImpl implements MemberRepositoryCustom {
    private final JPAQueryFactory queryFactory;

    public MemberRepositoryImpl(EntityManager em) {
        this.queryFactory = new JPAQueryFactory(em);
    }
    
    @Override
    public List<MemberTeamDto> search(MemberSearchCondition condition) {
        return queryFactory
                .select(new QMemberTeamDto(
                        member.id.as("memberId"),
                        member.username,
                        member.age,
                        team.id.as("teamId"),
                        team.name.as("teamName")
                ))
                .from(member)
                .leftJoin(member.team, team)
                .where(
                        usernameEq(condition.getUsername()),
                        teamNameEq(condition.getTeamName()),
                        ageGoe(condition.getAgeGoe()),
                        ageLoe(condition.getAgeLoe())
                )
                .fetch();
    }

    private BooleanExpression usernameEq(String username) {
        return hasText(username) ? member.username.eq(username) : null;
    }

    private BooleanExpression teamNameEq(String teamName) {
        return hasText(teamName) ? team.name.eq(teamName) : null;
    }

    private BooleanExpression ageGoe(Integer ageGoe) {
        return ageGoe != null ? member.age.goe(ageGoe) : null;
    }

    private BooleanExpression ageLoe(Integer ageLoe) {
        return ageLoe != null ? member.age.loe(ageLoe) : null;
    }

    private BooleanExpression ageBetween(Integer ageLoe, Integer ageGoe) {
        return ageLoe(ageLoe).and(ageGoe(ageGoe));
    }
}
```
* Querydsl을 이용하는 기능이 너무 특화된 기능이라면, 굳이 MemberRepository로 상속하도록 하는게 아니라 별도의 클래스를 만들고 거기서 쿼리를 관리해도 좋다.

### Querydsl 페이징 연동
* 사용자 정의 인터페이스에 페이징을 할 수 있는 메서드 추가
```java
public interface MemberRepositoryCustom {
    List<MemberTeamDto> search(MemberSearchCondition condition);
    Page<MemberTeamDto> searchPageSimple(MemberSearchCondition condition, Pageable pageable); // 페이징 메서드
    Page<MemberTeamDto> searchPageComplex(MemberSearchCondition condition, Pageable pageable); // 페이징 메서드
}
```

* 전체 카운트를 한번에 조회하는 단순한 방법
```java
public Page<MemberTeamDto> searchPageSimple(MemberSearchCondition condition, Pageable pageable) {
    QueryResults<MemberTeamDto> results = queryFactory
        .select(new QMemberTeamDto(
                member.id.as("memberId"),
                member.username,
                member.age,
                team.id.as("teamId"),
                team.name.as("teamName")
        ))
        .from(member)
        .leftJoin(member.team, team)
        .where(
                usernameEq(condition.getUsername()),
                teamNameEq(condition.getTeamName()),
                ageGoe(condition.getAgeGoe()),
                ageLoe(condition.getAgeLoe())
        )
        .offset(pageable.getOffset())
        .limit(pageable.getPageSize())
        .fetchResults();    // count 쿼리와 결과를 가져오는 select 쿼리가 한번에 나간다.

        List<MemberTeamDto> content = results.getResults();
        long total = results.getTotal();
        return new PageImpl<>(content, pageable, total);
}
```

* 데이터와 전체 카운트를 별도로 조회하는 방법
  * 카운트 쿼리의 경우 조인을 탈 필요가 없는 경우도 있기 때문에 별도로 작성하는게 성능을 높일 수 있음
```java
@Override
public Page<MemberTeamDto> searchPageComplex(MemberSearchCondition condition, Pageable pageable) {
    List<MemberTeamDto> content = queryFactory
        .select(new QMemberTeamDto(
                member.id.as("memberId"),
                member.username,
                member.age,
                team.id.as("teamId"),
                team.name.as("teamName")
        ))
        .from(member)
        .leftJoin(member.team, team)
        .where(
                usernameEq(condition.getUsername()),
                teamNameEq(condition.getTeamName()),
                ageGoe(condition.getAgeGoe()),
                ageLoe(condition.getAgeLoe())
        )
        .offset(pageable.getOffset())
        .limit(pageable.getPageSize())
        .fetch();   // 데이터 조회

    long total = queryFactory
        .selectFrom(member)
        .leftJoin(member.team, team)
        .where(
                usernameEq(condition.getUsername()),
                teamNameEq(condition.getTeamName()),
                ageGoe(condition.getAgeGoe()),
                ageLoe(condition.getAgeLoe())
        )
        .fetchCount();  // count 조회
        return new PageImpl<>(content, pageable, total);
}
```

### CountQuery 최적화
* Count쿼리는 생략 가능한 경우가 있고, 이것을 스프링 데이터 JPA에서 지원해주기도 한다.
  * 페이지 시작시 컨텐츠 사이즈가 페이지 사이즈보다 작을 때
  * 마지막 페이지인 경우에는 count쿼리를 쓸 필요가 없다. (offset + pagesize = total)
* `PageableExecutionUtils.getPage()`함수는 위의 두 조건의 경우 count쿼리를 나가지 않게 해준다.


## 스프링 데이터 JPA가 제공하는 Querydsl 기능
### 인터페이스 지원 - QuerydslPredicateExecutor
* QuerydslPredicateExecutor를 이용하면 스프링 데이터 JPA에서 Querydsl조건으로 넣을 수 있는 Predicate를 통해 결과를 조회하는게 가능해진다.
* MemberRepository에서 QuerydslPredicateExecutor를 상속
```java
public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom, QuerydslPredicateExecutor<Member> { 
    List<Member> findByUsername(String username);
}
```
* QuerydslPredicateExecutor
```java
public interface QuerydslPredicateExecutor<T> {
    Optional<T> findOne(Predicate var1);

    Iterable<T> findAll(Predicate var1);

    Iterable<T> findAll(Predicate var1, Sort var2);
    
    Iterable<T> findAll(Predicate var1, OrderSpecifier<?>... var2);

    Iterable<T> findAll(OrderSpecifier<?>... var1);

    Page<T> findAll(Predicate var1, Pageable var2);

    long count(Predicate var1);

    boolean exists(Predicate var1);
}
```
* QuerydslPredicateExecutor에서 정의한 메서드들은 Querydsl Predicate 조건절에 넣을 수 있으며, 이를 스프링 데이터 JPA에서 지원해준다.
* 하지만 조인을 할 수 없다는 단점이 있고, MemberRepository 같은 레파지토리에 Predicate 파라미터를 넘겨줘야 한다.
  * 서비스나 컨트롤러 계층에서 직접 만들어서 넘겨줘야 하는데 이는 강한 결합이 생겨 좋지 않다.

### Querydsl Web 지원
* Querydsl Web지원은 컨트롤러 레벨에서 Predicate를 받을 수 있도록 QuerydslPredicateArgumentResolver를 지원해준다.

### 레파지토리 지원 - QuerydslRepositorySupport
#### 장점
* `getQuerydsl().applyPagination()` 스프링 데이터가 제공하는 페이징을 Querydsl로 편리하게 변환 가능하다.
* EntityManager를 제공해준다.

#### 단점
* Querydsl 3.x버전을 대상으로 만들었기 때문에 Querydsl4.x에 나온 JPAQueryFactory로 시작할 수 없다. (select로 시작할 수 없음)
* 스프링 데이터 Sort 기능이 정상동작하지 않는다.


## 실무 Querydsl 사용법
### extends, implements 사용하지 않기
* 일반적으로 필요한 Repository를 만들면 Spring Data Jpa 기능을 이용하기 위해 JpaRepository를 상속받고, 또 Querydsl Jpa를 위해 사용자 정의 Repository를 만들고 이를 상속받으며, 이를 구현한 실제 구현 객체가 필요하다. (혹은 QuerydslRepositorySupport 클래스를 통해 사용자 정의 클래스에서 상속)
* 하지만 이렇게 매번 상속받는 것이 불편하기도 하고 JpaQueryFactory만 있으면 상관 없기 때문에 상속받는 구조보다는 이것만 주입받도록 하는 것이 가장 깔끔하다.
```java
@Repository
@RequiredArgsConstructor
public class MemberRepositoryCustom { 
    private final JpaQueryFactory queryFactory; // 빈으로 등록해줘야 함 
}
```
#### JpaQueryFactory 빈으로 등록하는 방법
```java
@Configuration
public class QuerydslConfiguration {
    @Autowired
    EntityManager em;

    @Bean
    public JPAQueryFactory jpaQueryFactory() {
       return new JPAQueryFactory(em);
    }
}
```

### 동적쿼리엔 BooleanExpression 사용
* 동적쿼리를 작성하는 방법엔 3가지가 있다.
  * BooleanBuilder 사용하는 방법
  * where절 파라미터로 predicate를 사용하는 방법
  * where절 파라미터로 predicate를 상속한 booleanExpression을 사용하는 방법
* BooleanBuilder를 사용하는 방법은 어떤 쿼리가 나가는지 예측하기 힘들다는 단점이 있다.
* Predicate보다는 BooleanExpression을 사용하는게 좋은 이유는, BooleanExpression은 and와 or같은 메서드들을 이용해 조합해서 새로운 BooleanExpression을 만들 수 있다는 장점이 있다. (재사용성이 높음)
* 또한 BooleanExpression은 null을 반환하게 되면 where절에서 조건이 무시되기 때문에 안전하다.

### exist 메서드 사용금지
* Querydsl에 있는 exist는 count쿼리를 사용하기 때문에, Querdsl에서는 exist를 사용하지 않는것이 좋다.
  * count쿼리는 SQL에서 exist와 같은 역할을 해줄 수 있기 때문
* SQL exist 쿼리는 첫 번째로 조건에 맞는 값을 찾는다면 바로 반환하도록 하지만, count쿼리는 전체 행을 모두 조회하기 때문에 성능이 떨어진다.
  * 스캔 대상이 앞에 있을수록 성능 차이는 심해짐
* 따라서 Querydsl에서는 `fetchFirst()`를 사용하는게 좋다.
  `fetchFirst()`의 내부 구현에는 `limit(1)`이 있어 결과를 한개만 가져오도록 하기 때문에 SQL exist문과 큰 차이가 없다.

### Cross Join 피하기
* 묵시적 조인이라고 하는, 조인을 명시하지 않고 엔티티에서 다른 엔티티를 조회해 비교하는 경우 JPA가 알아서 크로스 조인을 하게 된다.
* 크로스 조인을 하게 되면 나올 수 있는 데이터가 그냥 조인들보다 많아지기 때문에 성능상의 단점이 있다.
* 크로스 조인을 피하기 위해서는 쿼리를 보고 크로스 조인이 나간다면 명시적 조인을 이용해 해결해야 한다.

### 조회할 땐 엔티티보다 DTO를 우선적으로 가져오기
* 데이터베이스에서 엔티티를 가지고 오면 영속성 컨텍스트의 1차 캐시기능을 사용하게 되기도 하고, 불필요한 컬럼을 조회하기도 하며, N+1 문제(OneToOne에서 연관관계의 주인이 아닌 곳에서)가 생길 수도 있다.
* 하지만 엔티티를 조회할 필요가 있는 경우도 있으므로, 기준을 두고 조회를 하는게 좋다.
  * 실시간으로 엔티티 변경이 필요한 경우엔 엔티티 조회
  * 성능 개선이나 대량의 데이터 조회가 필요한 경우엔 DTO조회

```java
public List<MemberTeamDto> findSameTeamMember(Long teamId) {
    return queryFactory
        .select(new QMemberTeamDto(
                member.id,
                member.username,
                member.age,
                Expressions.asNumber(teamId),
                team.name
        ))
        .from(member)
        .innerJoin(member.team, team)
        .where(member.team.id.eq(teamId))
        .fetch();
}
```
* 위 예제의 경우 select절에 필요한 컬럼만 데이터 베이스에서 가져오도록 한다.
* teamId의 경우 이미 기존에 매개변수로 받았으므로 데이터베이스에서 가져올 필요가 없으므로, 성능상에서 약간의 이득을 볼 수 있다.
* 이 예제는 DTO클래스에 `@QueryProjection`을 해서 DTO도 Q타입 클래스를 만들도록 했기 때문에 기존 Projections보다 타입 세이프하다는 장점이 있지만, Querydsl에 더 의존적이라는 단점도 있다.

### Group By 최적화하기
* 일반적으로 MySQL에서 group by를 실행하면 group by 컬럼에 의한 Filesort라는 정렬 알고리즘이 추가적으로 실행된다. (이 쿼리는 index가 없다면 발생)
* Filesort가 발생하면 상대적으로 느려지기 때문에, order by null을 사용하면 된다.
* 하지만 Querydsl에서는 order by null을 지원하지 않는다.
* 그렇기 때문에 OrderByNull이라는 클래스를 만들어 이를 통해 order by null을 지원하도록 할 수 있다.

```java
public class OrderByNull extends OrderSpecifier { 
    public static final OrderByNull DEFAULT = new OrderByNull();
    
    private OrderByNull() {
        super(Order.ASC, NullExpression.DEFAULT, NullHandling.Default);
    }
}
```

```java
public List<Integer> useOrderByNull() {
    return queryFactory
        .select(member.age.sum())
        .from(member)
        .innerJoin(member.team, team)
        .groupBy(member.team)
        .orderBy(OrderByNull.DEFAULT)
        .fetch();
}
```

* order by null은 페이징 쿼리인 경우 사용하지 못한다.
  * 추가적인 정보로 정렬을 해야 할 경우 100건 이하라면 애플리케이션 메모리로 가져와 정렬하는 것이 좋다.
  * 일반적으로 DB자원보다 애플리케이션 자원이 더 싸기 때문에 효율적이다.

### 커버링 인덱스 사용하기
> 커버링 인덱스
> * 원하는 데이터를 인덱스에서만 추출할 수 있는 인덱스이다.
> * B-Tree 스캔만으로 원하는 데이터를 가져올 수 있으며, 컬럼을 읽기 위해 굳이 데이터 블록을 보지 않아도 된다.
> * 인덱스는 행 전체 크기보다 훨씬 작으며, 인덱스 값에 따라 정렬되기 때문에 Sequential Read 접근할 수 있기 때문에 커버링 인덱스를 사용하면 결과적으로 쿼리 성능을 비약적으로 올릴 수 있다.
> * 쿼리 내 모든 항목이 인덱스 칼럼으로만 이루어 지게 되며 인덱스 내부에서 쿼리가 완성되므로, DB 데이터 블록을 가져오지 않고 I/O만으로 이뤄지기 때문에 성능이 올라간다고 생각하면 된다.
* 커버링 인덱스는 쿼리를 충족시키는데 필요한 모든 컬럼을 가지고 있는 인덱스이다.
* select, where, ordre by, group by 등에서 사용되는 모든 컬럼이 인덱스에 포함된 상태로, No Offset 방식과 더불어 페이지 조회성능을 향상시키는 가장 보편적인 방법이다.
* 커버링 인덱스를 사용할 땐 from절에 subQuery에서 커버링 인덱스를 통해 필터를 하도록 하는게 보편적인데, Querydsl에서 JPQL은 from절에서 서브쿼리를 지원하지 않는다.
* 해결 방법은 두번의 select절을 이용하는 것이다.
  * 첫 번째 select절로 커버링 인덱스를 활용해 조회대상의 PK를 조회한다.
  * 그 후, 두 번째 select절로 해당 PK로 필요한 컬럼 항목들을 조회한다.

```java
public List<MemberDto> useCoveringIndex(int offset, int limit) {
    List<Long> ids = queryFactory
        .select(member.id)
        .from(member)
        .where(member.username.like("member%"))
        .orderBy(member.id.desc())
        .limit(limit)
        .offset(offset)
        .fetch();

    if(ids.isEmpty()) {
        return new ArrayList<>();
    }

    return queryFactory
        .select(new QMemberDto(
                member.username,
                member.age
        ))
        .from(member)
        .where(member.id.in(ids))
        .orderBy(member.id.desc())
        .fetch();
}
```
* 이 방식의 단점은 너무 많은 인덱스가 생길 수 있다는 점이지만, 결국 쿼리의 모든 항목이 인덱스로 필요하기 때문에 느린 쿼리가 발생할 때마다 새로운 신규 인덱스가 생성될 수 있다.
* 인덱스의 크기도 점점 커질 수 있는데 인덱스도 결국 데이터이기 때문에 들어가는 항목이 많아진다면 인덱스가 비대해진다는 단점이 있다.

