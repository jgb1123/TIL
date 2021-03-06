# JAVA 기초

## 1. JAVA의 특징
### 1) 운영체제에 독립적이다.
* 특정 CPU에서만 작동하거나 특정 OS에 따라 다르게 작성해야 하는 언어들이 있다.
* 자바는 JRE(JVM + 클래스 라이브러리)가 설치되어있는 모든 운영체제에서 실행이 가능하다
  > 
  > JVM(Java virtual Machine)
  >  * 자바코드로 작성한 프로그램을 해석해 실행하는 별도의 프로그램이다.
  >  * 운영체제별 규칙을 따르는 별도의 절차가 필요하나, JVM은 이 문제를 해결해 줄 수 있다.
  >  * 자바는 JVM을 거치기 때문에 C나 C++에 비해 속도는 느린 편이다.
### 2) 객체 지향 언어(Object Oriented Programming, OOP)
* 객체는 프로그램이 동작하는 부품이라고 생각하면 된다.
* 여러 객체들을 만들고 하나의 프로그램을 실행하는 개념이 OOP이다.
* 객체지향적으로 설계된 프로그램은 유지보수가 쉽고 확정성이 높다.

### 3) 함수형 프로그래밍 지원
* Java8부터 함수형 프로그래밍을 지원하는 문법인 람다식과 스트림이 추가되었다.
* 이를 이용해 컬렉션의 요소를 필터링, 매핑, 집계 처리하기 쉬워지고 코드가 간결해진다.


### 4) 자동 메모리 관리
* Garbage Collector를 실행시켜 자동으로 사용하지 않는 메모리를 수거한다.
* 개발자는 메모리를 관리하는 수고를 덜 수 있다.

## 2. JAVA 기본 구조
```java
public class 클래스명{
    public static void main(String[] args){
    }
}
```
1. Class

* 자바는 객체 지향 언어이기 때문에 모든 코드를 클래스 내에 작성한다.

* 간단하게 클래스는 객체를 찍어낼 수 있는 틀이라고 생각하면 된다.

* 여러가지 Method의 모음집이라 생각하면 된다.

2. main 메소드

* 메소드란 클래스 내부에 정의된 함수로, 일련의 명령문을 모아놓은 집합이다.
* 자바를 실행하면 main 메소드를 가장 먼저 찾아 실행한다.

3. 접근 제어자

* 클래스의 변수와 메소드를 다른 클래스에서 사용하거나, 사용할 수 없도록 지정할 수 있다.
* 접근 제어자는 클래스, 변수, 메소드의 접근 권한을 정의할 때 사용하는 키워드다.

* `public > protected > default > private`
  * public : 모든 접근을 허용
  * protected : 같은 패키지에 있는 객체와 상속 관계의 객체들만 허용
  * default : 같은 패키지에 있는 객체들만 허용
  * private : 같은 클래스 내에서만 허용

4. static

* static은 변수나 메서드가 객체의 것이 아니라, 클래스에 속한 것임을 지정하는 키워드다.


5. return과 void

* 메소드는 리턴할 값의 타입을 명시해 줘야 한다. 메서드가 작업을 마치고 데이터를 반환하는것을 리턴이라고 한다.
* 데이터를 리턴하지 않을 경우에 void키워드를 쓴다.

## 3. 타입(Type)
* 자바의 타입은 데이터의 실제 값을 의미하는 기본타입과 저장된 주소를 값으로 갖는 참조타입이 있다.
  > 원시타입(primitive type)
  >* 데이터의 실제 값을 의미
  >* 정수(byte, short, int, long), 실수(float, double), 문자(char), 논리(boolean)
  >
  > 참조타입(reference type)
  > * 데이터가 저장된 주솟값을 말한다.

### 1) 정수 타입
* 정수 타입은 byte, short, int, long 4가지 타입이 있다.

| 타입    | 메모리   | 범위               |
|-------|-------|------------------|
| byte  | 1byte | -2^7 ~ 2^7 - 1   |
| short | 2byte | -2^15 ~ 2^15 - 1 |
| int   | 4byte | -2^31 ~ 2^31 - 1 |
| long  | 8byte | -2^63 ~ 2^63 - 1 |

*int는 정수의 기본형으로, 정수를 입력하면 기본적으로 int로 인식한다.
*int의 범위를 벗어 나면 long타입에 L을 붙여준다.
  ```java
  long a=12345678910L;
```
* 변수의 값이 자료형을 넘어가면 overflow, underflow가 발생한다.

### 2) 실수 타입
* 실수 타입은 float(4byte), double(8byte) 2가지 타입이 있다.
* float은 숫자 뒤에 f를 붙여야 하지만, double은 실수형의 디폴트 값이기 때문에 d를 안붙여도 된다.

### 3) 논리 타입
* 논리 타입은 boolean 한가지이다.
* 참(`true`) 또는 거짓(`false`)을 저장할 수 있는 데이터 타입이다.
* JVM이 다룰 수 있는 데이터의 최소단위가 1byte라 1byte의 크기를 차지한다.

### 4) 문자 타입
* 문자 타입 char는 단 하나의 문자만 저장할 수 있다.
* 유니코드에 기반하여 문자를 표현하며, 2byte의 크기를 차지한다.

## 4. 문자열(String)
* 문자열은 원시타입이 아니고 참조타입이다.
```java
String n1 = "장근범"; // (1)문자열 리터럴을 대입하는 방식
String n2 = new String("장근범"); // (2)new 연산자로 객체를 생성하고 문자열을 대입하는 방식
```
* (1)번 방식은 문자열 리터럴을 생성하는 방식인데, Heap영역 안에있는 String constant Pool 영역에 할당된다.
* (2)번 방식은 Heap영역에 객체가 생성된다.
* 위 상황에서 `n1==n2`로 ==연산을 할 경우 참조 값이 다르므로 False가 된다.

## 5. 변수와 상수
### 1) 변수
#### 변수 사용 이유
* 메모리에 데이터의 저장 공간을 확보한다.
* 데이터에 이름을 붙여 소통한다.
* 데이터를 재사용한다.

#### 변수의 선언, 할당
```java
int n1;     //변수를 선언한다 
n1 = 6;     // 변수에 값을 할당한다

int n2 = 6;     // 선언과 할당을 동시에 할 수 있다.
n2 = 9;      // 재할당이 가능하다
```

#### 변수 명
자바에서 변수명은 일반적으로 camelCase를 사용한다. 
> camelCase : 낙타의 등을 닮아 붙여진 이름. 두번째 단어부터 대문자로 시작해 구문한다

**사용할 수 없는 변수명**
1. 숫자로 시작하는 변수명
2. 자바에서 이미 사용중인 예약어(reserved word)

### 2) 상수
#### 상수란
* 상수(Constant)는 변하지 않는 수이면서 변하면 안되는 수를 말한다.(고정값)
* final 예약어를 사용해 선언한다
  ```java
  final double CALCULATOR_PI = 3.14;
  ```
* 대문자에 `_`를 넣어 구분하는 SCREAMING_SNAKE_CASE를 사용한다

#### 상수 사용 이유
* 오타로 인한 에러를 방지한다
* 변경하면 안 되는 값을 보존한다.
* 데이터를 재사용한다.

### 3) 리터럴
* 프로그래밍에서 리터럴이란 문자가 가리키는 값 그 자체를 의미한다.
* 상수와 리터럴 둘다 변하지 않는 값을 의미하지만 상수는 바뀌지 않는 변수라 생각하면 되고, 리터럴은 변수에 넣은 변하지 않는 데이터를 의미한다고 생각하면 된다.

### 4) 타입 변환
1. 자동 타입 변환

   `byte(1byte) -> short(2byte)/char(2byte) -> int(4byte) -> long(8byte) -> float(4byte) -> double(8byte)`
    * float은 4byte인데 int, long보다 더 뒤쪽에 있는 이유는 float이 표한할 수 있는 값이 더 정밀하기 때문
2. 수동 타입 변환
   
    * 캐스팅 연산자인 `()`를 사용하고, 연산자 안에 변환하고자 하는 타입을 적어주면 된다.

메모리 용량이 넉넉해진 지금은 일반적으로 정수는 int 또는 long, 실수는 double을 사용한다.

## 6. 연산자
### 1) 산술 연산자
* 사칙연산에 사용되는 연산자(`+`, `-`, `*`, `/`)와 나머지를 구하는 연산자(`%`)가 있다.

### 2) 대입 연산자
* 대입연산자(`=`) 는변수에 값을 대입할 때 쓴다.
* 프로그래밍에서 `=`는 오른쪽 결과를 왼쪽에 대입
#### 복합 대입 연산자
`+=` : 더하고 대입

`-=` : 빼고 대입

`*=` : 곱하고 대입

`/=` : 나누고 대입

`%=` : 나머지를 대입

```java
x-=y; // x에서 y를 뺸 값을 x에 대입
```

### 3) 비교 연산자
* 비교 연산자는 boolean타입으로 평가될 수 있는 조건식에 사용된다.
* 대소비교 연산자(`>`, `<`, `>=`, `>=`)와, 등가비교 연산자(`==`, `!=`)가 있다.

* ❗ String은 참조 타입이 때문에 등가 비교 연산자 `==`를 사용하면, 값이 아닌 주소값을 비교하게 된다. 문자열이 나타내는 내용을 비교하려면 equals() 메서드를 사용하면 된다.

### 4) 조건 연산자 (삼항 연산자)
조건 연산자는 ` 조건식 ? 참일 때 결과 : 거짓일 때 결과`와 같이 사용한다.

### 5) 증감 연산자
`++` : 1만큼 증가

`--` : 1만큼 감소
```java
int x=3;
x++; //x에 1을 더해서 4가됨
```

### 6) 논리 연산자
(조건식) `&&` (조건식) : 조건식 모두 참이여야 참

(조건식) `||` (조건식) : 조건식중 하나라도 참이라면 참

`!`(조건식) : 조건식의 상태를 부정

### 7) 비트 연산자
* 비트 연산자는 bit단위로 환산하여 연산한다.
* 양 측의 변수를 이진수로 바꾼뒤 각 자릿수를 비교한다.

a`&`b : a와 b가 모두 1일때 1

a`|`b : a와 b 둘 중 하나라도 1이면 1

a`^`b : a와 b가 같지 않으면 1

`~`a : a의 보수값(not)을 의미한다.

#### 비트 시프트 연산자
a`<<`b : a의 bit를 b만큼 왼쪽으로 움직인다.(빈칸은 0으로 채워짐)

a`>>`b : a의 bit를 b만큼 오른쪽으로 움직인다.(빈칸은 최상위 부호비트와 같은 값으로 채워짐)

a`>>>`b : a의 bit를 b만큼 오른쪽으로 움직인다.(빈칸은 0으로 채워짐)
### 연산자 우선순위
1. 괄호 `[]` `()`
2. 증감(`++`, `--`), 부호(`+`, `-`), 비트 (`~`), 논리(`!`)
3. 산술(`*`, `/`, `%`)
4. 산술(`+`, `-`)
5. 쉬프트(`<<`, `>>`, `>>>`)
6. 비교(`<`, `>`, `<=`, `>=`)
7. 비트(`&`)
8. 비트(`^`)
9. 비트(`|`)
10. 논리(`&&`)
11. 논리(`||`)
12. 조건(`?:`)
13. 대입(`=`, `+=`, `-=`, `*=`, `%=`)

## 7. 조건문
### 1) if문
* if문은 소괄호 안 조건식이이 참일때 실행하고자 하는 코드를 중괄호에 적어주면 된다.
* else if
```java
if(조건식1) {
	//조건식1이 true라면 실행되는 코드	
} else if (조건식2) {
	//조건식1이 false면서 조건식2가 true일 때 실행되는 코드
} else {
	//조건식1과 2가 모두 false일 때 실행되는 코드
}
```


### 2) Switch문
* if문의 경우 조건식이 boolean값이어야 하지만, Switch문은 **변수가 어떤값을 갖느냐에 따라 실행문이 선택**된다.
* switch문은 괄호 안의 값과 동일한 값을 갖는 case로 가서 실행문을 실행한다.
* 괄호 안의 값과 동일한 값을 갖는 case가 없으면 default로 가서 실행문을 실행한다.(default는 생략 가능)
```java
switch (num1){
    case"1":
        System.out.println("1번 실행문");
        break; // break는 다음 case를 실행하지 않고, switch문을 탈출한다.//
    case"2":
        System.out.println("2번 실행문");
        break;
    case"3":
        System.out.println("3번 실행문")
        break;
    default: //switch문의 소괄호 안 값과 같은 case 값이 없으면, 여기서 실행
        System.out.println("일치하는 case가 없습니다.");
        break;
}
```
* switch문에는 int뿐만아니라 char타입 변수도 사용 가능하고, 자바7부터는 문자열도 올 수 있다.


## 8. 반복문
* 반복문에는 for문, while문이 있다.
* 어느쪽을 사용해도 상관 없지만 보통 for문은 반복횟수를 알고 있을 때, while문은 조건에 따라 반복할 때 사용한다
### 1) for문
* for문은 조건식이 참인 동안 주어진 횟수만큼 실행문을 반복적으로 수행한다.
```java
for(초기화; 조건식; 증감식){
    실행문
}
```
과 같이 사용한다.
초기화 : for문이 시작할 때 최초 한번만 수행되며, 사횽할 변수의 초기값을 정한다.
조건식 : 조건식 안의 값이 true면 실행문을 실행하고, false면 반복문이 종료된다.
증감식 : 반복 횟수를 결정하는 규칙이다.

### enhanced for문 (for-each문)
* 향상된 for문은 카운터 변수와 증감식을 사용하지 않는다.
* 배열 및 컬렉션 항목의 개수만큼 반복하고 자동적으로 for문을 빠져나간다.
```java
String[] names = {"AAA", "BBB", "CCC"};
for(String name : names) {  //String으로 선언한 name에 배열 names의 값이 하나씩 들어가면서
    System.out.println(name); // 실행문을 반복한다
}
```
* 배열에서 꺼낸 항목을 저장할 `변수 선언`, `:`, `사용할 배열`로 구성되어 있다.
* 사용할 배열에서 값이 존재하면 해당 값을 변수에 저장하고 실행문을 실행한다.

### 2) while문
* while문은 조건식이 ture일 경우에 계속해서 반복한다.
* for문과 while문은 초기화, 증감식 위치만 다를 뿐 상호 대체가 가능하다.
```java
초기화;
while(조건식){
    실행문;
    증감식;
}
```
* 초기화와 증감식은 필요가 없다면 생략할 수 있다.
### do-while문
do-while문은 무조건 실행문을 1번은 실행하고, 조건식에 따라 반복을 결정한다.
```java
do{
    실행문
} while(조건식)
```

### 3) break, continue
#### break
* break문은 반복문이나 switch문에서 실행을 중지할 때 사용한다.
* 반복문에서 대개 if문과 같이 사용된다.
* 반복문이 중첩되어 있을 경우 break문은 가장 가까운 반복문만 종료한다.

#### continue
* continue문은 반복문에서만 사용되는데, 블록 내부에서 continue문이 실행되면 for문의 증감문, while문의 조건식으로 바로 이동하여 작동한다
* 반복문에서 대개 if문과 같이 사용된다.

## 9. 배열

### 1) 1차원 배열
#### 배열의 선언
```java
int[] arr1;
int arr2[];
```
* arr이라는 이름으로 변수를 선언했지만 이것은 **참조변수를 위한 공간만 만든 것**이다.

#### 배열의 생성, 초기화

```java
int[] arr1 = new int[3]; // 배열 선언 및 생성

int[] arr2; // 배열 선언
arr2 = new int[5]; // 배열 생성

int[] arr3 = {1, 2, 3, 4} //배열의 선언과 동시에 초기화 가능

int[] arr4; //배열 선언
arr4 = {1, 3, 5, 7, 9}; //배열 초기화 (에러 발생)
arr4 = new int[]{1, 3, 5, 7, 9}; //배열 생성 및 초기화 (정상 동작)
```

#### 배열의 길이와 인덱스
![](./images/배열.PNG)

* `배열이름.length`로 배열이 가진 요소의 갯수를 알 수 있다.

### 2) 다차원 배열
* 배열 안에 배열이 중첩된 배열을 말한다.

  ```java
  int[][] arr = new int[3][4]; 
  ```
  위와 같이 배열을 선언 후 생성하면 2차원 배열이 된다.

|            | 0         | 1         | 2         | 3         |
|------------|-----------|-----------|-----------|-----------|
| **arr[0]** | arr[0][0] | arr[0][1] | arr[0][2] | arr[0][3] |
| **arr[1]** | arr[1][0] | arr[1][1] | arr[1][2] | arr[1][3] |
| **arr[2]** | arr[2][0] | arr[2][1] | arr[2][2] | arr[2][3] |

* 이러한 구조가 1번 더 반복되면 3차원 배열이 된다.


### 가변 배열
* 2차원 이상의 다차원 배열은 마지막차수의 배열은 유동적으로 바꿀 수 있다.

```java
int[][] arr = new int[2][];
arr[0] = new int[3];
arr[1] = new int[2];
```
|              |      0      |      1      |     2      |
|:------------:|:-----------:|:-----------:|:----------:|
|  **arr[0]**  |  arr[0][0]  |  arr[0][1]  | arr[0][2]  |
|  **arr[1]**  |  arr[1][0]  |  arr[1][1]  |            |

* 메모리가 비효율적으로 사용되는 것을 방지할 수 있다.
* 이 경우 `arr.length`는 2, `arr[0].length`는 3, `arr[1].length`는 2가 된다.

___
참고

https://madplay.github.io/post/java-string-literal-vs-string-object

https://mommoo.tistory.com/14

https://chung-develop.tistory.com/21

https://staticclass.tistory.com/25

https://park-youjin.tistory.com/11

https://beingdesigner.tistory.com/6

codestates 교육