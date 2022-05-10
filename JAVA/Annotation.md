# Annotation

## 애너테이션이란?
* 주석처럼 프로그래밍 언어에 영향을 미치지 않으며, 유용한 정보를 제공해 준다.
* 표준 애너테이션, 메타 애너테이션, 사용자 정의 애너테이션이 있다.
* JDK에서 제공하는 표준 애너테이션들은 주로 컴파일러를 위한 것으로, 컴파일러들에게 유용한 정보를 제공해 준다.

| 애너테이션                    | 설명                                   |
|--------------------------|--------------------------------------|
| **@Override**            | 컴파일러에게 오버라이딩하는 메서드라는 것을 알린다.         |
| **@Deprecated**          | 앞으로 사용하지 않을 것을 권장하는 대상에 붙인다.         |
| **@SuppressWarnings**    | 컴파일러의 특정 경고메시지가 나타나지 않게 해준다.         |
| @SafeVarargs             | 지네릭스 타입의 가변인자에 사용한다.(JDK1.7~)        |
| **@FunctionalInterface** | 함수형 인터페이스라는 것을 알린다.(JDK1.8~)         |
| @Native                  | native메서드에서 참조되는 상수 앞에 붙인다.(JDK1.8~) |

| 메타애너테이션                  | 설명                                   |
|--------------------------|--------------------------------------|
| @Target                  | 애너테이션이 적용가능한 대상을 지정하는데 사용한다.         |
| @Documented              | 애너테이션 정보가 javadoc으로 작성된 문서에 포함되게 한다. |
| @Inherited               | 애너테이션이 자손 클래스에 상속되도록 한다.             |
| @Retention               | 애너테이션이 유지되는 범위를 지정하는데 사용한다.          |
| @Repeatable              | 애너테이션을 반복해서 적용할 수 있게 한다.(JDK1.8~)    |

* 메타애너테이션은 애너테이션을 만들 때 사용한다.

## 표준 애너테이션
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
