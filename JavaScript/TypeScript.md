# TypeScript
## TypeScript?
* 자바스크립트의 단점을 보완해 만든 언어이다.
* 마이크로소프트에서 개발 및 유지하고 있으며 엄격한 문법을 지원한다.
* 자바스크립트로 작성된 프로그램이 타입스크립트 프로그램으로도 동작한다.
  * 컴파일 시 자바스크립트 파일을 만들어 실행하기 때문이다.
* 타입스크립트에서 원하는 타입을 정의하고 프로그래밍을 하면 자바스크립트로 컴파일되어 실행할 수 있다.

## 장점
### 에러 예방
* 타입을 부여하기 때문에 코드 작성 시 알맞지 않은 타입을 넣거나, 타입을 넣지 않았을 경우 IDE에서 확인할 수 있다.
* 사전에 오류를 디버깅 할 수 있고 나중에 디버깅하는 시간을 줄여주기 때문에 효율적이다.

### 실행 속도
* 자바스크립트는 동적 타입의 인터프리터 언어이다.
* 따라서 자바스크립트는 런타임 시 타입을 결정해서 적용되는데, 컴퓨터에게 오류가 있는지 없는지를 체크하라고 맡긴 것과 같기 때문에 실행 속도가 비교적 오래 걸린다.
* 하지만 타입스크립트는 사람이 코드 작성 시 오류를 체크하고 타입을 미리 결정하기 때문에 실행속도가 비교적 빠르다는 장점이 있다.

### 가독성, 안정성
* 자바스크립트의 경우 코드를 읽을 경우 타입이 어떤 것인지 짐작하기 어려울 때가 많다.
* 또한 실행 중 버그를 찾기 때문에 테스트 때 미처 발견하지 못한 에러가 운영 중에 발견될 수 있다.
* 하지만 타입스크립트는 타입을 명시할 수 있고 컴파일 시 오류를 찾기 때문에 보다 더 안정적이며, 가독성 또한 올라간다.

## 단점
### 세팅
* 자바스크립트에 비해 초기 세팅이 까다롭다. (컴파일 옵션, 모듈 사용 설정 등)

### 코드량 증가
* 자바스크립트보다 더 많은 코드를 작성해야 한다.
* 이로인해 개발 기간이 늘어날 수 있고, 코드를 더 작성하기 때문에 코드가 더 복잡해 질 수 있다.

### 학습시간
* 새로운 언어이기 때문에 어느정도 러닝커브가 필요하다.

## 기본 타입 선언
### 문자열
```typescript
let hello: string = "hello!"
```

### 숫자
```typescript
let num: number = 123;
```

### 배열
```typescript
let arr1: number[] = [10, 20, 30];
let arr2: Array<number> = [10, 20, 30];
let arr3: Array<string> = ["hello", "world"];
let arr4: [string, number] = ["hello", "123];
```

### 객체
```typescript
let person1: object = { name: "JGB", age: 30};
let person2: { name: string; age:number } = {
  name: "JGB"
  age: 30
};
```
### 불리언
```typescript
let isThatTrue: boolean = true;
```

## 함수 선언
### 함수 타입 선언
```typescript
function add(x: number, y: number): number {
  return x + y;
}
```

### 선택적 매개변수
```typescript
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
    return firstName + " " + lastName;
  } else {
    return firstName;
  }
}

let result1 = buildName("GB");
let result2 = buildName("GB", "Jang");
```

## 인터페이스
* 자주 사용하는 타입들을 object 형태의 묶음으로 정의해 새로운 타입을 만드는 기능이다.
### interface 선언
```typescript
interface User {
  age: number;
  name: string;
}
```

### 변수 활용
```typescript
const jgb: User = { name: "GBJang, age: 30 }
```

### 함수 인자로의 활용
```typescript
function checkUser(user: User) {
  console.log(user);
}

checkUser({ name: "GBJang", age: 30 });
```

### 함수 구조 활용
```typescript
interface Add {
  (x: number, y: number): number;
}

let addFunc: Add = (a, b) => a + b;

console.log(addFunc(7, 10));
```

### 배열 활용
```typescript
interface StringArr {
  [index: number]: string;
}

let arr: StringArr = ["a", "b", "c"];
```

### 객체 활용
```typescript
interface Obj {
  [key: string]: string;
}

const obj: Obj {
  person1: "GBJang",
  person2: "HYKim"
}
```

### interface 확장
```typescript
interface Person {
  name: string;
  age: number;
}

interface Developer extends Person {
  position: string;
}

const jgb: Developer = {
  name: "GBJang",
  age: 30,
  position: "BackEnd"
};
```

## 타입(type)
* interface와는 다르게 새로운 타입을 생성하는 것이 아닌 별칭을 부여하는 것이다.
* interface와 type의 가장 큰 차이점은 타입의 확장 가능 여부이다.
* type은 extends 키워드는 사용할 수 없으며, 따라서 type 보다는 interface 사용이 권장된다.

### 타입 별칭 선언
```typescript
type StrOrNum = string | number;

const str1: StrOrNum = "hello";
const str2: StrOrNum = 123;
```

## 연산자
### Union Type (유니온 타입)
* 한 개 이상의 type을 선언할 때 사용하며, `|`키워드를 사용한다.

```typescript
function strOrNum (value: string | number) {
  if(typeof value === 'string') {
    ...
  } else if(typeof value === 'number') {
    ...
  } else {
    ...
  }
}
```

### Intersection Type (교차 타입)
* 함수 호출의 경우 함수 인자에 명시한 type을 모두 제공해야 한다.

```typescript
interface Person {
  name: string;
  age: number;
}

interface Skill {
  name: string;
  position: string;
}

type Developer = Person & Skill;

let devPerson: Developer = {
  name: "GBJang",
  age: 30,
  position: "BE"
};
```

## Class
### 접근 제한자
* public, private, protected가 있다.

| 접근 제한자    | public | protected | private |
|-----------|--------|-----------|---------|
| 클래스 내부    | O      | O         | O       |
| 자식 클래스 내부 | O      | O         | X       |
| 클래스 인스턴스  | O      | X         | X       |

### Class에서 타입 선언
```typescript
class Person {
  // constructor 위에 선언
  private name: string;
  public age: number;
  readonly log: string;
  
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}
```

## Enum
* TypeScript는 상수들의 집합을 정의할 수 있다.
* 숫자와 문자열 기반 열거형을 제공한다.

### 숫자형 enum
* 자동으로 0에서 1씩 증가하는 값을 부여한다

```typescript
enum Brands {
  Nike,		    // 0
  Adidas,		// 1
  NewBalance	// 2
}

const myShoes = Brands.Nike;  //0
```

### 문자형 enum
```typescript
enum Player {
  kim = '김',
  park = '박'
}

const player = Player.park;	// 박
```

## 제네릭
* Java의 제네릭과 비슷하다.
* 다양한 타입에서 작동하는 컴포넌트를 작성할 수 있다.

### 제네릭 선언
* `<T>`와 같이 타입을 선언하며, 알파벳은 T를 많이 사용한다.

```typescript 
function logText<T>(text: T): T {
  consol.log(text);
  return text;
}

logText<string>('Hello!');
```

### interface에 제네릭 선언
```typescript
interface Product<T> {
  menu: T;
  price: number;
}

const noodle: Product<string> = { menu: 'noodle', price : 5000 };
```

### 제네릭 타입 제한
#### 배열 힌트
```typescript
function textLength<T>(text: T[]): T[] {
    console.log(text.length);
    return text;
}

textLength<string>(['hello', 'world']);
```

#### 정의된 타입 이용
```typescirpt
interface LengthType {
    length: number;
}

function logTextLenth<T extends LengthType>(text: T): T {
    console.log(text.length);
    return text;
}

logTextLenth('hello world'); // 11
logTextLenth(100); // 에러
logTextLenth({ length: 100 }); // 100
```

#### keyof
```typescript
interface Product {
    menu: string;
    price: number;
    stock: number;
}

function getProductOption<T extends keyof Product>(productOption: T): T {
    return productOption;
}

getProductOption('price'); // 'menu', 'price', 'stock'만 인자로 사용 가능
```