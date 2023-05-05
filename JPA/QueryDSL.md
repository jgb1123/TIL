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
