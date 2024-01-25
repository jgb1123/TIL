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
  * `KafkaTemplate`, `ProducerFactory`, `ConsumerFactory`
* 기본적인 Kafka 구성을 활성화하고 기본값을 설정한다
  * ex) Kafka 서버의 주소, Serializer 클래스 등
