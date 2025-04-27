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
where절은 조건을 지정하여 원하는 데이터를 필터링한다.
ex) 컴퓨터 공학과 교수중에 급여가 80000을 넘는 사람을 모두 찾으시오
```sql
 select name
 from instructor
 where dept_name = ‘Comp. Sci.' and salary > 80000
```
결과는 and, or, not으로 연결 가능

## form 절
쿼리에 사용할 테이블 나열, 다른 조건이 없으면 catesian(X)곱과 같은 결과가 나온다.
ex) instructor와 teaches의 데카르트 곱을 구하시오
```SQL
select *
from instructor, teaches
```

## Joins
ex) 컴퓨터 공학과가 제공하는 각 과목의 과목 ID, 학기, 연도, 과목 제목을 찾으시오.
```SQL
select section.course_id, semester, year, title
from section, course
where  section.course_id = course.course_id and
  dept_name = ‘Comp. Sci.'
```
ex) 수업을 가르친 모든 교수들의 이름과 그들이 가르친 과목 ID를 찾으시오
```SQL
select name, course_id
from instructor, teaches
where  instructor.ID = teaches.ID
```

## Natural Join
Natural Join은 모든 공통 attibute의 값이 같은 tuple들을 매칭하고, 각 공통 column은 하나만 남긴다.
**Natural Join의 위험성**: 관련 없는 속성들이 같은 이름을 가질 경우 잘못 연결될 수 있다.
ex) 교수들의 이름과 그들이 가르치는 과목 제목을 함께 나열하라.
1. Incorrect version
```SQL
select name, title
from instructor natural join teaches natural join course;
```
2. Correct version
```SQL
select name, title
from instructor natural join teaches, course
where teaches.course_id = course.course_id;
```
3. Another correct version
```SQL
select name, title
from (instructor natural join teaches) join course using(course_id);
```

## The Rename Operation
