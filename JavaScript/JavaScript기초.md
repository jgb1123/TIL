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

