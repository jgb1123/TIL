# Generics

## 1) 지네릭스란?
* 컴파일 시 타입을 체크해 주는 기능이다. (JDK1.5~)
* Run time error인 형변환 에러를 compile time error로 가져온 것이라고 생각하면 된다. (실핼 중 발생하는 에러보다 실행 전 발생하는 에러가 더 낫기 때문)
* 객체의 타입 안정성을 높인다.
* 타입 체크와 형변환의 번거로움을 줄일 수 있으므로 코드가 간결해 진다.

## 2) 타입 변수
`클래스명<E>` : 지네릭 클래스 (E의 class명또는 E Class명이라 읽음)

`E` : 타입변수 또는 타입 매개변수(E는 타입 문자이며 타입문자로는 보통 E, T를 많이 씀)

`클래스명` : 원시타입(raw type)
* **클래스를 작성**할 때 Object타입 대신 타입변수를 선언해서 사용한다.
  ```java
  public class ArrayList<E> extends AbstractList<E> {
      private transient E[] elementaData;
      public boolean add(E o){}
      public E get(int index){}
  } 
  ```
* **객체를 생성**할 때에는 타입변수 대신 실제 타입을 지정해야 한다.
  ```java
  ArrayList<Tv> tvList = new ArrayList<Tv>(); //타입변수 대신 실제 타입을 지정한다.
  Tv t = list.get(0); // 원래 list는 object로 반환해서 (tv)로 형변환을 해줘야 하나 지네릭스 사용으로 생략가능.
  ```

## 3) 지네릭스의 다형성

* 참조변수와 생성자의 대입된 타입은 서로 **일치해야 한다**. (JDK1.7부터는 생성자에 타입지정 생략 가능)
  ```java
  ArrayList<Product> productList = new ArrayList<Product>(); 
  ```
* 지네릭 클래스의경우 불일치해도 된다.(다형성 성립)
  ```java
  List<TV> tvList = new ArrayList<tv>();
  ```
* 매개변수의 다형성도 성립한다.
  ```java
  ArrayList<Product> productList = new ArrayList<Product>(); 
  productList.add(new Tv);
  productList.add(new Audio);
  ```

## 4) Iterator, HashMap에서의 지네릭스
* Iterator사용 시 타입 지정을 하면 형변환 생략이 가능해진다.
  ```java
  Iterator<Student> it = list.iterator();
  while(it.hasNext()){
    Student s = it.next();  // (Student)형변환 생략 가능.
  }
  ```
* HashMap처럼 여러 개의 타입 변수가 필요한 경우 `,`를 구분자로 선언한다.
  ```java
  HashMap<String, Student> map = new HashMap<String, Student>();
  ```

## 5) 제한된 지네릭 클래스
* extends로 대입할 수 있는 타입을 제한할 수 있다.
  ```java
  class FruitBox<T extends Fruit & Eatable>{  // Fruit, Eatable의 자손만 타입으로 지정 가능하다.
    ArrayList<T> list = new ArrayList<T>();
  }
  ```
* ❗인터페이스인 경우에도 extends를 사용한다(인터페이스에서 implements가 아니다.)

## 6) 지네릭스의 제약사항
* 타입 변수에 대입은 인스턴스 별로 다르게 가능하다. 그러므로 **static멤버에 타입변수는 사용 불가능**하다.
* 객체와 배열을 생성할 때 타입변수를 사용할 수 없다. (new 다음에 타입변수가 올 수 없다)

## 7) 와일드 카드 <?>
* 하나의 참조변수로 대입된 타입이 다른 객체를 참조할 수 있다.
```java
ArrayList<? extends Product> list = new ArrayList<Tv>();
ArrayList<? extends Product> list = new ArrayList<Audio>();
```

* `<? extends T>` 와일드 카드의 상한 제한. T와 그 자손들만 가능하다.

* `<? super T>` 와일드카드의 하한 제한. T와 그 조상들만 가능하다.

* `<?>` 제한 없음. 모든타입이 가능하다. (`<Extends Object>`와 동일)

* 메서드의 매개변수에도 와일드 카드를 사용할 수 있다.(다형성을 얻을 수 있다.)
```java
static Juice makeJuice(FruitBox<? extends Fruit> box){  //와일드카드가 없으면 Fruit만 가능
    ~~~
} 
```

## 8) 지네릭 메서드
* 지네릭 메서드는 지네릭 타입이 선언된 메서드로, 타입변수는 메서드 내에서만 유효하다.
* 메서드를 호출할 때마다 타입을 대입해야 한다. (대부분 생략 가능하다.)
* 드물지만 타입을 생략하지 못할 때는 클래스 이름까지 써야 한다.

### 매개변수에서의 와일드카드 vs 지네릭 메서드
* 와일드카드는 하나의 참조변수로 서로 다른 타입이 대입된 여러 지네릭 객체를 다루기 위한 것이다.
* 지네릭 메서드는 메서드를 **호출**할 때마다 다른 지네릭 타입을 대입할 수 있게 한 것이다.

## 9) 지네릭 타입의 형변환과 제거
### 지네릭 타입의 형변환
* 지네릭타입과 원시타입 간의 형변환은 바람직 하지 않다.
  ```java
  Box<Object> objBox = null;
  Box box = (Box)objBox;        // 지네릭타입 -> 원시타입 가능하지만 경고발생
  objBox = (Box)<Object>)box;   // 원시타입 -> 지네릭타입 가능하지만 경고발생
  ```
  * 애초에 원시타입을 쓰는게 바람직하지 않다.

* 서로 다른 타입이 대입 된 지네릭 타입끼리는 형변환이 불가능하다
  ```java
  objBox = (box<String>)strBox; // Box<String> -> Box<Object> 불가능
  ```

* 와일드 카드가 사용된 지네릭 타입으로는 형변환이 가능하다.
  ```java
  Box<? extends Object> wbox = (Box<? extends Object>)new Box<String>();    //형변환 생략가능
  Box<? extends Object> wbox = new Box<String>();   //가능, 위 문장과 동일함
  ```

### 지네릭 타입의 제거
* 컴파일러는 지네릭 타입을 **제거**하고, 필요한 곳에 형변환을 넣는다.(하위호환이 가능하게 하기 위해서)
1. 지네릭 타입의 경계(bound)를 제거한다.
2. 지네릭 타입 제거 후 타입이 불일치하면 형변환을 추가한다.
3. 와일드카드가 포함된 경우 적절한 타입으로 형변환 한다.


___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
