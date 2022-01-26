---
title: SQL 기초 문법1_review
layout: post
post-image: https://media.vlpt.us/images/dms873/post/e99e7c29-6dd1-4c4c-ae6f-57326892a60a/SQL.png
description: SQL 테이블 구조, select, groupby, order, join과 관련된 지난 번 학습 내용에 관한 review
tags:
- SQL
- select
- groupby
- order
- join
---


# 1. Country 별로 ContactName이 ‘A’로 시작하는 Customer의 숫자를 세는 쿼리를 작성하세요.

<br><br>

## 기존 답안

<br>

```sql
SELECT country,
COUNT(ContactName) FROM Customers
WHERE ContactName LIKE 'A%'
GROUP BY country; 
```

<br>

## review

>
#### Count 안에 컬럼명을 넣으면 그 컬럼 값에 null이 있을 때는 카운트를 하지 않고, 컬럼명이 없으면 null이 있어도 카운트를 합니다. <br>
=> 컬럼명이 없을 때는 그냥 row 수를 세는 것 <br>
#### 보통 전체적인 row 수를 파악하는 것이 더 중요한 경우가 많아 count(1) 혹은 count(*)를 주로 사용. 두 가지는 서로 차이가 없다고 해도 무방하다.

<br>

![리뷰 후 결과](/assets/images/SQL_practice1_review_1.png)

<br>

# 2. Customer 별로 Order한 Product의 총 Quantity를 세는 쿼리를 작성하세요.

<br><br>

## 기존 답안

<br>

```sql
SELECT O.CustomerID, COUNT(OD.Quantity)
FROM OrderDetails AS OD, Orders AS O
WHERE OD.OrderID = O.OrderID
GROUP BY CustomerID;
```

<br>

## review

>#### 콤마(,)를 통해서 조인을 해주면 inner join이 된다. 보통, 제일 기준이 되는 테이블을 맨 왼쪽에 두고 left join을 제일 많이 쓰고, inner join은 말 그대로 교집합을 검사하고 싶은 경우나 중복을 제거하기 위한 기술적인 목적으로 가끔 사용. <br>
=> 이 경우에는 inner join이나 left join 둘 다 사용해도 크게 무방 <br>
#### 총 Quantitiy를 계산하기 위해 이 경우에는 count()보다 sum()이 더 적절하다.

<br>

![리뷰 후 결과](/assets/images/SQL_practice1_review_2.png)

<br>


# 3. 년월별, Employee별로 Product를 몇 개씩 판매했는지를 표시하는 쿼리를 작성하세요.


<br><br>

## 기존 답안

<br>

```sql
SELECT O.OrderDate,
	COUNT(OD.Quantity)
FROM Orders AS O, Employees AS E, OrderDetails AS OD
WHERE O.EmployeeID = E.EmployeeID
GROUP BY OrderDate;
```

<br>

## review

>#### 우선 문제의 핵심은, 연/월/일 형태로 되어 있는 OrderDate을 어떻게 연/월 형태로 바꿀지를 고민하는 문제. <br>
=> MySQL에서는 date_format() 등의 함수를 많이 쓰는데, 해당 실습 환경에선 날짜를 문자로 보고 substr() 함수를 사용 가능 <br>
#### 두 기준으로 동시에 group by를 하기 위해서는 두 기준을 group by 절에 모두 명시

<br>

![리뷰 후 결과](/assets/images/SQL_practice1_review_3-1.png)

=> substr(문자열, 시작 위치, 나타낼 개수) slice를 해주는 함수
=> AS를 사용하지 않고 뒤에 공백 후 붙여주기만 해도 별칭이 형성

<br>

---