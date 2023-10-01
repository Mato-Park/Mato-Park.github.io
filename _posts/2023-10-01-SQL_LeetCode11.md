---
title: "[SQL] LeetCode 풀이: Advanced String Functions / RegEx / Clause"

categories:
    - Data
tags:
    - Data
    - SQL
    - LeetCode
last_modified_at: 2023-10-01T21:00:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

안녕하세요. Leetcode SQL50 마지막 파트인 Advanced String Functions/RegEx/Clause 파트입니다. <br>

# 7. Advanced String Functions / RegEx / Clause
## 7.1 1667. Fix Names in a Table

&#160; Text 데이터의 첫 번째 글자는 대문자로 하고 그 다음 문자부터 소문자로 고치는 문제입니다.

### 7.1.1 Solution
```sql
SELECT
  user_id,
  Concat(Upper(Left(name,1)), Lower(Substr(name, 2))) as name
From Users
ORDER BY
  user_id
;
```
&#160; 텍스트를 첫 번째 글자와 나머지로 나눈 다음 각각 대문자로, 소문자로 바꿔준 다음 다시 하나의 텍스트로 합쳐주면 됩니다.
> - Concat(): 텍스트를 합치는 함수
> - Upper(): 텍스트를 대문자로 바꿔주는 함수
> - Lower(): 텍스트를 소문자로 바꿔주는 함수
> - Left(a: text, b: int) : a(text)를 좌측 기준으로 b(int)까지 반환하는 함수
> - Substr(a: text, b: int, c: int) : a(text)를 b(int)부터 c(int) 길이만큼 반환하는 함수

## 7.2 1527. Patients With a Condition
&#160; 진단명이 DIAB1으로 시작하는 환자를 골라내는 문자입니다. 정규표현식을 활용하여 골라내면 됩니다.

### 7.2.1 Solution
```sql
SELECT
  patient_id,
  patient_name,
  conditions
FROM
  Patients
Where
  conditions Like '% DIAB1%' or conditions Like 'DIAB1%'
;
```
&#160; 진단명이 'DIAB1'으로 시작하는 Case만 골라와야합니다. 다만, 예시 테이블을 보면 DIAB1 앞에 다른 병명이 붙은 것을 확인할 수 있습니다. 따라서, 모든 Case를 고려하여 다음과 같이 조건을 적용하면 됩니다. 

## 7.3 196. Delete Duplicate Emails
&#160; 중복된 이메일을 제거합니다. 만약 중복된 이메일이 있다면 id가 큰 값을 제거해줍니다.

### 7.3.1 Solution
```sql
DELETE p1
  FROM Person p1, Person p2
  WHERE p1.email = p2.email and p1.id > p2.id
;
```
&#160; Self Table Join을 활용하여 중복된 이메일 중 id가 큰 값을 제거해줍니다.


## 7.4 176. Second Highest Salary
&#160; Employee 중 Salary가 두번째로 높은 사람을 찾는 문제입니다.

### 7.4.1 Solution
```sql
SELECT
  (SELECT distinct salary FROM Employee ORDER BY salary desc limit 1 offset 1)
As SecondHighestSalary
;
```
&#160; Subquery를 활용헤 Salary가 두번째로 높은 salary를 찾아줍니다. ```offset 1```을 통해 첫 번째 위치가 아닌 두번째 위치에서 데이터를 가져오도록 합니다.


## 7.5 1484. Group Sold Products By The Date
&#160; sell_date 별로 제품 판매 수와 판매 제품들을 구하는 문제입니다.

### 7.5.1 Solution
```sql
SELECT
  a.sell_date,
  count(a.product)  as num_sold,
  group_concat(a.product order by a.product) as products
FROM
  (SELECT DISTINCT * FROM Activities) a
GROUP BY
  a.sell_date
ORDER BY
  a.sell_date
;
```
&#160; 특별히 어려운 문제는 아닙니다. ```Group_Concat()``` 함수를 활용해 그룹 별로 텍스트를 하나의 텍스트로 합쳐줍니다.

## 7.6 1327. List the Products Ordered in a Period
&#160; 2020년 2월 최소 100개의 물건을 판매한 제품을 구하는 문제입니다.

### 7.6.1 Solution
```sql
SELECT
  a.product_name,
  Sum(b.unit) as unit
FROM
  Products a
  Left Join Orders b
  on a.product_id = b.product_id
Where
  order_date LIKE "2020-02%"
Group By
  a.product_name
HAViNG
  Sum(b.unit) >= 100
;
```
&#160; 2020년 2월 데이터를 가져오기 위해 "2020-02%"를 주면 2020-02로 시작하는 데이터만 가져오게 됩니다. 

## 7.7 1517. Find Users With Valid E-Mails
&#160; 유효한 이메일 형식을 가진 데이터를 불러옵니다.
> 1. prefix name은 문자로 시작함
> 2. 도메인은 '@leetcode.com'이어야함

### 7.7.1 Solution
```sql
SELECT
  user_id,
  name,
  mail
FROM
  Users
Where
  mail REGEXP  '^[A-Za-z][A-Za-z0-9_.-]*@leetcode[.]com'
;
```
&#160; 정규표현식을 활용하기 위해서 ```mail REGEXP  '^[A-Za-z][A-Za-z0-9_.-]*@leetcode[.]com'```과 같이 REGEXP 구문을 활용합니다. 
> - **^**은 다음 문자열로 시작하는 문자열을 찾습니다.
> - **[]**은 []안에 나열된 패턴의 문자열을 찾습니다.

&#160; 따라서 위 구문을 풀어보면 ```^[A-Za-z]```은 알파벳 문자로 시작하는 문자열을 찾습니다. 그 다음 ```[A-Za-z0-9_.-]```은 []안에 나열된 알파벳, 숫자, _, ., -의 문자열 조합을 가진 패턴을 찾습니다.
