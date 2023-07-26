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
