---
title: "Django로 웹 개발하기 - 6. Post API view 만들기"
excerpt: "Django로 Post 모델 만들기"

categories:
  - Django
tags:
  - Django
  - 웹개발
  - Post 모델
  - API
last_modified_at: 2020-03-16T13:00:00-15:00
---

이 글은 fast_campus의 파이썬 웹 개발 수업을 듣고 정리한 내용으로, Django에서 API를 사용하여 post view를 만들어 보자.

## API (Application Programming Interface)

- **응용 프로그램에서 사용할 수 있도록**, **운영 체제나 프로그래밍 언어가 제공하는 기능을 제어**할 수 있게 만든 인터페이스

## post_list 만들기

### 1. blogs\ views.py

```py
from django.shortcuts import render
from blogs.models import Post

def post_list(request):
    #posts = Post.objects.all()
    posts = Post.objects.order_by('-created_at')    # 최신순부터 게시물 정렬
    return render(request, 'blogs/posts_list.html', context={'posts':posts})
```

### 2. posts_list.html 생성

- templates/blogs/posts_list.html

### 3. url 연결

blogs/ urls.py

```py
from django.urls import path
from . import views

urlpatterns = [
    path('post/', views.posts_list, name='posts_list'),
]
```

tistory/ urls.py

```py
urlpatterns = [
  ...
  path('blogs/', include('blogs.urls')),
]
```

### 4. admin에 post_list() 추가하기

admin.py

```py
from .models import Post

admin.site.register(Post)
```
