---
layout: single
title: SQL을 이해하고 사용하는 DJANGO ORM
categories: django
tags: 
 - python
 - django
toc: true
---

# SQL을 이해하고 사용하는 Django ORM

## Djang ORM 특징
1. ORM은 객체와 관계형 데이터베이스를 연결 해준다. 
2. ORM을 이용하면 개발자가 직접 쿼리문을 입력하지 않아도 DML을 수행 할 수 있다.
3. ORM은 객체지향적인 코드로 데이터를 다루기 때문에 코드 가독성이 높아지고 생산성이 향상 된다.
4. 추상화를 통해 데이터베이스와 코드베이스를 느슨하게 연결해주기 때문에 인프라 변경에 유연하게 대처 할 수 있다.
5. ORM 은 lazy loading과 cache를 제공하여 불필요한 쿼리를 줄여 주기도 한다.
6. 쿼리하는 데이터가 복잡할 수록 ORM이 불편한 경우도 많다. ORM의 한계로 인해 SQL의 모든 기능을 활용하기 어렵다. (실력이 있다면 가능하겠지만 본인이 생각하는 쿼리를 ORM으로 완벽하게 구현하기는 좀 까다로운 편이다.)
7. 동작원리를 제대로 이해하지 못하고 사용하는 ORM은 매우 비효율적인 쿼리를 만들어 낸다. 

## Django ORM

### 기초 SQL
Django Orm을 읽힐때 특정 메소드가 SQL구문과 1:1로 대응한다고 생각하면 안된다.
예를 들어 GROUP BY 구문와 완벽하게 대응되는 메소드는 존재하지 않는다.
특정 메소드를 읽히기 보다는 SQL을 이해하고 ORM이 동작하는 원리를 먼저 알아야 한다. 문서를 보며 ORM 메소드 사용법을 아무리 봐도 원하는 데이터를 추출하지 못하는 이유이기도 한다.
```python
User.objects.all() <- SQL에서 table sacn
User.objects.filter(is_activa=True) <-SQL의 where 절
```

ORM은 queryset이라는 객체를 통해 데이터를 반환한다. 따라서 아래와 같이 데이터의 반환값을 변수에 할당 할 수 있다.
```python
queryset = User.objects.all()
# 할당된 쿼리 셋은 이터러블한 객체이다.
```

쿼리셋은 하나의 쿼리에 대한 데이터라고 봐도 무방하다.
쿼리셋에 사용된 SQL은 아래와 같이 확인 가능하다.
```python
queryset = User.objects.all()
str(queryset.query)
SELECT * FROM User
```

sqlparse를 이용하면 SQL을 보기 좋기 출력 할 수 있다.

또 SQL 실행계획은 아래와 같이 확인 가능하다. 

실행 계획은 query의 성능 분석을 위해 반드시 확인해야한다. 실행계획은 꼭 공부하기를 바란다. 

제대로 된 백앤드 개발자가 되고 싶다면 말이다 !!! 무조건 공부하세욤

```python
import sqlparse

query = str(queryset.query)
print(sqlparse.format(quert, reindent=True))

# query 실행계획
print(queryset.explain())
```

SQL의 집계함수를 이용하면 집합에 대한 연산을 쉽게 할 수 있다.

대표적인 집계 함수는 'SUM', 'MAX', 'MIN', 'AVG', 'COUNT' 등이 있다.

집계 함수의 특징은 여러 record로 부터 단일 결과값을 반환 한다는 것이다.

물론 Django ORM에서도 당연히 있다.

Django ORM에서는 aggregate 메소드를 통해 집계 함수를 제공한다.

User table의 전체 id 개수를 알고 싶다면 아래와 같이 하면 된다.

User table의 전체 record에 대한 집계가 하나의 결과로 집약된 것을 확인 할 수 있다.

결과값에 __count 는 alias를 지정하지 않았을 떄 DJango에 의해 기본적으로 추가 되는 네이밍 이다.
```python
User.objects.aggregate(Count('id')) => {"id__count":3}
SELECT
    COUNT(id) AS id__count
FROM
    User
```
집계 결과를 변경하고 싶으면 아래와 같이 변경 할 수있다.

```python
User.objects.aggregate(count=Count('id')) => {"count":3}
SELECT
    COUNT(id) AS count
FROM
    User
```
table의 record 개수를 세는 기능은 자주 필요하기 때문에 Django는 이를 위한 count() 메소드를 별도로 지원한다.

```python
User.objects.count() = 3
SELECT
    COUNT(*)
FROM
    User
```

특정 컬럼을 기준으로 집계할 때 해당 컬럼 값이 null 이면 집계에서 제외된다.

만약 id 컬럼이 null을 허용 한다면 두개의 쿼리는 같은 결과를 보장한다고 할 수 없다.

```python
# aggregate()
SELECT
    COUNT(id)
FROM
    User

# count()
SELECT
    COUNT(*)
FROM
    User
```

price 가 null이면 집계에서 빠지고 0이면 집계에 추가된다.

결국 null 과 0이 집계에 영향을 준다.

이렇게 결과가 달라지는 이유는 null 은 0이 아니기 때문이다.

Data Base에서 null은 데이터가 없다는 것을 의미 한다.

그래서 집계함수에서 null값은 어떠한 영향도 주지 않는다.

```python
Goods.objects.aggregate(AVG('price')) => {'price__avg':10000}

```
|ID|제목|가격|
|---|---|---|
|1|상품1|10,000|
|2|상품2|10,000|
|3|상품3|0|
평균이 10,000

|ID|제목|가격|
|---|---|---|
|1|상품1|10,000|
|2|상품2|10,000|
|3|상품3|null|
평균이 15,000

데이터를 조회 할 때 날짜를 기준으로 조회하는 경우가 많다.

gte 와 lte를 사용하면 비교 표현식을 사용하는 쿼리로 변경된다.

```python
User.objects.filter(
    joined_at__gte = date(2023, 1, 1),
    joined_at__lte = date(2023, 12, 31)
)

SELECT
    *
FROM
    USer
WHERE
    joined_at >= '2022-01-01'
    AND joined_at <= '2022-12-31'
```

Django는 특정기간에 대해 조회 할 수 있도록 range 메소드를 제공한다.

range를 이용하면 gte lte 대신 아래와 같이 변경 가능하다.

range를 사용 할 때 흔히 발생하는 실수는 조회 조건이 되는 끝 값을 잘못 지정하는 것이다.

range 메소드는 BETWEEN문을 생성하는데 BETWEEN문은 구간에 대해서 Inclusive 하게 동작한다.

Inclusive 하다는 뜻은 조건의 시작값과 끝값이 결과 집합에 포함 된다는 것을 의미한다.

```python
User.objects.filter(
    joined_at__range = (
        date(2023, 1, 1), date(2023, 12, 31)
    )
)

SELECT
    *
FROM
    USer
WHERE
    joined_at
    BETWEEN '2022-01-01' AND '2022-12-31'
```

문제는 조회하는 컬럼이 Datetime field 일때 이다.

조회하는 컬럼이 Datetime 일 경우 인자로 date를 전달 했지만 쿼리는 datetime format으로 생성된다.

Django가 컬럼의 type에 맞게 주어진 값을 시분초가 0인 datetime으로 변경한 것이다.

따라서 아래의 ORM은 아래의 쿼리문을 생성하게 된다. ( 의도치 않은 결과 초래 )
```python
User.objects.filter(
    joined_at__range = (
        date(2023, 1, 1), date(2023, 12, 31)
    )
)

SELECT
    *
FROM
    USer
WHERE
    joined_at
    BETWEEN '2022-01-01 00:00:00' 
    AND '2022-12-31 00:00:00'
```

인자를 datetime으로 변경해도 시분초를 명시적으로 지정해주지 않으면 위의 결과와 동일한 쿼리가 생성된다. 

```python
User.objects.filter(
    joined_at__range = (
        datetime(2023, 1, 1), datetime(2023, 12, 31)
    )
)

SELECT
    *
FROM
    USer
WHERE
    joined_at
    BETWEEN '2022-01-01 00:00:00' 
    AND '2022-12-31 00:00:00'
```

따라서 날짜 조회시 AND 조건에는 exclusive 하게 쿼리하는 것이 안전하다.

```python
User.objects.filter(
    joined_at__gte = date(2023, 1, 1),
    joined_at__lt = date(2023, 12, 31)
)

SELECT
    *
FROM
    USer
WHERE
    joined_at >= '2022-01-01 00:00:00'
    AND joined_at <= '2022-01-01 00:00:00'
```

Union 메소드를 이용하면 쿼리셋의 결과값을 합칠 수 있다.

테이블이 다를지라도 컬럼값이 동일하다면 union이 가능하다.
```python
books = Book.objects.all()
ebooks = EBook.objects.all()

bopoks.union(ebooks)
(
SELECT
    *
FROM
    Book
) UNION (
SELECT
    *
FROM
    EBook
)
```
