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