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
        SpringApplication.run(DemoApplication.class, args);
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

#### scheduled 속성
* scheduled 속성을 이용해 스케줄링 방식을 설정할 수 있다.

##### FixedDelay
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

##### fixedRate
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

##### initialDelay
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

##### cron
* cron 표현식을 사용하여 작업을 예약할 수 있다.

```java
@Scheduled(cron = "* * * * * *")
public void cronTest() {
    System.out.println("cron Test");
}
```
* 첫 번째 *부터 초(0-59), 분(0-59), 시간(0-23), 일(1-31), 월(1-12), 요일(0:일, 1:월, 2:화, 3:수, 4:목, 5:금, 6:토) 이다.

> * Cron 표현식
> 
> * `*` : 모든 조건
> * `?` : 설정 값 없음(날짜와 요일에서만)
> * `-` : 범위를 지정할 때
> * `,` : 여러 값을 지정할 때
> * `/` : 증분값 (초기값과 증가치 설정에 사용)
> * `L` : 마지막 (지정할 수 있는 범위의 마지막 값 설정 시 사용) (날짜와 요일에서만)
> * `W` : 가장 가까운 평일을 설정할 때
> * `#` : N번째 주 특정 요일을 설정할 때 (요일에서만 사용)