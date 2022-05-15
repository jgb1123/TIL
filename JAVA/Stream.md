# Stream

## 스트림이란?
* 다양한 데이터 소스를 표준화된 방법으로 다루기 위한 것이다. (jdk1.8~)
* 컬렉션(List, Set, Map), 배열 등으로부터 Stream을 만들 수 있다.
* Stream을 만들고 나면 똑같은 방식으로 작업을 통일할 수 있다.
* 데이터 소스 -> 스트림 -> 중간 연산 -> 최종 연산 순으로 진행된다.


## 스트림의 특징
* 스트림은 데이터 소스로부터 데이터를 읽기만할 뿐 변경하지 않는다. (원본을 변경하지 않는다.)
* 스트림은 Iterator처럼 일회용이다.(필요하면 다시 스트림을 생성해야 한다.)
* 최종 연산 전까지 중간연산이 수행되지 않는다. (지연된 연산)
* 스트림은 작업을 내부 반복으로 처리할 수 있다.
* 스트림의 작업을 병렬로 처리할 수 있다. (멀티쓰레드)
* 기본형 스트림이 있다. (IntStream, LongStream, DoubleStream, ...)
    * 오토박싱&언박싱의 비효율이 제거된다. (Stream<Integer>대신 IntStream사용)
    * 숫자와 관련된 유용한 메서드를 Stream<T>보다 더 많이 제공한다. (sum(), Average() 등)

## 스트림의 생성
### 1) 컬렉션
* collection인터페이스의 `stream()`으로 컬렉션을 스트림으로 변환한다.
```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
Stream<Integer> intStream = list.stream();  // list를 스트림으로 변환

intStream.forEach(System.out::print);   // 최종연산
intStream.forEach(System.out::print);   // 에러, Stream은 1회용
```

### 2) 배열
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

### 3) 임의의 수
#### 난수를 요소로 갖는 스트림 생성하기 (IntStream, LongStream 등)
```java
IntStream intStream = new Random().ints();  // 무한 스트림
intStream.limit(5).forEach(System.out::println);    // 5개의 요소만 출력한다.

IntStream intStream = new Random().ints(5); // 유한 스트림 5개만 반환한다. 
```
#### 지정된 범위의 난수를 요소로 갖는 스트림 생성하기 (IntStream, LongStream 등)
* `Random().ints(int start, int end)` start부터 end 이전까지의 범위를 갖는 무한 스트림
* `Random().ints(long streamSize, int start, int end)` 유한 스트림
### 4) 특정범위의 정수
#### 특정 범위의 정수를 요소로 갖는 스트림 생성하기 (IntStream, LongStream)
* `IntStream.range(int start, int end);` start부터 end 이전까지 
* `IntStream.rangeClosed(int start, int end);` start부터 end까지

### 5) 람다식
#### 람다식을 소스로 하는 스트림 생성하기
* `iterate(T seed, UnaryOperator<T> f)` 초기값(seed)에 무한스트림
* `generate(Supplier<T> s)` 초기값이 없으며 무한스트림이다.
``` java
Stream<Integer> evenStream = Stream.iterate(0, n->n+2);
Stream<Integer> randomStream = Stream.generate(Math::random); 
```
### 6) 파일과 빈 스트림

#### 파일을 소스로 하는 스트림 생성하기
* `Files.list(Path dir)` 파일 또는 디렉토리
* `Files.lines(Path path)` 파일의 내용을 라인 단위로 읽어서 스트림으로 만든다.
* `Files.lines(Path path, Charset cs)` 
* `lines()` BufferedReader(Text파일 읽을때 편리한 클래스)의 메서드이다. 

#### 비어있는 스트림 생성하기
`Stream.empty()` 빈 스트림을 생성한다.

## 스트림의 연산
### 중간 연산
* 연산 결과가 스트림인 연산으로, 반복적으로 적용 가능하다.

#### 1) skip(), limit()
* `skip(long n)` 스트림을 n만큼 건너 뛴다.
* `limit(long maxSize)` maxSize 이후의 요소들은 잘라낸다.
```java
IntStream intStream = IntStream.rangeClosed(1, 10); // 12345678910
intStream.skip(3).limit(5).forEach(System.out::print);  // 45678 
```
#### 2) filter(), distinct()
* `distinct()` 중복을 제거한다.
* `filter(Predicate<T> predicate)` 조건에 안 맞는 요소를 제거한다.
```java
IntStream intStream = IntStream.of(1, 2, 2, 3, 3, 3, 4, 5);
intStream.distinct().forEach(System.out::print);    // 12345

IntStream intStream = IntStream.rangeclosed(1, 10); //12345678910
intStream.filter(i->i%2==0).forEach(System.out::print); // 246810
```
#### 3) sorted()
* `sorted()` 스트림 요소의 기본 정렬(Comparable)로 정렬한다
* `sorted(Comparator<T> comparator)` 지정된 Comparator로 정렬한다.
##### 정렬 기준
1. Comparator의 comparing()으로 정렬기준 제공한다.
* `comparing(Function<T, U> keyExtractor)`
* `comparing(Function<T, U> keyExtractor,  Comparator<U> keyComparator)`
  * return type이 Comparator이다.

2. 추가 정렬 기준을 제공할 때는 thenComparing()을 이어붙여 사용한다.
* `thenComparing(Comparator<T> other)`
* `thenComparing(Function<T, U> keyExtractor)`
* `thenComparing(Function<T, U> keyExtractor, Comparator<U> keyComp)`

#### 4) map()
* `map(Function<T,R> mapper)` Stream<T> 에서 Stream<R>로 변환한다.
```java
Stream<File> fileStream = Stream.of(new File("Ex1.java"), new File("Ex1"));

Stream<String> filenameStream = fileStream.map(File::getName);


```
#### 5) peek()
* `peek(Consumer<T> action)` 스트림의 요소를 소비하지 않고 작업을 수행한다.
* 작업 중간 결과를 확인하는 용도로 사용한다. (디버깅용도)
`.peek(s->System.out.printf("filename=%s%n, s))`

#### 6) flatMap()
* 스트림의 스트림을 스트림으로 변환한다.
* 여러개의 문자열 배열을 하나의 문자열 배열인것 처럼 변환할 때 사용할 수 있다.
```java
Stream<String[]> strArrStream = Stream.of(
        new String[]{"abc", "def", "jkl"},
        new String[]{"ABC", "GHI", "JKL"}
);
Stream<String> strStream = strArrStream.flatMap(Arrays::stream);

strStream.map(String::toLowerCase)
        .distinct()
        .sorted()
        .forEach(System.out::print); // abcdefghijkl 출력
```

### Optional<T>
* T타입 객체의 래퍼클래스이다.
* Stream처럼 `filter()`, `map()`, `flatMap()`을 사용할 수 있다.
```java
public final class Optional<T> {
    private final T value;  // T타입의 참조변수이다.
        ...                 // 모든 종류의 객체를 저장할 수 있다.(null 포함)
}
```
* Optional을 이용해 null을 간접적으로 다룰 수 있다.
  * null을 직접 다루는 것은 위험하다. (NullPointerException 발생 위험)
  * null 체크를 하려면 if문이 필수인데 코드가 지저분해진다.

#### Optional<T> 객체 생성하기

```java
String str = "abc";
Optional<String> optVal = Optional.of(str);
Optional<String> optVal = Optional.of("abc");   // 위 두문장과 동일
Optional<String> optVal = Optional.of(null) // NullPinterException 발생
Optional<String> optVal = Optional.ofNullable(null); // OK
```
* null대신 빈 Optional<T> 객체를 사용하자.
```java
Optional<String> optVal = null; // null로 초기화는 바람직하지 않음
Optional<String> optVal = Optional.empty(); 빈 객체로 초기화
```

#### Optional<T> 객체의 값 가져오기
1. Optional객체의 값 가져오기(`get()`, `orElse()`. `orElseGet()`, `orElseThrow()`)
```java
Optinal<String> optVal = Optional.of("abc");
String str1 = optVal.get(); // 저장된 값을 반환한다. null이면 예외발생
String str2 = optVal.orElse("");    // 저장된 값이 null일때 빈문자열을 반환
String str3 = optVal.orElseGet(String::new);    // 람다식 사용가능(Supplier), 예외종류 지정 가능
string str4 = optVal.orElseThrow(NullPointerException::new); //널이면 예외 발생
//orElse()와 orElseGet()을 많이 이용한다.
```

2. Optional의 값이 null이면 false, 아니면 true를 반환(`isPresent()`)
```java
if(Optional.ofNullable(str).isPresent()){ // if문과 함께사용하여 null이 아닐때만 작업 수행
    System.out.println(str);
} 
```
#### 기본형 값을 감싸는 래퍼클래스
* OptionalInt, OptionalLong, OptionalDouble
* Optional<T>를 사용해도 되지만, 성능 때문에 사용한다.


### 최종 연산
* 연산 결과가 스트림이 아닌 연산으로, 단 한번만 적용 가능하다. (스트림의 요소를 소모)

#### 1) forEach()
* `forEach(Consumer<T> action)` 각 요소에 지정된 작업을 수행한다
* `forEachOrdered(Consumer<T> action)` 순서를 유지하여 작업을 수행한다. (병렬 스트림에서 사용)
```java
IntStream.range(1, 10).parallel().forEach(System.out::print);   //916458723
IntStream.range(1, 10).parallel().forEachOrdered(System.out::print);    // 123456789
```
#### 2) allMatch(), anyMatch(), noneMatch()
* `allMatch(Predicate<T> p)` 조건식을 모두 만족시키는지 확인한다
* `anyMatch(Predicate<T> p)` 조건식을 하나라도 만족하는지 확인한다.
* `noneMatch(Predicate<T> p)` 조건식을 모두 만족하지 않는지 확인한다.

 
#### 3) findFirst(), findAny()
* `findAny()` 스트림의 아무 요소 하나를 반환한다. (Optional로 반환한다.)
  * 병렬 스트림에서 filter와 같이 사용하여 조건에 맞는 요소를 반환한다.
* `findFirst()` 스트림의 첫 번째 요소 하나를 반환한다. (Optional로 반환한다.)
  * 직렬 스트림에서 사용한다.
```java
Optional<Student> result = stuStream.filter(s->s.getTotalScore() <= 100).findFirst();
Optional<Student> result = parallelStream.filter(s->s.getTotalScore() <= 100).findAny();
```
#### 4) count(), max(), min(), sum(), average()
* `count()` 스트림의 요소의 개수를 반환한다.
* `max(Comparator<T> comparator)` 기준에 따라 스트림의 최대값을 반환한다.
* `min(Comparator<T> comparator)` 기준에 따라 스트림의 최소값을 반환한다.
* `sum()` 모든 요소에 대한 합을 반환한다. (기본형 스트림에서만)
* `average()` 모든 요소에 대한 평균을 반환한다. (기본형 스트림에서만)

#### 5) reduce()
* `reduce()`는 스트림의 요소를 하나씩 줄여가며 누적연산을 수행한다.
* `reduce(BinaryOperator<T> accumulator)`초기값이 없기 때문에 Optional로 반환한다.
* `reduce(T identity, BinaryOperator<T> accumulator)` T로 반환한다.
  * identity - 초기값
  * accumulator - 이전 연산결과와 스트림의 요소에 수행할 연산
```java
int count = intStream.reduce(0, (a,b)->a+1);    //count()
int sum = intStream.reduce(0, (a,b)->a+b);  // sum()
int max = intStream.reduce(Integer.MIN_VALUE, (a,b)->a>b?a:b);  // max()
int min = intStream.reduce(Integer.MAX_VALUE, (a,b)->a<b?a:b);  // min()
```
* reduce()로의 처리방식은 아래와 같은 구조라 생각하면 된다.
   ```java
   //sum()
   int a = identity;
   
   for(int b : stream)
       a = a + b;
   ```

#### 6) collect()
* `collect(Collector collector)` Collector를 구현한 클래스의 객체를 매개변수로 한다.
* 주로 요소를 그룹화 하거나 분할한 결과를 컬렉션에 담아 반환하는데 사용한다.
* reduce()와 다르게 collect()는 **그룹별로 나눠서 처리**할 때 사용한다.
* `Collector`는 collect()에 필요한 메서드를 정의해놓은 인터페이스이다.
* `Collectors` 클래스는 다양한 기능의 collector를 제공한다. (Collector를 구현한 클래스)

##### Collectors
* `toList()`, `toSet()`, `toMap()`, `toCollection()` 스트림을 컬렉션으로 변환
* `toArray()` 스트림을 배열로 변환
   ```java
   Student[] stuNames = studentStream.toArray(Student[]::new);
   Object[] stuNames = studentStream.toArray();
   ```
* `counting()`, `summingInt()`, `maxBy()`, `minBy()` 스르팀의 통계정보 제공
   ```java
   long count = stuStream.collect(counting());
   long totalScore = stuStream.collect(summingInt(Sutdent::getTotalScore));
   Optional<Student> topStudent = stuStream
                  .collect(maxBy(Comparator.comparingInt(Student::getTotalScore)));
   ```
* `reducing()` 스트림을 리듀싱 한다.
   ```java
   Optinal<Integer> max = intStream.boxed().collect(reducing(Integer::max)); 
   long sum = intStream.boxed().collect(reducing(0, (a,b)->a+b));
   ```
* `joining()` 문자열 스트림의 요소를 모두 연결한다.
   ```java
   String studentNames = stuStrea.map(Student::getName).collect(joining());
   String studentNames = stuStrea.map(Student::getName).collect(joining(",")); //구분자
   ```


### 스트림의 그룹화와 분할
* collect()는 그룹화를 통해 나눠놓고 작업이 가능하다.

#### 1) partitioningBy()
* 스트림의 요소를 2분할 한다.
```java
Map<Boolean, Long> suNumBySex = stuStream
        .collect(partitioningBy(Student::isMale, counting()));  //분할 + 통계

Map<Boolean, Map<Boolean, List<Student>>> failedStuBySex = stuStream
        .collect(partitioningBy(Student::isMale,
                 partitioningBy(s->s.getScore() < 150));    //다중 분할
```

#### 2) groupingBy()
* 스트림의 요소를 그룹화 한다.
```java
Map<Integer, List<Student>> stuByBan = stuStream
        .collect(groupingBy(Student::getBan, toList()));    //그룹화

Map<Integer, Map<Integer, List<Student>>> stuByHakAndBan = stuStream
        .collect(groupingBy(Student::getHak,
                 groupingBy(Student::getBan)));   //다중 그룹화
```


___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
