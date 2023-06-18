---
title: "[SQL] LeetCode 풀이"

categories:
    - Data
tags:
    - Data
    - SQL
    - LeetCode
last_modified_at: 2023-06-18T17:56:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

&#160; 안녕하세요. 데이터 분석가에게 SQL 활용 능력은 필수적이라 생각됩니다. 웬만한 데이터 분석가 JD 요구사항에 SQL 활용능력이 없는 경우는 없더군요. 저도 데이터 분석가 업무를 하면서 실무에서 유사(?) SQL을 활용해 데이터 마트를 만들지만 내가 SQL을 제대로 다루고 있는지 항상 의문이 따라왔습니다. 게다가 제 SQL 활용 능력이 어느정도인지 궁금하기도 했습니다. 따라서 실무 외에 SQL을 제대로 한 번 공부해보고 싶었고, 그 중 하나로 웹에 SQL 문제를 풀어보기로 했습니다. 구글링을 해보니 SQL 문제를 제공하는 여러 사이트가 나왔고, 그 중에서 LeetCode가 50문제 정도를 무료로 제공하길래 시작으로 괜찮다 싶어서 정했습니다! 리트코드 50문제를 풀어보고 문제별로 Solution을 이 블로그에 정리하고자 합니다.

# 1. Select
&#160; 첫 Section은 Select로 아주 기본적인 SQL 문을 활용해 풀 수 있는 문제들이었다.
## 1.1 Recyclable and Low Fat Products
### 1.1.1 Table Description & Question
**Table** description

|Colum Name|Type|
|---|---|
|Product_id|int|
|low fats|enum| 
|recyclable|enum|

> table name is "PRODUCTS"<br>
> product_id is the primary key for this table. <br>
> low_fats is an ENUM of type ('Y', 'N') where 'Y' means this product is low fat and 'N' means it is not.<br>
> recyclable is an ENUM of types ('Y', 'N') where 'Y' means this product is recyclable and 'N' means it is not.

**Question**<br>
Write an SQL query to find the ids of products that are both low fat and recyclable.<br>
Return the result table in any order.
### 1.1.2 Solution
```sql
SELECT 
    product_id
FROM 
    PRODUCTS
WHERE 
    low_fats = 'Y' and recyclable = 'Y'
```

문제가 요구하는 것은 low fat이고 재활용 가능한 제품명을 추출해 보여달라는 것이다. low fat & recyclable한 제품만을 추출하기 위해 조건절을 추가해주면 된다. WHERE 조건절도 각각 본인 스타일에 맞게 쓰면 된다. 하나의 정답만 있는 것은 아니다.

### 1.1.3 Good to Know
Enum 타입 데이터는 Character형 데이터 타입이며, 해당 Column에 할당할 수 있는 값들을 지정해 줄 수 있다. 해당 데이터 칼럼에 값들을 제약할 수 있는 기능을 재공해준다(데이터 정합성 확보에 유리함). Enum 타입 데이터 칼럼은 값과 index 모두 활용하여 값을 불러올 수 있다. <br>
다만, Enum 타입에 새로운 데이터를 추가할 때 번거로움이 있으며 정렬이나 연산 시 불리하다는 의견도 있는 것 같다. 추가로 Enum 타입 데이터를 지원하지 않는 DBMS로의 이식이 어려울 수 있을 것 같다. 따라서 굳이 Enum 타입 데이터를 활용하기 보다는 참조 테이블을 활용하는 것이 좋을 수 있다.

## 1.2 Find Customer Referee
### 1.2.1 Table Description & Question
**Table** description

|Colum Name|Type|
|---|---|
|id|int|
|name|varchar| 
|refree_id|int|

**Input:**<br>
Customer table:<br>

| id | name | referee_id |
|---|----|---|
| 1  | Will | null       |
| 2  | Jane | null       |
| 3  | Alex | 2          |
| 4  | Bill | null       |
| 5  | Zack | 1          |
| 6  | Mark | 2          |

> table name is "Customer"<br>
> Each row of this table indicates the id of a customer, their name, and the id of the customer who referred them.

**Question**<br>
Write an SQL query to report the names of the customer that are not referred by the customer with id = 2.
Return the result table in any order.

### 1.2.2 Solution
```sql
SELECT
  name
FROM
  Customer
WHERE
  referee_id != 2 or isnull(referee_id)
```

input Table을 보면 refree_id에 null값이 포함되어 있다. 만약 Where 조건절에 refree_id != 2 조건만 있으면 refree_id가 null인 값을 제외시켜 버리기 때문에, refree_id가 null인 record를 불러오기 위해 조건을 추가해야 한다. 따라서 refree_id != 2 or isnull(refree_id)로 조건절을 줘야 refree_id가 2인 record를 제외한 모든 record를 불러오게 된다.

### 1.3.3 Good to Know
다른 사람이 올린 Solution 중, null값을 처리하는 다양한 방법이 있길래 알아두면 좋을 것 같아 기록하고자 한다.
```sql
refree_id IS NULL
Coalesce(refree_id, 0) != 2 (or <>2)
```
isnull() 등 함수를 활용하여 null값을 처리하는게 익숙하다보니 함수로 처리했는데, 함수를 쓰지 않아도 "IS Null" 구문으로 처리가 가능하다. 직관적으로 이해하기 쉽다. <br>
coalesce 함수는 보통 Null값이 있는 데이터 컬럼을 처리할 때 종종 사용하는 함수이다. 첫 번째 인수로 들어가 있는 컬럼의 값이 Null인 경우 두 번째 인수의 값을 할당하고 두 번째 인수의 값도 Null인 경우 그 다음 인수의 값을 할당해주는 식의 함수이다. 위의 코드를 해석하면 refree_id가 null인 경우에 0을 할당해주고 그 값이 2가 아닌 경우를 판별하는 조건문이다. If 문을 활용하는 것보다 훨씬 깔끔하다 ㅎㅎ

## 1.3 Big Countries
### 1.3.1 Table Description & Question
**Table** description

| Column Name | Type    |
|-------------|---------|
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | bigint  |

> name is the primary key column for this table.<br>
> Each row of this table gives information about the name of a country, the continent to which it belongs, its area, the population, and its GDP value.

**Input:**<br>
Customer table:<br>

| name        | continent | area    | population | gdp          |
|-------------|-----------|---------|------------|--------------|
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |

**Question** <br>
A country is big if:

- it has an area of at least three million (i.e., 3000000 km2), or
- it has a population of at least twenty-five million (i.e., 25000000).

Write an SQL query to report the name, population, and area of the big countries.<br>
big 조건에 부합하는 국가의 국가명, 인구, 면적을 report하면 된다.

### 1.3.2 Solution
```sql
SELECT
  name,
  population,
  area
FROM 
  World
WHERE
  area >= 3000000 or population >= 25000000
```
간단한 문제이므로 추가설명은 하지 않도록 하겠다.

## 1.4 Article Views I
### 1.4.1 Table descriptoin & Question
**Table** description

| Column Name   | Type    |
|---------------|---------|
| article_id    | int     |
| author_id     | int     |
| viewer_id     | int     |
| view_date     | date    |

> There is no primary key for this table, it may have **duplicate rows.** <br>
> Each row of this table indicates that some viewer viewed an article (written by some author) on some date. <br>
> Note that **equal author_id and viewer_id indicate the same person.**

주의해야 할 점은 데이터가 중복인 row가 있다는 것과 author_id와 viewer_id가 값이 같다면 동일인물이라는 것이다. 

**Input:**<br>
Customer table:<br>

| article_id | author_id | viewer_id | view_date  |
|------------|-----------|-----------|------------|
| 1          | 3         | 5         | 2019-08-01 |
| 1          | 3         | 6         | 2019-08-02 |
| 2          | 7         | 7         | 2019-08-01 |
| 2          | 7         | 6         | 2019-08-02 |
| 4          | 7         | 1         | 2019-07-22 |
| 3          | 4         | 4         | 2019-07-21 |
| 3          | 4         | 4         | 2019-07-21 |

**Question** <br>
Write an SQL query to find all the authors that viewed at least one of their own articles.<br>
Return the result table sorted by id in ascending order.

### 1.4.2 Solution
```sql
SELECT DISTINCT
    author_id as id
FROM
    Views
WHERE
    author_id = viewer_id
ORDER BY
    id asc
```
중복값을 처리하기 위해 SELECT 문 뒤에 DISTINCT를 추가했다. 실무에서도 Table을 로드할 때, 습관적으로 Distinct를 붙여주곤 한다. 특히, 여러 테이블을 Join할 때, 중복값이 있는 경우 결과값이 뻥튀기 되어 컴퓨팅 리소스를 한계 이상으로 활용하여 다운될 수 있는 위험이 있기 때문이다. 물론 중복이 애초에 안생기게 하는 것이 최선이지만 혹시 모를 사고를 미연에 방지하기 위한 습관이다.

### 1.4.3 Good to Know
SQL린이 시절, 오름차순 내림차순이 항상 헷갈렸다. 이제는 헷갈릴 일이 없긴 하지만, 오름차순은 낮은 값부터 높은 값 순으로 정렬하는 거고 영어로 보통 ```asc or ascending```로 표기한다. 내림차순은 반대이며 보통 ```desc or descending```으로 표기한다.

## 1.5 Invalid Tweets
### 1.5.1 Table Description & Question
**Table** description

| Column Name    | Type    |
|----------------|---------|
| tweet_id       | int     |
| content        | varchar |

> tweet_id is the primary key for this table.<br>
> This table contains all the tweets in a social media app.

**Input:**<br>
Customer table:<br>

| tweet_id | content                          |
|----------|----------------------------------|
| 1        | Vote for Biden                   |
| 2        | Let us make America great again! |

**Question** <br>
Write an SQL query to find the IDs of the invalid tweets. The tweet is invalid if the number of characters used in the content of the tweet is **strictly greater than 15.**

### 1.5.2 Solution
```sql
SELECT
    tweet_id
FROM
    Tweets
WHERE
    length(content) > 15
```

문제에서 요구한 글자수가 15보다 많은 tweet_id를 추출하기 위해서 content 데이터의 글자수를 알아야한다. character형태의 데이터의 글자수를 알기 위해서는 ```length()``` 함수를 활용하면 된다.

