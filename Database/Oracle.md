# Oracle
## Oracle, MySQL 문법 차이
### 현재 날짜 확인
* Oracle : `SYSDATE`
* MySQL : `now()`

### 날짜 형식 
#### Date to String
* Oracle : `TO_CHAR(날짜, '형식')`
  * YYYY : 4자리 년도
  * YY : 2자리 년도
  * DD : 31일 형태의 일
  * DDD : 366일 형태의 일
  * HH24 : 24시 형태의 시
  * HH12 : 12시 형태의 시

* MySQL : `DATE_FORMAT(날짜, '형식')`
  * %Y : 4자리 년도
  * %y : 2자리 년도
  * %M : 영어로 월 표현
  * %m : 숫자로 월 표현
  * %H : 24시간 형태의 시
  * %h : 12시간 형태의 시
  * %T : hh:mm:ss

#### String to Date
* Oracle : TO_DATE('문자열', '형식')
* MySQL : STR_TO_CHAR('문자열', '형식')

### 요일
* Oracle : TO_CHAR()
  * 요일을 1~7로 인식(일 ~ 토)

* MySQL : DATE_ADD(), DATE_SUB()
  * 요일을 0~6으로 인식(일 ~ 토)

### NULL 값 확인
* Oracle : NVL(컬럼, '문자열')
  * 컬럼의 값이 NULL인 경우 문자열로 대치

* MySQL : IFNULL(컬럼, '문자열')
  * 컬럼 값이 NULL인 경우 문자열로 대치

### 형변환
* Oracle
  * TO_CHAR(날짜, '형식') - Date to String
  * TO_CARH(숫자) - Number to String
  * TO_NUMBER('문자열') - String to Number
  * TO_DATE('문자열', '형식') - String to Date

* MySQL
  * CAST(a AS b)
  * CONVERT(a, b)

### 문자 합치기
* Oracle : CONCAT('A', 'B'), 'A' || 'B' || 'C' ...
* MySQL : CONCAT('A', 'B', 'C' ...)

### 페이징처리
* Oracle : ROWNUM
* MySQL : LIMIT

### 시퀀스
* Oracle : NEXTVAL, CURRVAL
* MySQL : 시퀀스 존재 X

## 숫자 함수
### ABS(N)
* N의 절대값을 반환

### ROUND(A, B)
* A의 소수점 B번째 자리수까지 반올림 (B가 -2이면 10의 자릿수까지 반올림)
* B는 생략 가능

### TRUNC(A, B)
* A의 소수점 B번째 자리에서 버림 (B가 -2이면 10의자리숫까지 버림)
* B는 생랴 ㄱ가능

### CEIL(N)
* 소수점 올림
* N보다 크거나 같은 정수 중 제일 작은 수

### FLLOR(N)
* 소수점 버림
* N보다 작거나 같은수 중 제일 큰 수

### POWER(A, B)
* A의 B제곱

### SQRT(N)
* N의 제곱근

### LOG(A, B)
* 밑을 A로 하여 B의 로그 값
* A는 0 또는 1 이외의 정수이며, B는 양수

### SIGN(N)
* N이 양수면 1, 음수면 -1, 0이면 0을 반환

### CREATEST(A, B, ...)
* 입력값 중에서 가장 큰 값

### LEAST(A, B, ...)
* 입력값 중에서 가장 작은 값

## 문자열 함수
### Lower(문자열)
* 문자열을 모두 소문자로 변환

### UPPER(문자열)
* 문자열을 모두 대문자로 변환

### INITCAP(문자열)
* 문자열의 첫 번째는 대문자, 나머지는 소문자로 변환

### LENGTH(문자열)
* 문자열의 길이를 반환

### CONCAT(문자열1, 문자열2) / ||
* 문자열1과 문자열2를 합친다

### SUBSTR(문자열, 시작 위치, 길이)
* 문자열의 시작 위치부터 길이만큼 자를 후 반환
* 길이는 생략 가능하며 생략 시 끝까지 반환
* 음수를 사용하여 뒤에서부터 자를 수 있다.

### LPAD(문자열, 길이, 문자) / RPAD(문자열, 길이, 문자)
* 입력받은 문자열에서 길이까지 문자로 채운다
* LPAD는 왼쪽부터, RPAD는 오른쪽부터 채운다.

### TRIM(문자열) / LTRIM(문자열, 옵션) / RTRIM(문자열, 옵션)
* TRIM은 문자열의 양쪽 공백을 제거한다
* LTRIM은 문자열의 왼쪽 공백을 제거하거나, 특정 문자나 반복적인 문자를 제거한다.
* RTRIM은 문자열의 오른쪽 공백을 제거하거나, 특정 문자나 반복적인 문자를 제거한다.

### TRANSLATE(문자열1, 문자열2, 문자열3)
* 문자열1에서 문자열2를 문자열3으로 대체한다.
* 문자열2와 문자열3은 1:1로 대응하며, 만약 대응되는 문자가 없으면 문자열1의 해당 문자는 제거된다.

### REPLACE(문자열1, 문자열2, 문자열3)
* 문자열1에서 문자열2를 문자열3으로 대체한다.
* 문자열1에 존재하는 문자열2가 완벽히 일치하지 않으면 대체 불가능하다.