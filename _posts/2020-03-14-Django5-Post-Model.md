---
title: "Django로 웹 개발하기 - 5. Post 모델 만들기"
excerpt: "Django로 Post 모델 만들기"

categories:
  - Django
tags:
  - Django
  - 웹개발
  - Post 모델
last_modified_at: 2020-03-15T15:00:00-17:00
---

이 글은 fast_campus의 파이썬 웹 개발 수업을 듣고 정리한 내용으로, Django에서 Post 모델을 만들어 보자.

## DB 모델링 설계하기

1. 어떤 필드가 필요한지 생각한다.
2. 생각 정리가 되면 적어본다.
3. 각 필드가 어떤 타입이 되어야하는지 생각 혹은 논의한다.

---

## Post 모델 만들기

### 1. 앱 만들기(blogs, helpers)

- 글 쓰기, 댓글, 공감 버튼 등등의 기능

`python manage.py startapp blogs`  
`python manage.py startapp helpers`

앱을 만들었으면 settings.py 에 만든 앱 추가

```py
INSTALLED_APPS += [
    ...,
    'helpers',
    'blogs',
]
```

### 2. helpers 모델 만들기

helpers / models.py

```py
from django.db import models

# post model등의 Base가 될 BaseModel
class BaseModel(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)
    modified_at = models.DateTimeField(auto_now=True)

class Meta:
    abstract = True       # Db에 table로 만들지 않도록! '이 model은 추상 클래스야'
```

### 3. blogs 모델 만들기

blogs / models.py

```py
from django.db import models

from helpers.models import BaseModel
from users.models import User

class Post(BaseModel):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    title = models.CharField(max_length=255, blank=False)
    content = models.TextField()
    image=models.ImageField(blank=True, null=True)
```

### 4. migrations 생성

- `python manage.py makemigrations helpers`을 해보면

```
ERRORS:
blogs.Post.image: (fields.E210) Cannot use ImageField because Pillow is not installed.
        HINT: Get Pillow at https://pypi.org/project/Pillow/ or run command "python -m pip install Pillow".
```

위와 같은 error 발생

- 이유 : ImageField를 사용하기 위해 필요한 Pillow library가 설치되어 있지 않음
- 해결 : `pip install Pillow`

- `python manage.py makemigrations helpers`
- `python manage.py migrate`
