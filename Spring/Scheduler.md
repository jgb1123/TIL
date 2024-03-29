# Scheduler
## Spring Batch?
* Batch란 여러 데이터를 한번에 묶어서 처리하는 작업 방식을 의미한다.
* Spring Batch란 대용량 일괄처리의 편의를 위해 설계된 Batch Framework이다.
* 주로 데이터 마이그레이션, 대용량 데이터의 일괄적인 처리, 정기적인 데이터 분석 등에 사용된다.
* Spring Batch에는 트랜잭션 관리, Chunk 기반 처리, 실패처리, 병렬처리, 스케줄링 등의 기능을 제공한다.
* Spring Batch를 사용하면 데이터 처리 작업을 단계별로 나누어 처리할 수 있다.
  * ItemReader, ItemProcessor, ItemWriter
  * 이한 단계뜰을 청크단위위로 실행하여 대용량 데이터를 효율적으로 처리할 수 있다.
* 즉, 스프링 배치는 대규모 데이터 처리 작업들을 간단하게 구현하고 관리할 수 있는 프레임워크이며 스프링과의 통합성과 확장성을 제공한다.

### Spring Batch의 핵심 조건
* 대량의 데이터를 가져오고 전달하며, 계산등의 처리를 할 수 있어야 한다.
* 사용자의 개입 없이 실행되어야 한다.
* 잘못된 데이터를 충돌이나 중단 없이 처리할 수 있어야 한다.
* 문제가 발생하였을 때 어디서 잘못되었는지 추적 가능해야 한다.(로깅, 알림)
* 지정한 시간 안에 처리를 완료해야 하며, 동시에 실행되는 다른 애플리케이션을 방해하지 않도록 해야 한다.

## Spring Scheduler?
* Scheduler란 특정한 시간에 어떠한 작업을 자동으로 실행시키는 작업이다.
* 대표적인 예로 Spring Scheduler와 Quartz 등이 있다.
* Spring Scheduler는 이러한 Scheduler 라이브러리이다.
* Spring Scheduler는 Spring Framework 사용 시 추가적인 의존성이 불필요하며, 어노테이션을 통해 간단하게 사용이 가능하다.
* Spring Scheduler는 하나의 Thread pool을 사용한다.

## Spring Scheduler 사용방법
### @EnableScheduling
* SpringBootApplication class에 `@EnableScheduling` 어노테이션을 추가한다.

```java
@EnableScheduling
@SpringBootApplication
public class SchedulerTestApplication {
    public static void main(String[] args) {
        SpringApplication.run(SchedulerTestApplication.class, args);
    }
}
```

### @Scheduled
* 스케줄링을 해야할 곳에 `@Scheduled` 애너테이션을 추가한다.

```java
@Service
public class SchedulerTestService {
    
    @Scheduled(fixedDelay = 1000)
    public void testService() {
        System.out.println("Scheduler Test");
    }
}
```
#### @Scheduled 조건
* Bean에 등록된 클래스에서만 사용 가능하다.
* 해당 메서드는 void 타입이어야 한다.
* 해당 메서드는 파라미터를 사용할 수 없다.

### @Scheduled 속성
* scheduled 속성을 이용해 스케줄링 방식을 설정할 수 있다.

#### FixedDelay
* milliseconds 단위로 이전 task의 종료 시점으로부터 정의된 시간만큼 지난 후 task를 실행한다.

```java
@Scheduled(fixedDelay = 1000)
public void fixedDelayTest() {
    System.out.println("fixedDelay Test");
}
```

```
@Scheduled(fixedDelayString = "1000")
public void fixedDelayTest() {
    System.out.println("fixedDelayString Test");
}
```
* fixedDelayString는 fixedRate와 같고 값만 문자열로 작성한다

#### fixedRate
* milliseconds 단위로 이전 task의 시작시점으로부터 정의된 시간만큼 지난 후 task를 실행한다.

```java
@Scheduled(fixedRate = 1000)
public void fixedDelayTest() {
    System.out.println("fixedRate Test");
}
```

```java
@Scheduled(fixedRateString = "1000")
public void fixedDelayTest() {
    System.out.println("fixedRate Test");
}
```
* fixedRateString는 fixedRate와 같고 값만 문자열로 작성한다.

#### initialDelay
* 스케줄러에서 메서드가 등록되자마자 수행하는 것이 아닌 초기 지연시간을 설정한다.

```java
@Scheduled(fixedRate = 5000, initialDelay = 30000)
public void initialDelayTest() {
    System.out.println("initialDelay Test");
}
```
* 위와같이 사용하면 30초의 대기시간 후 5초마다 동작한다.

```java
@Scheduled(fixedRate = 5000, initialDelayString = "30000")
public void initialDelayTest() {
    System.out.println("initialDelay Test");
}
```
* initialDelayString은 initialDelay와 같고 값만 문자열로 작성한다.

#### cron
* cron 표현식을 사용하여 작업을 예약할 수 있다.

```java
@Scheduled(cron = "* * * * * *")
public void cronTest() {
    System.out.println("cron Test");
}
```
* 첫 번째 *부터 초(0-59), 분(0-59), 시간(0-23), 일(1-31), 월(1-12), 요일(0:일, 1:월, 2:화, 3:수, 4:목, 5:금, 6:토) 이다.

##### cron 표현식
* `*` : 모든 조건
* `?` : 설정 값 없음(날짜와 요일에서만)
* `-` : 범위를 지정할 때
* `,` : 여러 값을 지정할 때
* `/` : 증분값 (초기값과 증가치 설정에 사용)
* `L` : 마지막 (지정할 수 있는 범위의 마지막 값 설정 시 사용) (날짜와 요일에서만)
* `W` : 가장 가까운 평일을 설정할 때
  * `10W` 10일이 평일이면 10일, 10일이 토요일이면 9일, 10일이 일요일이면 11일에 실행
* `#` : N번째 주 특정 요일을 설정할 때 (요일에서만 사용)
  * `4#2` 2째주 목요일에 실행

##### cron 예시
* `0 0 18 * * *` 매일 18시에
* `0 0 14 10,20 * ?` 매달 10일, 20일 14시에
* `0 0 22 L * ?` 매달 마지막날 22시에
* `0 0 0/1 * * *` 1시간 마다
* `0 0/5 9,18 * * *` 매일 9시00분 ~ 9시55분, 18시00분~18시55분 사이에 5분 마다
* `0 0/5 9-18 * * *` 매일 9시00분 ~ 18시55분 사이에 5분 마다
* `0 30 10 1 * *` 매달 1일 10시 30분에
* `0 30 10 ? 3 1-5` 매년 3월내 월~금 10시 30분에
* `0 30 30 ? * 6L` 매달 마지막 토요일 10시 30분에

##### zone
* cron 표현식을 사용했을 때 사용할 time zone으로, 따로 설정하지 않으면 기본적으로 Local의 time zone이다.
```java
@Scheduled(cron = "* * * * * *", zone = "Asia/Seoul")
public void cronTest() {
    System.out.println("cron Test");
} 
```

#### @Scheduled 속성 선택
* 각각의 속성의 특징을 잘 이해하고 상황에 맞게 사용할 줄 알아야 한다.
* `fixedDelay`같은 경우에는 내부 로직이 돌아가는 시간도 고려되기 때문에 정확히 원하는 시간마다 정확히 맞춰서 해당 로직을 실행시키고자 할 땐 문제가 생기기도 한다.
* `fixedRate`같은 경우에는 원하는 정확한 시간마다 반복할 순 있지만, 내부 로직의 실행 시간까지 고려를 해야하거나, 특정 세부적인 케이스에서는 사용하는 데에는 한계가 있다. (밤 12부터 아침6시까지는 5분마다 실행, 각 정각마다 실행 등)
* 그에 반면 cron의 경우 여러 세부적인 케이스에 대해서 다양한 설정이 가능하다는 장점이 있다. 하지만 내부 로직이 돌아가는 시간도 고려해야 하는 상황에는 부적절 할 수 있다.
* 여러 옵션들 중 그 상황에 가장 잘 맞는 옵션을 선택해야 한다.
* 보통 `fixedDalay`와 `cron`으로 대부분 케이스의 커버가 가능하다.

### Thread Pool
```java
@Component
@Slf4j
public class TestScheduler {
	
    @Scheduled(fixedRate = 1000)
    public void test1() throws InterruptedException {
    	Thread.sleep(10000);      // 10초 대기
        log.info("test1 : " + LocalDateTime.now());
        
    }
    
    @Scheduled(fixedRate = 1000)
    public void test2() {
        log.info("test1 : " + LocalDateTime.now());   
    }
}
```
* 위와 같은 상황에서는 test1에서의 작업 때문에 test2는 1초마다 한번씩 돌 수 없는 상황이 생긴다.
* Scheduler를 사용 시 이러한 문제를 해결하기 위해 단일 Thread가 아닌 멀티 쓰레드로 동작하도록 해야 한다.

#### Config Class를 통한 설정
```java
@Configuration
public class SchedulerConfig implements SchedulingConfigurer {

	@Override
	public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
		ThreadPoolTaskScheduler threadPoolTaskScheduler = new ThreadPoolTaskScheduler();

        threadPoolTaskScheduler.setPoolSize(5);
        threadPoolTaskScheduler.setThreadPrefix("Thread - ");
        threadPoolTaskScheduler.initialize();
        
		taskRegistrar.setTaskScheduler(threadPoolTaskScheduler);
	}
}
```

#### application.properties를 통한 설정
```properties
spring.task.scheduling.pool.size=5
```


### Shedlock
* Spring Scheduler에서는 여러 대의 인스턴스에 대한 클러스터링 기능은 제공하지 않는다.
* 따라서 서버의 인스턴스가 여러 대로 늘어나야 하는 상황에서 Scheduler가 중복으로 실행되어 문제를 일으킬 수 있다.
* ShedLock은 분산 스케줄러는 아니고 단지 Lock을 통해 스케줄러의 중복 실행을 방지해줄 뿐이다.
  * 분산 스케줄러가 필요하다면 Quartz와 같은 분산 스케줄러를 사용하는 것이 좋다.
* ShedLock은 병렬로 실행할 준비는 되지 않았지만 안전하게 반복 실행할 수 있는 예약된 작업이 있는 상황에서 사용하도록 설계되었다.
* 여러 스케줄러가 동시에 실행될 때 하나의 스케줄러가 락을 걸고 작업을 수행하며, 작업을 마친 스케줄러는 락을 반환하는 구조이다.
* 이러한 Shedlock의 구현에는 반드시 저장소가 필요하다.

#### Shedlock 사용법
##### @EnableSchedulerLock
* `@EnableScheduling`이 있는곳에 `@EnableSchedulerLock`를 함께 선언해 주면 된다.

##### @SchedulerLock
* `@SchedulerLock` Lock을 사용할 스케줄링 Task에 선언해주면 된다.
  * 해당 애너테이션이 달린 메서드만 Lock을 사용하게 되며, 다른 스케줄링 Task들은 ShedLock 라이브러리가 무시한다.
* Lock에 대한 이름을 지정할 수 있고, 동일한 이름을 가진 task는 동시에 오직 하나만 실행된다.
```java
@Component
@Slf4j
public class TestScheduler {

  @Scheduled(cron = "0 * * * * *")
  @SchedulerLock(name = "cronTestLock", lockAtLeastFor = "20s", lockAtMostFor = "50s")
  public void cronTest() throws InterruptedException {
    Thread.sleep(10000);      // 10초 대기
    log.info("test1 : " + LocalDateTime.now());
  }
}
```
* `name` : Lock의 네임을 지정한다. 다른 스케줄러 Task들과 중복되지 않도록 유니크하게 설정해야 한다.
* `defaultLockAtLeastFor`, `lockAtLeastFor` : Lock이 유지되는 최소 시간이다.
  * Task의 반복 시간이 매우 짧으며, 수행 시간이 매우 빠를 경우 중복 실행이 일어나는 문제를 방지할 수 있다.
* `defaultLockAtMostFor`, `lockAtMostFor` : Lock이 유지되는 최대 시간이다.
  * Task의 수행 시간이 매우 길어지거나 끝나지 않을 경우 다음 Task가 실행되지 않는 문제를 방지할 수 있다.
* `lockAtLeastFor`, `lockAtMostFor`를 지정하지 않으면 `defaultLockAtLeastFor`, `defaultLockAtMostFor`이 적용된다.

##### Lock Provider
* Shedlock에는 Redis, JDBC, MongoDB등 많은 Provider를 제공하고 있다.
* 따라서 필요에 맞게 선택하면 되고, 제공되는 Provider들과 필요한 데이터베이스 테이블 DDL, 설정 법 등은 [Shedlock Github](https://github.com/lukas-krecan/ShedLock)에 자세히 소개되어 있다.
* 필요에 맞게 선택 후 설정을 완료하면 스케줄링 Task가 실행되면 Lock이 생성되어 데이터베이스에 저장되게 된다.
* Shedlock은 히스토리를 남기지 않고 Lock유지가 필요한 시간만큼만 데이터가 존재하다가 지워진다.
  * 휘발성으로 데이터가 관리되기 때문에 in-memory DB를 사용해도 좋다.