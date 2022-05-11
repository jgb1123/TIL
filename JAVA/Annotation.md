# Annotation

## 애너테이션이란?
* 주석처럼 프로그래밍 언어에 영향을 미치지 않으며, 유용한 정보를 제공해 준다.
* 표준 애너테이션, 메타 애너테이션, 사용자 정의 애너테이션이 있다.

## 표준 애너테이션
* JDK에서 제공하는 애너테이션으로, 주로 컴파일러를 위한 것이다.
* 컴파일러에게 유용한 정보를 제공해 준다.

| 애너테이션                    | 설명                                   |
|--------------------------|--------------------------------------|
| **@Override**            | 컴파일러에게 오버라이딩하는 메서드라는 것을 알린다.         |
| **@Deprecated**          | 앞으로 사용하지 않을 것을 권장하는 대상에 붙인다.         |
| **@SuppressWarnings**    | 컴파일러의 특정 경고메시지가 나타나지 않게 해준다.         |
| @SafeVarargs             | 지네릭스 타입의 가변인자에 사용한다.(JDK1.7~)        |
| **@FunctionalInterface** | 함수형 인터페이스라는 것을 알린다.(JDK1.8~)         |
| @Native                  | native메서드에서 참조되는 상수 앞에 붙인다.(JDK1.8~) |


### 1) @Override
* 오버리이딩을 올바르게 했는지 컴파일러가 체크하게 한다.
* 오버라이딩을 할 때 메서드이름을 잘못적는 실수를 하는 경우가 많다. 이러한 실수들을 막아준다.
```java
class Parent{
    void parentMethod(){}
}
class Child extends Parent{
    @override
    void parentMethod(){}   //오버라이딩 할 때 오타 실수 등을 하면 컴파일러가 알려준다.
}
```

### 2) @Deprecated
* 앞으로 사용하지 않을 것을 권장하는 필드나 메서드에 붙인다.
* 예를들면 더 좋은 메서드가 생기면 옛날 메서드에 쓰지 말라고 붙인다. (하위호환성 때문에 안 쓴다고 메서드를 지울 순 없다.)
```java
@Deprecated
public int getDate(){
    return normalize().getDayOfMonth();
}
```
* @Deprecated가 붙은 대상이 사용된 코드를 컴파일하면 경고문구가 나온다.(에러는 아니다.)

### 3) @FunctionalInterface
* 함수형 인터페이스에 붙이면 컴파일러가 올바르게 작성했는지 체크한다.
* 함수형 인터페이스에는 하나의 추상메서드만 가져야 한다는 제약이 있다.
```java
@functionalInterface
public interface Runnable{
    public abstract void run(); // 추상메서드가 하나 더있으면 에러가 난다.
}
```

### 4) @SuppressWarnings
* 컴파일러의 경고메시지가 나타나지 않게 억제한다.
* 괄호()안에 억제하고자 하는 경고의 종류를 문자열로 지정하면 된다.

```java
@SuppressWarnings("unchecked")
ArrayList list = new ArrayList();
list.add(obj);
```
* 보통 컴파일러가 알려주는 경고문구를 보고 이 애너테이션을 붙이면 된다.(경고를 확인했고, 문제 없다는걸 알림)
* 둘 이상의 경고를 동시에 억제하려면 `@SuppressWarnings({"Depreacation", "unchecked"})`와 같이 배열처럼 작성하면 된다.
* `javac -Xlint ~~~.java`와 같이 -Xlint옵션으로 컴파일하면 경고메시지를 확인할 수 있다.

## 메타 애너테이션
* 메타 애너테이션은 애너테이션을 만들 때 사용하는 애너테이션이다.
* java.lang.anotation 패키지에 들어있다.

| 메타애너테이션                  | 설명                                   |
|--------------------------|--------------------------------------|
| @Target                  | 애너테이션이 적용가능한 대상을 지정하는데 사용한다.         |
| @Retention               | 애너테이션이 유지되는 범위를 지정하는데 사용한다.          |
| @Documented              | 애너테이션 정보가 javadoc으로 작성된 문서에 포함되게 한다. |
| @Inherited               | 애너테이션이 자손 클래스에 상속되도록 한다.             |
| @Repeatable              | 애너테이션을 반복해서 적용할 수 있게 한다.(JDK1.8~)    |

### 1) @Target
* 애너테이션을 정의할 때 적용 가능한 대상을 지정할 때 사용한다.

```java
@Target ({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
@Retention(RetentionPolicy.SOURCE)
public @interface SuppressWarnings{
    String[] value();
}
```

### 2) @Retention
* 애너테이션이 **유지(retention)되는 기간**을 지정하는데 사용한다.
* 컴파일러에 의해 사용되는 애너테이션의 유지 정책은 `SOURCE`이다. ex) @Override
* 실행시에 사용가능한 애너테이션의 정책은 `RUNTIME`이다. ex) @FunctionalInterface


### 3) @Documented
* javadoc으로 작성한 문서에 포함 시키고자 할 때 사용한다.
* 직접 사용할 일은 별로 없다.

### 4) @Inherited
* 애너테이션을 자손 클래스에 상속 하고자 할 때 사용한다.

### 5) @Repeatable
* 반복해서 붙일 수 있는 애너테이션을 정의할 때 사용한다.
* @repeatable이 붙은 애너테이션은 반복해서 붙일 수 있다.
* @repeatable이 붙은 애너테이션을 하나로 묶을 컨데이너 애너테이션도 정의해야 한다. (배열 사용)


## 사용자 정의 애너테이션
* 애너테이션을 사용자가 직접 만들어 쓸 수 있다.
### 1) 애너테이션의 정의
```java
@interface 애너테이션명{
    타입 요소이름();  // 요소에 애너테이션도 올 수 있다.
    ...
}
```
### 2) 애너테이션의 요소
* 애너테이션의 메서드는 추상 메서드이며, 애너테이션을 사용할 때 추상메서드를 호출해서 우리가 원하는 값으로 지정한다.

* 애너테이션 사용 시 값을 지정하지 않았을 때 사용될 수 있는 기본값을 지정할 수 있다.
```java
@interface TestInfo{
    int count() default 1;
}
```

* 요소가 하나이고, 이름이 value일 때에는 요소의 이름을 생략할 수 있다.
```java
@interface TestInfo{
    String value();
} 
@TestInfo("abcd")   // value="abcd"로 안해도 된다.
class TestClass{...}
```

* 요소의 타입이 배열인 경우, 중괄호`{}`를 사용해야 한다.
```java
interface TestInfo{
    String[] testTools();   
} 

@TestInfo(testTools= {"AAA", "BBB"})    // {}는 값이 하나일 때에는 안써도 된다.
@TestInfo(testTools= {})                // 값이 없을 땐 {}를 꼭 써줘야 한다.
```
#### java.lang.annotation.Annotation
* Annotation은 모든 애너테이션의 조상이지만 상속은 불가능하다. (Annotation은 인터페이스이다.)
* 애너테이션들은 이 Annotation의 추상메서드들을 구현하지 않고 사용할 수 있다.
```java
public interface Annotation{     // Annotation은 인터페이스 이다.
    boolean equals(Object obj);  // Annotation은 이러한 추상메서드들을 가지고 있고
    int hashCode();              // 애노테이션 들은 이러한 추상메서드들을 구현할 필요는 없고 사용할 수는 있다.
    String toString();           // 모든 애너테이션들은 이러한 추상메서드들을 가지고 있는 셈이다.
    
    Class<? extends Annotation> annotationType();   // 애너테이션의 타입을 반환한다.
}
```

#### Marker Annotation
* 요소가 하나도 정의되지 않은 애너테이션들을 말한다.
* 따로 줄 값 같은 건 없지만 애너테이션이 사용되어진 것만 봐도 그 의미를 알 수 있게 된다.


#### 애너테이션 요소의 규칙
* 요소의 타입은 기본형, String, enum, 애너테이션, Class만 허용된다.
* 괄호()안에 매개변수를 선언할 수 없다.
* 예외를 선언할 수 없다.
* 요소를 타입 매개변수로 정의할 수 없다.

___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
