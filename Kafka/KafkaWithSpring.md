# Kafka With Spring

## 의존성 추가
```groovy
implementation 'org.springframework.kafka:spring-kafka'
```

## Config 설정
### ProducerConfig

```java
@EnableKafka
@Configuration
public class KafkaProducerConfig {
    @Bean
    public ProducerFactory<String, String> producerFactory() {
        Map<String, Object> properties = new HashMap<>();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "172.18.0.101:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);

        return new DefaultKafkaProducerFactory<>(properties);
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}
```
* `BOOTSTRAP_SERVERS_CONFIG` : Producer가 처음으로 연결할 Broker의 위치를 설정한다.
* `KEY_DESERIALIZER_CLASS_CONFIG` & `VALUE_DESERIALIZER_CLASS_CONFIG` : Producer가 Key와 Value값의 데이터를 Broker로 전송하기 전에 데이터를 byte array로 변환하는데 사용하는 직렬화 메커니즘을 설정한다.
  * Kafka는 네트워크를 통해 데이터를 전송하기 때문에 객체를 byte array로 변환하는 직렬화 과정이 필요함

### ConsumerConfig
```java
@EnableKafka
@Configuration
public class KafkaConsumerConfig {
    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> properties = new HashMap<>();
        properties.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "172.18.0.101:9092");
        properties.put(ConsumerConfig.GROUP_ID_CONFIG, "consumerGroupId");
        properties.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);

        return new DefaultKafkaConsumerFactory<>(properties);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String ,String> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory
                = new ConcurrentKafkaListenerContainerFactory<>();

        kafkaListenerContainerFactory.setConsumerFactory(consumerFactory());

        return kafkaListenerContainerFactory;
    }
}
```

### @EnableKafka
* `@EnableKafka` 어노테이션은 Spring 애플리케이션이 Kafka를 사용할 수 있도록 활성화해준다.
* `@KafkaListener` 어노테이션이 붙은 메서드를 감지하고 Kafk메시지를 처리하는 리스너로 등록한다.
* Kafka Producer 및 Consumer를 사용하는데 필요한 빈들을 자동으로 등록한다
  * ex) `KafkaTemplate`, `ProducerFactory`, `ConsumerFactory`
* 기본적인 Kafka 구성을 활성화하고 기본값을 설정한다
  * ex) Kafka 서버의 주소, Serializer 클래스 등

## Producer
```java
@RestController
@RequiredArgsConstructor
public class TestProducerController {
    private final KafkaTemplate<String, Object> kafkaTemplate;
    
    @PostMapping("/sendTest")
    public ResponseEntity<String> sendMessage(@RequestBody PostDto postDto) {
        kafkaProducer.send("my-topic", postDto.getMessage());
        return ResponseEntity.ok("success");
    }
}
```

## Consumer
```java
@Service
public class TestConsumer {
    @KafkaListener(topic = "my-topic", groupId="consumerGroupId")
    public void listener(Obejct data) {
      System.out.println(data);
    }
}
```

## 데이터 신뢰성
* Kafka를 통해 원하는 로직을 단 한번만 실행시키는 것이 보장되지 않을 수 있는 상황이 생긴다.
* 이러한 문제를 해결하기 위해 여러가지 고려해야 할 사항들이 있다.

### Consumer Group의 분리
* 예시로 이벤트를 Consume하고, RDB에 데이터를 저장하는 로직과 Redis에 pub을 하는 기능이 있을 수 있다.
* Redis pub 로직을 실행하고, RDB에 데이터를 저장하는데 만약 트랜잭션에서 문제가 발생하면 롤백 되므로 Kafka commit이 실패하게 된다.
* RDB는 롤백되지만 Redis에는 이미 pub이 보내지게 되고 이벤트를 다시 consume하기 때문에 Redis에서는 pub이 여러번 발생하게 된다.
* 이러한 문제를 방지하기 위해선 Consumer Group을 분리하여 DB에 데이터를 쓰는 Consumer Group과 Redis에 pub하는 Consumer Group을 분리해야 한다.
* Consumer Group을 분리하여 되면 각각의 작업을 처리하도록하면 데이터 일관성을 유지하고 중복 발행을 반지할 수 있다.

### Producer idempotent
* 이벤트 중복 생성을 방지하기 위해 enable.idempotent옵션을 true로 변경해야 한다. (kafka 3.0 이후 부터는 dafault)
  * retry는 0보다 크게, max.in.flight.requests.per.connection은 5 이하여야 한다.
* 또한 acks를 all로 설정한다.
  * akcs를 all로 설정해도 follower에 produce가 되지 않을 수 있다.
  * ack신호를 받아야 하는 in-sync replicas의 수를 명시해 줘야 한다.
  * 기본적으로 min.insync.replicas는 1로 설정되어 있기 때문에 leader에서만 ack를 받으면 produce가 된다.
  * 모든 replica에 produce가 잘 되는것을 보장받기 위해서는 이 옵션을 replication factor와 맞춰 설정해야 한다.

## Round-Robin 문제
* Kafka에서는 이벤트를 생성하는 여러 방법이 있다.
  * 특정 파티션을 지정하여 이벤트를 생성하게 할 수 있다.
  * key값을 지정하면 hash를 통해 생성된 갑승로 파티션을 지정하여 해당 파티션에 produce된다.
  * 기본적으로 고르게 파티션에 이벤트가 프로듀스 되도록 하는 방식이 있다. (Round-Robin)

### Produce시 문제
* 이벤트는 produce시 batch를 통해 여러개의 이벤트를 모아서 처리한다.
* Round-Robin 방식에서는 모든 파티션에 이벤트를 고르게 생성해야 한다.
* 따라서 produce 중, 특정 파티션에 produce가 어려운 상황이 발생하면 해당 파티션에 대한 produce 작업이 지연되어 대기하는 시간이 필요하게 된다.

### Consume시 문제
* 이벤트는 consume시 하나의 파티션에 대해서 하나의 consumer가 특정 batch size를 가지고 이벤트를 consume한다.
* Round-Robin 방식에서는 파티션마다 이벤트가 분산되어 있기 때문에 한번에 가져올 수 있는 이벤트 숫자가 적고, 더 여러번 배치작업을 수행해야 한다.

