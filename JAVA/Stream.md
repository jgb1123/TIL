# Stream

## 스트림이란?
* 다양한 데이터 소스를 표준화된 방법으로 다루기 위한 것이다. (jdk1.8~)
* 컬렉션(List, Set, Map), 배열 등으로부터 Stream을 만들 수 있다.
* Stream을 만들고 나면 똑같은 방식으로 작업을 통일할 수 있다.
* 데이터 소스 -> 스트림 -> 중간 연산 -> 최종 연산 순으로 진행된다.

* 중간 연산 연산결과가 스트림인 연산. 반복적으로 적용 가능하다.
* 최종 연산 연산결과가 스트림이 아닌 연산. 단 한번만 적용 가능하다. (스트림의 요소를 소모)


## 스트림의 특징
* 스트림은 데이터 소스로부터 데이터를 읽기만할 뿐 변경하지 않는다. (원본을 변경하지 않는다.)
* 스트림은 Iterator처럼 일회용이다.(필요하면 다시 스트림을 생성해야 한다.)
* 최종 연산 전까지 중간연산이 수행되지 않는다. (지연된 연산)
* 스트림은 작업을 내부 반복으로 처리할 수 있다.
* 스트림의 작업을 병렬로 처리할 수 있다. (멀티쓰레드)
* 기본형 스트림이 있다. (IntStream, LongStream, DoubleStream, ...)
    * 오토박싱&언박싱의 비효율이 제거된다. (Stream<Integer>대신 IntStream사용)
    * 숫자와 관련된 유용한 메서드를 Stream<T>보다 더 많이 제공한다. (sum(), Average() 등)


## 스트림 만들기
### 컬렉션
* collection인터페이스의 `stream()`으로 컬렉션을 스트림으로 변환한다.
```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
Stream<Integer> intStream = list.stream();  // list를 스트림으로 변환

intStream.forEach(System.out::print);   // 최종연산
intStream.forEach(System.out::print);   // 에러, Stream은 1회용
```

### 배열
#### 객체 배열로부터 스트림 생성
* `Stream.of(T... values)` 가변인자들을 변환
* `Stream.of(T[])` 배열을 변환
* `Arrays.stream(T[])` Arrays class의 stream()이란 메서드로 배열을 변환
* `Arrays.stream(T[] array, int start, int end)`  배열의 인덱스 start부터 end 이전까지만 변환한다.

#### 기본형 배열로부터 스트림 생성
* `InteStream.of(int...values)`
* `IntStream.of(int[])`
* `Array.stream(int[])`
* `Array.stream(int[] array, int start, int end)`

### 임의의 수
#### 난수를 요소로 갖는 스트림 생성하기
```java
IntStream intStream = new Random().ints();  // 무한 스트림
intStream.limit(5).forEach(System.out::println);    // 5개의 요소만 출력한다.

IntStream intStream = new Random().ints(5); // 유한 스트림 5개만 반환한다. 
```
#### 지정된 범위의 난수를 요소로 갖는 스트림 생성하기
* `ints(int start, int end)` 무한 스트림
* `ints(long streamSize, int start, int end)` 유한 스트림

#### 특정 범위의 정수를 요소로 갖는 스트림 생성하기(IntStream, LongStream)
* `IntStream.range(int start, int end);` start부터 end 이전까지
* `IntStream.rangeClosed(int start, int end);` start부터 end까지

___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
