# Relational Model & Relational Algebra
## Structure of Relational Databases
- 관계형 데이터베이스는 table 모음으로 구성된다.
  - 각 table에는 unique name이 할당되면 이를 **relation** 이라고 한다.
  - row는 table relation ship을 나타내며 **tuple**이라고 한다
  - 각 column은 **attribute**하고 한다.
![image](https://github.com/user-attachments/assets/6d8ee7a0-5dd8-4910-833a-44b73e8a04f2)

## Formal Definition of Relation(집합들 간의 관계)
- A1, A2, …, An을 attributes 라고 하자
- D1, D2, …, Dn을 domains의 attributes라고 하자
  - domain의 attribute는 **atomic**한 성질이 있다.
  - null값이 가능하지만 최대한 없도록 설정한다
- relation r은 D1x D2 x … x Dn의 부분 집합으로 주어지면
  - table에서 r의 튜플 t는 행으로 표현.
  - ex) x = {1,2}, y = {1,3} x X y는 4개이지만, relation이 "<"이라면 가능한것은 (2,3) 1개이다.

## DataBase
- database 여러 relation으로 구성

## Schema and Instance
- **Dataschema** : DB의 logical 설계로, 여러 table(DB전체)의 데이터 구조와 제약조건이다.
- **Database instance** : 특정 시점의 DB에 저장된 값 <br>
![image](https://github.com/user-attachments/assets/cff1196b-8c51-49d3-9424-864c7277d786)
- attributes A1, A2, …, An을 **relation schema**를 다음과 같이 나타낸다.
```
r(A1, A2, …, An)
```
- table의 현재값은 **relation shema**라고 한다.
```
realtion : 개별 테이블에 대한 것
database : 여러 relation(=DB전체)에 대한것
```
- tuple의 순서는 중요하지 않다.

## keys 
- keys : tuple을 식별 가능하게 하는 attribute(각 tuple마다 다르다) 
- **superkey** : 각 tuple(행)을 구별하게 하는 relation r(R)이다. minimal할 필요는 없다.
- **candidate key** : super key중에서 minimal한거
- **primary key** : candidate key중 하나(아무거나 선택된 것)
- **Foreign key** : 한 table에서 다른 table의 key를 참조 (Referencing relation(foreign key -> 중복과 null이 안된다)과 Referenced relation으로 나누어 진다.) 참조하면 기본키가 된다.
## Schema Diagram for University Database
![image](https://github.com/user-attachments/assets/dfcb7280-d0ef-485d-8745-7229bc535273)

## Relational Query Languages
- **Pure(질의)** languages: Relational algebra
- Relational operators

## Relational Algebra
- basic operations
  - Select(σ) : 특정 조건을 만족하는 tuple을 선택
  - Project(∏) : 특정 컬럼 선택
  - Union(∪) : 중복을 제거한 모든 tuple 
  - Set difference(-) : 
  - Cartesian product(x) : 
  - Rename(ρ) :
  - join(⋈) : 2개의 집합을 조건하에 연결
 
## Select (𝜎): selection of tuples
- Relation r
![image](https://github.com/user-attachments/assets/4ca5af9e-8eda-4444-b11e-4cf808a9b09f)
- Select tuples with A=B and D > 5 -> σ (A = B ∧ D > 5)(r)
![image](https://github.com/user-attachments/assets/a3ec1b11-a93e-44af-b012-cba82c494510)

## Project(Π): selection of columns
- Relation r
![image](https://github.com/user-attachments/assets/8e802ab0-a2b4-477d-a230-6668e5d91809)
- Select A and C -> π (A, C)(r)
![image](https://github.com/user-attachments/assets/f5bb2eca-8ea8-424c-8f3e-9d85efc8adc0)

## Joining two relations: Cartesian Product (×)
중복을 고려 안하고 모든 조합을 생성한다.
- Relations r, s:
![image](https://github.com/user-attachments/assets/27f559c2-0fe5-43d7-8c84-b97ebdf4ca29)
- r x s:
![image](https://github.com/user-attachments/assets/2b8f12f1-53a9-4d67-9676-83468a7e818d)

## Joining two relations: Natural Join (⨝)
동일한 컬럼 이름이 있으면 자동으로 Equi join을 한다.
![image](https://github.com/user-attachments/assets/55d504b6-3609-48ab-86f6-c2f82f986123)
### Equi join
두 테이블간 같은 값을 가진 속성을 기준으로 조인 비교연산자는 =만 사용
![image](https://github.com/user-attachments/assets/5d89ed13-8522-4d39-a832-77f9e71e5874)

## theta join
![image](https://github.com/user-attachments/assets/27b31e68-51f9-4c07-b971-53b06cb45541)

## Union(∪) of two relations
중복을 제외한 모든 튜블 반환
![image](https://github.com/user-attachments/assets/6592b041-832d-4d78-83ac-5d89c2377a82)

## Set difference (–) of two relations
앞에는 있고 뒤에는 없는것을 반환
![image](https://github.com/user-attachments/assets/5cd1e9d6-c2eb-4a57-afd1-d1fb71a590a0)
