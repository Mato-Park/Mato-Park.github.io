---
title: "[SQL] LeetCode 풀이: Basic Joins"

categories:
    - Data
tags:
    - Data
    - SQL
    - LeetCode
last_modified_at: 2023-06-29T22:07:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

&#160; 안녕하세요. LeetCode SQL50 두번째 파트인 Basic Joins입니다. Join은 2개 이상의 테이블을 Key를 활용해 하나로 합치는 기능입니다. Join의 방법은 left, inner, outer 등이 있으며 굉장히 많이 활용되는 기능입니다.

# 2. Basic Joins
## 2.1 Replace Employee ID With The Unique Identifier
### 2.1.1 Table Description & Question
**Table** description

table: Employees

|Colum Name|Type|
|---|---|
| id            | int     |
| name          | varchar |

> id is the primary key for this table.<br>
> Each row of this table contains the id and the name of an employee in a company.<br>

table: EmployeeUNI

| Column Name   | Type    |
|---------------|---------|
| id            | int     |
| unique_id     | int     |

> (id, unique_id) is the primary key for this table.<br>
> Each row of this table contains the id and the corresponding unique id of an employee in the company.

**Question**<br>
Write an SQL query to show the unique ID of each user, If a user does not have a unique ID replace just show null.<br>
Return the result table in any order.
### 2.1.2 Solution
```sql
SELECT
    b.unique_id     as unique_id,
    a.name          as name
FROM
    Employees a
Left Join 
    EmployeeUNI b on a.id = b.id
```

user_id를 key로 Empolyee테이블에 EmployeeUNI테이블의 unique_id를 붙이면 되는 아주 간단한 문제이다.

### 2.1.3 Good to Know
Left Join 할 때, 매칭되는 값이 없는 경우에는 null로 붙는다.

## 2.2 Product Sales Analysis I
### 2.2.1 Table Description & Question
**Table** description

table: Sales

|Colum Name|Type|
|---|---|
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |

> (sale_id, year) is the primary key of this table.<br>
> product_id is a foreign key to Product table.<br>
> Each row of this table shows a sale on the product product_id in a certain year.<br>
> Note that the price is per unit.

table: Product 

| Column Name  | Type    |
|--------------|---------|
| product_id   | int     |
| product_name | varchar |

> product_id is the primary key of this table.<br>
> Each row of this table indicates the product name of each product.

**Question**<br>
Write an SQL query that reports the product_name, year, and price for each sale_id in the Sales table.<br>
Return the result table in any order.

### 2.2.2 Solution
```sql
SELECT
    a.year              as year,
    a.price             as price,
    b.product_name      as product_name
FROM 
    Sales a
Left Join 
    Product b on a.product_id = b.product_id
```
위 문제와 마찬가지로 간단한 Join이라 부가설명 없이 넘어가도록 한다.

## 2.3 Customer Who Visited but Did Not Make Any Transactions
### 2.3.1 Table Description & Question
**Table** description

table: Visits

| Column Name | Type    |
|-------------|---------|
| visit_id(PK)    | int     |
| customer_id | int     |

> This table contains information about the customers who visited the mall.

table: Transactions

| Column Name    | Type    |
|----------------|---------|
| transaction_id(PK) | int     |
| visit_id       | int     |
| amount         | int     |

> This table contains information about the transactions made during the visit_id.

**Question** <br>
Write a SQL query to find the IDs of the users who visited without making any transactions and the number of times they made these types of visits.

### 2.3.2 Solution
```sql
SELECT 
    a.customer_id            as customer_id,
    Count(a.customer_id)     as count_no_trans
FROM 
    Visits a
Left Join 
    Transactions b on a.visit_id = b.visit_id
WHERE
    isnull(b.visit_id)
GROUP By 
    a.customer_id
```
Join 하는 경우에도 WHERE 절이나 Group By를 통한 집계도 가능하다!

## 2.4 Rising Temperature
### 2.4.1 Table descriptoin & Question
**Table** description

table: Weather

| Column Name   | Type    |
|---------------|---------|
| id(PK)        | int     |
| recordDate    | date    |
| temperature   | int     |

> This table contains information about the temperature on a certain day.

**Question** <br>
Write an SQL query to find all dates' Id with higher temperatures compared to its previous dates (yesterday).<br>
Return the result table sorted by id in ascending order.

### 2.4.2 Solution
```sql
SELECT
    a.id
FROM 
    Weather a
Left Join 
    Weather b on a.recordDate = DATE_ADD(b.recordDate, Interval 1 DAY)
Where
    a.temperature > b.temperature
```
동일 테이블에 Join하는 self table join이며, 어제보다 오늘 기온이 높은 날을 찾아야 한다. 그러기 위해선 오늘 온도와 어제 온도를 비교할 수 있어야 한다. 기본 아이디어는 어제 날짜를 마치 오늘인 것처럼 변환해서 Join하는 것이다. 위 SQL 구문에서 Key 부분을 풀어서 설명하자면, b 테이블 날짜에서 하루를 더해준 날짜와 a 테이블 날짜를 Join 하게 되면 a 테이블 입장에서 전날 데이터가 Join 되게 된다. 예를 들어, '2023년 6월 28일'에서 하루 더해주면 '2023년 6월 29일'이 되고 이를 기존 테이블에 Join 했을 때, '2023년 6월 29일'에 28일 데이터가 붙게 된다. 처음 문제를 보고 막막했는데 아이디어만 잘 낸다면 어렵지 않게 풀 수 있다.

### 1.4.3 Good to Know
```DATE_ADD``` 함수는 기준 날짜에 지정한 값만큼 더해주는 시간 함수이다. 첫번째 인수로는 기준 시간을 입력하고, 두번째 인수로 원하는 단위를 입력하면 된다. 'Interval 1 DAY'는 하루만큼 더하라는 의미이며, second, month, year 등 다양한 인수를 넣어줄 수 있다.
