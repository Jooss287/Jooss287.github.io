---
title: Django를 처음 설치하기
date: 2020-04-07 18:30:00 +0900
categories: [Language, Python, Django]
tags: [django]     # TAG names should always be lowercase
toc: true
---

## Django 설치
Django의 환경을 Anaconda 가상환경에 세팅해서 세팅을 확실하게 구별하고자 했다.
Anaconda Prompt에서 가상환경을 만들고 Django를 설치하였다.  
[Django의 공식 문서의 튜토리얼](https://docs.djangoproject.com/ko/3.0/intro/install/)
을 보면서 Django의 사용법을 익히고자 한다.
```shall
Conda Create -n web_server_env python=3.7
Activate web_server_env
python -m pip install Django
```
설치가 완료된 후 Django가 정상적으로 설치되었는지 확인해보자.  
```shall
python -m django --version
설치된 경우 > 3.0.5
설치되지 않은 경우 > No module named django 
```
설치된 Django의 버전이 정확하게 나오면 정상적으로 설치되었다. 이제 
프로젝트에 적용하여 Django의 기본 세팅을 진행한다.
Django project를 생성하기 위해서 프로젝트를 생성 할 폴더로 cd명령어를 사용하여 이동 한 뒤 Django 명령어를 사용하여 프로젝트를 생성한다.  
```shall
C:\Users\PC_id> cd D:\PycharmProjects
D:\PycharmProjects> django-admin startproject <Project name>
```
cd 명령어를 사용하여 C:\Users\PC_id 에서 D:\PycharmProjects로 이동한 뒤 project를 생성한다.  
실제로 프로젝트 폴더에 생성된것을 확인 할 수 있다.  
![생성된 프로젝트 확인](/assets/img/20-04-07_create_django_project.PNG)  
프로젝트 내에 Django에서 생성해주는 기본 파일들이 있고 그 파일들을 이용하여 웹 서버를 구성 할 수  있다.
기본 생성되는 파일들의 기능들을 살펴보자.  
> manage.py - Django와 상호작용 하는 커맨드라인 유틸리티 [공식문서링크](https://docs.djangoproject.com/ko/3.0/ref/django-admin/)    
> settings.py - Django 프로젝트의 환경 및 구성 저장 [공식문서링크](https://docs.djangoproject.com/ko/3.0/topics/settings/)  
> urls.py - Django project의 URL 선언 저장 [공식문서링크](https://docs.djangoproject.com/ko/3.0/topics/http/urls/)  
> wsgi.py - 현재 프로젝트를 서비스하기 위한 WSGI 호환 웹 서버 진입점 [공식문서링크](https://docs.djangoproject.com/ko/3.0/howto/deployment/wsgi/)  

#### 설문조사 앱 만들기
설문조사 어플리케이션을 만들기 위해 프로젝트 내에 어플리케이션을 생성한다.
```
python manage.py startapp <application_name>
```
어플리케이션의 view 페이지를 만들어준다. 실질적으로 url을 통해서 리턴을 받을 수 있도록 코드를 작성한다.
```
polls/views.py
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world.")
```
공식 문서에는 작성한 View를 url과 연결시키기 위해 해당 어플리케이션 폴더에서 urls.py가 존재해야한다는데.. 어디서 문제가 생긴것인지 urls.py가 존재하지 않는다.
일단은 문서와 동일하게 만들어 주기 위해서 직접 urls.py를 만들고 내부 코드를 똑같이 붙여넣었다.
```
polls/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```
urlpatterns 에서 view의 index메소드를 연결시켰다. 
url을 프로젝트의 url과 연결시키기 위해 프로젝트 urls.py에서 어플리케이션 urls.py와 연결시킨다. 
```
mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
``` 
마찬가지로 urlpatterns 내에 polls 파일 내에 있는 urls를 include함수를 호출하여 연결시켰다.   
여기까지 진행 한 후 Django에서 제공하는 테스트 서버를 사용하여 결과를 확인 할 수 있다.
```
python manage.py runserver
```
공식문서에 따르면, path 함수에는 route, view, kwargs, name가 있다고 하는데 공부는 다음에...