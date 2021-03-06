---
title: SQL 기초 문법1
layout: post
post-image: https://media.vlpt.us/images/dms873/post/e99e7c29-6dd1-4c4c-ae6f-57326892a60a/SQL.png
description: SQL 테이블 구조, select, groupby, order, join과 관련된 내용 학습
tags:
- SQL
- select
- groupby
- order
- join
---


# 1. Country 별로 ContactName이 ‘A’로 시작하는 Customer의 숫자를 세는 쿼리를 작성하세요.

<br><br>

```sql
SELECT country,
COUNT(ContactName) FROM Customers
WHERE ContactName LIKE 'A%'
GROUP BY country; 
```

<br>

![1번 문제 결과](/assets/images/SQL_practice1_1.png)

<br>

# 2. Customer 별로 Order한 Product의 총 Quantity를 세는 쿼리를 작성하세요.

<br><br>

```sql
SELECT O.CustomerID, COUNT(OD.Quantity)
FROM OrderDetails AS OD, Orders AS O
WHERE OD.OrderID = O.OrderID
GROUP BY CustomerID;
```

<br>

![2번 문제 결과](/assets/images/SQL_practice1_2.png)

<br>


# 3. 년월별, Employee별로 Product를 몇 개씩 판매했는지를 표시하는 쿼리를 작성하세요.

<br><br>

### 3-1 연월별 Product 개수

<br>

```sql
SELECT O.OrderDate,
	COUNT(OD.Quantity)
FROM Orders AS O, Employees AS E, OrderDetails AS OD
WHERE O.EmployeeID = E.EmployeeID
GROUP BY OrderDate;
```

<br>

![3-1번 문제 결과](/assets/images/SQL_practice1_3-1.png)

<br>

### 3-2 Employee별 Product 개수

<br>

```sql
SELECT O.EmployeeID, 
	COUNT(OD.Quantity)
FROM Orders AS O, Employees AS E, OrderDetails AS OD
WHERE O.EmployeeID = E.EmployeeID
GROUP BY E.EmployeeID;
```

<br>

![3-2번 문제 결과](/assets/images/SQL_practice1_3-2.png)

<br>

---