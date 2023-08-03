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