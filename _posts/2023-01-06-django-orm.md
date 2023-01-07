---
layout: single
title: django orm
categories: django
tag: [python, django]
---
# 
# Django ORM n + 1
## ORM n + 1 문제는 무엇인가 ? 

### Djnago orm의 장점
1. 객체 지향적인 코드로 인해 더 직관적이고 비즈니스 로직에 더 집중할 수 있게 도와준다.
2. 재사용 및 유지보수에 용이하다.
3. DBMS에 대한 종속성이 줄어든다. (여러 RDBMS 에서 사용 가능)


### Django orm의 단점
1. ORM으로만 완벽한 서비스를 구현할 수 없다.
2. 프로시저가 많은 시스템에선 ORM의 객체 지향적인 장점을 활용하기 어렵다.
3. 프로젝트의 복잡성이 크면 구현하는 난이도가 상승한다.


## DJango의 Lazy-Loading 방식과 Eager-Loading 방식을 알아야 한다
**Lazy-Loading 이란 ?**
> ORM 에서 명령을 실행할 때마다 DB에서 Data를 가져오는 것이 아니라, 실제로 Data를 불러와야 되는 시점이 왔을 때 쿼리를 날린다.


**Eager-Loading 이란 ?**
> 사전에 쓸 데이터를 포함해서 쿼리를 날리기 떄문에 반복적으로 데이터를 호출 할때 사용하면 불필요하게 늘어나는 쿼리 요청을 방지 할 수 있다.


#### DB에 쿼리가 날라감.
```python
# 이런식이면 쿼리가 n + 1번 나감
products = Product.objects.all() # DB에서 데이터를 가져 오지 않았다.
for prouct in products:
    print(prouct) # 이제 쿼리문이 반복적으로 실행된다.
```


### seleted_related
> 정방향 참조
```python
products = Product.objects.seleted_related('user') # 지금 쿼리를 날림
```

### prefetch_related
> 역방향 참조
```python
products = Product.objects.prefetch_related('user') # 지금 쿼리를 날림
```


### 다른 방식
```python
# 이런식으로 list 에 넣어도 한꺼번에 실행 된다.
products = list[Product.objects.all()]
```

### 나의 경험
> django-admin을 사용할 떄 이상하게 별거 아닌 쿼리인데 Loading이 상당히 느렸다.  
> Django-debug-toobar로 현재 실행되는 쿼리를 확인하니 숫자가 어마무시 했다. 이때  get_queryset method 를 오버라이드 하여, Eager-Loading 형태로 변경해주었다.
> 성능 개선 !