# Enum
## 1) 열거형이란?
* 관련된 상수들을 같이 묶어 놓은 것이다.
* Java에서는 타입에 안전한 열거형을 제공한다. (값과 타입 모두 확인한다.)
* 열거형을 사용하면 코드가 단순해지고 가독성이 좋아진다.
* 키워드 enum을 사용하기 때문에 구현의 의도를 분명히 알 수 있다.

## 2) 열거형 정의
* 열거형 정의하는 법
  ```java
  enum 열거형이름 { 상수명1, 상수명2, 상수명3, ...} //각 상수명에 0, 1, 2, ...순으로 값이 부여가 된다.
  ```
* 열거형 선언 후 사용하는 법
  ```java
  Direction dir;    // 열거형이름 변수명;
  dir = Direction.EAST; // 변수를 해당 상수로 초기화
    ```
* 열거형 상수의 비교에 `==`, `compareTo()`를 사용할 수 있다.
  ```java
  if(dir==Direction.EAST){  //등가비교연산자 == 사용 가능
    ...
  } else if(dir > Direction.WSET){  //불가능. 열거형 상수에 비교연산자는 사용 불가
    ...
  } else if(dir.compareTo(direction.WEST)){ // compareTo()사용 가능
    ...
  }
  ```
    * 열거형 상수들은 각각의 객체이기 때문에 비교연산자를 쓸 수 없다.
## 3)  java.lang.Enum
* 열거형의 최고 조상이며, 아래 메서드를 상속받는다. (모든 열거형에서 사용 가능)
* `getDeclaringClass()` 열거형의 Class객체를 반환한다
* `name()` 열거형 상수의 이름을 문자열로 반환한다.
* `ordinal()` 열거형 상수가 정의된 순서를 0부터 반환한다.(상수가 정의된 순서이다. **값과는 무관**하다.)
* `valueOf(Class<T> enumType, String name)` 지정된 열거형에서 name과 일치하는 열거형 상수를 반환한다.
* `values()` 모든 열거 객체들을 배열로 반환한다.
* `valueOf(String name)` 지정된 문자열과 일치하는 열거형 상수를 반환한다.
  * `values()`, `valueOf()`는 컴파일러가 자동으로 추가해준다.


## 4) 열거형에 멤버 추가
* 불연속적인 열거형 상수의 경우, 원하는 값을 괄호`()` 안에 적는다.
* `()`를 사용하려면, 인스턴스 변수와 생성자를 새로 추가해 줘야 한다.
  ```java
  enum Direction {
    EAST(1, ">"), SOUTH(2, "V"), WEST(3,"<"), NORTH(4, "^"); // ,로 구분하여 여러 값 추가 가능
    
    private final int value;    // 정수를 저장할 필드(인스턴스 변수)를 추가
    private final String symbol; // 문자열을 저장할 필드(인스턴스 변수)를 추가
    Direction (int value, String symbol) {  // 생성자 추가 
        this.value = value;
        this.symbol = symbol;
    } 
    public int getValue() { return value; }
    public String getSymbol() { return symbol; }
  }
  ```
  * 열거형의 생성자는 항상 private이므로 외부에서 객체 생성이 불가능하며, 생략가능하다.


___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg

codestates 교육
