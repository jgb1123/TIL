# JavaScript
## 자바 스크립트란?
* HTML은 구조, CSS는 디자인, 자바스크립트는 동작을 담당한다고 생각하면 된다.
* 자바스크립트 프레임워크인 React, Vue.js, Angular등을 제대로 활용하기 위해 반드시 알아야 한다.
* 자바스크립트 코드는 `<script>` 태그를 HTML문서 안에 넣어 사용이 가능하다.
* 또한 HTML과 별개인 .js 확장자의 파일로 저장한 후 불러올 수 있다.
  * `<script>`의 src 속성으로 HTML문서 위치의 기준 파일 경로를 입력해주면 됨

    ```html
    <script src="./index.js"></script>
    ```
### 자바스크립트 특징
* 인터프리터 언어로 코드를 컴파일하지 않고 바로 실행할 수 있다.
* 변수의 타입을 선언하지 않고 실행 시간에 동적으로 타입이 결정된다.
* 주로 웹 브라우저에서 클라이언트 사이드 애플리케이션 개발을 위해 사용된다.
* 자바스크립트는 객체지향 프로그래밍을 지원하여 객체와 프로토타입 체인을 이용하여 상속을 구현한다.
* 함수를 변수에 할당하고 함수를 다른 함수의 인자로 전달하거나 반환할 수 있다.
* 함수가 자신의 외부 범위에 있는 변수에 접근할 수 있는 기능인 클로저가 있다.
* DOM을 조작하여 웹 페이지의 내용과 스타일을 동적으로 변경할 수 있다.
* 콜백 함수나 프로미스, async/await를 사용하여 비동기 처리를 지원한다.
* AJAX기술을 통해 서버와 비동기적으로 실시간 통신 기능을 제공한다.
* Node.js를 사용하여 서버사이드 애플리케이션도 개발할 수 있다.

### Vue, Angular, React
#### Vue.js
* 간편한 문법과 직관적인 디자인으로 빠르게 배울 수 있다.
* 점진적으로 채택 가능한 특징으로 기존 프로젝트에 쉽게 통합할 수 있다.
* 양방향 데이터 바인딩을 지원하여 데이터와 UI가 항상 동기화된다.
* 컴포넌트 기반 아키텍처로 재사용성과 모듈성이 뛰어나다.
* 가볍고 빠른 성능을 제공한다.

#### Angular
* TypeScript를 기본 언어로 사용하여 정적 타입 검사와 개발 생산성을 높인다.
* 디렉티브를 활용하여 템플릿 내에서 특별한 기능을 수행할 수 있다.
* 컴포넌트 기반 라우팅으로 SPA 개발에 적합한 라우팅을 제공한다.
* 의존성 주입(DI)을 지원하여 모듈화와 테스트 용이성을 강조한다.
* RxJS를 활용하여 비동기 프로그래밍을 강력하게 지원한다.

#### React
* 가상 DOM을 사용하여 효율적인 렌더링을 지원하여 성능을 최적화한다.
* JSX를 사용하여 컴포넌트와 UI를 정의하여 가독성과 생산성을 높인다.
* 단방향 데이터 흐름으로 데이터의 흐름이 예측 가능하고 유지보수가 쉽다.
* 컴포넌트 기반 아키텍처로 재사용성과 구조화된 개발을 장려한다.
* 라이브러리 중심으로 생태계가 풍부하여 다양한 기능을 지원한다.

## 변수
* let은 데이터 값 변경이 가능한 변수이며, const는 데이터 값의 수정이 불가능한 상수이다.
```javascript
let value;  // 가능
const value;    // 불가

let name = '군밤';
name = '군밤12';   // 값 변경 가능

const name = '군밤';
name = '군밤12';   // 값 변경 불가
```
* 숫자 타입 변수 간에 계산이 가능하며 문자열 타입 변수 간 결합이 가능하다.
```javascript
let a = 1, b = 2;
const a = 1, b = 2;

let num1 = 10;
let num2 = 20;
let sum = num1 + num2;
console.log(sum)    // 30

let name = '군밤';
let num = '12';
let nickname = name + num;
console.log(nickname);  // '군밤12'

const num1 = 10;
const num2 = 20;
const sum = num1 + num2;
console.log(sum);   // 20

const name = '군밤';
const num = '12';
const nickname = name + num;
console.log(nickname);  // '군밤12'
```
* const를 적극 사용해야 변경이 필요한 데이터와 그렇지 않은 데이터의 구분이 쉬워져 코드의 가독성이 높아진다.
* 변수는 문자, 숫자, $, _만 사용 가능하다.
* 첫글자는 숫자가 될 수 없고 예약어는 사용할 수 없다.

## 연산자
### 비교연산자
|    구문     | 의미                  |
|:---------:|---------------------|
|  A == B   | A,B의 값이 같은지         |
|  A === B  | A,B의 값과 데이터 타입이 같은지 |
|  A != B   | A,B의 값이 다른지         |
|  A !== B  | A,B의 값과 데이터 타입이 다른지 |
|   A < B   | A가 B보다 작은지          |
|  A <= B   | A가 B보다 작거나 같은지      |
|   A > B   | A가 B보다 큰지           |
|  A >= B   | A가 B보다 크거나 같은지      |

```javascript
console.log('ABC' == 'ABC'); // true
console.log('ABC' != 'CCC'); // true

const arr1 = [1, 2, 3];
const arr2 = [1, 2, 3];
console.log(arr1 == arr2); // 참조하는 곳이 다르므로 false

const arr3 = [1, 2, 3];
const arr4 = arr3;
console.log(arr3 == arr4); // 참조하는 곳이 같으므로 true
```

### 대입 연산자
|   구문    | 의미         |
|:-------:|------------|
|  x = y  | x = y      |
| x += y  | x = x + y  |
| x -= y  | x = x - y  |
| x *= y  | x = x * y  |
| x **= y | x = x ** y |
| x /= y  | x = x / y  |
| x %= y  | x = x % y  |

## 함수
* 함수는 처리 작업을 하나로 모아 이름을 지정하여 작업을 반복하여 사용하고 싶을 때 사용한다.
```javascript
function myFunction(a) {
        const result = a + 1;
    return result;
}
const functionResult = myFunction(1);
console.log(functionResult);  // 결과 2
```
* 파라미터 개수는 제한이 없으며 ,로 구분한다.
* 파라미터가 없는 함수도 있다.
* return 구문으로 함수가 종료되며, 하나의 함수에서 return 구문으로 여러 값을 반환할 수 있다.
  * 배열 사용 
    ```javascript
    function getValues() {
      const value1 = 1;
      const value2 = 2; 
      return [value1, value2];
    }

    const values = getValues();
    console.log(values[0]);  // 결과 1
    console.log(values[1]);  // 결과 2
    ```
  * 객체 사용
    ```javascript
    function getValues() {
      const value1 = 1;
      const value2 = 2;
      return {
        value1,
        value2
      };
    }
    
    const values = getValues();
    console.log(values.value1); // 결과 1
    console.log(values.value2); // 결과 2
    ```

### 화살표 함수
* 함수를 간략히 기술하고 싶거나 this로 묶고 싶을 때는 화살표 함수를 사용한다.
```javascript
const numberSum = (a, b, c) => {
  const result = a + b + c;
  return result;
};
```
* 파라미터가 하나인 경우는 소괄호를 생략 가능하며 파라미터가 0개 혹은 2개 이상이면 생략 불가능하다.
```javascript
const myFunction = a => {
  return a + 1;
};
```

### 파라미터
* 초기값이 설정된 파라미터는 값을 전달받지 않으면 디폴트 파라미터를 사용한다.
```javascript
function myFunction(a, b = 2) { // b의 인수를 생략하면 초기값 2가 적용된다.
  const result = a + a * b;
  return result;
} 
```
* 임의의 파라미터를 가지는 함수를 정의하고 싶으면 `...파라미터`를 사용하면 된다.
```javascript
function numberSum(...prices) {
  let result = 0;
  for(const value of prices) {
    result += value;
  }
}
const result = numberSum(1, 2);
console.log(result); // 결과 3
```

## 조건문
### if
* 자바스크립트는 if, else if, else를 사용하여 조건에 따른 처리가 가능하다
* if 단독으로도 사용 가능하며 else if를 생략한 if, else절도 가능하다.
```javascript
const age = 28;

if(age >= 40 {
  alert('age는 30 이상');
} else if (age >= 20) {
  alert('age는 20 이상 30 미만');
} else {
  alert('age는 20 미만');
}
// age는 20 이상 30 미만 출력
```

### switch
* swtich문은 case값이 조건식에 만족 시 처리되며, 만족되는 조건이 하나도 없으면 default값으로 처리된다.
* break는 종료하는 명령문이다.
```javascript
const bloodType = 28;

switch(age) {
  case 'A':
    alert('A형');
    break;
  
  case 'B':
    alert('B형');
    break;
  
  case 'AB':
    alert('AB형');
    break;
  
  default:
    alert('O형');
    break;
}
```
* switch 식은 값만 비교하는 `==`와 달리 값과 타입의 비교 둘 다 이루어진다.

## 반복문
### for문
* for문은 반복 작업을 처리하며 대량의 데이터를 처리하거나 배열을 다룰 때 유용하다.
```javascript
for(let i = 0; i < 5; i++) {
  console.log(i);
}
// 0부터 4까지 순서대로 출력
```

### while문
* while문은 조건을 만족하면 계속 반복 작업을 처리하며 for문과 유사하나 반복 조건만을 지정한다.
* 코드를 통해 반복 종료 시점을 지정해야 한다.
```javascript
let i = 0;
while(i < 5) {
  console.log(i);
  i += 1;
}
// 0부터 4까지 순서대로 출력
```

### continue
* for문과 while문의 반복 처리 작업 중 예외가 필요할 경우 사용한다.
* continue문을 사용하면 해당 루프의 작업을 실행하지 않고 넘어간다.

## 브라우저 저장소
### 쿠키
* 웹사이트에 유저의 정보를 저장하는 것이다.
* 서버와 데이터를 공유하는 용도로 사용되며 데이터의 유효기간을 정할 수 있다.
* 장점으로는 대부분의 브라우저가 지원을 한다는 점이지만, 단점으로는 4kb 데이터 저장 제한으로 사이즈가 매우 작으며 서버에 매 HTTP 요청으로 데이터 전달 낭비가 발생할 수 있다.

### 로컬 스토리지
* 가볍지만 기능이 많지 않고 단순히 key와 value를 String 타입으로 저장하는 기능만 있다.
* 최대 저장 용량은 5~10MB로 제한하여 간단한 텍스트만 담을 수 있지만 만료기간 없이 저장이 가능하여 자동 로그인과 같은 곳에 사용된다.

```javascript
localStorage.setItem('age', 30); // 'age'라는 키에 '30'이라는 값을 저장
localStorage.getItem('age') // 'age'라는 키에 저장된 값인 '30'을 반환
localStorage.removeItem('age'); // 'age'라는 키에 저장된 값을 제거

localStorage.setItem('name', 'JGB');
console.log(localStorage.length) // 1 출력
localStorage.clear(); // 로컬 스토리지를 완전히 비움
console.log(localStorage.length) // 0 출력
```

### 세션 스토리지
* 로컬 스토리지와 달리 만료기간이 있어 브라우저를 종료하거나 새탭을 열 때 데이터가 초기화된다.
  * 로컬 스토리지와 세션 스토리지의 차이는 영구성이다.
* 입력폼 정보 저장, 비로그인 장바구니, 글쓰기 도중 내용 복구 등과 같은 자동 임시 저장 용도로 사용한다.
* 사용법은 로컬스토리지와 동일하다. 
  ```javascript
  sessionStorage.setItem('age', 30); // localStorage의 메서드들과 메서드 동일
  ```

