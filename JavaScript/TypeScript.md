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
let arr4: [string, number] = ["hello", "123"];
```

### 객체
```typescript
let person1: object = { name: "JGB", age: 30};
let person2: { name: string; age:number } = {
  name: "JGB",
  age: 30
};
```
### 불리언
```typescript
let isThatTrue: boolean = true;
```

### 튜플
* 요소의 타입과 개수가 고정된 배열을 표현할 수 있다.
* 요소들의 타입이 모두 같을 필요는 없다.

```typescript
// 튜플 타입으로 선언
let x: [string, number];
// 초기화
x = ["hello", 10]; // 성공
// 잘못된 초기화
x = [10, "hello"]; // 오류
```

```typescript
console.log(x[0].substring(1)); // 성공
console.log(x[1].substring(1)); // 오류, 'number'에는 'substring' 이 없음
```

```typescript
x[3] = "world"; // 오류, '[string, number]' 타입에는 프로퍼티 '3'이 없음

console.log(x[5].toString()); // 오류, '[string, number]' 타입에는 프로퍼티 '5'가 없음
```

### Any
* 애플리케이션을 만들 때 알지 못하는 타입을 표현해야 할 수도 있다.
* 이 값들은 사용자로부터 받은 데이터나 파티 라이브러리같은 동적인 컨텐츠에서 올 수 있다.
* 이 경우 타입 검사를 하지 않고, 그 값들이 컴파일 시간에 검사를 통과하길 원할 수 있다.

```typescript
let notSure: any = 4;
notSure = "hello"; // 성공
notSure = false; // 성공
```

* nay타입은 타입의 일부만 알고 전체는 알지 못할 때 유용한다.
  * 예시로, 여러 다른 타입이 섞인 배열을 다룰 수 있다.
  ```typescript
  let list: any[] = [1, true, "free"];
  list[1] = 100;
  ```

### Void
* 어떤 타입도 존재할 수 없음을 나타내기 때문에 any와 반대 타입과 같다.
* void는 보통 함수에서 반환값이 없을 때 반환 타입을 표현하기 위해 많이 쓴다.
```typescript
function warnUser(): void {
    console.log("This is my warning message");
}
```
* void로 타입 변수를 선언하는것은 유용하지 않다.
  * 그 변수에는 null(--strictNullChecks를 사용하지 않을 때만 해당)또는 undefined만 할당할 수 있다.

### Never
* 절대 발생할 수 없는 타입을 나타낸다.
* 함수 표현식이나 화살표 함수 표현식에서 항상 오류를 발생시키거나 절대 반환하지 않는 반환 타입으로 사용된다.
* never 타입은 모든 타입의 하위 타입이다. (never 타입을 다른 타입에 할당할 수 있음)
* 하지만 never타입은 다른 타입에 할당할 수 없다. (any타입 조차 never타입에 할당할 수 없음)

```typescript
// never를 반환하는 함수는 함수의 마지막에 도달할 수 없음
function error(message: string): never {
    throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {
  }
}
```

### Ojbect
* object는 원시타입이 아닌 타입을 나타낸다. (number, string, boolean, bigint, symbol, null, undefined가 아닌 나머지)
  * 예를 들어 함수, 배열, 사용자 지정 객체, 내장 객체 등을 모두 포함한다.
* declare와 함께 아래 예제와 같이 JavaScript 환경에서 사용되는 외부 함수나 라이브러리와 함께 TypeScript를 사용할 수 있다.
```typescript
declare function create(o: object | null): void;
```

### 타입 단언(Type assertions)
* TypeScript보다 개발자가 값에 대해 더 잘 알고 있을때가 있다.
  * 보통 어떤 엔티티의 실제 타입이 현재 타입보다 더 구체적일 때 발생한다.
* 타입단언은 개발자가 컴파일러에게 본인이 타입을 알고있으니 해당 타입으로 다루라고 선언하는 방법이다.

```typescript
// as키워드
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

```typescript
// <>이용
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;
```


* 타입 단언은 아래와같은 상황에 많이 사용된다.
  * 외부 라이브러리를 사용할 때, 라이브러리가 TypeScript와 함께 사용하기에 충분한 타입 정보를 제공하지 않는 경우
  * TypeScript의 타입 추론이 개발자의 의도와 일치하지 않는 경우
  * TypeScript가 특정 연산 또는 패턴을 올바르게 타입검사하지 못하는 경우
* 하지만 타입 단언을 남용하면 타입 안정성에 문제가 발생할 수 있으므로 가능한 TypeScript의 타입을 이용하는 것이 좋다.



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

## 유틸리티
### Partial<T>
* T의 모든 프로퍼티를 선택적으로 만드는 타입을 구성한다.
* 주어진 타입의 모든 하위 타입 집합을 나타내는 타입을 반환한다.

```typescript
interface Todo {
    title: string;
    description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
    return { ...todo, ...fieldsToUpdate };
}

const todo1 = {
    title: 'organize desk',
    description: 'clear clutter',
};

const todo2 = updateTodo(todo1, {
    description: 'throw out trash',
});
```

### Readonly<T>
* T의 모든 프로퍼티를 읽기 전용으로 설정한 타입을 구성한다.
* 생성된 타입의 프로퍼티는 재할당할 수 없다.

```typescript
interface Todo {
    title: string;
}

const todo: Readonly<Todo> = {
    title: 'Delete inactive users',
};

todo.title = 'Hello'; // 에러
```

* 런타임에 실패할 할당 표현식을 나타낼 때 유용한다.

```typescript
function freeze<T>(obj: T): Readonly<T>;
```

### Record<K, T>
* 타입 T의 프로퍼티의 집합 K로 타입을 구성한다.
* 타입의 프로퍼티들을 다른 타입에 매핑시키는 데 사용할 수 있다.

```typescript
interface PageInfo {
    title: string;
}

type Page = 'home' | 'about' | 'contact';

const x: Record<Page, PageInfo> = {
    about: { title: 'about' },
    contact: { title: 'contact' },
    home: { title: 'home' },
};
```

### Pick<T,K>
* T에서 프로퍼티 K의 집합을 선택해 타입을 구성한다.

```typescript
interface Todo {
    title: string;
    description: string;
    completed: boolean;
}

type TodoPreview = Pick<Todo, 'title' | 'completed'>;

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false,
};
```

### Omit<T,K>
* T에서 모든 프로퍼티를 선택한 다음 K를 제거한 타입을 구성한다.

```typescirpt
interface Todo {
    title: string;
    description: string;
    completed: boolean;
}

type TodoPreview = Omit<Todo, 'description'>;

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false,
};
```

### Exclude<T,U>
* T에서 U에 할당할 수 있는 모든 속성을 제외한 타입을 구성한다.

```typescript
type T0 = Exclude<"a" | "b" | "c", "a">;  // "b" | "c"
type T1 = Exclude<"a" | "b" | "c", "a" | "b">;  // "c"
type T2 = Exclude<string | number | (() => void), Function>;  // string | number
```

### Extract<T,U>
* T에서 U에 할당할 수 있는 모든 속성을 추출하여 타입을 구성한다.

```typescript
type T0 = Extract<"a" | "b" | "c", "a" | "f">;  // "a"
type T1 = Extract<string | number | (() => void), Function>;  // () => void
```

### NonNullable<T>
* T에서 null과 undefined를 제외한 타입을 구성한다.

```typescript
type T0 = NonNullable<string | number | undefined>;  // string | number
type T1 = NonNullable<string[] | null | undefined>;  // string[]
```

### Parameters<T>
* 합수 타입 T의 매개변수 타입들의 튜플 타입을 구성한다.

```typescript
declare function f1(arg: { a: number, b: string }): void
type T0 = Parameters<() => string>;  // []
type T1 = Parameters<(s: string) => void>;  // [string]
type T2 = Parameters<(<T>(arg: T) => T)>;  // [unknown]
type T4 = Parameters<typeof f1>;  // [{ a: number, b: string }]
type T5 = Parameters<any>;  // unknown[]
type T6 = Parameters<never>;  // never
type T7 = Parameters<string>;  // 오류
type T8 = Parameters<Function>;  // 오류
```

### ConstructorParameters<T>
* 생성자 함수 타입의 모든 매개변수 타입을 추출할 수 있게 해준다.
* 모든 매개변수 타입을 가지는 튜플타입을 생성한다. (T가 함수가 아닌 경우 never)

```typescript
type T0 = ConstructorParameters<ErrorConstructor>;  // [(string | undefined)?]
type T1 = ConstructorParameters<FunctionConstructor>;  // string[]
type T2 = ConstructorParameters<RegExpConstructor>;  // [string, (string | undefined)?]
```

### ReturnType<T>
* 함수 T의 반환 타입으로 구성된 타입을 만든다.

```typescript
declare function f1(): { a: number, b: string }
type T0 = ReturnType<() => string>;  // string
type T1 = ReturnType<(s: string) => void>;  // void
type T2 = ReturnType<(<T>() => T)>;  // {}
type T3 = ReturnType<(<T extends U, U extends number[]>() => T)>;  // number[]
type T4 = ReturnType<typeof f1>;  // { a: number, b: string }
type T5 = ReturnType<any>;  // any
type T6 = ReturnType<never>;  // any
type T7 = ReturnType<string>;  // 오류
type T8 = ReturnType<Function>;  // 오류
```

### InstanceType<T>
* 생성자 함수 타입 T의 인스턴스 타입으로 구성된 타입을 만든다.

```typescript
class C {
    x = 0;
    y = 0;
}

type T0 = InstanceType<typeof C>;  // C
type T1 = InstanceType<any>;  // any
type T2 = InstanceType<never>;  // any
type T3 = InstanceType<string>;  // 오류
type T4 = InstanceType<Function>;  // 오류
```

### Required<T>
* T의 모든 프로퍼티가 필수로 설정된 타입을 구성한다.

```typescript
interface Props {
    a?: number;
    b?: string;
};

const obj: Props = { a: 5 }; // 성공

const obj2: Required<Props> = { a: 5 }; // 오류: 프로퍼티 'b'가 없습니다
```

### ThisParameterType
* 함수 타입의 this 매개변수의 타입 혹은 함수 타입에 this 매개변수가 ㅇ벗을 경우 unknown을 추출한다.
* --strictFunctionTypes가 활성화 되었을 때만 올바르게 동작한다. [strictFunctionTypes?](https://typescript-kr.github.io/pages/compiler-options.html)

```typescript
function toHex(this: Number) {
  return this.toString(16);
}

function numberToString(n: ThisParameterType<typeof toHex>) {
  return toHex.apply(n);
}
```

### OmitThisParameter
* 함수 타입에서 this 매개변수를 제거한다.
* --strictFunctionTypes가 활성화 되었을 때만 올바르게 동작한다.

```typescript
function toHex(this: Number) {
  return this.toString(16);
}

const fiveToHex: OmitThisParameter<typeof toHex> = toHex.bind(5); // bind는 이미 반환타입으로 OmitThisParameter 사용 중, 예제를 위함

console.log(fiveToHex());
```

### ThisType<T>
* 변형된 타입을 반환하지 않는다.
* 문맥적 this타입에 표시하는 역할을 한다.
* 이 유틸리티를 사용하기 위해선 --noImplicitThis 플래그를 사용해야 한다.[noImplicitThis?](https://typescript-kr.github.io/pages/compiler-options.html)

```typescript
type ObjectDescriptor<D, M> = {
  data?: D;
  method?: M & ThisType<D & M>; // 메서드 안 this의 타입은 D & M
}

function makeObject<D, M>(desc: ObjectDescriptor<D, M>): D & M {
  let data: object = desc.data || {};
  let methods: object = desc.method || {};
  return { ... data, ...methods } as D & M;
}

let obj = makeObject({
  data: { x: 0, y: 0},
  methods: {
    moveBy(dx: number, dy: number) {
      this.x += dx; // 강력하게 타입이 정해진 this
      this.y += dy; // 강력하게 타입이 정해진 this
    }
  }
})

obj.x = 10;
obj.y = 20;
obj.moveBy(5, 5);
```

## Iterables
* 객체가 Symbol.iterator 프로퍼티에 대한 구현을 갖고 있으면 이터러블로 간주한다.
* Array, Map, Set, String, Int32Array, Uint32Array 등과 같은 일부 내장 타입에는 이미 Symbol.iterator 프로퍼티가 구현되어있다.

### for..of 문
* for..of는 객체의 Symbol.iterator 프로퍼티를 호출하여 이터러블 객체를 반복한다.

```typescript
let someArray = [1, "string", false];

for (let entry of someArray)
  console.log(entry); // 1, "string", false
}
```

#### for..of vs for..in
* for..in 은 반복되는 객체의 키 목록을 반환하며, for..of는 반복되는 객체의 숫자 프로퍼티 값 목록을 반환한다.

```typescript
let list = [4, 5, 6];

for (let i in list){
  console.log(i); // "0", "1", "2"
}

for (let i of list){
  console.log(i); // "4", "5", "6"
} 
```

* for..in은 모든 객체에서 작동한다. 객체의 프로퍼티를 검사하는 방법으로 사용된다.
* for..of는 이터러블 객체의 값에 주로 관심이 있다.

```typescript
let pets = new Set(["Cat", "Dog", "Hamster"]);
pets["species"] = "mammals";

for (let pet in pets){
  console.log(pet); // "species"
}

for (let pet of pets){
  console.log(pet); // "Cat", "Dog", "Hamster"
}
```