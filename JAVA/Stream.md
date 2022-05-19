# Stream

## 스트림이란?
* 다양한 데이터 소스를 **표준화된 방법**으로 다루기 위한 것이다. (jdk1.8~)
* 컬렉션(List, Set, Map), 배열 등으로부터 Stream을 만들 수 있다.
* Stream을 만들고 나면 똑같은 방식으로 작업을 통일할 수 있다.
* 데이터 소스 -> 스트림 -> 중간 연산 -> 최종 연산 순으로 진행된다.


## 스트림의 특징
* 스트림은 데이터 소스로부터 데이터를 읽기만할 뿐 변경하지 않는다. (**원본을 변경하지 않는다.**)
  ```java
  List<Integer> list = Arrays.asList(3, 1, 5, 4, 2);
  List<Integer> sortedList = list.stream().sorted()
                    .collect(Collectors.toList());
  System.out.println(list);         // [3, 1, 5, 4, 2]로 원본은 변경 X
  System.out.println(sortedList)    // [1, 2, 3, 4, 5]
  ```
* 스트림은 Iterator처럼 **일회용**이다.(필요하면 다시 스트림을 생성해야 한다.)
* 최종 연산 전까지 중간연산이 수행되지 않는다. (지연된 연산)
  ```java
  IntStream intStream = new Random().ints(1, 46);   // 1~45범위의 무한 스트림
  intStream.distinct().limit(6).sorted()            // 중간연산
            .forEach(i->System.out.print(i+","));   // 최종연산
  ```
* 스트림은 작업을 내부 반복으로 처리할 수 있다.
  ```java
  for(String str : strList) 
        System.out.println(str);
  
  stream.forEach(System.out::println); // 위와 동일
  ```
* 스트림의 작업을 병렬로 처리할 수 있다. (멀티쓰레드)
  ```java
  Stream<String> strStream = Stream.of("dd", "aa", "CCC", "b");
  int sum = strStream.parallel()        //병렬 스트림으로 속성만 변경
                .mapToInt(s->s.length()).sum(); // 모든 문자열의 길이의 합
  ```
* 기본형 스트림이 있다. (IntStream, LongStream, DoubleStream, ...)
    * 오토박싱&언박싱의 비효율이 제거된다. (Stream<Integer>대신 IntStream사용)
    * 숫자와 관련된 유용한 메서드를 Stream<T>보다 더 많이 제공한다. (sum(), Average() 등)
    * 데이터 소스가 기본형일때만 쓸 수 있다.
## 스트림의 생성
### 1) 컬렉션
* Collection인터페이스의 `stream()`으로 컬렉션을 스트림으로 변환한다.
  ```java
  List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
  Stream<Integer> intStream = list.stream();  // list를 스트림으로 변환
  
  intStream.forEach(System.out::print);   // 최종연산
  intStream.forEach(System.out::print);   // 에러, Stream은 1회용
  ```

### 2) 배열
#### 객체 배열로부터 스트림 생성
* `Stream<T>` `Stream.of(T... values)` 가변인자들을 변환
* `Stream<T>` `Stream.of(T[])` 배열을 변환
* `Stream<T>` `Arrays.stream(T[])` Arrays class의 stream()이란 메서드로 배열을 변환
* `Stream<T>` `Arrays.stream(T[] array, int start, int end)`  배열의 인덱스 start부터 end 이전까지만 변환한다.
  ```java
  Stream<String> strStream = Stream.of("a", "b", "c");
  Stream<String> strStream = Stream.of(new String[]{"a", "b", "c"});
  Stream<String> strStream = Arrays.stream(new String[]{"a", "b", "c"});
  Stream<String> strStream = Arrays.stream(new String[]{"a", "b", "c"},0, 2); // a와 b만 들어간다.
  ```

#### 기본형 배열로부터 스트림 생성
* `IntStream` `IntStream.of(int...values)`
* `IntStream` `IntStream.of(int[])`
* `IntStream` `Array.stream(int[])`
* `IntStream` `Array.stream(int[] array, int start, int end)`

### 3) 임의의 수
#### 난수를 요소로 갖는 스트림 생성하기 (IntStream, LongStream, DoubleStream)
* 난수 스트림을 반환하는 `Random()` 클래스를 이용한다. (객체를 생성해야 한다.)
  ```java
  IntStream intStream = new Random().ints();  // 무한 스트림
  intStream.limit(5).forEach(System.out::println);    // 5개의 요소만 출력한다.
  
  IntStream intStream = new Random().ints(5); // 유한 스트림. 5개만 반환한다. 
  ```
#### 지정된 범위의 난수를 요소로 갖는 스트림 생성하기 (IntStream, LongStream, DoubleStream)
* `Random().ints(int start, int end)` start부터 end 이전까지의 범위를 갖는 무한 스트림
* `Random().ints(long streamSize, int start, int end)` 유한 스트림
### 4) 특정범위의 정수
#### 특정 범위의 정수를 요소로 갖는 스트림 생성하기 (IntStream, LongStream)
* `IntStream.range(int start, int end);` start부터 end 이전까지 
* `IntStream.rangeClosed(int start, int end);` start부터 end까지

### 5) 람다식
#### 람다식을 소스로 하는 스트림 생성하기
* `iterate(T seed, UnaryOperator<T> f)` 초기값(seed)이 있는 무한스트림 (이전 요소에 종속적)
* `generate(Supplier<T> s)` 초기값이 없는 무한스트림이다. (이전 요소에 독립적)
  ``` java
  Stream<Integer> evenStream = Stream.iterate(0, n->n+2); // 0, 2, 4, ...
  Stream<Integer> randomStream = Stream.generate(Math::random);
  ```
### 6) 파일과 빈 스트림

#### 파일을 소스로 하는 스트림 생성하기
* `Stream<Path>` `Files.list(Path dir)` 파일 또는 디렉토리
* `Stream<String>` `Files.lines(Path path)` 파일의 내용을 라인 단위로 읽어서 스트림으로 만든다. (Log파일분석이나 다량의 Text파일 처리할때 좋다)
* `Stream<String>` `Files.lines(Path path, Charset cs)` 
* `Stream<String>` `lines()` BufferedReader(Text파일 읽을때 편리한 클래스)의 메서드이다. 

#### 비어있는 스트림 생성하기
* `Stream` `Stream.empty()` 빈 스트림을 생성한다.

## 스트림의 연산
### 중간 연산
* 연산 결과가 스트림인 연산으로, 반복적으로 적용 가능하다.

#### 1) skip(), limit()
* `Stream<T>` `skip(long n)` 스트림을 n만큼 건너 뛴다.
* `Stream<T>` `limit(long maxSize)` maxSize 이후의 요소들은 잘라낸다.
```java
IntStream intStream = IntStream.rangeClosed(1, 10); // 12345678910
intStream.skip(3).limit(5).forEach(System.out::print);  // 45678 
```
#### 2) filter(), distinct()
* `Stream<T>` `distinct()` 중복을 제거한다.
* `Stream<T>` `filter(Predicate<T> predicate)` 조건에 안 맞는 요소를 제거한다.
```java
IntStream intStream = IntStream.of(1, 2, 2, 3, 3, 3, 4, 5);
intStream.distinct().forEach(System.out::print);    // 12345

IntStream intStream = IntStream.rangeclosed(1, 10); //12345678910
intStream.filter(i->i%2==0).forEach(System.out::print); // 246810
```
#### 3) sorted()
* `Stream<T>` `sorted()` 스트림 요소의 기본 정렬(Comparable)로 정렬한다
* `Stream<T>` `sorted(Comparator<T> comparator)` 지정된 Comparator로 정렬한다.
```java
strStream.sorted()  // 기본 정렬
strStream.sorted(Comparator.naturalOrder())  // 기본 정렬 
strStream.sorted((s1,s2)->s1.compareTo(s2));  // 람다식 
strStream.sorted(String::compareTo);    // 위 문장과 동일 
// CCaaabccdd 출력        
        
strStream.sorted(Comparator.reverseOrder()) // 기본 정렬의 역순
strStream.sorted(Comparator.<String>naturalOrder().reversed()) // 이렇게는 잘 안씀
// ddccbaaaCC 출력
        
strStream.sorted(String.CASE_INSENSITIVE_ORDER) //대소문자 구별 안함
// aaabCCccdd 출력
        
strStream.sorted(String.CASE_INSENSITIVE_ORDER.reversed()) // 위문장 역순
// ddCCccbaaa 출력
        
strStream.sorted(Comparator.comparing(String::length))  // 길이 순 정렬 
strStream.sorted(Comparator.comparingInt(String::length))   //오토박싱 x 
// bddCCccaaa 출력
        
strStream.sorted(Comparator.comparing(String::length).reversed()) // 위문장 역순
// aaaddCCccb 출력
```


##### 정렬 기준
1. Comparator의 comparing()으로 정렬기준 제공한다.
* `Comparator` `comparing(Function<T, U> keyExtractor)`
* `Comparator` `comparing(Function<T, U> keyExtractor,  Comparator<U> keyComparator)`
```java
studentStream.sorted(Comparator.comparing(Student::getBan)) // 반별로 정렬
             .forEach(System.out::println);
```


2. 추가 정렬 기준을 제공할 때는 thenComparing()을 이어붙여 사용한다.
* `thenComparing(Comparator<T> other)`
* `thenComparing(Function<T, U> keyExtractor)`
* `thenComparing(Function<T, U> keyExtractor, Comparator<U> keyComp)`
```java
studentStream.sorted(Comparator.comparing(Student::getBan) // 반별로 정렬
                .thenComparing(Student::getTotalScore)  // 총점별 정렬
                .thenComparing(Student::getName))   // 이름별 정렬
                .forEach(System.out::println);
```


#### 4) map()
* `Stream<R>``map(Function<T,R> mapper)` Stream<T> 에서 Stream<R>로 변환한다.
```java
Stream<File> fileStream = Stream.of(new File("Ex1.java"), new File("Ex1"));

Stream<String> filenameStream = fileStream.map(File::getName);
```

#### 5) peek()
* `Stream<T>` `peek(Consumer<T> action)` 스트림의 요소를 소비하지 않고 작업을 수행한다.
* 작업 중간 결과를 확인하는 용도로 사용한다. (디버깅 용도)
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
#### 7) boxed()
* `boxed()` 기본형 요소(int, long, double)들을 래퍼클래스(Integer, Long, Double)로 박싱해서 Stream을 생성한다.
```java
Int[] intArray = {1, 2, 3, 4, 5};
IntStream intStream = Arrays.stream(intArray);
intStream.boxed().forEach(obj->System.out.print(obj.intValue()));   // Integer로 박싱 후 출력
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
* **null이 될 수 있는 값은 Optional 객체에 넣어서 쓰면 된다.** (NullPointerException을 방지할 수 있다.)
#### Optional<T> 객체 생성하기

```java
String str = "abc";
Optional<String> optVal = Optional.of(str);
Optional<String> optVal = Optional.of("abc");   // 위 두문장과 동일
Optional<String> optVal = Optional.of(null) // NullPointerException 발생
Optional<String> optVal = Optional.ofNullable(null); // OK
```
* null대신 빈 Optional<T> 객체를 사용하자.
```java
Optional<String> optVal = null; // null로 초기화는 가능하나 바람직하지 않음
Optional<String> optVal = Optional.empty(); // 빈 객체로 초기화

String str = ""; // String 초기화 시 null이 아닌 빈 문자열로 초기화 하는 이유와 같음.
```

#### Optional<T> 객체의 값 가져오기
* `T` `get()` 저장된 값을 반환한다. null이면 발생
* **`T` `orElse(T other)` 저장된 값이 null이면 other 반환한다.**
* **`T` `orElseGet(Supplier<T> supplier)` 값이 null이면 람다식 결과를 반환한다.**
* `T` `orElseThrow()` 값이 null이면 예외를 Throw한다.
  ```java
  Optinal<String> optVal = Optional.of("abc");
  String str1 = optVal.get(); // 저장된 값을 반환한다. null이면 예외발생
  String str2 = optVal.orElse("");    // 저장된 값이 null일때 빈문자열을 반환
  String str3 = optVal.orElseGet(String::new);    // 람다식 사용가능(Supplier), 예외종류 지정 가능
  string str4 = optVal.orElseThrow(NullPointerException::new); //널이면 예외 발생
  ```

* `boolean` `isPresent()` Optional의 값이 있으면 true를, null이면 false를 반환한다.
  ```java
  if(Optional.ofNullable(str).isPresent()){ // if문과 함께사용하여 null이 아닐때만 작업 수행
      System.out.println(str);
  } 
  ```
* 0을 저장한건지 아예 저장 안한건지도 `isPresent()`로 확인 가능하다
#### 기본형 값을 감싸는 래퍼클래스
* OptionalInt, OptionalLong, OptionalDouble
* Optional<T>를 사용해도 되지만, 성능 때문에 사용한다.


### 최종 연산
* 연산 결과가 스트림이 아닌 연산으로, 단 한번만 적용 가능하다. (스트림의 요소를 소모)

#### 1) forEach()
* `void` `forEach(Consumer<T> action)` 스트림의 모든 요소에 지정된 작업을 수행한다
* `void` `forEachOrdered(Consumer<T> action)` 순서를 유지하여 작업을 수행한다. (병렬 스트림에서 사용)
  ```java
  IntStream.range(1, 10).parallel().forEach(System.out::print);   //916458723
  IntStream.range(1, 10).parallel().forEachOrdered(System.out::print);    // 123456789
  ```
#### 2) allMatch(), anyMatch(), noneMatch()
* `boolean` `allMatch(Predicate<T> p)` 조건식을 모두 만족시키는지 확인한다
* `boolean` `anyMatch(Predicate<T> p)` 조건식을 하나라도 만족하는지 확인한다.
* `boolean` `noneMatch(Predicate<T> p)` 조건식을 모두 만족하지 않는지 확인한다.

 
#### 3) findFirst(), findAny()
* `Optional<T>` `findFirst()` 스트림의 첫 번째 요소 하나를 반환한다. (Optional로 반환한다.)
  * 직렬 스트림에서 사용한다.
* `Optional<T>` `findAny()` 스트림의 아무 요소 하나를 반환한다. (Optional로 반환한다.)
  * 병렬 스트림에서 filter와 같이 사용하여 조건에 맞는 요소를 반환한다.
  ```java
  Optional<Student> result = stuStream.filter(s->s.getTotalScore() <= 100).findFirst();
  Optional<Student> result = parallelStream.filter(s->s.getTotalScore() <= 100).findAny();
  ```
#### 4) count(), max(), min(), sum(), average()
* `long` `count()` 스트림의 요소의 개수를 반환한다.
* `Optional<T>` `max(Comparator<T> comparator)` 기준에 따라 스트림의 최대값을 반환한다.
* `Optional<T>` `min(Comparator<T> comparator)` 기준에 따라 스트림의 최소값을 반환한다.
* `sum()` 모든 요소에 대한 합을 Optional로 반환한다. (**기본형 스트림에서만 있음**)
* `average()` 모든 요소에 대한 평균을 Optional로 반환한다. (**기본형 스트림에서만 있음**)
  * ❗average()는 기본형 스트림과 반환 Optional이 다를 수 있다. (IntStream에서 쓰면 OptionalDouble반환)
#### 5) toArray()
* `Object[]` `toArray()` 스트림의 모든 요소를 배열로 반환한다.
* `A[]` `toArray(IntFunction<A[]> generator)` 특정 타입의 배열로 반환한다.

#### 6) reduce()
* 스트림의 요소를 하나씩 줄여가며 누적연산을 수행한다.
* `Optional<T>` `reduce(BinaryOperator<T> accumulator)`초기값이 없기 때문에 Optional로 반환한다.
* `T` `reduce(T identity, BinaryOperator<T> accumulator)` T로 반환한다.
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
  //위 예제에서 sum()
  int a = identity;
  
  for(int b : stream)
      a = a + b;
  ```

#### 7) collect()
* `collect(Collector collector)` Collector를 구현한 클래스의 객체를 매개변수로 한다.
* 주로 요소를 그룹화 하거나 분할한 결과를 컬렉션에 담아 반환하는데 사용한다.
* reduce()와 다르게 collect()는 **그룹별로 나눠서 처리**할 때 사용한다.
* `Collector`는 collect()에 필요한 메서드를 정의해놓은 인터페이스이다.
* `Collectors` 클래스는 다양한 기능의 collector를 제공한다. (Collector 인터페이스를 구현한 클래스)

##### Collectors
* `toList()`, `toSet()`, `toMap()`, `toCollection()` 스트림을 컬렉션으로 변환한다.
  ```java
  List<String name> = stuStream.map(Student::getName)     // Stream<Student>에서 Stream<String>
                           .collect(Collectors.toList()); // Stream<String>에서 List<String>
  
  ArrayList<String> list = names.stream()
       .collect(Collectors.toCollection(ArrayList::new)); // Stream<String>에서 ArrayList<String>
  
  Map<String, Person> map = personStream
       .collect(Collectors.toMap(p->p.getRegId(), p->p)); // Stream<Person>에서 Map<String,Person> 
  ```
* `toArray()` 스트림을 배열로 변환
  ```java
  Student[] stuNames = studentStream.toArray(Student[]::new); // 이렇게 사용하면 Student[] 반환
  Object[] stuNames = studentStream.toArray(); // toArray()에 매개변수가 없으면 Object[] 반환 
  ```
* `counting()`, `summingInt()`, `maxBy()`, `minBy()` 스르팀의 통계정보 제공
   ```java
   // import java.util.Stream.Collectors.* 라고 가정 (Collectors 생략 가능)
   long count = stuStream.collect(counting());  // Collectors.counting()
   long totalScore = stuStream.collect(summingInt(Sutdent::getTotalScore)); // 그룹 별 합계를 얻을 수 있다.
   Optional<Student> topStudent = stuStream
                  .collect(maxBy(Comparator.comparingInt(Student::getTotalScore))); // 그룹 별 1등 찾을 수 있다.
   ```
* `reducing()` 스트림을 리듀싱 한다. (그룹 별로 리듀싱을 할 수 있다.)
   ```java
   Optinal<Integer> max = intStream.boxed().collect(reducing(Integer::max)); 
   long sum = intStream.boxed().collect(reducing(0, (a,b)->a+b));
   ```
* `joining()` 문자열 스트림의 요소를 모두 연결한다.
   ```java
   String studentNames = stuStrea.map(Student::getName).collect(joining());
   String studentNames = stuStrea.map(Student::getName).collect(joining(",")); //구분자
   ```


##### 스트림의 그룹화와 분할
* collect()는 그룹화를 통해 나눠놓고 작업이 가능하다.
* Collectors의 메서드를 통해 분할이 가능하다.

###### 1) partitioningBy()
* 스트림을 2분할 한다.
* `Collector` `partitioningBy(Predicate predicate)`
* `Collector` `partitioningBy(Predicate predicate, Collector downstream)`

```java
Map<Boolean, List<Student>> stuBySex = stuStream
        .collect(partitioningBy(Student::isMale));  // 분할
List<Student> maleStudent = stuBySex.get(true); // Map에서 남학생 목록을 얻는다.
List<Student> femaleStudent = stuBySex.get(false); // Map에서 여학생 목록을 얻는다.

Map<Boolean, Long> stuNumBySex = stuStream
        .collect(partitioningBy(Student::isMale, counting()));  // 분할 + 통계
System.out.println("남학생 수 :"+ stuNumBySex.get(true));  // 남학생수 : 8
        
Map<Boolean, Map<Boolean, List<Student>>> failedStuBySex = stuStream   // 다중 분할
        .collect(partitioningBy(Student::isMale,              // 성별로 분할 (남/여)
                 partitioningBy(s->s.getScore() < 150));      // 성적으로 분할 (불합격/합격)
List<Student> failedMaleStu = failedStuBySex.get(true).get(true));
```

###### 2) groupingBy()
* 스트림의 요소를 그룹화 한다.
* `Collector` `groupingBy(Function classifier)`
* `Collector` `groupingBy(Function classifier, Collector downstream)`
* `Collector` `groupingBy(Function classifier, Supplier mapFactory, Collector downstream)`
```java
Map<Integer, List<Student>> stuByBan = stuStream
        .collect(groupingBy(Student::getBan, toList()));    //그룹화

Map<Integer, Map<Integer, List<Student>>> stuByHakAndBan = stuStream // 다중 그룹화
        .collect(groupingBy(Student::getHak,            // 학년별 그룹화
                 groupingBy(Student::getBan)));         // 반별 그룹화

Map<Integer>, Map<Integer, Set<Student.level>> stuByHakAndBan = stuStream
        .collect(
                groupingBy(Student::getHak, groupingBy(Student::getBan, // 학년별 반별 다중 그룹화
                    mapping(s->{    // 성적등급 Level로 변환
                        if(s.getScore()>=200)      return Student.level.HIGH;
                        else if(s.getScore()>=100) return Student.level.Mid;
                        else                       return Student.Level.Low;
                    }, toSet())    // mapping()     // enum Level {HIGH, HID, LOW}
                ))    //groupingBy()  
        ); //collect()
```


___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
