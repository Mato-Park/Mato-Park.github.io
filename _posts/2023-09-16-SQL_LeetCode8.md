---
title: "[SQL] LeetCode 풀이: Advanced Select and Joins(1/2)"

categories:
    - Data
tags:
    - Data
    - SQL
    - LeetCode
last_modified_at: 2023-09-12T21:00:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

&#160; 안녕하세요. 이번 Section은 Advanced Select and Joins입니다.

# 5. Advanced and Select Joins
## 5.1 1731. The Number of Employees Which Report to Each Employee
&#160; 모든 manager에 이름과 다이렉트로 보고하는 employee에 수, 그리고 employee의 평균 나이를 구하는 문제입니다.
단계를 나눠보면, 
> 1. manager 알아내기
> 2. manager에게 리포트하는 employee 수 집계하기
> 3. manager에게 리포트하는 employee의 평균 나이 집계하기

순으로 정리하면 될 것 같습니다.

### 5.1.1 Solution
```sql
SELECT
    a.employee_id             as employee_id,
    a.name,
    count(b.employee_id)      as reports_count,
    round(avg(b.age), 0)      as average_age
FROM
    Employees a
INNER Join Employees b
    on a.employee_id = b.reports_to
GROUP BY
    a.employee_id
ORDER By
    a.employee_id
;
```
&#160; manager를 찾아내기 위해 Self Table Join을 활용했습니다. Join Key를 a 테이블에서는 employee_i와 b 테이블에서 reports_to로 설정해 줍니다. 게다가 INNER Join을 했기 때문에 a.employee_id와 b.reports_to가 두 테이블에 모두 있는 케이스만 Join이 됩니다. 그려보면 a 테이블에는 매니저만 남고, b 테이블이 employee가 join되는 그림입니다. 따라서 최종 테이블에 manager만 남게 됩니다.<br>
&#160; 그 다음부터는 집계함수를 이용하여 집계하면 문제는 풀립니다!

## 5.2 1789. Primary Department for Each Employee
&#160; Employee 별로 primary department를 구하는 문제입니다. 이 문제에서 고려해야 할 점은 employee가 속한 department가 꼭 1개가 아니라 1개 이상인 경우가 있다는 것입니다. 

### 5.2.1 Solution
```sql
SELECT
    employee_id,
    department_id
FROM 
    Employee
WHERE
    primary_flag = 'Y'
UNION
SELECT
    employee_id,
    department_id
FROM
    Employee
Where
    employee_id in (SELECT employee_id FROM Employee 
                    Group By employee_id Having count(*) = 1)
;

```
&#160; Employee 별로 속한 department수가 상이하기 때문에 아예 나눠서 처리하는 게 적절해 보입니다. 1. department가 2개 이상인 경우, primary_flag = 'Y'인 조건으로 primary department를 구하고 2. 1개일 경우는 그 department가 primary department이기에 department수가 1개인 경우만 조건으로 걸어서 primary department를 구해줍니다. 그 다음에는 Union을 활용해 두 결과를 하나의 테이블로 구해주면 됩니다!

### 5.2.2 Good to Know - UNION
&#160; SQL에서는 UNION과 UNION ALL을 활용해 두 테이블을 하나의 테이블로 합칠 수 있습니다. 파이썬에서 pd.concat()과 유사한 것으로 생각하면 되겠네요. 사용법은 간단합니다.
```(A 테이블) UNION (ALL) (B 테이블)``` 입니다. 컬럼명을 기준으로 union되는 것 같으니 컬럼명에 주의하고 활용하면 됩니다. UNION의 경우에는 중복값을 제거하는 반면, UNION ALL은 중복값을 제거해주지 않으므로 상황에 맞게 사용하시면 됩니다!

## 5.3 610. Triangle Judgement
&#160; x, y와 z 변의 길이로 삼각형을 그릴 수 있는지를 판변하는 문제입니다. 3변의 길이가 주어졌을 때, 삼각형임을 판별하는 식은 다음과 같습니다.
> 가장 큰 변의 길이가 다른 두 변의 길이의 합보다 작아야 함

### 5.3.1 Solution
```sql
SELECT
    x, y, z,
    case
        WHEN 
        x < y + z and y < x + z and z < x + y
        Then 'Yes'
        ELSE 'No' 
        END as triangle
FROM
    Triangle
;
```
&#160; Case 구문을 활용해서 삼각형을 판별할 수 있는 조건을 줍니다. 다만, x, y와 z 중, 가장 큰 변의 길이가 무엇인지 모르기 때문에 x, y와 z 각각 판별해줘야 합니다. 만약, x, y와 z 중 하나라도 위 조건을 만족하지 못한다면 삼각형을 형성할 수 없습니다. 따라서 위 조건을 모두 만족하는 경우 'Yes', 하나라도 만족하지 못하는 경우에는 'No'로 판별해주기 위에 각 조건들을 And로 연결했습니다!

### 5.3.2 Good to Know - CASE구문
&#160; CASE 구문은 주로 여러 조건에 따라 구분해야 할 때 사용합니다. 필요한 조건이 하나인 경우에는 If()를 사용할 수 있지만 여러개인 경우 CASE 구문을 사용하는게 적절합니다. 그리고 직관적으로 이해할 수 있고 사용법도 쉽습니다. 기본적인 사용법은 다음과 같습니다.
```sql
CASE
    WHEN A THEN AA
    WHEN B THEN BB
    WHEN C THEN CC
    ELSE DD
    END as CASE_EXAMPLE
```
&#160; A를 만족하는 경우 AA를 할당하고 B를 만족하는 경우 BB, C를 만족하는 경우 CC, A,B와 C 어디에도 해당하지 않는 경우에 DD를 할당합니다.
