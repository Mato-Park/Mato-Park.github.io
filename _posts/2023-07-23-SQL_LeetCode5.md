---
title: "[SQL] LeetCode 풀이: Basic Aggregate Functions(2/2)"

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

&#160; 안녕하세요. 이번 포스트부터는 LeetCode50 Aggregate Functions 두번째 파트 이어 가겠습니다.

# 3. Basic Aggregate Functions
## 3.5 Queries Quality and Percentage
### 3.5.1 Table Description & Question
**Table** description

table: Queries

|Colum Name   |Type     |
|-------------|---------|
| query_name  | varchar |
| result      | varchar |
| position    | int     |
| rating      | int     |

> There is no primary key for this table, it may have duplicate rows.<br>
This table contains information collected from some queries on a database.<br>
The position column has a value from 1 to 500.<br>
The rating column has a value from 1 to 5. Query with rating less than 3 is a poor query.<br>


**Question**<br>
- We define query quality as: The average of the ratio between query rating and its position.
- We also define poor query percentage as: The percentage of all queries with rating less than 3.
- Write an SQL query to find each query_name, the quality and poor_query_percentage.
- Both quality and poor_query_percentage should be rounded to 2 decimal places.
- Return the result table in any order.


### 3.5.2 Solution
```sql
SELECT
    query_name,
    round(Avg(rating/position), 2)      as quality,
    round(Avg(Case When rating < 3 then 1 else 0 end) * 100, 2)     as poor_query_percentage
    -- round(avg(rating < 3) * 100, 2) as poor_query_percentage
FROM
    Queries
Group By
    query_name
;
```
&#160; 문제에서 요구한 대로 quality와 poor_query_percentage를 구하면 된다. 다만, poor_query_percentage를 구하는 방법은 사람마다 스타일에 따라 다양한 방법으로 풀 수 있을 것 같다. 난 CASE 구문을 사용해서 rating 3을 기준으로 나눠줘서 Avg를 구했다. 다른 사람들의 풀이를 참조해보니 단순하게 ```rating < 3```구문으로도 구현한 방법도 있었다.

## 3.6 Monthly Transactions I
### 3.6.1 Table Description & Question
**Table** description

table: Prices

|Colum Name     |Type     |
|---------------|---------|
| id(PK)        | int     |
| country       | varchar |
| state         | enum    |
| amount        | int     |
| trans_date    | date    |

> The table has information about incoming transactions.<br>
> The state column is an enum of type ["approved", "declined"].<br>


**Question**<br>
Write an SQL query to find for each month and country, the number of transactions and their total amount, the number of approved transactions and their total amount.<br>
Return the result table in any order.

### 3.6.2 Solution

```sql
SELECT
    DATE_FORMAT(trans_date, '%Y-%m')        as month,
    country,
    Count(id)                               as trans_count,
    SUM(CASE WHEN state="approved" THEN 1 else 0 End)                 as approved_count,
    SUM(amount)     As trans_total_amount,
    SUM(CASE When state="approved" THEN amount else 0 END)          as approved_total_amount
FROM
    Transactions
Group By
    month,
    country
;
```
&#160; 집계함수를 적절히 사용하면 간단하게 풀 수 있는 문제이다. CASE구문과 IF 구문은 취향에 따라 쓰면 될 것 같다. 사실 취향보다는 성능이 좋거나 가독성이 좋은 코드로 활용하는게 좋겠지만, 성능비교는 어려우므로 내 취향에 맞는 코드를 선택했다.

## 3.7 Immediate Food Delivery II
### 3.7.1 Table Description & Question
**Table** description

table: Delivery

| Column Name                 | Type    |
|-----------------------------|---------|
| delivery_id(PK)             | int     |
| customer_id                 | int     |
| order_date                  | date    |
| customer_pref_delivery_date | date    |

> The table holds information about food delivery to customers that make orders at some date and specify a preferred delivery date (on the same order date or after it).

**Question** <br>
- If the customer's preferred delivery date is the same as the order date, then the order is called immediate; otherwise, it is called scheduled.<br>
- The first order of a customer is the order with the earliest order date that the customer made. It is guaranteed that a customer has precisely one first order.<br>
- Write an SQL query to find the percentage of immediate orders in the first orders of all customers, rounded to 2 decimal places.<br>

&#160; 고객 별 첫주문이 Immediate Order인 비율을 구해야 하는 문제이다. 그렇다면 1. 고객 별로 첫 주문날짜를 구해야 하며, 2. 첫 주문이 Immediate인지 schedule인지 구해야 한다.

### 3.7.2 Solution
```sql
SELECT
    round((SUM(CASE WHEN 
        b.first_order_date = a.customer_pref_delivery_date
        THEN 1 ELSE 0 END) / count(distinct a.customer_id))*100, 2)        as immediate_percentage
FROM
    Delivery a
LEFT Join
    (SELECT
        customer_id,
        Min(order_date) as first_order_date
    FROM
        Delivery
    Group By
        customer_id
    ) b
    On a.customer_id = b.customer_id
ORDER By 
    a.customer_id, a.order_date
;
```
&#160; 처음 고민했던 부분은 고객 별 첫 주문 날짜를 어떻게 구해야할까였다. Immediate인지, schedule인지 판단하는 두 번째 부분은 크게 어렵지 않게 풀 수 있었다. 그룹 별 sorting하여 first value를 찾아볼까도 했었는데 어려워서 진행하진 않았다. 다음 Idea로는 서브퀴리를 활용해 'first_order_date'를 구하여 Join하는 것이었다. 그에 맞게 SQL query를 작성하였고 문제는 잘 풀렸다. 

### 3.7.3 Improved
```sql
where (customer_id, order_date) in
(
    Select customer_id, min(order_date) from Delivery group by customer_id
)
```
&#160; 문제를 풀고 다른 분들의 solutions을 참조해봤는데 역시나 좋은 답변이 있었다. 다른 사람들은 WHERE () in (); 절을 사용한 Solution들이 많았다. Join절 없이 (customer_id, order_rate)가 (customer_id, min(order_date))와 일치하는 Record들만 가져올 수 있도록 조건을 설정해서 first_order_date를 구할 수 있었다.

## 3.8 Game Play Analysis IV
### 3.8.1 Table descriptoin & Question
**Table** description

table: Activity

| Column Name  | Type    |
|--------------|---------|
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |

> (player_id, event_date) is the primary key of this table.<br>
This table shows the activity of players of some games.<br>
Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on someday using some device.

**Question** <br>
- Write an SQL query to report the fraction of players that logged in again on the day after the day they first logged in, rounded to 2 decimal places. In other words, you need to count the number of players that logged in for at least two consecutive days starting from their first login date, then divide that number by the total number of players.<br>

&#160; 이틀 연속으로 접속한 유저의 비율을 구하는 문제이다.
### 3.8.2 Solution
```sql
SELECT
  ROUND(COUNT(DISTINCT player_id) / (SELECT COUNT(DISTINCT player_id) FROM Activity), 2) AS fraction
FROM
  Activity
WHERE
  (player_id, DATE_SUB(event_date, INTERVAL 1 DAY))
  IN (
    SELECT player_id, MIN(event_date) AS first_login FROM Activity GROUP BY player_id
  )
;
```
&#160; 이 문제는 사실 못 풀었다. 그래서 다름 사람들은 어떻게 풀었는지 Solution을 보면서 공부했다. 위 SQL Query는 앞선 문제와 유사한 WHERE () IN () 절을 사용했다. 풀어보면, 각 event_date - 1일자와 첫 event_date(Min으로 구함)가 같은 Record만을 가져오는 것이다. 그렇다면 이틀 연속으로 접속한 유저 수를 구할 수 있고(```Count(Distinct player_id)```), subquery를 활용해 전체 유저 수(```SELECT Count(Distinct player_id) FROM Activity```)를 구하여 나눠주는 방식이다. <br><br>

&#160; 또 다른 방식으로는 다음과 같은 Solution이 있었다.
```sql
SELECT 
    player_id, 
    DATE_SUB(event_date, INTERVAL 1 DAY)=min(event_date) over(partition by player_id) as is_return 
FROM
    activity
```
&#160; 전체 쿼리는 아니고 is_return을 구하는 쿼리인데, 이튿날 접속한 유저를 구하는 쿼리이다. ```Over(partition by *)```구문은 * 별로 집계를 수행할 때 쓰는 구문이다. ```GROUP BY```절과 유사하면서도 차이점이 있는데, 1. 집계된 값을 반환하면서 동시에 기존 행 값도 함께 볼 수 있으며, ```Over()``절과 함께 Window함수와 함께 사용할 수 있다. 이 부분도 따로 정리해야겠다.<br>
&#160; 위 구문을 보면, player_id 별로 event_date - 1일자가 min(event_date)와 같으면 1 다르면 0을 반환해 이틀 연속으로 접속한 유저를 구분할 수 있게끔 한 구문이라고 해석하면 될 것 같다.

