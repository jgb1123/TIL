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
