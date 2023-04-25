# QueryDSL
## QueryDSL?
* JPA에는 쿼리 메서드의 기능과 @Query를 통해 많은 기능을 이용할 수 있다.
* 하지만 고정된 형태의 값을 가진다는 한계가 있다.
* 즉, 단순한 검색조건을 만들어야 하는 상황에서는 JPA만으로 충분히 사용 가능하지만, 복잡한 동적 쿼리가 필요할 경우엔 한계가 생긴다.
* 이때 QueryDSL을 사용하면 복잡한 동적쿼리들도 처리할 수 있게 된다.
* QueryDSL은 오픈소스 프로젝트로, JPQL을 Java코드로 작성할 수 있도록 하는 라이브러리이다.
* QueryDSL은 select, from, where등 쿼리 작성에 필요한 키워드를 메서드 형식으로 제공한다.

## 기본 Q-Type
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

## 검색 조건 쿼리
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

## 결과 조회 쿼리
* fetch() : 리스트를 조회, 결과가 없으면 빈 리스트를 반환한다.
* fetchOne() : 단 건 조회, 결과가 없으면 null, 결과가 둘 이상이면 예외발생
* fetchFirst() : 첫번째로 발견되는 결과 조회, limit(1).fetchOne()와 같다.
* fetchResult() : 페이징을 포함한 결과 조회
* fetchCount() : count 쿼리로 변경해서 count 수 조회가능

## 정렬
* orderBy()를 사용해 정렬할 수 있다.
* desc()를 이용해 내림차순으로, acs()를 이용해 오름차순으로 정렬할 수 있다.
* nullLast()나 nullFisrt()로 null 데이터에 순서를 부여할 수 있다.

## 페이징
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
