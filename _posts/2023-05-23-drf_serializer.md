---
layout: single
title: DRF Serializer ### 2
categories: django
tags: 
 - python
 - django
 - DRF
toc: true
---

# Pure Django와 Django REST Framework의 차이점


|특징|PureDjango|Django REST Framework|
|------|---|---|
|개발 목적|웹 풀스택 개발|백앤드 API 서버 개발|
|개발 결과|웹 페이지를 포함한 웹 서비스|여러 클라이언트에서 사용할 수 있는 API 서버|
|응답 형태|HTML|JSON|
|다른 파일|templates|serializers.py|

Django와 DRF의 차이점은 명확하다. 둘다 개발해 본 입장으로서 Django도 templates 폴더를 프론트 개발자가 개발 하더라도 결국에는 template file 도 django에서 데이터를 뿌려주기에 django 개발자의 손을 타야된다.
하지만 DRF는 응답 형태가 json이기에 여러 프론트 개발자는 json 파일 및 api 명세서를 보고 개발을 하면 된다.
즉 ***분업이 명확하다는 이야기이다.***

# DRF Serializer의 개념
- Pure DJango에서는 serializer의 기능을 template 수행한다는 개념으로 생각하면 된다. Serializer의 사전적 의미는 직렬화이다. 직렬화는 DJango 프로젝트에서 내가 만든 모델로부터 뽑은 queryset, 즉 모델 인스턴스를 JSON 타입으로 바꾸는 것이다. Django 모델을 JSON으로 변환하기 위한 틀 이라고 생각하면 쉽다.

- DRF 내에서 데이터를 저장할 때에는 Django의 모델을 통해 저장한다. 모델은 데이터베이스 테이블을 추상화한 개념이다. Django에서 데이터는 JSON 포맷이 아닌 Python 객체의 형태로 저장된다. 

- API는 데이터를 Cilent에게 보내주는데 어떠한 작업도 하지않고 데이터를 보내준다면 Client는 Python 객체 데이터를 받게 될 것이다. 당연히 Python 데이터를 읽을 수 있는 문자열로 보내주어야 하고, **Python -> JSON** 이 변환 작업을 직렬화( Serializer )라고 생각하면 된다.


- 반대로 Client가 서버에 데이터를 보내주는 경우도 있다. 회원가입 처럼 http의 post method 요청을 보낼 경우이는 JSON의 형태로 오면 서버는 이 JSON을 그대로 저장 할 수 없다. 모델에 저장할려면 Python 객체의 형태여야 하기 때문에 이 변환 작업을 역직렬화 ( Deserializer ) 라고 한다.

Serializer는 직렬화, 역직렬화의 기능을 가지고 있다. 클라이언트와 API의 데이터 양식을 맞춰주는 변환기라고 생각한다. 참으로 고마운 존재다.

# Model 과 serializer 코드
```python

# DJango의 개발을 모델로 부터 시작이다.

from django.db import models

class Product(models.Model):
    id = models.AutoField(primary_key=True)
    brand_name = models.CharField(max_length=50)
    name = models.CharField(max_length=50)
    price = models.CharField(max_length=50)
    descrtiption = models.CharField(max_length=50)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

```bash
# 내가 작성한 모델을 DB에 반영해야한다.
python manage.py makemigrations
python manage.py migrate
```


```python
# {app}/serializer.py
# example/serializer.py
from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'brand_name', 'name', 'price', 'descrtiption']
```

위의 코드에서느 serializers.ModelSerializer 를 사용했지만, serializers.Serializer를 사용해도 된다. 모델에서 사용하는 field의 정의와 좀 다르게 하고 싶다면 말이다. 그리고 create, update 함수를 사용 할수도 있지만. ModelSerializer를 사용하면 코드의 양도 줄일 수 있고, field의 선언도 모델에서 했으니, 코드 관리 측면에서도 좋다고 생각한다.

이제 views.py 를 코딩해서 작업을 해보자

```python
# {app}/views.py
# example/views.py
from rest_framework import status
from rest_framework.response import Response
from rest_framework.decorators import api_view
from .models import Product
from .serializers import ProductSerializer


@api_view(['GET', 'POST']) # GET POST 요청을 받을 수 있도록
def productsAPI(request):
    if request.method == 'GET':
        products = Product.objects.all()

        # 시리얼라이저에 데이터를 한번에 집어넣기 ( 직렬화, many=True )
        serializer = ProductSerializer(products, many=True)
        return Response(serializer.data, status=status.HTTP_200_OK)
    elif request.method == 'POST':

        # POST 요청으로 들어온 데이터를 시리얼라이저에 집어넣기
        serializer = ProductSerializer(request.data)
        if serializer.is_valid(): # 유효성 검사
            # 시리얼라이저의 역직렬화를 통해 sava()
            # 모델시리얼라이저의 기본 create() 함수가 동작
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

DRF에서 serializer는 상당히 중요한 역할을 차지한다.
views 코드가 serializer 덕분에 상당히 간결해졌다. 한 model에서 여러개의 serializer를 사용해 field를 선택하는 경우도 꽤 많다. list page api 와 detail page api를 생각해보면 필요한 데이터가 다르다. 이 경우 2개를 serializer 를 사용하면 불필요한 데이터를 보낼 필요가 없다.

오늘은 여기까지 !!!