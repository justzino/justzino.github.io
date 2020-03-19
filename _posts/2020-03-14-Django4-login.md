---
title: "Django로 웹 개발하기 - 4. login, logout 기능 만들기"
excerpt: "Django로 login, logout 기능 만들기"

categories:
  - Django
tags:
  - Django
  - 웹개발
  - login 기능
last_modified_at: 2020-03-14T13:00:00-15:00
---

이 글은 fast_campus의 파이썬 웹 개발 수업을 듣고 정리, 추가한 내용으로 장고 내장 모듈로 login, logout 기능을 만들어 보자.

## Django 기본 인증

### django.contrib.auth.urls 모듈

- 인증 관련 URL 패턴과 View가 해당 모듈에 다음과 같이 미리 정의 되어 있다.
- 바꿔도 되지만 보통 이대로 사용

|        |   URL pattern    |   View   | Template                 |
| :----: | :--------------: | :------: | :----------------------- |
| Login  | /accounts/login  | login()  | registration/login.html  |
| Logout | /accounts/logout | logout() | registration/logout.html |

## Login 기능 만들기

settings.py 에서 django.contrib.auth 앱은 디폴트로 설치 되어 있고, 관련 기본 변수를 정의해야 한다!

1. settings.py 의 INSTALLED_APPS에 Django.contrib.auth 가 적혀 있는지 확인하자. (기본으로 적혀있음)

   ```py
   INSTALLED_APPS = [
     ...
     'django.contrib.auth',
   ]
   ```

2. urls.py 에서

   ```py
   urlpatterns = [
       ...
       path('accounts/', include('django.contrib.auth.urls'))
   ]
   ```

3. templates\registrations 폴더를 만들고 그 안에 login.html 작성

## Django Tips

1. 코드에 프로젝트 이름 넣지 않는 것을 추천
2. 배포할 때 DEBUG = False를 반드시!!!
