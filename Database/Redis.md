# Redis
## Redis?
* Remote Dictionary Server의 약자로, 간단한 키-값 형태의 데이터 구조를 지원하는 오픈소스 NoSQL 데이터베이스이다.
* 간단한 키-값 형태의 데이터 구조를 지원하며 이러한 데이터를 메모리에 저장하여 빠른 읽기와 쓰기를 가능하게 한다.
* 따라서 캐싱, 세션 관리, 채팅 및 메시지, 세션 클러스터링 등에 많이 사용된다.

## Redis의 특징
### 성능
* 모든 Redis 데이터는 메모리에 저장되어 대기시간을 낮추고 처리량을 높인다.
* 읽기 및 쓰기의 작업 속도가 디스크 기반 데이터베이스보다 빠르다.

### 데이터 구조
* Redis의 데이터는 String, List, Set, Hash, Sorted Set, JSON 등 다양한 데이터 타입을 지원한다.
* 애플리케이션의 요구 사항에 알맞는 다양한 데이터 타입을 활용할 수 있다.

### 개발 용이성
* Redis는 쿼리문이 필요하지 않으며 단순한 명령 구조로 데이터의 저장 및 조회등이 가능하다.
* 또한 Java, Python, C, C++, C#, JavaScript 등의 다수의 언어를 지원한다.

### 영속성
* Redis는 영속성을 보장하기 위해 데이터를 디스크에 저장할 수 있다.
* 서버에 치명적인 문제가 발생하더라도 디스크에 저장된 데이터를 통해 복구가 가능하다.

### 싱글 쓰레드 방식
* Redis는 싱글 쓰레드 방식을 사용하여 한 번에 하나의 명령어만 처리한다.
* 따라서 연산이 원자성(Atomicity)을 갖고 처리하기 때문에 Race Condition이 거의 발생하지 않는다.

## Redis 설정
### build.gradle
```groovy
dependencies {
    ...
    implementation 'org.springframework.boot:spring-boot-starter-data-redis'
    ...
}
```

### application.yml
```yaml
spring:
  cache:
    type: redis
  redis:
    host: localhost
    port: 6379
    timeout: 1
```

### RedisConfig.class
```java
@Configuration
public class RedisConfig {
    @Value("${spring.data.redis.host}")
    private String host;

    @Value("${spring.data.redis.port}")
    private int port;
    
    @Value("${spring.data.redis.timeout}")
    private int timeout;

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        return new LettuceConnectionFactory(new RedisStandaloneConfiguration(host, port));
    }

    @Bean
    public RedisTemplate<String, String> redisTemplate() {
        RedisTemplate<String, String> redisTemplate = new RedisTemplate<>();
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new StringRedisSerializer());
        redisTemplate.setConnectionFactory(redisConnectionFactory());
        return redisTemplate;
    }
    
    @Bean
    public RedisCacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        RedisCacheConfiguration configuration = RedisCacheConfiguration
                .defaultCacheConfig()
                .entryTtl(Duration.ofHours(timeout))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer())) // redis 캐시 키 저장방식을 StringSeriallizer로 지정
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer())); // redis 캐시 값 저장방식을 GenericJackson2JsonRedisSerializer로 지정
        
        return RedisCacheManager.RedisCacheManagerBuilder.fromConnectionFactory(connectionFactory).cacheDefaults(configuration).build();
    }
}
```
#### RedisConnectionFactory
* Redis와의 연결을 설정한다. 
* Lettuce 라이브러리를 사용하여 Redis와의 연결을 설정한다.

#### RedisTemplate
* Redis에 데이터를 저장하고 조회하기 위한 Template이다.
* String 타입의 Key와 Value를 사용하며 해당 템플릿은 직접적으로 사용하는 것이 아니라 CacheManager를 통해 캐시관리에 사용된다.
* 

#### CacheManager
* Redis를 기반으로 한 캐시 기능을 관리하는 클래스이다.
* 캐시 관련 설정과 캐시된 데이터를 저장하고 조회하는 기능을 제공하여 Redis를 통한 효율적인 캐싱을 쉽게 구현할 수 있도록 도와준다.
* Redis와 연결된 RedisTemplate을 사용하여 캐시 데이터를 Redis에 저장하며 캐시된 데이터의 유효기간, 캐시 이름등을 설정할 수 있다. (`RedisCacheConfiguration`)

## 캐싱 적용
* @Cacheable 애너테이션을 사용하여 메서드에 캐시를 적용할 수 있다.
* 해당 애너테이션을 메서드에 추가하면 해당 메서드의 결과가 캐시에 저장되고 이후에 해당 메서드가 호출되면 캐시된 결과가 반환된다.

### @Cacheable 속성
#### value, cacheNames
* 캐시의 이름을 지정한다.
* 하나 이상의 캐시 이름을 지정할 수 있고 메서드의 결과가 이러한 이름의 캐시에 저장되며 같은 이름의 캐시가 여러 메서드에서 공유될 수 있다.
* 이 값을 지정하지 않으면 메서드 이름이 기본 캐시 이름으로 사용된다.
```java
@Cacheable("myCache") // "myCache"라는 이름의 캐시에 결과 저장
public Member getMember(Long memberId) {
        ...
}
```

#### key
* 캐시의 Key를 동적으로 지정할 수 있다.
* SpEL(스프링 표현 언어)을 사용하여 메서드의 매개변수나 메서드 내의 값을 참조하여 캐시 Key를 생성할 수 있다.
```java
@Cacheable(value = "myCache", key = "#memberId") // "myCache"에 memberId 값을 Key로 사용하여 결과 저장
public Member getMember(Long memberId) {
        ...
}
```

#### condition
* 캐시를 적용할지 여부를 지정하는 조건을 설정할 수 있다.
* SpEL을 사용하여 조건을 표현하며 조건이 참일 때만 캐시를 적용한다.
```java
@Cacheable(value = "myCache", condition = "#result != null") // 메서드의 결과가 null이 아닐 때만 캐시 적용
public Member getMember(Long memberId) {
        ...
} 
```

#### unless
* condition 속성과 반대되는 개념으로 캐시를 적용하지 않을 조건을 지정한다.
* SpEL을 사용하여 조건을 표현하며 조건이 참이면 캐시를 적용하지 않는다.
```java
@Cacheable(value = "myCache", unless = "#result == null") // 메서드의 결과가 null이면 캐시 적용 안함
public Member getMember(Long memberId) {
        // ...
}
```

#### keyGenerator
* 캐시 Key를 동적으로 생성하는데 사용할 커스텀 Key생성기를 지정한다.
* 복잡한 Key 생성 로직을 적용하고 싶을 때 사용한다.
```java
@Cacheable(value = "myCache", keyGenerator = "customKeyGenerator") // KeyGenerator를 구현한 customKeyGenerator를 사용하여 캐시 Key 생성
public Member getMember(Long memberId) {
        ...
} 
```

#### sync
* 스프링의 비동기 처리와 관련하여 캐싱 동작을 제어하는데 사용된다.
* 해당 속성을 true로 설정하면 캐시된 결과가 아직 생성되지 않았을 경우 새로운 캐시 값을 생성하는 메서드를 동기적으로 호출하게 된다.
```java
@Cacheable(value = "myCache", sync = true)
public Member getMember(Long memberId) {
        ... (새로운 캐시 값을 생성하는데 오래 걸리는 작업이 실행)
}  
```
* 위 예시처럼 캐시를 생성하는데 오래 걸리는 작업이 있을때, sync 속성을 true로 설정하면 이 작업이 동기적으로 처리되어 다른 쓰레드가 동시에 같은 캐시 값을 생성하지 않는다.

#### cacheResolver
* 캐시를 찾는 방법을 커스터마이징 하는데 사용된다.
* CacheResolver 인터페이스를 구현하는 CustomCacheResolver를 지정할 수 있다.
* 이를 통해 특정 조건에 따라 다른 캐시를 사용하거나 캐시 동작을 동적으로 컨트롤할 수 있다.
```java
@Cacheable(value = "myCache", cacheResolver = "customCacheResolver") // CacheResolver를 구현한 customCacheResolver를 사용
public Member getMember(Long memberId) {
        ...
} 
```
