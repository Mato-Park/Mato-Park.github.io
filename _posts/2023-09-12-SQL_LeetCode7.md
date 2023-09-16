---
title: "[SQL] LeetCode 풀이: Sorting and Grouping(2/2)"

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

&#160; 안녕하세요. 드디어 문제는 다 풀었습니다! 문제 풀었던 것들 정리하면서 부족한 부분은 채우고자 합니다. 문제를 어떻게 풀었는지와 공부한 것들만 적고 필요없는 부분은 굳이 안 적으려고 합니다.

# 4. Sorting and Grouping
## 4.6 1729 Find Followers Count
&#160; 각 user 별로 follower가 몇 명인지를 구하는 문제입니다. 쉬운 문제네요.

### 4.6.1 Solution
```sql
SELECT
    user_id,
    count(follower_id)  as followers_count
FROM
    Followers
Group By
    user_id
Order By
    user_id asc
;
```
&#160; user 별로 follwer 수를 집계하면 됩니다!

## 4.7 619. Biggest Single Number
&#160; MyNumbers Table에 num 중 오직 한 번만 나타난 것 중 가장 큰 값을 구하는 문제입니다. num 별로 수를 count로 집계하여 그 값이 1인 num만 가져오는 조건을 주면 될 것 같습니다.
### 4.7.1 Solution
```sql
SELECT
    Max(num)    as num
FROM
    MyNumbers
WHERE
    num in (SELECT num FROM MyNumbers Group By num HAVING count(num) = 1)
;
```
&#160; 문제를 보고 떠올렸던 것은 우선 NyNumbers에서 중복되지 않은 값만 추려서 가져오자였습니다. 그래서 HAVING을 사용해서 ```count(num) = 1``` 조건을 걸어서 중복된 값들은 제거했습니다.
그 다음에 ```in```을 사용해 해당 num만 가져온 후, ```max```값을 구했습니다.

