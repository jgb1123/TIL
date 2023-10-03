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

## MyBatis 프로세스
### 애플리케이션 실행 시
* 애플리케이션이 SqlSessionFactoryBuilder를 사용하여 SqlSessionFactory를 빌드하도록 요청한다.
* SqlSessionFactoryBuilder는 MyBatis 설정 파일을 읽어와 이를 기반으로 SqlSessionFactory를 생성한다.

### 클라이언트 요청 시
* 애플리케이션은 SqlSessionFactory에서 SqlSession을 생성한다.
* SqlSession은 데이터베이스와의 연결을 관리하며, Mapper Interface를 통해 데이터베이스 작업을 수행한다.
* 애플리케이션은 SqlSession을 사용하여 Mapper Interface의 구현 객체를 가져온다.
* 애플리케이션은 Mapper Interface의 메서드를 호출한다.
* Mapper Interface의 구현 객체는 SqlSession 메서드를 호출하고 SQL 실행을 요청한다.
* SqlSession은 Mapping File에서 실행할 SQL을 찾아서 데이터베이스에 대해 실행한다.

## 예제
### build.gradle 의존성 추가
```groovy
dependencies {
    ...
    implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:{Version}'
    ...
}
```

### DB 설정 (H2 기준)
```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
```

### MyBatis 설정
* `src/main/resources/mybatis-config.xml` 파일 생성
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- DB 연결 정보 설정 -->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                <property name="driver" value="org.h2.Driver" />
                <property name="url" value="jdbc:h2:mem:testdb" />
                <property name="username" value="sa" />
                <property name="password" value="password" />
            </dataSource>
        </environment>
    </environments>

    <!-- MyBatis 매퍼 파일 위치 설정 -->
    <mappers>
        <mapper resource="com/example/mapper/UserMapper.xml" />
    </mappers>
</configuration>

```


### Mapper 인터페이스 작성
```java
@Mapper
public interface UserMapper {
    List<User> getAllUsers();
    User getUserById(Long id);
    void insertUser(User user);
    void updateUser(User user);
    void deleteUser(Long id);
}
```

### Mapper XML 파일 작성
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.UserMapper">
    <select id="getAllUsers" resultType="com.example.model.User">
        SELECT * FROM users
    </select>

    <select id="getUserById" resultType="com.example.model.User">
        SELECT * FROM users WHERE id = #{id}
    </select>

    <insert id="insertUser" parameterType="com.example.model.User">
        INSERT INTO users (name, email) VALUES (#{name}, #{email})
    </insert>

    <update id="updateUser" parameterType="com.example.model.User">
        UPDATE users SET name = #{name}, email = #{email} WHERE id = #{id}
    </update>

    <delete id="deleteUser" parameterType="java.lang.Long">
        DELETE FROM users WHERE id = #{id}
    </delete>
</mapper>
```

### Service, Controller 작성
```java
@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;

    public List<User> getAllUsers() {
        return userMapper.getAllUsers();
    }

    public User getUserById(Long userId) {
        return userMapper.getUserById(userId);
    }

    public void insertUser(User user) {
        userMapper.insertUser(user);
    }

    public void updateUser(User user) {
        userMapper.updateUser(user);
    }

    public void deleteUser(Long userId) {
        userMapper.deleteUser(userId);
    }
}
```

```java
@RestController
@RequestMapping("/users")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @GetMapping("/{userId}")
    public User getUserById(@PathVariable Long userId) {
        return userService.getUserById(userId);
    }

    @PostMapping
    public void insertUser(@RequestBody User user) {
        userService.insertUser(user);
    }

    @PutMapping("/{userId}")
    public void updateUser(@PathVariable Long userId, @RequestBody User user) {
        user.setId(userId);
        userService.updateUser(user);
    }

    @DeleteMapping("/{userId}")
    public void deleteUser(@PathVariable Long userId) {
        userService.deleteUser(userId);
    }
}
```
