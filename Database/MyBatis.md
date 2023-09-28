# MyBatis
## MyBatis?
* 관계형 데이터베이스 프로그래밍을 좀 더 쉽게 도와주는 SQLMapper기술이다.
* JDBC를 통해 RDBMS에 액세스하는 작업을 캡슐화하고, 기존 JDBC의 중복 작업을 간소화해준다.
* XML파일 형태인 mapper를 통해 SQL쿼리를 분리하는 환경을 제공하며, Java 객체와 매핑하는 작업을 도와준다.
* JDBC의 경우 Repository에서 바로 JDBC API쪽으로 접근하여 DB를 연결했지만, MyBatis를 사용할 경우 Repository와 JDBC API사이에 MyBatis가 위치해 편리한 Access를 제공한다.

## MyBatis의 장점
* JDBC보다 사용하기 편리하다.
* JPA와같은 ORM기술에 비해 학습의 부담이 적다.
* 복잡한 쿼리 또는 동적쿼리를 작성할 수 있다.
* SQL쿼리와 애플리케이션 코드와 분리할 수 있어 간결성 및 유지보수성이 향상된다.
* 빠른 개발과 생산성이 향상된다.

## 주요 컴포넌트
### MyBatis 설정파일
* DB의 접속정보 또는 Mapping파일의 경로나 alias등을 설정하는 XML파일이다.

### SqlSessionFactoryBuilder
* MyBatis 설정파일을 읽고 SqlSessionFactory를 생성한다.

### SqlSessionFactory
* SqlSession을 생성한다

### SqlSession
* SQL의 실행이나 트랜잭션을 관리한다.
* Thread-Safe 하지 않으므로 thread마다 필요에 따라 생성한다.

### Mapping File
* SQL과 객체 매핑 설정을 하는 XML파일이다.

