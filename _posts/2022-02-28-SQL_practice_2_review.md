---
title: SQL 기초 문법2_review
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

## review

>
#### 쿼리 잘 작성하셨습니다. 참고로 count(1)과 count(*)는 차이가 없다고 보셔도 무방합니다!

<br><br>


# 2. 2006년 1분기에 고객(customer)별 주문(order) 횟수, 주문한 상품(product) 의 카테고리(category) 수, 총 주문 금액(quantity * unit_price)을 찾는 쿼리를 작성하세요. (힌트: join)
<br><br>

## 기존 답안

<br>

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

<br><br>

## review

<br>

Feedback 1)

<br>

>2주차에 학습한 내용을 토대로 Northwind DB에 대한 ERD를 확인해보시면 orders 테이블과 order_details 테이블은 1:N 관계에 있습니다. ***즉 order_details 테이블에는 한 id에 여러 order_id가 존재할 수 있다는 의미***입니다. 이 문제에서 처럼 주문 횟수를 세기 위해서는 결국 order_id를 세면 되는 것인데, 바로 이 order_id가 중복이 될 수 있기 때문에 그냥 count()를 하면 안됩니다. 이때 사용할 수 있는 문법이 바로 ***count(distinct 컬럼명)*** 입니다. 마찬가지 이유로 ***category 수를 세기 위해서도 distinct가 필요***합니다. count()를 할 때에 특히 실수하기 쉬운 부분이니 꼭 잘 알아두시기 바랍니다!

<br>

Feedback 2)

<br>

>그냥 join이라고만 쓰면 앞에 inner가 생략되어 있는 건데 이렇게 생략해서 쓰지 마시고 ***inner join 혹은 left (outer) join이라고 풀어서 쓰는 습관***을 들이시면 좋을 것 같습니다. 쿼리를 쓰는 사람 입장에서도 읽는 사람 입장에서도 더 명확해지니까요!
그리고 1주차에 각 join 방식 별 차이를 공부하셨을 텐데 이건 꼭 필수로 자세히 알아주시기 바랍니다. 팁을 드리자면 보통은 제일 기준이 되는 테이블을 맨 왼쪽에 두고 ***left join을 제일 많이 쓰는 것*** 같구요. ***inner join***은 말그대로 ***교집합을 검사하고 싶은 경우나 중복을 제거하기 위한 기술적인 목적***으로 씁니다. 이 문제의 상황에서는 inner join이나 left join 둘다 사용하셔도 크게 상관없어 보입니다.


<br><br>

## Feedback 적용 답안

<br>

```sql
select o.customer_id, 
    count(distinct o.id) order_cnt, 
    count(distinct p.category) category_cnt, 
    sum(od.quantity * od.unit_price) sum_of_order_price
from orders o
    left join order_details od on o.id = od.order_id
    left join products p on od.product_id = p.id
where '2006-01-01' <= o.order_date
    and o.order_date < '2006-04-01'
group by o.customer_id;
```
<br><br>

![2번 Feedback 결과](/assets/images/SQL_practice2_review_2.png)

<br><br>

# 3. 2006년 3월에 주문(order)된 건의 주문 상태(status_name)를 찾는 쿼리를 작성하세요. (join을 사용하지 않고 쿼리를 작성하세요.) (힌트: orders_status 사용, sub-query)

<br><br>

## 기존 답안

<br>

```sql
select o.id as Order_ID, 
    (select status_name from orders_status as os where os.id = o.status_id) as status_name
from orders as o
where (year(o.order_date), month(o.order_date)) = (2006, 3);
```

<br>

![3-1번 문제 결과](/assets/images/SQL_practice2_3.png)

<br><br>

## review

<br>

Feedback 1)

>order_details_status는 order_details에 대한 상태를 나타내는 테이블이고, orders_status가 orders에 대한 상태를 나타내는 테이블입니다. 따라서 이 문제에서는 order_detail_status 대신 orders_status 테이블을 이용해주시는게 더욱 적절합니다. 이런 내용을 확인하기 위해 ***ERD 상에서 어떤 테이블들끼리 연결되어 있는지를 보시면 도움***이 됩니다.

<br>

Feedback 2)

>orders 테이블에는 status_id라고 해서 의미를 알 수 없는 숫자만 적어놓고, 이 숫자의 의미를 알기 위해서는 orders_status 테이블을 봐야 합니다. 이런 식으로 테이블을 구성하는 걸 ***‘코드화’***한다고 합니다. 즉 status_id는 코드이고, 코드 값은 status_name 입니다. 그냥 간단하게 orders 테이블에 문자열로 주문상태를 기록해두면 좋을 텐데 굳이 이렇게 코드화를 하는 이유는 ***저장공간을 아끼기 위해서*** 입니다. ***컬럼 한 두 개야 크게 차이는 없겠지만 이런 컬럼이 많아지고, 그런 테이블이 많아지고 하다보면 전체적으로는 큰 차이가 생길 수 있어*** 보통 이런 코드화를 많이 합니다. 이 문제는 바로 이런 코드화 되어있는 값을 읽어오는 경우에 대한 예제라고 생각하시면 됩니다.

<br>

Feedback 3)

>사실 join을 이용하면 훨씬 쉽게 짤 수도 있는 쿼리인데, 이런 식으로 select 절에 subquery를 쓰는 방법도 있습니다. 특히 이 문제와 같이 ***코드 값을 읽어오는 경우에 이렇게 subquery를 사용하는 걸 약간의 테크닉처럼 많이 사용***하는 것 같습니다. Join을 이용한 방법도 전혀 문제는 없지만 자주 나오는 패턴이라 같이 익혀두시면 좋을 것 같습니다. Join을 이용한 쿼리는 예시답안을 참고해주세요.

<br>

Feedback 4)

>참고로 subquery는 전혀 어렵게 생각하실 필요가 없습니다. 그저 쿼리 안에 또 다른 쿼리가 나오는 형태일 뿐이고, 쿼리를 작성하는 도중에  '***아 이 부분에는 다른 쿼리로 미리 계산된 결과를 사용하고 싶다***'라는 생각이 들 때 활용해 볼 만한 문법입니다. 쿼리를 읽으실 때도 도중에 subquery를 만나면 잠시 그 쿼리 안으로 들어가서 내용을 해석하고 다시 밖으로 나오면 됩니다. 혹시라도 인터넷에서 자료를 찾아보시다가 subquery가 쿼리 안 어디에 위치하는 지에 따라서 이름을 다르게 부르는 것은 굳이 알아두실 필요가 없습니다(select 절에 나오면 뭐고, from 절에 나오면 뭐고 하는 식으로). 현업에서는 전혀 사용하지 않는 용어입니다. ***subquery는 쿼리 어디에서든지 나올 수 있고, 그저 쿼리안의 쿼리일 뿐***입니다.

<br><br>

## Feedback 적용 답안 1) join을 사용할 경우

<br>

```sql
select o.id, os.id, os.status_name
from orders o
    left join orders_status os on o.status_id = os.id
where '2006-03-01' <= o.order_date 
    and o.order_date < '2006-04-01';
```
<br><br>

## Feedback 적용 답안 2) sub-query를 사용할 경우

<br>

```sql
select id, status_id, (select status_name from orders_status os where os.id = o.status_id) status_name
from orders o
where '2006-03-01' <= order_date 
    and order_date < '2006-04-01';
```
<br><br>

![3번 Feedback 결과](/assets/images/SQL_practice2_review_3.png)

<br><br>

# 4. 2006년 1분기 동안 세 번 이상 주문(order) 된 상품(product)과 그 상품의 주문 수를 찾는 쿼리를 작성하세요. (order_status는 신경쓰지 않으셔도 됩니다.) (힌트: sub-query or having)


<br>

## 기존 답안

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

<br><br>

Feedback 1)

<br>

>이 문제는 1. 상품별 주문 수 찾기, 2. 주문 수가 세 번 이상인 것 찾기 의 두 단계로 접근할 수 있는 문제입니다. 첫 번째 예시답안은 이 두 단계를 서브 쿼리를 이용해 그대로 구현한 것이구요. 두 번째 예시답안은 이를 having을 이용해 쿼리를 더욱 간결하게 만들어 준 모습입니다.

<br>

Feedback 2)

<br>

>Having은 ***집계함수의 결과에 조건을 거는 문법***입니다. 이 문제에서 처럼 count()가 되어질 결과에 미리 having을 통해 조건을 걸 수 있습니다.

<br><br>

## Feedback 적용 답안 1) sub-query를 사용할 경우

<br>

```sql
select *
from (
    select product_id, count(distinct o.id) cnt
    from orders o
        left join order_details od on o.id = od.order_id
    where '2006-01-01' <= order_date 
        and order_date < '2006-04-01'
    group by product_id
    ) a
where cnt >= 3;
```
<br><br>

## Feedback 적용 답안 2) having을 사용할 경우

<br>

```sql
select product_id, count(distinct o.id) cnt
from orders o
    left join order_details od on o.id = od.order_id
where '2006-01-01' <= order_date 
    and order_date < '2006-04-01'
group by product_id
having count(distinct o.id) >= 3;
```
<br><br>

![4번 Feedback 결과](/assets/images/SQL_practice2_review_4.png)

<br><br>

# 5-1. 2006년 1분기, 2분기 연속으로 주문(order)을 받은 직원(employee)을 찾는 쿼리를 작성하세요. (order_status는 신경쓰지 않으셔도 됩니다.) (힌트: sub-query, inner join)

<br><br>

Feedback)

<br>

>***Inner join을 통해 교집합을 구현하는 것***이 핵심인 문제였습니다. 1분기에 주문을 받은 직원과 2분기에 주문을 받은 직원을 subquery를 통해 각각 따로 만든 다음 inner join을 통해 그 둘의 교집합을 계산하면 됩니다.


<br><br>

## Feedback 적용 답안 

<br>

```sql
select o1.employee_id
from 
    (select distinct employee_id
    from orders
    where '2006-01-01' <= order_date 
        and order_date < '2006-04-01') o1
        
    inner join
    
    (select distinct employee_id
    from orders
    where '2006-04-01' <= order_date 
        and order_date < '2006-07-01') o2
        
    on o1.employee_id = o2.employee_id;
```
<br><br>

![5-1번 Feedback 결과](/assets/images/SQL_practice2_review_5-1.png)

<br><br>

# 5-2. 2006년 1분기, 2분기 연속으로 주문(order)을 받은 직원(employee) 별로, 2006년 월별 주문 수를 찾는 쿼리를 작성하세요. (order_status는 신경쓰지 않으셔도 됩니다.) (힌트: sub-query 중첩, date_format() )

<br><br>

Feedback)

<br>

>5-1에서 작성한 쿼리를 활용해 조건을 만족하는 특정 employee를 골라내기만 하면 나머지 부분은 비교적 쉽게 작성할 수 있는 문제였습니다. 
연월별로, 직원별로 집계를 하기 위해서 이 기준으로 group by를 해줘야 합니다.
참고로 ***group by 1, 2는 select 절에 있는 첫 번째, 두 번째 컬럼으로 group by를 하겠다는 의미***로 쿼리를 편하게 줄여서 쓴 것입니다. 이런 편의 기능도 사용해 보셔도 좋을 것 같습니다.


<br><br>

## Feedback 적용 답안 

<br>

```sql
select employee_id, date_format(order_date, '%Y-%m') ym, count(1) cnt
from orders
where employee_id in (
    select o1.employee_id
        from 
            (select distinct employee_id
            from orders
            where '2006-01-01' <= order_date 
                and order_date < '2006-04-01') o1
            inner join
            (select distinct employee_id
            from orders
            where '2006-04-01' <= order_date 
                and order_date < '2006-07-01') o2
            on o1.employee_id = o2.employee_id
        )
group by 1, 2;
```
<br><br>

![5-2번 Feedback 결과](/assets/images/SQL_practice2_review_5-2.png)

<br><br>

---