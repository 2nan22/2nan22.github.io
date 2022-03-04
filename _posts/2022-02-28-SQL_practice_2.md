---
title: SQL 기초 문법2
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


# 1. 상품(product)의 카테고리(category)별로, 상품 수와 평균 가격대(list_price)를 찾는 쿼리를 작성하세요.

<br><br>

```sql
select category, count(1) as num_of_product, 
    avg(list_price) as price_of_average
from products
group by category;
```

<br>

![1번 문제 결과](/assets/images/SQL_practice2_1.png)

<br>

# 2. 2006년 1분기에 고객(customer)별 주문(order) 횟수, 주문한 상품(product) 의 카테고리(category) 수, 총 주문 금액(quantity * unit_price)을 찾는 쿼리를 작성하세요. (힌트: join)
<br><br>

```sql
select o.customer_id as Customer_ID, concat(c.first_name, " ",c.last_name) as Customer_Name, count(o.id) as Num_of_orders,  
    count(p.category) as Num_of_category, sum(od.quantity * od.unit_price) as Total_order_price
from orders as o
join order_details as od 
    on o.id = od.order_id
join products as p
    on od.product_id = p.id
join customers as c
    on o.customer_id = c.id
-- where o.order_date between '20060101' and '20060331'
where (year(o.order_date), quarter(o.order_date)) = (2006, 1)
group by Customer_ID
order by Customer_ID;
```

<br>

![2번 문제 결과](/assets/images/SQL_practice2_2.png)

<br>


# 3. 2006년 3월에 주문(order)된 건의 주문 상태(status_name)를 찾는 쿼리를 작성하세요. (join을 사용하지 않고 쿼리를 작성하세요.) (힌트: orders_status 사용, sub-query)

<br><br>

```sql
select o.id as Order_ID, 
    (select status_name from orders_status as os where os.id = o.status_id) as status_name
from orders as o
where (year(o.order_date), month(o.order_date)) = (2006, 3);
```

<br>

![3-1번 문제 결과](/assets/images/SQL_practice2_3.png)

<br>

# 4. 2006년 1분기 동안 세 번 이상 주문(order) 된 상품(product)과 그 상품의 주문 수를 찾는 쿼리를 작성하세요. (order_status는 신경쓰지 않으셔도 됩니다.) (힌트: sub-query or having)


<br>

```sql
select id as order_id, 
      order_date as orders_date
from orders as o
where (year(order_date), quarter(order_date)) = (2006, 1)
and order_id = 
group by order_date;
```

<br>

<!-- ![3-2번 문제 결과](/assets/images/SQL_practice1_3-2.png) -->

<br>

# 5-1. 2006년 1분기, 2분기 연속으로 주문(order)을 받은 직원(employee)을 찾는 쿼리를 작성하세요. (order_status는 신경쓰지 않으셔도 됩니다.) (힌트: sub-query, inner join)

<br><br>

# 5-2. 2006년 1분기, 2분기 연속으로 주문(order)을 받은 직원(employee) 별로, 2006년 월별 주문 수를 찾는 쿼리를 작성하세요. (order_status는 신경쓰지 않으셔도 됩니다.) (힌트: sub-query 중첩, date_format() )

---