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

```sql
CREATE TABLE tb_boolean (bool_value BOOLEAN);

INSERT INTO tb_boolean VALUES (FALSE);
SELECT * FROM tb_boolean WHERE bool_value=FALSE;
SELECT * FROM tb_boolean WHERE bool_value=TRUE;
```

실제로 값을 조회해보면 0 또는 1 값이 조회된다.

<br/>

mysql은 C/C++ 언어처럼 TRUE와 FALSE를 1과 0에 매핑해서 사용하는 것이다.

<br/>

이 때 FALSE는 C/C++처럼 0이지만, TRUE는 C/C++과 다르게 1만 나타낸다.

<br/>

```sql
CREATE TABLE tb_boolean (bool_value BOOLEAN);
INSERT INTO tb_boolean VALUES (FALSE), (TRUE), (2), (3), (4);

SELECT * FROM tb_boolean WHERE bool_value IN (FALSE, TRUE);
```

결과는 0, 1만 나온다.

<br/>

불리언 타입은 ENUM 타입으로 관리하는것이 더 좋을수도 있다.

<br/>

동등 비교는 = 와 <=>가 있다.

<br/>

=는 NULL에 대해서는 비교를 하지 못한다. 결과는 무조건 NULL.

<br/>

<=>는 NULL도 하나의 문자로 보고 비교할 수 있다. Null-Safe 비교 연산자라고 한다.

<br/>

```sql
SELECT 1 = 1, NULL = NULL, 1 = NULL;
SELECT 1 <=> 1, NULL <=> NULL, 1<=> NULL;
```

<br/>

AND: &&
OR: ||

```sql
SET sql_mode = 'PIPES_AS_CONCAT';
SELECT 'abc' || 'def' AS concated_String;
```

PIPES_AS_CONCAT 설정을 하면 OR 로는 못쓰고 concat 용도로 사용할 수 있다.

<br/>

AND가 OR 보다 우선이다.

<br/>
<br/>

REGEXO 연산자

<br/>

```sql
SELECT 'abc' REGEXP '^[a-z]';
```

 <br/>

^: 문자열의 시작을 표시. 일치하는 부분이 반드시 문자열의 제일 앞에 있어야 한다.

<br/>

$: 문자열의 끝을 표시. 일치하는 부분이 반드시 문자열의 제일 끝에 있어야 한다.

<br/>

[]: 문자 그룹을 표시. 대괄호는 문자열이 아니라 문자 하나와 일치하는지 확인 [x-z]라고 표현하면 x, y, z 중 하나인지 확인

 <br/>

(): 문자열 그룹을 표시. (xyz)라고 표현하면 'xyz'가 있는지 확인

 <br/>

|: abc|xyz 라고 하면 abc이거나 xyz인지 확인

 <br/>

.: 어떤 문자든지 1개의 문자를 표시. ... 이라고 표현하면 실제 문자의 값과 관계없이 3개의 문자로 구성된 문자열을 찾는다.

  <br/>

\*: 이 기호 앞에 표시된 정규 표현식이 0 또는 1번 이상 반복될 수 있다.

   <br/>

+: 이 기호 앞에 표시된 정규 표현식이 1번 이상 반복될 수 있다.

?: 이 기호 앞에 표시된 저규 표현식이 0 또는 1번만 올 수 있다.

<br/>

REGEXP 연산자는 인덱스를 사용하지 못하지만, LIKE는 인덱스를 사용할 수 있다. 하지만 LIKE 에서 %나 \_가 앞에 있으면 인덱스의 Left-most 특성으로 인해 레인지 스캔을 사용하지 못하고 인덱스를 처음부터 끝까지 읽는 인덱스 풀 스캔 방식으로 쿼리가 처리된다. 와일드 카드 문자가 뒤쪽에 있다면 인덱스 레인지 스캔을 사용할 수 있다.

<br/>

Left-Most 방식은 인덱스의 가장 왼쪽 컬럼부터 조건을 검색하는 방식이다. 예를 들어, 인덱스가 (age, name)으로 구성되어 있다면, Left-Most 방식으로 검색을 수행하면 먼저 age 값이 조건에 맞는 레코드들을 찾은 후, 그 중에서 name 값을 비교하여 조건에 맞는 레코드를 찾아내는 방법이다. Left-Most 방식은 인덱스의 첫 번째 컬럼에 대한 조건이 매우 선택적일 때 유용하다.

<br/>

MYSQL8.0 부터는 IN (subquery) 형태로 작성하면 옵티마이저가 세미 조인 최적화를 통해 더 빠른 쿼리로 변환해서 실행한다.

```sql
SELECT *
FROM dept_emp USE INDEX(PRIMARY)
WHERE dept_no IN (
  SELECT dept_no
  FROM departments
  WHERE dept_no BETWEEN 'd003' AND 'd005'
)
AND emp_no=10001;
```

위 쿼리는 아래 쿼리와 같고, 위 커리는 옵티마이저에 의해 inner join 쿼리로 재작성되서 최적화된다.

```sql
SELECT *
FROM dept_emp USE INDEX(PRIMARY)
WHERE dept_no IN ('d003', 'd004', 'd005');
AND emp_no=10001;
```

<br/>

mysql 8.0 이전 버전까지는 IN 절에 튜플(레코드)을 사용하면 항상 풀 테이블 스캔을 했었다.

```sql
SELECT *
FROM dept_emp
WHERE (dept_no, emp_no) IN (('d001', 10017), ('d002', 10144), ('d003', 10054);
```

8.0버전부터는 인덱스를 최적으로 사용할 수 있게 개선됐다. 그 전에는 성능에 문제가 생겨서 쿼리를 쪼개어 여러번 실행했다.

<br/>

NOT IN 의 실행계획은 인덱스 풀 스캔으로 표시되는데, 동등이 아닌 부정형 비교여서 인덱스를 이용해 처리 범위를 줄이는 조건으로는 사용할 수 없기 때문이다.

<br/>

MYSQL의 함수는 기본 내장 함수와 사용자 정의 함수(UDF, User Defined Function)이 있다.
