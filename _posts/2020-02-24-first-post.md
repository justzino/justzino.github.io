---
title: "Django 사용법 및 DRF 사용법"
excerpt: "Django를 사용하여 웹페이지를 만들기 위해 처음 해야할 설정들과 명령어"

categories:
  - Django
tags:
  - Django
  - DRF
  - 환경설정
last_modified_at: 2020-03-10T17:21:00-05:00
---

## 사용 명령어

- virtualenv 폴더명(fc_env) 가상환경 생성
- .\Scripts\activate.ps1 가상환경 실행
- pip install django django 설치
- django-admin startproject 폴더명 jango project를 실행할 폴더 생성
- django-admin startapp 이름(fcuser, order, project...) app생성
- python manage.py createsuperuser 관리자 생성
- python manage.py makemigrations
- python manage.py migrate
- pip install djangorestframework REST API 를 위한 프레임워크 DRF 설치
- pip freeze > requirement.txt 설치한 pip 목록 저장

---

# DRF (Django REST Framework)

실제 Django 에서 PUT, PATCH, DELETE 를 제대로 지원하지 않기 때문에 RESTful 하게 작업하려면 DRF가 필요하다.
DRF의 사용법과 기능을 간단히 알아보자.

## 1. 설정 (DRF 사용을 위한)

_settings.py_

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

## 2. request.data (PUT, PATCH)

Django 에서 POST로 온 폼 데이터는 request.POST.get('name') 형태로 불러 올 수 있었지만 PUT과 PATCH는 지원하지 않는다.  
DRF에서는 이를 request.data로 불러올 수 있다.

_views.py_

```python
from rest_framework.decorators import api_view

@api_view(['PATCH'])
def changePassword(request):

    pw = request.data.get('password')
    ...
```

이런식으로 POST 로 온 form 데이터를 불러올 수 있다.

---

# DRF의 기능

## 직렬화(Serializer)

- DRF의 메인 기능

API 통신의 데이터 타입은 주로 JSON 으로 한다.  
하지만 Django에서 Model 데이터는 보통 Queryset 이라는 복잡한 타입으로 되어있다.  
이런 타입을 JSON으로 바꾸기 위해선, 파이썬 네이티브 타입으로 변환해주는 직렬화(Serializer) 란 작업이 필요하다.

## 1. Serializer 생성

DRF의 Serializer는 Django의 Form과 사용방법이 거의 비슷하다.

_models.py_

```python
class Pet(models.Model):

    name = models.CharField(max_length=50)
    age = models.InteagerField()
```

위와 같은 Pet 모델을 직렬화 해보자.

---

먼저 App 폴더 안에 serializers.py라는 파일을 만들어주자.

_serializers.py_

````py
from rest_framework import serializers
class PetSerializer(serializers.Serializer):

    name = serializers.CharField(max_length=50)
    age = serializers.IntegerField()```
````

## 2. JSON 변환과정

이 Serializer를 불러와서 JSON으로 반환해주자.

_views.py_

```py
from .serializers import PetSerializer

def getPet(request):

    pet = Pet.objects.get(pk=1)
    serializer = PetSerializer( pet )
```

\_id가 1인 펫을 QuerySet 이라는 복잡한 Django type에서 Python의 Dictionary로 변환하였다.

이제 이것을 전송하기 위해 문자열로 변환해주자.

````py
from .serializers import PetSerializer
from rest_framework.renderers import JSONRenderer
from django.http import HttpResponse

def getPet(request):

    pet = Pet.objects.get(pk=1)
    serializer = PetSerializer(pet)
    data = JSONRenderer().render(serializer.data)
    return HttpResponse(data)```
````

## 3. Array 직렬화

위는 하나의 객체만 직렬화 하는 과정이다.  
이번엔 여러개의 객체 인스턴스를 배열에 담아 직렬화 해보자.

> views.py

```py
from .serializers import PetSerializer

def getPets(request):

    pets = Pet.objects.all()
    serializer = PetSerializer( pets, many=True )
```

many=True 만 같이 넣어주면 다수의 Pet 모델이 들어온 것을 인식한다.

하지만 현재의 serializer.data는 이런식의 데이터이다.

```py
[ {'name': 'pet_name'},{ ...} ]
```

json은 배열이 아니라 object 형태로 작성해야하기 때문에 이 배열을 object 에 담아 문자열로 변환시키자.

> views.py

```py
from .serializers import PetSerializer

import json

def getPets(request):

    pets = Pet.objects.all()
    serializer = PetSerializer( pets, many=True )
    data = json.dumps({'pets':serializer.data})
    return HttpResponse(data)
```

> 이번엔 내장모듈인 json을 활용해 인코딩하였다.

이제 아래와 같은 문자열이 반환될 것이다.

```py
{ 'pets' : [ {...}, ... ] }
```

# jQuery

- 기존 : 서버에 데이터 요청(페이지에 접속하는 것 자체)을 페이지 이동으로써 하게됨(POST form을 전달 함으로써)
- Ajax를 사용 : 페이지를 전환하면서 데이터를 요청하는 것이 아니라, 백그라운드에서 처리- front-end와 back-end 분리!
- bootstrap을 사용하면 이미 들어있음.
