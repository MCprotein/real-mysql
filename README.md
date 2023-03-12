데이터베이스나 테이블의 구조를 변경하기 위한 문장: DDL (Data Definition Language)

<br/>

테이블의 데이터를 조작(읽고, 쓰기)하기 위한 문장 : DML (Data Manipulation Language)

<br/>

mysql8.0에서 sql_mode 기본값

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
  insert나 updadte 문장으로 데이터를 변경하는 경우 컬럼의 타입과 저장되는 값의 타입이 다를 때 자동으로 타입 변경
- ANSI_QUOTES
  홑따옴표만 문자열 값 표기로 사용
- ONLY_FULL_GROUP_BY
  group by 절이 사용된 문장의 select 절에는 group by 절에 명시된 컬럼과 집계함수 (count 또는 sum과 같은 그룹 함수)만 사용
- PIPE_AS_CONCAT
  mysql에서 ||는 or 연산자와 같은 의미인데, 이것을 설정하면 문자열 연결 연산자(concat)로 사용
- PAD_CHAR_TO_FULL_LENGTH
  mysql에서는 char 타입이여도 varchar과 같이 유효 문자열 뒤의 공백 문자는 제거된다. 이것을 설정하면 제거되지 않는다.
- NO_BACKSLASH_ESCAPES
  역슬래시 문자는 원래 이스케이프 문자인데, 이것을 설정하면 더 이상 이스케이프 용도로 사용할 수 없고 다른 문자와 같은 취급
- IGNORE_SPACE
  프로시저나 함수명에 있는 괄호 사이의 공백 무시, 내장 함수는 모두 예약어로 간주되어 테이블이나 컬럼의 이름으로 사용될 수 없다. 백틱 사용하면 가능
- REAL_AS_FLOAT
  real 타입이 float 타입의 동의어로 바뀐다. 원래는 double 타입.
- NO_ZERO_IN_DATE & NO_ZERO_DATE
  2020-00-00 또는 0000-00-00 과 같은 잘못된 날짜를 저장하는 것이 불가능해진다.
- ANSI
  mysql 서버가 최대한 sql 표준에 맞게 동작하게 만들어준다.
  REAL_AS_FLOAT, PIPE_AS_CONCAT, ANSI_QUOTES, IGNORE_SPACE, ONLY_FULL_GROUP_BY 모드의 조합으로 구성된 모드
- TRANDITIONAL
  STRICT_TRANS_TABLES나 STRICT_ALL_TABLES와 비슷하지만 더 엄격한 방법으로 SQL의 작동을 제어한다.
  STRICT_TRANS_TABLES, STRICT_ALL_TABLES, NO_ZERO_DATE, ERROR_FOR_DIVISION_BY_ZERO, NO_ENGINE_SUBSTITUTION 모드의 조합
