---
title: "[SQL] LeetCode 풀이: Basic Aggregate Functions(1/2)"

categories:
    - Data
tags:
    - Data
    - SQL
    - LeetCode
last_modified_at: 2023-07-20T22:08:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

&#160; 안녕하세요. 이번 포스트부터는 LeetCode50 Aggregate Functions 이어 가겠습니다. 문제를 풀어보다가 중간중간 막히는 부분이 많았던 구간인 것 같습니다.😂

# 집계 함수(Aggregate Funstion)란?
&#160; 집계 함수란 **테이블 전체 혹은 여러 행으로부터 하나의 결과값을 반환하는 함수**입니다. 예를 들어, Avg, Sum, Max, Min, Count 등 함수가 집계 함수이며, GROUP BY 절과 함께 사용됩니다.

# 3. Basic Aggregate Functions
## 3.1 Not Boring Movies
### 3.1.1 Table Description & Question
**Table** description

table: Cinema

|Colum Name|Type|
|---|---|
| id(PK)         | int      |
| movie          | varchar  |
| description    | varchar  |
| rating         | float    |

> Each row contains information about the name of a movie, its genre, and its rating.<br>
rating is a 2 decimal places float in the range [0, 10]

**Question**<br>
- Write an SQL query to report the movies with an odd-numbered ID and a description that is not "boring".
- Return the result table ordered by rating in descending order..<br>


### 3.1.2 Solution
```sql
SELECT
    id,
    movie,
    description,
    rating
FROM 
    Cinema
Where
    description != 'boring' and
    id % 2 <> 0 -- or mod(id, 2) <> 0
Order By
    rating desc
;
```
&#160; 간단한 문제이다. 지루하지 않은 영화를 필터링하기 위하여 ```description != 'boring'``` 조건절을 추가한 다음, 홀수인 id를 추출하기 위한 조건절을 추가하면 된다.<br>
&#160; 홀수인 id를 고르기 위해서는 id(int)를 2로 나눴을 때 나머지가 0인지 1인지 체크하면 된다.<br>

### 3.1.3 Good to Know
&#160; 나머지를 구하는 방법은 여러가지가 있다. 우선 '%' 연산자도 나머지를 반환하는 연산자인데 위 풀이에서 ```id % 2```는 id를 2로 나눈 나머지를 반환한다. mod(a,b)함수도 나머지를 반환하는 함수이며 a를 b로 나눈 나머지를 반환한다.

## 3.2 Average Selling Price
### 3.2.1 Table Description & Question
**Table** description

table: Prices

|Colum Name|Type|
|---|---|
| product_id(PK)    | int     |
| start_date(PK)    | date    |
| end_date(PK)      | date    |
| price             | int     |

> Each row of this table indicates the price of the product_id in the period from start_date to end_date.<br>
> For each product_id there will be no two overlapping periods. That means there will be no two intersecting periods for the same product_id.<br>

table: UnitsSold

| Column Name  | Type    |
|--------------|---------|
| product_id    | int     |
| purchase_date | date    |
| units         | int     |

> There is no primary key for this table, it may contain duplicates.<br>
> Each row of this table indicates the date, units, and product_id of each product sold.

**Question**<br>
Write an SQL query to find the average selling price for each product. average_price should be rounded to 2 decimal places.<br>
Return the result table in any order.

### 3.2.2 Solution

```sql
SELECT
    a.product_id    As product_id,
    round(sum(a.price * b.units) / sum(b.units), 2)         as average_price
From
    Prices a
Left Join
    UnitsSold b
    on a.product_id = b.product_id and a.start_date <= b.purchase_date and a.end_date >= b.purchase_date
GROUP BY 
    a.product_id
;
```
&#160; 관건은 UnitsSold 테이블의 purchase_date가 Prices 테이블의 start_date와 end_date사이에 오도록 하는 것이다. 어떻게 하면 될까 고민해보다가 JOIN절의 On절에서 처리해보자 생각해보고 무작정해봤다. 그런데 Join이 잘 되길래 문제를 수월하게 풀 수 있었다. <br>
&#160; 기존에 쓰던 툴은 이렇게 특정 Column이 어떤 기간 내에 오도록 Join하는 경우 제공하는 기능이 있었다. WildMatch라는 함수가 제공되어서 Key와 start인수, end인수를 입력하면 자동으로 매칭해줬다. 그래서 SQL도 유사한 함수가 있나 찾아봤는데 제공하는 함수는 없길래 다른 방법을 생각해봤고 적용했다.

### 3.2.3 Improve
&#160; 무작정 적은 코드라 맞는지 틀린지 알 수가 없어 문제를 풀어보고나면 다른 사람들의 풀이를 참조해보곤 하는데 좋은 코드가 있었다!
```sql
-- 내가 작성한 쿼리문
Left Join
    UnitsSold b
    on a.product_id = b.product_id and a.start_date <= b.purchase_date and a.end_date >= b.purchase_date
-- 다름 사람들의 solution 참조해서 개선
Left Join
    UnitsSold b
    on a.product_id = b.product_id and  b.purchase_date Between a.start_date and a.end_date
```
&#160; Between구문이 있는지 몰랐었다. 속도가 뭐가 빠른지는 대용량 데이터를 테스트 해보지 않아서 잘 모르겠다. 다만, 아래 코드가 직관적으로 읽기 수월하다고 생각한다.

## 3.3 Project Employees I
### 3.3.1 Table Description & Question
**Table** description

table: Project

| Column Name       | Type    |
|-------------------|---------|
| project_id(PK)    | int     |
| employee_id(PK)   | int     |

> employee_id is a foreign key to Employee table.<br>
Each row of this table indicates that the employee with employee_id is working on the project with project_id.

table: Employee

| Column Name      | Type    |
|------------------|---------|
| employee_id(PK)  | int     |
| name             | varchar |
| experience_years | int     |

> employee_id is the primary key of this table. It's guaranteed that experience_years is not NULL.<br>
Each row of this table contains information about one employee.

**Question** <br>
Write an SQL query that reports the average experience years of all the employees for each project, rounded to 2 digits.<br>
Return the result table ordered by student_id and subject_name.

### 3.3.2 Solution
```sql
SELECT
    a.project_id                          as project_id,
    round(Avg(b.experience_years), 2)     as average_years
FROM
    Project a
Left Join
    Employee b
    On a.employee_id = b.employee_id
Group By
    a.project_id
;
```
&#160; 기본적인 문제로 어려운 문제는 아니라 생각한다. 

## 3.4 Percentage of Users Attended a Contest
### 3.4.1 Table descriptoin & Question
**Table** description

table: Users

| Column Name     | Type    |
|-----------------|---------|
| user_id(PK)     | int     |
| user_name       | varchar |

> Each row of this table contains the name and the id of a user.

table: Register

| Column Name     | Type    |
|-----------------|---------|
| contest_id(PK)  | int     |
| user_id(PK)     | int     |

> Each row of this table contains the id of a user and the contest they registered into.

**Question** <br>
Write an SQL query to find the percentage of the users registered in each contest rounded to two decimals.<br>
Return the result table ordered by percentage in descending order. In case of a tie, order it by contest_id in ascending order.

### 3.4.2 Solution
```sql
SELECT
    a.contest_id                                as contest_id,
    Round(Count(a.contest_id) / (SELECT count(b.user_id) From Users b ) * 100, 2)     as percentage
FROM
    Register a
Group by
    a.contest_id
Order by
    percentage desc, contest_id
;
```
&#160; 이 문제는 쉽지 않았고 고민을 많이 했었다. 각 contest별로 참석율을 구하기 위해서는 총 user_id 수를 구한다음 나눠줘야 했다. 방법은 위 SQL코드처럼 subquery를 활용하는 방법이다. SELECT 절에서 사용됐고 단일 상수값을 반환하는 Subquery이다. Subquery는 아직은 생소해서 날잡고 한 번 정리해야 할 것 같다.

<!-- ### 3.4.3 Good to Know
&#160; 우선 첫번째 방법은 위 SQL코드처럼 subquery를 활용하는 방법이다. SELECT 절에서 사용됐고 단일 상수값을 반환하는 Subquery이다. Subquery는 아직은 생소해서 날잡고 한 번 정리해야 할 것 같다.<br>
&#160; 두번째 방법은 해당 값을 변수로 선언하여 활용하는 방법이다. 
```sql
DECLARE @user int = (select count(*) from Users)

SELECT
    a.contest_id, round((count(r.user_id) * 1) / @user * 100, 2)    as percentage
FROM 
    Register a
GROUP BY a.contest_id 
ORDER BY  percentage Desc,  a.contest_id Asc
```
변수를 선언해서 활용하는 SQL 구문을 짜는 것도 꽤 깔끔해보인다. -->
