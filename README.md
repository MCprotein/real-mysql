책 github: https://github.com/wikibook/realmysql80

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

<br/>

IFFULL(a, b): a가 NULL인지 아닌지 비교하고, NULL일 경우 b로 대체한다.

```sql
select ifnull(0, 1);
# 0
select ifnull(NULL, 2);
# 2
```

<br/>

ISNULL(a): a가 NULL이면 1, 아니면 0을 반환한다.

```sql
select isnull(1/0);
```

<br/>

<h3>현재 시각 조회 (NOW, SYSDATE)</h3>

하나의 SQL에서 모든 NOW() 함수는 같은 값을 가진다.
SYSDATE()는 하나의 SQL 내에서도 호출되는 시점에 따라 결과값이 달라진다.

<br/>

```sql
select NOW() as a, SLEEP(2), NOW() as b;

select SYSDATE() as a, SLEEP(2), SYSDATE() as b;
```

<br/>

여기서 NOW는 같은 값을 출력하지만, SYSDATE는 2초차이난다.

<br/>

SYSDATE()의 두가지 큰 잠재적인 문제

1. SYSDATE()가 사용된 SQL은 레플리카 서버에서 안정적으로 복제되지 못한다.
2. SYSDATE() 함수와 비교되는 칼럼은 인덱스를 효율적으로 사용하지 못한다.

<br/>

SYSDATE()는 함수가 호출될때마다 다른 값을 반환하므로 상수가 아니다. 그래서 인덱스를 스캔할 때도 매번 비교되는 레코드마다 함수를 실행해야한다. 그래서 아래와 같은 쿼리문이 있을 때 emp_no에는 인덱스를 사용하지만, from_date에는 인덱스를 사용하지 못한다.

```sql
select emp_no, salary, from_date, to_date
from salaries
where emp_no=10001 AND from_date>NOW();
```

<br/>

일반적인 웹서비스에서는 SYSDATE() 를 사용할 이유가 없다.

```
%Y: 4자리 연도
%m: 2자리 숫자 표시의 월(01 ~ 12)
%d: 2자리 숫자 표시의 일자(01 ~ 31)
%H: 2자리 숫자 표시의 시 (00 ~ 23)
%i: 2자리 숫자 표시의 분 (00 ~ 59)
%s: 2자리 숫자 표시의 초 (00 ~ 59)
```

```sql
select date_format(now(), '%Y-%m-%d %H:%i:%s');
```

sql에서 표준형태 (년-월-일 시:분:초)로 입력된 문자열은 자동으로 DATETIME타입으로 변환되어 처리된다. 그렇지 않은 형태는 STR_TO_DATE()를 이용해 문자열을 DATETIME 타입으로 변환한다.

<br/>

```sql
select str_to_date(now(), '%Y-%m-%d %H:%i:%s');
```

<br/>

날짜의 덧셈 뺄셈은 DATE_ADD()와 DATE_SUB()를 사용한다. 하지만 DATE_ADD()로 뺄셈까지 할 수 있어서 DATE_SUB()는 크게 필요하지 않다.

<br/>

```sql
select date_add(now(), interval 1 day) as tomorrow;

select date_add(now(), interval -1 day) as tomorrow;
```

YEAR, MONTH, DAY, HOUR, MINUTE, SECOND, MICROSECOND, QUARTER(분기), WEEK(주)

<br/>

UNIX_TIMESTAMP()는 1970-01-01 00:00:00 부터 경과된 초를 반환한다. 인자가 없으면 현재 시간 기준, 특정 날짜를 인자로 넣으면 그 날짜까지의 타임스탬프를 반환한다.

<br/>

FROM_UNIXTIME() 함수는 반대로 인자로 전달한 타임스탬프 값을 DATETIME타입으로 반환하는 함수이다.

```sql
select unix_timestamp();

select unix_timestamp('2020-08-23 15:06:45');

select from_unixtime(unix_timestamp('2020-08-23 15:06:45'))
```

mysql의 timestamp 타입은 4바이트 숫자 타입이기때문에 실제로 가질 수 있는 값은 1970-01-01 00:00:01 ~ 2038-01-09 03:14:07 이다.

<br/>

RPAD(), LPAD(): 문자열의 우측, 좌측에 문자를 덧붙여서 지정된 길이의 문자열로 만든다.
첫번째 인자: 패딩 처리 할 문자열 <br/>
두번째 인자: 반환될 문자열의 최대 길이 <br/>
세번째 인자: 어떤 문자를 패딩할 것인지

<br/>

```sql
select rpad('Cloee', 10, '_');

select lpad(123, 10, 0);

select rtrim('Cloee   ');

select ltrim('     Cloee');

select trim('   cloee    ');
```

<br/>

CONCAT(): 여러 개의 문자열을 연결해서 하나의 문자열로 반환 <br/>
CONCAT_WS(): 첫 번째 인자를 구분자로 사용

```sql
select concat('Georgi', 'Christian', CAST(2 as char));

select concat_ws('-', 'Georgi', 'Christian', CAST(2 as char));

```

GROUP_CONCAT(): 주로 GROUP BY와 함께 사용하며 GROUP BY가 없는 SQL에서 사용하면 단 하나의 결과값만 만들어낸다. GROUP_CONCAT()은 값들을 먼저 정렬한 후 연결하거나, 각 값의 구분자 설정도 가능하며, 여러 값 중에서 중복을 제거하고 연결하는 것도 가능하다.

```sql
select group_concat(dept_no) from departments;

select group_concat(dept_no separator '|') from departments;

select group_concat(dept_no order by emp_no desc)
from dept_emp
where emp_no between 100001 and 100003;

select group_concat(distinct dept_no order by emp_no desc)
from dept_emp
where emp_no between 100001 and 100003;
```

GROUP_CONCAT()의 기본 구분자는 ,이다. <br/>
SEPARATOR로 구분자를 변경할 수 있다. <br/>

GROUP_CONCAT() 함수는 지정한 칼럼의 값들을 연결하기 위해 제한적인 메모리 버퍼 공간을 사용한다. GROUP_CONCAT()의 결과가 시스템 변수에 지정된 크기를 초과하면 쿼리에서 경고 메세지가 발생한다. mysql 클라이언트 또는 gui 도구를 이용해서 실행하면 단순히 경고만 발생하고 쿼리 결과는 출력된다. 하지만 jdbc로 실행될때는 경고가 아니라 에러로 취급되어 쿼리가 실패하기때문에 GROUP_CONCAT()의 결과가 지정된 버퍼 크기를 초과하지 않게 주의해야 한다.

<br/>

GROUP_CONCAT() 함수가 사용하는 메모리 버퍼의 크기는 group_concat_max_len 시스템 변수로 조정할 수 있다. 기본으로 설정된 버퍼의 크기가 1kb 이므로 자주사용한다면 버퍼의 크기를 늘려서 설정핻두는것이 좋다.

<br/>

8.0 버전 부터는 개수를 제한 할 수도 있고 래터럴 조인이나 윈도우 함수도 사용할 수 있다.

```sql
SELECT GROUP_CONCAT(dept_no ORDER BY dept_name DESC)
FROM (
  SELECT *, RANK() OVER (ORDER BY dept_no) AS rnk
  FROM departments
) as x
WHERE rnk <= 5;
```

dept_no 순으로 department를 정렬한다음, 상위 5개만 GROUP_CONCAT() 실행

<br/>

```sql
SELECT d.dept_no, GROUP_CONCAT(de2.emp_no)
FROM departments d
LEFT JOIN LATERAL (SELECT de.dept_no, de.emp_no
                   FROM dept_emp de
                   WHERE de.dept_no = d.dept_no
                   ORDER BY de.emp_no ASC LIMIT 10
                   ) de2 ON de2.dept_no = d.dept_no
GROUP BY d.dept_no;
```

래터럴 조인을 이용해 부서별로 10명씩만 GROUP_CONCAT 실행

<br/>

CASE WHEN 은 함수가 아니라 SQL 구문이다. SWITCH 문과 같은 역할을 한다. <br/>
CASE로 시작하고 END로 끝나야 하며, WHEN ... THEN ... 은 필요한 만큼 반복해서 사용할 수 있다.

```sql
SELECT emp_no, first_name,
CASE gender WHEN 'M' THEN 'Man'
            WHEN 'F' THEN 'Woman'
            ELSE 'Unknown' END AS gender
FROM employees
LIMIT 10;

SELECT emp_no, first_name,
       CASE WHEN hire_date<'1995-01-01' THEN 'Old'
            ELSE 'New' END AS employee_type
FROM employees
LIMIT 10;
```

<br/>

아래의 쿼리는 2만여건의 레코드를 조회하는데, 급여 테이블을 조회하는 서브쿼리도 이 레코드 건수만큼 실행한다. <br/>

```sql
SELECT de.dept_no, e.first_name, e.gender,
       (SELECT s.salary FROM salaries s
        WHERE s.emp_no=e.emp_no
        ORDER BY from_date DESC LIMIT 1) AS last_salary
FROM dept_emp de, employees e
WHERE e.emp_no=de.emp_no
      AND de.dept_no='d001';
```

<br/>

이 쿼리문에서 여자인 경우에만 last_salary가 필요하다면, CASE WHEN으로 조건문을 만들 수 있다.

<br/>

```sql
SELECT de.dept_no, e.first_name, e.gender,
       CASE WHEN e.gender='F' THEN
                 (SELECT s.salary FROM salaries s
                  WHERE s.emp_no=e.emp_no
                  ORDER BY from_date DESC LIMIT 1)
            ELSE 0 END AS last_salary
FROM dept_emp de, employees e
WHERE e.emp_no=de.emp_no
      AND de.dept_no='d001';
```

이 쿼리문은 여자인 경우에만 서브쿼리가 실행된다.

<br/>

sql은 텍스트 기반으로 작동하기 때문에 SQL에 포함된 모든 입력값은 문자열처럼 취급된다.
