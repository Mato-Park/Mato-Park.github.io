---
title: "[SQL] LeetCode 풀이: Advanced Select and Joins(2/2)"

categories:
    - Data
tags:
    - Data
    - SQL
    - LeetCode
last_modified_at: 2023-09-18T21:30:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

&#160; 안녕하세요. 바로 이어가겠습니다!

# 5. Advanced and Select Joins
## 5.4 180. Consecutive Numbers

&#160; 이 문제는 적어도 3번 이상 반복되는 숫자를 구하는 문제입니다. 다양한 방법이 있겠지만 저는 Window 함수를 사용해 풀었습니다.

### 5.4.1 Solution
```sql
SELECT Distinct
    a.num  as ConsecutiveNums
FROM
(
    SELECT
        id,
        num,
        lag(num, 1) over()   as num_1,
        lag(num, 2) over()   as num_2
    FROM Logs
) a
Where a.num = a.num_1 and a.num = a.num_2
;
```
&#160; Window 함수 중에서 lead와 lag 중 원하는 것을 활용하면 됩니다. lag 혹은 lead만 2번 써도 되고 두 함수 모두 사용해도 됩니다. ```lag(num, 1) over()```은 num 컬럼에 바로 다음 순서의 데이터를 불러오고, ```lag(num 2) over```은 그 다음 2번째 데이터를 불러옵니다. 그렇게 테이블을 만든 다음에 num, num_1과 num_2가 모두 데이터만 불러오면 3번 반복되는 숫자를 가져올 수 있습니다.

### 5.4.2 Good to Know - Window Function
&#160; Window 함수는 group by 없이 집계함수를 쓰거나 순위, 행간의 데이터 전처리를 수행할 때 많이 사용됩니다. Window 함수에 대한 기본 양식은 다음과 같습니다.
> ```Funcion(Column) over (Partition By A Order By B)```

&#160; window로 활용할 수 있는 함수는 기본적인 aggregate function(sum, avg, count 등)과 nonaggregate function(rank, lead, lag 등)이 있습니다. 그리고 over 안에 Partition By는 Group By와 유사합니다. 예를 들어, 그룹 별 집계할 때 쓰이기도 하고, 그룹 내 Ranking을 찾을 때에도 쓰입니다. Order By는 말 그대로 정렬하는 기능이며 Order By와 동일합니다. <br>
&#160; Window 함수는 내용이 많아 여기 다 적긴 어렵고 따로 자세하게 적을 예정입니다!

## 5.5 1164. Product Price at a Given Date
&#160; 특정 시점(2019-08-16)에 모든 제품의 가격을 구하는 문제입니다. 하나 특이한 점은 Products 테이블에는 new_price 정보와 변경 날짜만 담겨있습니다. 변경 이전 금액은 모두 10으로 동일하다고 합니다. 따라서 케이스를 다음과 같이 2가지로 나눠야 합니다.
> 1. 2019-08-16 이전에 가격이 바뀐 경우
> 2. 2019-08-16 이후에 가격이 바뀐 경우

### 5.5.1 Solution
```sql
SELECT
    product_id,
    new_price as price
FROM
    Products
Where 
    (product_id, change_date) in (Select product_id, max(change_date) From Products Where change_date <= '2019-08-16' Group By product_id)
UNION
SELECT
    product_id,
    10  as price
FROM
    Products
Group By
    product_id
Having
    Min(change_date) > '2019-08-16'
;
```
&#160; 전 위 두가지 케이스 각각 해당하는 query를 질의하여 테이블을 불러오고 UNION을 활용하는 것으로 문제를 해결했습니다. 한 번에 처리하는 방법이 떠오르지 않더라구요. <br>
&#160; 첫 번째 케이스를 처리하기 위해서 변경 날짜가 2019-08-16 이전에 제품별로 가장 최근 가격 변경 날짜의 케이스만 불러왔습니다. 이 경우에는 2019-08-16 이전에 제품의 가격 변경이 있었고 new-price가 곧 price가 됩니다. <br>
&#160; 두 번째 케이스를 처리하기 위해서는 제품별로 첫 가격 변경 날짜가 2019-08-16 이후인 케이스만 불러오면 됩니다. 첫 가격 변경 날짜가 2019-08-16 이후인 케이스는 2019-08-16에는 price가 10이기 때문에, price 값을 10으로 설정해주면 됩니다.

## 5.6 1204. Last Person to Fit in the Bus
&#160; 버스 탑승객의 총 무게가 1000km에 제한이 있고, 대기자들 중 가장 마지막으로 탑승하는 사람을 찾으면 되는 문제입니다. 저는 탑승대기자들을 순서대로 정렬한 후, 몸무게를 누적합해서 1000kg limit에 걸리는 사람을 찾았습니다.

### 5.3.1 Solution
```sql
SELECT
    person_name
FROM
(
    SELECT
        turn,
        person_id,
        person_name,
        weight,
        SUM(weight) over (order by turn)    as total_weight
    FROM
        Queue
    ORDER By 
        turn asc
) a
WHERE 
    total_weight <= 1000
ORDER By 
    total_weight desc
LIMIT 1
;
```
&#160; 누적합을 구하기 위해서 위 5.4 케이스처럼 window 함수를 활용했습니다. Partition by 는 필요없으니 쓰지 않고 순서대로 정렬(order by turn)해준 다음 합계를 구하면 순서대로 누적합을 구할 수 있습니다. 누적합을 구했다면 이제 1000kg가 넘기 직전 마지막 사람을 구하면 됩니다. 저는 total_weight를 내림차순하여 가장 윗(limit 1)사람을 불러왔습니다.

## 5.6 1907. Count Salary Categories
&#160; income을 category화 하는 문제입니다. 

### 5.3.1 Solution
```sql
(
SELECT
    'Low Salary'    as category,
    Sum(income < 20000)    as  accounts_count
FROM
    Accounts
) UNION
(
SELECT
    'Average Salary'    as category,
    Sum(income Between 20000 and 50000) as accounts_count
FROM
    Accounts
) UNION
(
SELECT
    'High Salary'   as category,
    Sum(income > 50000)    as accounts_count
FROM
    Accounts
)
;
```
&#160; union을 활용해 Low, Average, High Salary 별로 query를 질의한 다음 합쳤습니다. 보통 다수의 프로그래밍 언어에서 bool 형태의 데이터 타입은 int 타입과 매칭되는데 True -> 1로, False -> 0으로 치환됩니다. 따라서 Sum(조건 문)의 경우, 조건을 만족하는 수를 구하는 쿼리가 됩니다. <br>
&#160; 처음에 Union을 쓰지 않고 CASE 구문을 활용해서 그룹 별로 category화 한 다음 Count하여 집계하려 했는데 문제가 풀리지 않았습니다. 왜냐하면 Average 그룹에 해당하는 데이터가 없었기에 Count로 집계한 경우 Null로 제외되기 때문입니다. 단순히 문제를 푸는 것을 떠나서 실상황에서 데이터를 추출할 때 0값인 경우도 필요한지 아닌지에 따라 적절한 Query를 사용해야 합니다. 딱 보면 한눈에 결과가 그려지는 실력이면 좋겠지만, 이렇게 해보면서 경험을 쌓아가고 공부하는 수밖에 없을 것 같네요. 하다보면 언젠가는 고수의 경지에 오를 수 있겠죠!ㅎㅎ
