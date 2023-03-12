데이터베이스나 테이블의 구조를 변경하기 위한 문장: DDL (Data Definition Language)

<br/>

테이블의 데이터를 조작(읽고, 쓰기)하기 위한 문장 : DML (Data Manipulation Language)

<br/>

<h3>SQL 모드</h3>

8.0에서 기본값

<br/>

- ONLY_FULL_GROUP_BY
- STRICT_TRANS_TABLES
- NO_ZERO_IN_DATE
- NO_ZERO_DATE
- ERROR_FOR_DIVISION_BY_ZERO
- NO_ENGINE_SUBSTITUTION

그 외

<br/>

- STRICT_ALL_TABLES & STRICT_TRANS_TABLES
  - insert나 updadte 문장으로 데이터를 변경하는 경우 컬럼의 타입과 저장되는 값의 타입이 다를 때 자동으로 타입 변경
- ANSI_QUOTES
  - 홑따옴표만 문자열 값 표기로 사용
- ONLY_FULL_GROUP_BY
  - group by 절이 사용된 문장의 select 절에는 group by 절에 명시된 컬럼과 집계함수 (count 또는 sum과 같은 그룹 함수)만 사용
- PIPE_AS_CONCAT
  - mysql에서 ||는 or 연산자와 같은 의미인데, 이것을 설정하면 문자열 연결 연산자(concat)로 사용
- PAD_CHAR_TO_FULL_LENGTH
  - mysql에서는 char 타입이여도 varchar과 같이 유효 문자열 뒤의 공백 문자는 제거된다. 이것을 설정하면 제거되지 않는다.
- NO_BACKSLASH_ESCAPES
  - 역슬래시 문자는 원래 이스케이프 문자인데, 이것을 설정하면 더 이상 이스케이프 용도로 사용할 수 없고 다른 문자와 같은 취급
- IGNORE_SPACE
  - 프로시저나 함수명에 있는 괄호 사이의 공백 무시, 내장 함수는 모두 예약어로 간주되어 테이블이나 컬럼의 이름으로 사용될 수 없다. 백틱 사용하면 가능
- REAL_AS_FLOAT
  - real 타입이 float 타입의 동의어로 바뀐다. 원래는 double 타입.
- NO_ZERO_IN_DATE & NO_ZERO_DATE
  - 2020-00-00 또는 0000-00-00 과 같은 잘못된 날짜를 저장하는 것이 불가능해진다.
- ANSI
  - mysql 서버가 최대한 sql 표준에 맞게 동작하게 만들어준다.
  - REAL_AS_FLOAT, PIPE_AS_CONCAT, ANSI_QUOTES, IGNORE_SPACE, ONLY_FULL_GROUP_BY 모드의 조합으로 구성된 모드
- TRANDITIONAL
  - STRICT_TRANS_TABLES나 STRICT_ALL_TABLES와 비슷하지만 더 엄격한 방법으로 SQL의 작동을 제어한다.
  - STRICT_TRANS_TABLES, STRICT_ALL_TABLES, NO_ZERO_DATE, ERROR_FOR_DIVISION_BY_ZERO, NO_ENGINE_SUBSTITUTION 모드의 조합

<br/>

<h3>11.1.2 영문 대소문자 구분</h3>

<br/>

mysql 서버는 설치된 운영체제에 따라 테이블 명의 대소문자를 구분한다.

<br/>

이 영향을 받지 않으려면 mysql 서버의 설정 파일에 lower_case_table_names 시스템 변수를 설정하면 된다.

<br/>
<br/>

mysql 에서는 숫자 타입과 문자열 타입 간의 비교에서 숫자 타입이 우선이므로, 문자열 값을 숫자 값으로 변환하여 비교를 수행한다.

<br/>

```sql
SELECT * FROM tab_test WHERE number_column='10001'; --- 1
SELECT * FROM tab_test WHERE string_column=10001; --- 2
```

여기서 1번은 '10001'을 숫자 타입으로 자동 변환하여 비교하므로 성능에 영향이 별로 없다.

<br/>

그런데 2번은 string_column의 모든 문자열 값을 숫자로 변환해서 비교하므로 인덱스가 있더라도 이용하지 못해 성능에 좋지 않다.

<br/>

숫자 값으로 변환할 수 없는 값이 존재할 경우, 쿼리 자체가 실패할 수도 있다.

<br/>

<h3>날짜</h3>

mysql에서는 정해진 날짜 포맷으로 표기하면 mysql 서버가 자동으로 DATE나 DATETIME 값으로 변환하기 때뭉네 복잡하게 STR_TO_DATE()같은 함수를 사용하지 않아도 된다.

<br/>

```sql
SELECT * FROM dept_emp WHERE from_Date='2011-04-29';
SELECT * FROM dept_emp WHERE from_Date=STR_TO_DATE('2011-04-29', '%Y-%m-%d');
```

두 쿼리의 차이는 없다.

<h3>불리언</h3>

BOOL이나 BOOLEAN 이라는 타입은 있지만 사실 이것은 TINYINT 타입에 대한 동의어이다.
