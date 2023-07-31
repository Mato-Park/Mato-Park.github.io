---
title: "[SQL] LeetCode 풀이: Sorting and Grouping(1/2)"

categories:
    - Data
tags:
    - Data
    - SQL
    - LeetCode
last_modified_at: 2023-07-31T21:00:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

&#160; 안녕하세요. LeetCode50 50문제 별로 없네 생각하면서 시작했는데 고민해보고 정리하면서 하다보니 예상보다는 길어지네요. 부지런히 공부해서 8월달 내에는 끝내야겠습니다

# 4. Sorting and Grouping
## 4.1 Number of Unique Subjects Taught by Each Teacher
### 4.1.1 Table Description & Question
**Table** description

table: Teacher

|Colum Name       |Type  |
|-----------------|------|
| teacher_id      | int  |
| subject_id(PK)  | int  |
| dept_id(PK)     | int  |

> (subject_id, dept_id) is the primary key for this table.<br>
Each row in this table indicates that the teacher with teacher_id teaches the subject subject_id in the department dept_id.


**Question**<br>
- Calculate the number of unique subjects each teacher teaches in the university.
- Return the result table in any order.


### 4.1.2 Solution
```sql
SELECT
    teacher_id,
    count(distinct subject_id)  as cnt
FROM
    Teacher
GROUP BY
    teacher_id
```
&#160; 선생님 별로 가르치는 과목 수를 구하면 된다. 중복을 제거하기 위하여 count 함수 안에 'distinct'를 적어주자 끝!

## 4.2 User Activity for the Past 30 Days 1
### 4.2.1 Table Description & Question
**Table** description

table: Activity

|Colum Name     |Type     |
|---------------|---------|
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |

> There is no primary key for this table, it may have duplicate rows.<br>
> The activity_type column is an ENUM of type ('open_session', 'end_session', 'scroll_down', 'send_message').<br>
> The table shows the user activities for a social media website. <br>
> Note that each session belongs to exactly one user.


**Question**<br>
- Write an SQL query to find the daily active user count for a period of 30 days ending 2019-07-27 inclusively. A user was active on someday if they made at least one activity on that day.
- Return the result table in any order.

### 4.2.2 Solution

```sql
Select
    activity_date   As day,
    count(distinct user_id) As active_users
FROM
    Activity
WHERE
    activity_date BETWEEN Date_Sub('2019-07-27', INTERVAL 29 DAY)
    AND '2019-07-27'
GROUP BY
    activity_date
;
```
&#160; 2019년 7월 27일을 기준으로 30일 전부터 기준일까지 일자별로 활성화 유저 수를 계산하면 된다. count를 사용하여 수를 집계하고 기간은 Where 절에 Between과 date_sub를 활용하여 기간 조건을 설정했다.

## 4.3 Product Sales Analysis 3
### 4.3.1 Table Description & Question
**Table** description

table: Sales

| Column Name     | Type  |
|-----------------|-------|
| sale_id(PK)     | int   |
| product_id(FK)  | int   |
| year(PK)        | int   |
| quantity        | int   |
| price           | int   |

> (sale_id, year) is the primary key of this table.<br>
> product_id is a foreign key to Product table.<br>
> Each row of this table shows a sale on the product product_id in a certain year.<br>
> Note that the price is per unit.

table: Product

| Column Name      | Type    |
|------------------|---------|
| product_id(PK)   | int     |
| product_name     | varchar |

> product_id is the primary key of this table.<br>
> Each row of this table indicates the product name of each product.<br>

**Question** <br>
- Write an SQL query that selects the product id, year, quantity, and price for the first year of every product sold.
- Return the resulting table in any order.

&#160; product별로 첫 해에 판매한 수량과 판매가격을 구하는 문제이다. 

### 4.3.2 Solution
```sql
SELECT
    product_id,
    year        As first_year,
    quantity,
    price
FROM
    Sales
Where
    (product_id, year) in (Select product_id, Min(year) as year
     From Sales Group By product_id)
;
```
&#160; 각 Product 별로 첫 해에 해당하는 Record를 어떻게 가져올까 고민을 많이 했었다. 처음에 떠올린 아이디어는 프로덕트 별로 연도를 오름차순으로 정렬한 다음 가장 위에 있는 Record를 가져오는 것이었다. 구글링도 해봤지만 내가 생각했던 솔루션은 찾기 어려웠다. 따라서 다른 방법을 고민하다가 찾은 것이 위 쿼리문이다.<br>
&#160; 아이디어는 간단하다. 프로덕트 별로 첫 해는 year 값이 가장 **낮은** 값일 것이다. 왜냐하면 날짜는 일정한 방향으로 흐르기 때문이다. 따라서 WHERE 절에 subquery를 활용하여 프로덕트 별로 year가 가장 낮은 Record를 가져올 수 있는 조건 절을 추가했다. 

## 4.4 Class More Than 5 Students
### 4.4.1 Table descriptoin & Question
**Table** description

table: Courses

| Column Name     | Type    |
|-----------------|---------|
| student(PK)     | varchar |
| class(PK)       | varchar |

> (student, class) is the primary key column for this table.<br>
Each row of this table indicates the name of a student and the class in which they are enrolled.

**Question** <br>
- Find all the classes that have at least five students.<br>
- Return the result table in any order.

### 3.8.2 Solution
```sql
SELECT
    class
FROM
    Courses
Group By
    class
Having
    Count(student) >= 5
;
```