# 예외

## 1. 프로그램 오류
1. **컴파일 에러**
* 컴파일 할 때 발생하는 에러
* 구문체크, 번역, 최적화, 생략된 코드 추가 등

2. **런타임 에러** 
* 실행 중 발생하는 에러
  * 에러(error) : 프로그램 코드에 의해서 수습될수 없는 심각한 오류
  * 예외(exception) : 프로그램 코드에 의해서 수습될 수 있는 다소 미약한 오류

3. **논리적 에러**
* 작성 의도와 다르게 동작하는 에러

## 2. 예외처리
* 프로그램 실행 시 발생할 수 있는 예외의 발생에 대비한 코드를 작성하는 것이다.
* 프로그램의 비정상 종료를 막고 정상적인 실행상태를 유지한다.
* try-catch문을 사용하거나, 예외를 선언하여 예외를 처리할 수 있다.

## 3. Exception, RuntimeException
1. Exception 클래스와 그 하위클래스들
* 사용자의 실수와 같은 외적인 요인에 의해 발생하는 예외
  * IOException (입출력 예외)
  * ClassNotFoundException (클래스가 존재하지 않을때)
* checked 예외이다. (컴파일러가 예외처리 여부를 체크한다. 예외처리가 필수적)
2. RuntimeException 클래스와 그 하위클래스들
* 프로그래머 실수로 발생하는 예외 
  * ArithmeticException (산술계산 예외)
  * ClassCastException (형변환 시 예외)
  * NullPointerException (어떤 객체가 null일 때)
  * IndexOutOfBoundsExceoption(배열범위 벗어남)
* Unchecked 예외이다.(컴파일러가 예외 처리 여부를 체크하지 않는다. 예외처리가 선택적)
## 4. try-catch문
```java
try{
    //예외가 발생할 가능성이 있는 문장들
}catch (Exception1 e1){
    // Exception1이 발생했을 경우 처리하기 위한 문장
}catch (Exception2 e2){
    // Exception2가 발생했을 경우 처리하기 위한 문장
}
```
* 괄호생략이 불가능하다.
* try블럭 내에서 예외가 발생하면 일치하는 catch블럭을 수행하고 try-catch문을 빠져나간다. 일치하는 catch블럭을 찾지 못하면 예외는 처리되지 못한다.
* try블럭 내에서 예외가 발생하지 않으면 catch블럭을 거치지 않고 전체 try-catch문을 빠져나간다.
* Exception이 선언된 catch블럭은 제일 뒤에 와야 한다. (Exception은 모든 예외를 처리할수 있는 최고 조상이기 때문이다.)
### 멀티 catch
```java
try{
    ~~~
}catch (ExceptionA | ExceptionB e){
    e.printStackTrace(); // ExceptionA, ExceptionB의 공통 멤버만 사용 가능
}
```
* 멀티 catch블럭으로 중복되는 코드를 줄일 수 있다.
* 예시와 같은 상황에선 ExceptionA와 ExceptionB의 공통멤버만 사용 가능하다
### finally
* 예외 발생여부와 관계없이 수행되어야 하는 코드를 넣는다
* 각 cathc문에서의 코드의 중복을 없앨 수 있다.
```java
try{
    //예외가 발생할 가능성이 있는 문장들
}catch (Exception1 e1){
    // Exception1이 발생했을 경우 처리하기 위한 문장
}catch (Exception2 e2){
    // Exception2가 발생했을 경우 처리하기 위한 문장
}
}finally{
    // 예외발생과 관계없이 수행 되는 코드
}
```
## 5. printStackTrace(), getMessage()
* 예외가 발생되면 예외정보가 들어있는 객체가 생성되고, 그 정보를 메서드들을 통해 가져올 수 있다.
### printStackTrace()
* 예외 발생 당시의 호출스택에 있었던 메서드의 정보와 예외 메서드를 화면에 출력한다.
### getMessage()
* 발생한 예외클래스의 인스턴스에 저장된 메시지를 얻을 수 있다.

## 예외 발생시키기
* 연산자 new를 이용해서 발생시키려는 예외 클래스의 객체를 만든다음 throw를 이용해서 예외를 발생
```java
try{
    Exception e = new Exception("고의로 발생");
    throw e;
}catch(Exception e){
    System.out.println(e.getMessage())
    e.printStackTrace();
}
```
## 6. 예외 선언
* 예외를 처리하는 방법은 try-catch문을 사용하는 방법과 예외를 선언하는 방법이 있다.
* 그중 예외를 선언하는 방법은 메서드가 호출시 발생가능한 예외를 호출하는 쪽에 알리는 것이다.(떠넘기는 것이다.)
```java
void 메서드명() throws Exception1, Exception2{ //최고 조상인 Exception을 사용하면 모두처리
    body
} 
```
### 예외 되던지기
* 예외를 처리한 후에 다시 예외를 발생시키는 것이다. 처리 후 throw로 다시 던진다.
* 호출한 메서드와 호출된 메서드 **양쪽 모두에서 예외처리** 하는 것이다.

## 7. 사용자 정의 예외 만들기
* 직접 예외 클래스를 정의할 수 있다.
* 조상은 exception과 RuntimeException중에서 선택한다.
* String 매개변수가 있는 생성자를 넣어야 한다.
```java
class MyException extends Exception{
    MyException(String msg);
        supuer(msg);
}
```

### 연결된 예외
* 한 예외가 다른 예외를 발생시킬 수 있다.
* 예외 A가 예외 B를 발생시키면, A는 B의 원인 예외이다.
#### Throwable InitCause(Throwable cause)
* 지정한 예외를 원인 예외로 등록한다

#### Throwable getCause()
* 원인 예외를 반환한다.

#### 예외 연결 이유
* 여러 예외를 하나로 묶어서 다루기 위해 사용한다.
* checked 예외를 unchecked 예외로 변경할 때 사용한다.


___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg