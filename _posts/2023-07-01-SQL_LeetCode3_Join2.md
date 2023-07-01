---
title: "[SQL] LeetCode 풀이: Basic Joins(2/2)"

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

&#160; 안녕하세요. LeetCode50 Basic Join 이어 가겠습니다.

# 2. Basic Joins
## 2.5 Average Time of Process per Machine
### 2.5.1 Table Description & Question
**Table** description

table: Activity

|Colum Name|Type|
|---|---|
| machine_id(PK)     | int     |
| process_id(PK)     | int     |
| activity_type(PK)  | enum    |
| timestamp          | float   |

> The table shows the user activities for a factory website.<br>
> machine_id is the ID of a machine.
process_id is the ID of a process running on the machine with ID machine_id.
activity_type is an ENUM of type ('start', 'end').
timestamp is a float representing the current time in seconds.
'start' means the machine starts the process at the given timestamp and 'end' means the machine ends the process at the given timestamp.
The 'start' timestamp will always be before the 'end' timestamp for every (machine_id, process_id) pair.<br>

**Question**<br>
- There is a factory website that has several machines each running the same number of processes. Write an SQL query to find the average time each machine takes to complete a process.<br>
- The time to complete a process is the 'end' timestamp minus the 'start' timestamp. The average time is calculated by the total time to complete every process on the machine divided by the number of processes that were run.<br>
- The resulting table should have the machine_id along with the average time as processing_time, which should be rounded to 3 decimal places.

Return the result table in any order.

### 2.5.2 Solution
```sql
SELECT
    a.machine_id,
    Round(Avg(b.timestamp - a.timestamp), 3)      As processing_time
FROM 
    Activity a
Left Join 
    Activity b on a.machine_id = b.machine_id and a.process_id = b.process_id
Where
    a.activity_type = 'start' and
    b.activity_type = 'end' 
    and a.timestamp < b.timestamp -- end timestamp가 start timestamp 보다 이후여야 한다는 제약이 필요함
Group by 
    a.machine_id
```
&#160; Self Table Join이다. 문제에서 processing time = endtime - starttime으로 정의하고 있다. 따라서, machind_id 별로 endtime과 starttime 간 차이를 구할 수 있게 두 컬럼을 붙여주면 된다. JOIN을 수행할 a 테이블은 'start'인 timestamp만을 가져오고, b 테이블은 'end'인 timestamp만을 가져와서 join을 수행하여 processing time을 구하면 된다.<br>
<br>
&#160; 문제에 보면 machine_id, process_id 별 end timestamp가 start timestamp가 이후여야 한다. 문제 풀 때는 까먹어서 SQL 구문에 넣지 않았지만, 실무에서는 매우 중요하다. 실제로는 데이터의 무결성(Data Integrity)가 지켜지지 않는 경우가 많기 때문에, 정확한 데이터를 산출하기 위해서는 위와 같은 조건을 꼭 추가하여야 한다.

### 2.5.3 Good to Know
Round함수는 지정한 자릿수에 따라 반올림을 수행하는 함수이다. 만약 지정한 자릿수가 '3'이라면 소수점 넷째 자리에서 반올림하여 셋째자리까지 표시해준다.

## 2.6 Employee Bonus
### 2.6.1 Table Description & Question
**Table** description

table: Employee

|Colum Name|Type|
|---|---|
| empId(PK)   | int     |
| name        | varchar |
| supervisor  | int     |
| salary      | int     |

> (sale_id, year) is the primary key of this table.<br>
> Each row of this table indicates the name and the ID of an employee in addition to their salary and the id of their manager.<br>

table: Bonus

| Column Name  | Type    |
|--------------|---------|
| empId(PK)    | int  |
| bonus(FK)    | int  |

> empId is a foreign key to empId from the Employee table.<br>
> Each row of this table contains the id of an employee and their respective bonus.

**Question**<br>
Write an SQL query to report the name and bonus amount of each employee with a bonus less than 1000.<br>
Return the result table in any order.

### 2.5.2 Solution
```sql
SELECT
    a.name,
    b.bonus
FROM 
    Employee a
Left Join 
    Bonus b on a.empId = b.empId
Where 
    b.bonus < 1000 or isnull(b.bonus)
```
위 문제와 마찬가지로 간단한 Join이라 부가설명 없이 넘어가도록 한다.

## 2.7 Students and Examinations
### 2.7.1 Table Description & Question
**Table** description

table: Students

| Column Name       | Type    |
|-------------------|---------|
| student_id(PK)    | int     |
| student_name      | varchar |

> Each row of this table contains the ID and the name of one student in the school.

table: Subjects

| Column Name      | Type    |
|------------------|---------|
| subject_name(PK) | varchar |

> Each row of this table contains the name of one subject in the school.

table: Examinations

| Column Name      | Type    |
|------------------|---------|
| student_id       | int     |
| subject_name     | varchar |

> There is no primary key for this table. <U>It may contain duplicates.</U><br>
> Each student from the Students table takes every course from the Subjects table.
Each row of this table indicates that a student with ID student_id attended the exam of subject_name.

**Question** <br>
Write an SQL query to find the number of times each student attended each exam.<br>
Return the result table ordered by student_id and subject_name.

### 2.7.2 Solution
```sql
SELECT
    a.student_id,
    a.student_name,
    b.subject_name,
    Count(c.subject_name)           As attended_exams
From
    Students a
Cross Join 
    Subjects b 
Left Join 
    Examinations c on 
    a.student_id = c.student_id and b.subject_name = c.subject_name
Group By 
    a.student_id, b.subject_name
Order By
     a.student_id asc, b.subject_name asc
;
```
&#160;문제에서 요구하는 답을 얻기 위해서는 3개의 테이블을 Join해야 한다. 학생별로 수강한 과목명을 붙이고 그 과목에 시험 응시한 과목을 붙여야 한다. students 테이블의 모든 학생들은 subjects에 있는 모든 과목을 수강했다고 한다. 그렇다면 students테이블의 students_id 별로 과목명을 모두 붙여주면 된다. 이럴때 사용하는 JOIN은 **OUTER JOIN**이며, MySQL에선 'CROSS JOIN'으로 수행한다. CROSS JOIN은 각 테이블의 키별로 모든 값을 매칭하는 JOIN 방법이다. 수행 결과로 students_id 별로 모든 subjects name이 JOIN된다.<br>
<br>
&#160; 다음으로 students id 별로 시험 응시 내역을 붙여주면 된다. 기존 Cross Join을 수행한 테이블에 Examination을 Left Join 해주자. 그러면 시험을 응시했으면 값이 붙고 없으면 Null값이 붙는다. 따라서 c.subject name을 count해주면 시험 응시 횟수를 구할 수 있게 된다.(null값은 count하지 않는다.)

### 2.7.3 Good to Know
&#160; 실무에서 데이터 마트를 구성하다보면 대부분의 트러블은 대부분 테이블 간 JOIN에서 발생한다. 그 이유는 1) Key가 잘못(중복, 데이터 타입 상이 등)됐거나, 2) Join에 종류를 잘못설정 했거나, 3) 기타 모종의 이유... 등으로 발생한다. 특히, Key에 중복이 발생되거나, Outer Join을 잘못 수행할 경우 데이터가 뻥튀기 되면서 컴퓨팅 리소스 한계 이상으로 사용하게 되면서 서버가 맛탱이가는 일이 발생하고 서비스를 내리거나 엔진을 재시작하는 일이 발생한다. 따라서, Join은 항상 신중하게 해야 한다.
> JOIN 수행 전 KEY가 적절한 지, 데이터 중복 여부 정도는 미리 체크하기!

## 2.8 Managers with at Least 5 Direct Reports
### 2.8.1 Table descriptoin & Question
**Table** description

table: Employee

| Column Name     | Type    |
|-----------------|---------|
| id(PK)          | int     |
| name            | varchar |
| department      | varchar |
| managerId       | int     |

> Each row of this table indicates the name of an employee, their department, and the id of their manager. If managerId is null, then the employee does not have a manager. No employee will be the manager of themself.

**Question** <br>
Write an SQL query to report the managers with at least five direct reports.<br>
Return the result table in any order.

### 2.8.2 Solution
```sql
SELECT
    a.name
FROM 
    Employee a
Left Join
    (SELECT
        managerId,
        count(id)   as n
    FROM
        Employee
    Group By 
        managerId)
    b on a.id = b.managerId
WHERE
    b.n >= 5
;
```
&#160; maneger id 별로 id가 몇 명인지 구한다음 값이 5 이상인 manager id를 구하면 되는 문제이다. 처음에 문제를 보고 어떻게 풀어야 할지 감이 안와서 subquery를 활용했다. 개인적으로는 Simple하게 가장 좋다고 생각하기에 굳이 subquery를 써야하는 상황이 아니면 쓰고 싶지 않았다. (현 수준 문제에서 subquery를 요구하는 거 같진 않았다.) 하지만, 다른 아이디어가 없었기에 subquery로 manager id 별로 id 수를 구한 다음 left join하여 값이 5이상인 manager를 추출하였다.<br><br>

**Better Solution**<br>

```sql
SELECT 
    a.name
FROM 
    Employee a
INNER JOIN 
    Employee b
    ON a.id = b.managerId
GROUP BY 
    a.name
HAVING 
    COUNT(a.name) >= 5;
```
&#160; 문제를 풀고 다른 사람의 solutions 중 개인적으로 가장 깔끔하다고 생각하여 가져왔다. 그리고 Having 절에 대해 처음 알게 됐다.(이전에는 몰랐다 ㅠㅠ)
SQL 코드도 짧아졌고 기존 코드보다 직관적으로 이해도 쉽다.

### 2.8.3 Good to Know: Where vs. Having
&#160; Where 절과 Having 절은 둘 다 필터링할 때 사용할 수 있다는 공통적인 기능을 제공하지만 차이가 있다. Having 절은 그룹을 필터링하는 데 쓰이며, 집계 함수를 사용하여 필터링이 가능한 반면, Where 절은 개별 행에 대한 필터링이 가능하며 집계 함수를 활용한 필터링이 불가능하다. 즉, Having은 그룹화 혹은 집계 값에 대한 레코드를 필터링하는데 사용되며, Where 절은 개별값에 대한 레코드를 필터링하는데 사용된다.<br>
<br>
[참조사이트](https://velog.io/@ljs7463/SQL-having-%EA%B3%BC-where-%EC%B0%A8%EC%9D%B4): Having 절에 대한 내용은 해당 블로그에서 참조하였습니다.


## 2.9 Confirmation Rate
### 2.9.1 Table descriptoin & Question
**Table** description

table: Signups

| Column Name     | Type    |
|-----------------|---------|
| user_id(PK)     | int      |
| time_stamp      | datetime |

> Each row contains information about the signup time for the user with ID user_id.

table: Confirmations

| Column Name        | Type    |
|--------------------|---------|
| user_id(PK)        | int      |
| time_stamp(PK)     | datetime |
| action             | ENUM     |

> user_id is a foreign key with a reference to the Signups table.
action is an ENUM of the type ('confirmed', 'timeout')
Each row of this table indicates that the user with ID user_id requested a confirmation message at time_stamp and that confirmation message was either confirmed ('confirmed') or expired without confirming ('timeout').

**Question** <br>
The confirmation rate of a user is the number of 'confirmed' messages divided by the total number of requested confirmation messages. The confirmation rate of a user that did not request any confirmation messages is 0. Round the confirmation rate to two decimal places.<br>
Write an SQL query to find the confirmation rate of each user.<br>
Return the result table in any order.

### 2.9.2 Solution
```sql
SELECT
   a.user_id,
   round(Coalesce(count(case when b.action='confirmed' then 1 end) / count(*), 0), 2)
       as confirmation_rate
FROM
   Signups a
Left Join
   Confirmations b on a.user_id = b.user_id
Group by
   a.user_id
;
```
&#160; confirmation rate을 구하기 위해서는 confirmed 수와 total 수를 구해야 한다. 그래서 case 절을 사용해서 confirmed 수를 구했고 count(*)를 활용해 total 수를 구했다. 근데 굳이 각각 수를 더해서 나눠줄 필요없이 avg 함수를 활용해서 confirmation rate을 구할 수 있다.<br>

```avg(case when c.action = 'confirmed' then 1 else 0 end)```, 이 방법도 깔끔하고 좋은 것 같다.

