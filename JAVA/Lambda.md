# Lambda Expression

## 1. 람다식(Lambda Expression)이란?
* 함수(메서드)를 간단한 식(expression)으로 표현하는 방법이다.

* 람다식은 익명 함수이다. 하지만 자바에서는 익명 **객체**이다 (자바에서는 메서드만 있을 수 없으므로 익명 클래스에 속해 있다.)
> 함수 vs 메서드
> 
> * 근본적으로는 동일하며, 함수는 일반적 용어이고 메서드는 객체지향개념 용어이다.
> * 함수는 클래스에 독립적이고, 메서드는 클래스에 종속적이다.
 
## 2. 람다식 작성
* 메서드의 이름과 반환타입을 제거하고 `->`를 블록 {}앞에 추가한다.
  ```java
  int max(int a, int b){    // 기본 메서드
  return a > b ? a : b;
  }
  ```
  ```java
  (int a, int b) -> {   // 람다식
  return a > b ? a : b;
  }
  ```

* 반환값이 있는 경우, 식이나 값만 적고 return문 생략 가능하다. (끝에 `;` 안 붙임)
  ```java
  (int a, int b) -> a > b ? a : b
  ```
  
* 매개변수의 타입이 추론 가능하면 생략 가능하다.(대부분의 경우 생략 가능하다.)
  ```java
  (a, b) -> a > b ? a : b
  ```

* 매개변수가 하나인 경우 `()`생략 가능하다. (타입이 없을 경우만) (매개변수가 없을시 빈괄호`()`)
  ```java
  a -> a*a
  ```

* 블록 안에 문장이 하나일 때 괄호`{}` 생략 가능하다.
  ```java
  (int a) -> System.out.println(i)
  ```
## 3. 익명 객체와 함수형 인터페이스
### 익명 객체
* 람다식은 익명 객체이다.
* 익명 객체는 객체의 선언과 생성을 동시에 한다.
  ```java
  (a, b) -> a > b ? a : b // 이 람다식은 원래 아래와 같은 구조의 객체이다.
  
  new Object() {      // 위 람다식은 원래 이러한 익명 객체이다.
      int max(int a, int b){
      return a > b ? a : b;
      }
  }
  ```
* 객체를 다루기 위해선 참조변수가 필요하고, 참조변수의 타입이 필요하다.
* 하지만 아래와 같이 Object타입으로 참조변수를 만들어도, max() 메서드는 사용할 수 없다.
  ```java
  Object obj = new Object() {
      int max(int a , int b){
      return a > b ? a : b;
      }
  };
  int value = obj.max(3, 5); // 사용 불가, Object에는 max()가 없음.
  ``` 
* 이러한 문제를 해결하기 위해 **함수형 인터페이스를 사용**한다.
### 함수형 인터페이스
* 함수형 인터페이스는 **단 하나의 추상메서드**만 선언된 인터페이스다.
* 람다식은 익명 클래스에 속한 익명 객체이기 때문에, 이를 사용하기 위해 함수형 인터페이스를 이용한다.
  ```java
  @FunctionalInterface    // 애너테이션을 쓰면 안전하다.
  interface MyFunction{ 
       public abstract int max(int a, int b)
  } 
  ```
* 함수형 인터페이스 타입의 참조변수로 람다식을 참조할 수 있다.
  ```java
  MyFunction f = new MyFunction() {   // 람다식 변환 전
      Public int max(int a, int b){
      return a > b ? a : b;
      }
  }

  MyFunction f = (a, b) -> a > b ? a : b;  // 위 문장과 동일한 람다식 
  int value = f.max(1, 2);    // MyFucntion이라는 함수형 인터페이스에 max라는 메서드가 있으므로 사용 가능
  ```
* 함수형 인터페이스의 메서드와 람다식의 **매개변수 개수와 반환타입이 일치**해야 한다.
* 메서드의 매개변수로 함수형 인터페이스 타입을 쓸 수 있다.
  ```java
  @FunctionalInterface
  interface MyFunction{
        void myMethod();
  }
  ```
  ```
  void aMethod(MyFunction f){ // 메서드의 매개변수로 람다식을 받는다.
       f.myMethod();   // 람다식을 호출한다.
  }
  ```
  ```java
  MyFunction f = () -> System.out.println("MyMethod()");
  aMethod(f);
  
  aMethod(() -> System.out.println("myMethod()"));    //위 2줄과 같은 의미
  ```
* 반환값으로 함수형 인터페이스 타입을 쓸 수 있다.
  ```java
  MyFunction myMethod(){
      return () -> {};
  } 
  ```

## java.util.function
* 자주 사용되는 다양한 함수형 인터페이스를 제공해준다.
### 1) 자주 사용되는 함수형 인터페이스
* `Runnable` 매개변수도 없고, 반환값도 없다. `run()`메서드 사용 (❗java.lang.Runnable)
* `T` `Supplier<T>` 매개변수는 없고, 반환값만 있다. `get()` 메서드 사용
* `void` `Consumer<T>` 매개변수만 있고, 반환값이 없다. `accept(T t)` 메서드 사용
* `R` `Function<T,R>` 일반적인 함수로 하나의 매개변수를 받아서 결과를 반환한다. `apply(T t)` 메서드 사용
* `boolean` `Predicate<T>` 조건식을 표현하는데 사용된다. `test(T t)`메서드 사용
  * 반환타입이 항상 Boolean이기 때문에 predicate<Integer>처럼 boolean을 생략할 수 있다.
### 2) 매개변수가 2개인 함수형 인터페이스
* `void` `BiConsumer<T,U>` 두개의 매개변수만 있고 반환값이 없다. `accept(T t, U u)`
* `boolean` `BiPredicate<T,U>` 조건식을 표현하는데 사용된다. 매개변수는 두개이고 반환타입은 boolean이다. `test(T t, U u)`
* `R` `BiFunction<T,U,R>` 두개의 매개변수를 받아서 하나의 결과를 반환한다. `apply(T t, U u)`
* 3개이상의 매개변수를 받아야 하는 경우, 직접 함수형 인터페이스를 만들면 된다.

### 3) 매개변수의 타입과 반환타입이 일치
* `T` `UnaryOperator<T>` Function의 자손으로, Function과 달리 매개변수와 결과의 타입이 같다. `apply(T t)`메서드 사용
* `T` `BinaryOperator<T>` BiFunction의 자손으로, Bifunction과 달리 매개변수 2개와 결과의 타입이 같다. `apply(T t, T t)`메서드 사용

### 4) Predicate의 결합
* and(), or(), negate()로 두 Predicate를 하나로 결합할 수 있다.
  ```java
  Predicate<Integer> a = i -> i < 100;
  Predicate<Integer> b = i -> i < 200;
  Predicate<Integer> c = i -> i%2==0;
  Predicate<Interger> all = (a.negate()).and(b).or(c) // i >= 100 && i < 200 || i%2 == 0
  ```


* `isEqual()`를 사용하여 등가비교를 할 수 있다. (static 메서드이다.)
  ```java
  Predicate<String> p = Predicate.isEqual(str1);  // str1과 비교하는 predicate
  boolean result = p.test(str2)                   // str1과 str2가 같은지 비교한 결과를 반환한다.
  
  boolean result = Predicate.isEqual(str1).test(str2)        // 위 2문장과 동일 
  ```


* andThen() : 두 함수를 합성한다.
  ```java
  Function<String, Integer> f = (s) -> Integer.parseInt(s, 16); //String 들어오면 Integer출력 
  Function<Integer, String> g = (i) -> Integer.toBinaryString(i);   //Integer들어오면 String출력
  Function<String, String> h = f.andThen(g);    // String들어오면 String출력
  ```
* compose() : andThen()과 반대로 합성한다.

## 컬렉션 프레임웍과 함수형 인터페이스
* 컬렉션 프레임웍에서 함수형 인터페이스를 이용하면 코드를 더 간결하게 만들 수 있다.
### Collection
* `boolean` `removeIf(Predicated<E> filter)` 조건에 맞는 요소를 삭제한다.
### List
* `void` `replaceAll(UnaryOperator<E> operator)` 모든 요소를 변환하여 대체한다.
### Iterable
* `Iterable` `forEach(Consumer<T> action)` 모든 요소에 작업 action을 수행한다.
### Map
* `V` `compute(K key, BiFunction<K,V,V> f)` 지정된 키의 값에 작업 f를 수행한다.
* `V` `computeIfAbsent(K key, Function<K,V> f)` 키가 없으면 작업 f를 수행 후 추가한다.
* `V` `computeIfPresent(K key, BiFunction<K,V,V> f)` 지정된 키가 있으면 작업 f를 수행한다.
* `V` `merge(K key, V value, BiFunction<V,V,V> f)` 모든 요소에 병합작업 f를 수행한다.
* `void` `forEach(BiConsumer<K,V>, action)` 모든 요소에 작업 action을 수행한다.
* `void` `replaceAll(BiFunction<K,V,V> f)` 모든 요소에 치환작업 f를 수행한다.

```java
list.forEach(i->System.out.print(i+","));   // list의 모든 요소를 출력한다.
list.removeIf(x->x%2==0 || X%3==0)  // list의 2또는 3의배수를 제거한다.
list.replaceAll(i->i*10);   // list의 모든 요소에 10을 곱한다.
map.forEach((k,v)->System.out.print("{"+k+","+v+"},")); // map의 모든 요소를 {k,v}형식으로 출력한다.
```
## 메서드 참조(method reference)
* 하나의 메서드만 호출하는 람다식은 메서드 참조로 더 간단히 할 수 있다. `클래스이름::메서드이름`
```java
Interger method(String s){
    return Integer.parseInt(s)
} 

Function<String, Integer> f = (String s) -> Integer.parseInt(s);    // 람다식
        // 지네릭으로 입력과 출력 타입을 알 수 있기때문에 없앨 수 있다.
Function<String, Integer> f = Integer::parseInt;    // 메서드 참조
```

* 생성자와 메서드 참조
```java
Supplier<MyClass> s = () -> new Myclass();  // 람다식

Supplier<Myclass> s = Myclass::new; // 메서드 참조
```

```java
Function<Integer, Myclass> s = (i) -> new Myclass(i);   // 람다식
        // 지네릭으로 입력과 출력 타입을 알 수 있기때문에 없앨 수 있다.
Function<Integer, Myclass> s = Myclass::new;    // 메서드 참조
```

* 배열과 메서드 참조
```java
Function<Integer, int[]> f = x -> new int[x];   // 람다식

Function<Integer, int[]> f = int[]::new:    // 메서드 참조 
```


___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
