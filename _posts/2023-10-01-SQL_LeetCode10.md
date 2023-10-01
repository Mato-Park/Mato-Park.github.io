---
title: "[SQL] LeetCode 풀이: Subqueries"

categories:
    - Data
tags:
    - Data
    - SQL
    - LeetCode
last_modified_at: 2023-10-01T16:40:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

안녕하세요. 이번 문제부터는 Subquery 파트입니다. <br>
&#160;원하는 데이터를 추출하기 위해서는 Subquery를 능숙하게 다룰 줄 알아야 합니다. Subquery는 말 그대로 Query속 query입니다. 좀 더 자세하게 얘기하면 ***SELECT 문 안에 또 다른 SELECT 문***을 의미합니다. Main query 안에서 괄호로 둘러쌓인 SELECT문입니다. Subquery는 어떤 값을 반환하는 곳에도 쓰이고, 테이블로 사용되거나 조건은 불러오는데 사용되기도 합니다.

# 6. Subqueries
## 6.1 1978. Employees Whose Manager Left the Company

&#160; 이 문제는 $30000 이하의 salary를 받고 manager가 직원 중 존재하지 않는(퇴사한) 직원을 구하는 문제입니다.

### 6.1.1 Solution
```sql
SELECT
    employee_id
FROM
    Employees
Where
    salary < 30000
    and manager_id Not in (SELECT employee_id FROM Employees)
Order By
    employee_id
;
```
&#160; 원하는 답을 구하기 위해서는 Where 절에 두 가지 조건이 필요합니다. 하나는 **$30000 이하**와 **manager가 퇴사한** 조건입니다. 2번째 조건을 만족시키기 위해서는 manager_id가 employer_id에 존재하는지 판변해야 합니다. Subquery를 사용해 Employee 테이블에서 employer_id를 가져온 다음, manager_id가 subquery를 통해 불러온 employer_id에 없는 지 ```Not in ()``` 구문을 활용해 필터링하면 manager가 퇴사한 employer를 필터링 할 수 있습니다. 

## 6.2 626. Exchange Seats
&#160; 학생들의 id가 연속하는 두명의 학생끼리 자리를 swap하는 문제이다. 마지막 id의 학생이 혼자일 경우 swap하지 않는다. 

### 6.2.1 Solution
```sql
SELECT
    rank() over (order by group_n asc, id desc) as id,
    student
FROM
(
    SELECT
        id,
        student,
        Round(id / 2)   as group_n
    FROM
    Seat
) a
;
```
&#160; 문제를 풀기 위해서 2가지 step으로 나눠서 문제를 풀었다. 첫 번째는 id가 연속하는 2명의 학생끼리 그룹을 만들어 준 다음, 두번째로 두명씩 짝지어진 그룹 내에서 학생끼리 Swap을 해줬다. 단계별로 설명해보면 다음과 같다. <br>
&#160; 학생들을 두 명씩 그룹짓기 위해 학생들의 id를 2로 나눠준 다음 그 몫을 구했다. 예를 들면, 학생의 id가 1과 2인 학생들의 id를 2로 나눈 값을 반올림하면 1, 2와 3인 학생들의 id를 2로 나눈 값을 반올림하면 2이므로 2명씩 순차적으로 짝지어지게 된다. 이렇게 두 명씩 그룹화 할 수 있다. 이런 방식으로 그룹화를 하고 Subquery로 불러온다.<br>
&#160; 다음으로는 그룹 내에 학생들의 id의 순서를 바꿔주면 됩니다. 순서를 바꿔주기 위해서 window function 중 하나인 rank 함수를 활용했습니다. Order by 기능을 이용해 group_n을 오름차순 순으로, 그 다음 기존 id를 내림차순 순으로 정렬해 rank를 부여하면 자연스럽게 순서가 바뀐 id를 갖게 됩니다. 


## 6.3 1341. Movie Rating
&#160; 두 가지 답을 찾아야 하는 문제입니다.
> 1. 가장 높은 평점을 부여한 사람
> 2. 2020년 2월 가장 높은 평균 평점을 받은 영화

### 6.3.1 Solution
```sql
(SELECT
    u.name As results 
FROM
    MovieRating As m, Users as u 
WHERE
    u.user_id = m.user_id 
Group By 
    m.user_id 
Order by 
    count(m.user_id) desc, u.name 
LIMIT 1)
UNION ALL
(SELECT 
    m.title As results
FROM
    MovieRating as r, Movies as m
WHERE
    m.movie_id = r.movie_id and r.created_at like "2020-02-%"
Group By 
    r.movie_id 
Order by 
    avg(r.rating) desc, m.title 
LIMIT 1);
```
&#160; Subquery를 사용하지 않고도 간단하게 풀리는 문제입니다. 각각 답을 구한 후, ```UNION ALL```을 활용해 테이블을 합쳐줍니다.

## 6.4 1321. Restaurant Growth
&#160; 레스토랑에서 매일 지난 7일동안 평균 매출액을 구하는 문제입니다. 일별로 지난 7일동안 평균 매출액을 구해야 하기 때문에 window function을 활용했습니다.

### 6.4.1 Solution
```sql
SELECT
    c.visited_on,
    c.amount,
    round(c.amount / 7, 2)  as average_amount
FROM (
    SELECT DISTINCT
        visited_on,
        SUM(amount) Over (ORDER BY visited_on RANGE BETWEEN INTERVAL 6 DAY PRECEDING AND CURRENT ROW) as amount,
        MIN(visited_on) OVER () as 1st_date
    FROM
        Customer) c
WHERE
    c.visited_on - 6 >= c.1st_date
;

```
&#160; 지난 7일간 평균 매출액을 구하기 위해 우선 지난 7일간 매출액 합계를 구하기 위해 window function을 활용했습니다. 지난 7일간을 어떻게 구현하면 될까요? 다음과 같은 구문을 활용했습니다. <br>
> ```Sum(amount) Over (ORDER BY visited_on RANGE BETWEEN INTERVAL 6 DAY PRECEDING AND CURRENT ROW)```

&#160; 특정 구간을 구하기 위해서 보통 ```BETWEEN A AND B```와 같이 BETWEEN 구문을 활용합니다. visited_on(방문일)을 기준으로 정렬하고, A에 ```INTERVAL 6 DAY PRECEDING```는 현재로부터 6일전을 의미하고 B에 ```CURRENT```는 현재를 의미합니다. 따라서 visited_on을 기준으로 6일 전 ~ 오늘까지를 의미하게 되며 그 기간동안의 합계를 구하는 구문이 됩니다. 복잡해 보일 수 있지만 하나하나 뜯어보면 어렵지 않게 이해할 수 있습니다.<br>
&#160; 그 다음 '1st_date'를 구해줍니다. 이는 visited_on을 기준으로 과거 6일치의 데이터가 없는 케이스를 제거해주기 위함입니다. 최종 테이블을 구현하기 위한 테이블 구문을 subquery로 미리 테이블을 불러온 다음 마지막으로 평균 매출액을 구하면 쉽게 최종 테이블을 구할 수 있습니다.

## 6.5 602. Friend Requests II: Who Has the Most Friends
&#160; 가장 많은 친구를 가진 사람과 가장 많은 사람과 친구인 사람을 구하는 문제입니다.

### 6.5.1 Solution
```sql
SELECT
    a.id,
    Sum(a.num)    as num
FROM
((SELECT
    requester_id    as id,
    count(accepter_id)  as num
FROM
    RequestAccepted
GROUP BY
    requester_id
) UNION ALL
(
    SELECT
        accepter_id as id,
        count(requester_id) as num
    FROM
        RequestAccepted
    GROUP BY
        accepter_id
)) a
GROUP BY 
    a.id
Order By
    num desc
LIMIT 1
;
```
&#160; Subquery안에 UNION ALL 구문을 활용해 테이블을 불러왔습니다. 하나하나 뜯어보겠습니다.<br>
&#160; 첫 번째 테이블을 보면, requerster_id별로 accepter_id 수를 집계했습니다. 두 번째 테이블은 반대로 accepter_id별로 requester_id 수를 집계했습니다. 각각 수를 집계한 이유는 요청자와 수락자 한 쌍이 모두 친구이기 때문입니다. 예를 들어, 1번이 2번에게 요청한 경우 1과 2는 친구이고, 3번이 1번에게 친구를 요청한 경우도 1과 3은 친구입니다. 따라서 1번에게는 2, 3의 친구 즉 2명의 친구가 있는 것입니다. 따라서 모든 id별로 각각 수를 집계한 것입니다.
<br>
&#160; id별로 requseter_id, accepter_id를 모두 집계한 다음 id별로 합계를 구한다면 총 친구 수를 구할 수 있습니다. 여기서 친구 수를 내림차순으로 정렬한 다음 가장 최상단에 값만 불러오면 가장 많은 친구를 가진 id를 찾을 수 있습니다.

## 6.6 585. Investments in 2016
&#160; 다음 조건을 만족하는 2016년 총 투자액을 구하는 문제입니다. 조건은 다음과 같습니다.
> 1. 2개 이상의 정책의 tiv_2015가 동일해야함
> 2. 다른 policyholder와 지역이 겹치지 않아야 함

### 6.6.1 Solution
```sql
SELECT
    Round(Sum(tiv_2016), 2) as tiv_2016
FROM
    Insurance i
    INNER JOIN (
        SELECT
            concat(lat, lon) as t
        FROM
            Insurance
        GROUP BY
            t
        HAVING
            count(t) = 1) i2
    On concat(i.lat, i.lon) = i2.t
WHERE
    tiv_2015 in (SELECT tiv_2015 FROM Insurance GROUP BY tiv_2015 Having count(pid) > 1)
;
```
&#160; 이 문제를 풀기 위해 2개의 subquery를 활용했습니다. 우선 INNER JOIN 절에 있는 subquery부터 보겠습니다.<br>
&#160; pid별로 지역이 겹치지 않는 pid만 남기기 위해 INNER JOIN을 수행한 사례입니다. lat과 lon을 concat(string을 결합)하여 t라는 컬럼을 생성하고 HAVING절을 통해 t가 1개인 경우만 불러옵니다. 이 테이블을 Insurance 테이블과 ```On concat(i.lat, i.lon) = i2.t``` 키를 활용해 결합한다면 i2 테이블의 t의 수가 1인 케이스만 결합되기에 지역이 겹치는 pid는 모두 제외되게 됩니다.
<br>
&#160; 그 다음 tiv_2015가 두 개 이상 동일한 케이스를 불러오기 위해 WHERE 절에도 subquery를 활용해줍니다. subquery를 활용해 tiv_2015별로 pid 수를 집계한다음 그 수가 2개 이상인 경우만 불러온 후, ```tiv_2015 in ()```을 활용해 조건을 주면 원하는 최종 테이블을 추출할 수 있습니다.

## 6.7 185. Department Top Three Salaries
&#160; 부서 별로 연봉이 가장 높은 3명의 사람을 구하는 문제입니다.

### 6.7.1 Solution
```sql
SELECT
    b.name as Department,
    a.name as Employee,
    a.salary as Salary
FROM (
    SELECT 
        *,
        dense_rank() OVER (Partition By departmentId ORDER BY salary desc) as salary_rank
    FROM
        Employee
    ) a
    Left Join Department b
    on a.departmentId = b.id
Where
    a.salary_rank <= 3
;
```
&#160; 어렵지 않게 푼 문제입니다. Window function 중 하나인 dense_rank() 함수를 활용해 부서별로 salary가 높은 순서를 구합니다. dense_rank는 중복된 것들은 하나로 처리하여 순서를 부여합니다.(2등이 2명일 경우 다음 등수를 4로 가지 않고 3으로 부여) 그 다음 Join 절을 이용해 부서명을 붙여주고 salary_rank가 3 이하인 경우를 구해주면 부서별로 salary가 높은 3명을 구할 수 있습니다!