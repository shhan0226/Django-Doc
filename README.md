# Django

## Introduction
C/C++을 이용한 다양한 기능 및 알고리즘 구현 예제

<br>

## Install

### 장고 설치
```
$ sudo apt-get install pytyon3-pip
$ pip3 install Django
$ pip3 install Django --upgrade
$ python -m django --version
```

### 장고 프로젝트 생성
```
$ django-admin startproject mysite
```

### 장고 프로젝트 생성된 내용.
```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py
```

### 장고 프로젝트 동작
```
$ python manage.py runserver
```

### 장고 프로젝트 포트 변경
```
$ python manage.py runserver 0:8000
```

<br>

## Contents


### 앱 만들기 <br>
 
* polls 디렉토리 생성 (투표 앱의 집이 만들어짐)
```
$ python manage.py startapp polls
```
```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

* 뷰화면 생성
```
$ vi polls/views.py 
```
```
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

* URLconf 생성, URL 확인 디렉토리 변경됨
```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    urls.py
    views.py
```

* pools/urls.py 설정

```
$ vi pools/urls.py 
```
```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

* URLconf에서 polls.urls 모듈 최상으로 보기
   * include() 함수는 다른 URLconf들을 참조할 수 있도록함
   
```
$ mysite/urls.py
```

```
from django.contrib import admin
from django.urls import include, path
urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

* 실행
   * http://localhost:8000/polls 또는 127.0.0.1:8000/polls
