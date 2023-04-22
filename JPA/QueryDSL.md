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
```
