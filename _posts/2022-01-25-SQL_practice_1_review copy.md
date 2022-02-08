---
title: ADsP 요약 Part.1 데이터의 이해
layout: post
post-image: https://cdn.class101.net/images/13004bf6-2ee0-4f3e-ba7e-b7bba5774de2/original
description: ADsP 자격증 준비를 위한 요약본 정리
tags:
- ADsP
- 데이터

---


# 1. 데이터와 정보

<br><br>

## 데이터의 정의

<br>

- 데이터의 의미는 과거의 관념적이고 추상적인 개념에서 기술적이고 사실적인 의미로 변화
- 데이터는 추론과 추정의 근거를 이루는 사실
- 단순한 객체로서의 가치뿐만 아니라 다른 객체와의 상호관계 속에서 가치를 찾는 것

<br>

## 데이터의 특성

<br>

- 존재적 특성
> 객관적 사실
- 당위적 특성
> 추론, 예측, 전망, 추정을 위한 근거

<br>

## 데이터의 유형 (중요)

<br>

| 정성적 데이터 | 정량적 데이터 |
| -----------   | ------------  |
| 언어, 문자    | 수치, 도형, 기호 |
| 저장, 검색, 분석에 많은 비용이 소모됨 | 정형화가 된 데이터로 비용 소모 적다 |
| 비정형 데이터 | 정형 데이터 |
| 주관적 내용 | 객관적 내용 |
| 통계 분석이 어려움 | 통계 분석이 용이 |
| ex) 회사 매출이 증가함 | ex) 나이, 몸무게, 주가 |


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