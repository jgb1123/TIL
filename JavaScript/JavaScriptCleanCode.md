# JavaScriptCleanCode
## Ternary Operator
### 나쁜 코드
```javascript
function getResult(score) {
  let result;
  if (score > 5) {
    result = 'up';
  } else if (score <= 5) {
    result = 'down';
  }
  return result
}
```

### 좋은 코드
```javascript
function getResult(score) {
  return score > 5 ? 'up' : 'donw';
}
```

## Nullish coalescing operator
### 나쁜 코드
```javascript
function printMessage(text) {
  let message = text;
  if (text == null || text == undefined) {
    message = 'No Content';
  }
  console.log(text);
}
```

### 좋은 코드
```javascript
function printMessage(text) {
  const message = text ?? 'No Content';
  console.log(text);
```

#### 주의
```javascript
function printMessage(text = 'No Content') {
  console.log(text);
}
```
* 위와 같은 default parameter는 undefind인 경우에만 적용된다. (null에는 적용 x) 

#### vs Logical OR operator
```javascript
function printMessage(text) {
  const message = text || 'No Content';
  console.log(text);
```
* Logical OR operator는 undefined, null, 0, -0, '', false과 같은 falsy의 경우 적용
* Nullish coalescing operator는 undefined, null인 경우에만 적용

## Object Destructuring
### 나쁜 코드
```javascript
function displayPerson(person) {
  displayAvatar(person.name);
  displayName(person.name);
  displayProfile(person.name, person.age);
}
```
```javascript
function displayPerson(person) {
  const name = person.name;
  const age = person.age;
  displayAvatar(name);
  displayName(name);
  displayProfile(name, age);
}
```

### 좋은 코드
```javascript
function displayPerson(person) {
  const { name, age } = person;
  displayAvatar(name);
  displayName(name);
  displayProfile(name, age);
}
```

## Spread Syntax
```javascript
const item = {type: 'A', size: 'M' };
const detail = { price:20, mode: 'Korea', gender: 'M' };
```
### 나쁜 코드
```javascript
item['price'] = detail.price; 
```
```javascript
const new Object = new Object();
newObject['type'] = item.type;
newObject['size'] = item.size;
newObject['price'] = detail.price;
newObject['made'] = detail.mode;
newObject['gender'] = detail.gender;
```
```javascript
const newObject = {
  type: item.type,
  size: item.size,
  price: detail.price,
  made: detail.made,
  gender: detail.gender,
};
```

### 덜 좋은 코드
```javascript
const shirt = Obect.assign(item, detail);
```
### 좋은 코드
```javascript
const shirt = { ...item, ...detail}; 
```
```javascript
const shirt = { ...item, ...detail, price: 40};
```

## Optional Chaining
```javascript
const bob = {
  name : 'julia',
  age: 20,
};

const anna = {
  name : 'julia',
  age: 20,
  job : {
    title : 'Software Engineer',
  },
};
```
### 나쁜 코드
```javascript
function displayJobTitle(person) {
  if (person.job && person.job.title) {
    console.log(person.job.title);
  }
}
```
### 좋은 코드
```javascript
function displayJobTitle(person) {
  if (person.job?.title) {
    console.log(person.job.title);
  }
}
```
```javascript
function displayJobTitle(person) {
    const title = person.job?.title ?? 'No Job Yet 🔥';
    console.log(title);
  }
}
```

## Template Literals
```javascript
const person = {
  name : 'julia',
  score: 4,
};
```

### 나쁜 코드
```javascript
console.log(
  'Hello ' + person.name;
);
```
### 좋은 코드
```javascript
console.log(`Hello ${person.name}`);
```
```javascript
const { name, score } = person;
console.log(`Hello ${name}, ${score}`);
```
```javascript
function greetings(person) {
  const { name, score } = person;
  console.log(`Hello ${name}, ${score}`);
}
```