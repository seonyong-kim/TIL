# SQL Basics
## Domain Types
- char(n) : 고정길이(n) 문자열
- varchar(n) : 거변길이(최대 n) 문자열
- int : 정수
- small int : small int
- numeric(p,d) : 고정 소수점 p자리중 d자리는 소수점
- float(n) : 부동 소수점 숫자, 정밀도가 최소 n
- real, double precision

## Create Table construct
```SQL
create table instructor(
  ID         char(5) primary key,
  name       varchar(20) not null,
  dept_name  varchar(20),
  salary     numeric(8,2),
  foreign key(dept_name) references(department))
```
- pk는 자동으로 not null인지 확인해준다.
```SQL
check(salary > 10000)
  or
check("kim" in "", "", "")
```

## Drop and Alter Table Constructs
- drop table : table 제거 <br> drop table r : r 테이블 삭제 **주의할점 : 왜래키 있으면 오류 발생할 수 있다**
- alter table : table 수정
    - alter table r add int fee
    - alter table r drop fee

## Basic Query Structure
```SQL
select A1,A2,A3
from r1,r2,r3
where P
```
- A는 조회할 컬럼
- r은 table
- P는 조건을 의미한다

## The select Clause
쿼리에 포함할 column(attribute)를 지정한다
- ex) 모든 instructor의 이름을 찾아라
```SQL
select name
from instructor
```
- SQL은 쿼리 결과 + 관계에서 중복을 허용한다<br>
📄 예시 테이블: instructor

ID|name|dept_name
---|---|---
101|Kim	Computer
102|Lee|Math
103|Park|Computer
104|Choi|Physics
105|Jung|Math
```SQL
SELECT dept_name
FROM instructor;
```
🔍 결과:
dept_name|
---|
Computer
Math
Computer
Physics
Math

- 중복 제거를 원하면 select뒤에 distinct 활용한다.
```SQL
SELECT DISTINCT dept_name
FROM instructor;
```
- 중복을 허용할거면 SELECT뒤에 ALL을 활용한다.
```SQL
SELECT ALL dept_name
FROM instructor;
```

## Where 절

