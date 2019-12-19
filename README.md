# Django

## Introduction
C/C++을 이용한 다양한 기능 및 알고리즘 구현 예제

<br>

## Install

* 장고 설치
```
$ sudo apt-get install pytyon3-pip
$ pip3 install Django
$ pip3 install Django --upgrade
$ python -m django --version
```

* 장고 프로젝트 생성
```
$ django-admin startproject mysite
```

* 장고 프로젝트 생성된 내용.
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

* 장고 프로젝트 동작
```
$ python manage.py runserver
```

* 장고 프로젝트 포트 변경
```
$ python manage.py runserver 0:8000
```

<br>

## 앱 만들기
 
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

## 데이터베이스 설정 & 설치 <br>

### Django의 DB 세팅 확인

* Django의 기본은 SQLite을 사용하도록 구성됨, 추가 수정 필요 없음
```
$ vi mysite/settings.py
```

* DATABASES 'default' 항복값 수정, db 변경이나 확인
```
ENGINE -- 'django.db.backends.sqlite3', 
'django.db.backends.postgresql', 
'django.db.backends.mysql',
'django.db.backends.oracle'
```

* 'NAME' 데이터베이스의 이름
```
os.path.join(BASE_DIR, 'db.sqlite3')
```

* 시간대에 맞춰 'TIME_ZONE' 값 설정

* 'INSTALLED_APPS'
```
django.contrib.admin	# 관리용 사이트
django.contrib.auth	# 인증 시스템
django.contrib.contenttypes	# 컨텐츠 타입을 위한 프레임워크
django.contrib.sessions	# 세션 프레임워크
django.contrib.messages	# 메세징 프레임워크
django.contrib.staticfiles	# 정적 파일을 관리하는 프레임워크
```

### Django의 DB 생성

* 데이터베이스 테이블 미리 만들기
```
$ python manage.py migrate
```

### 모델(model)
* 모델은 메타데이터를 가진 데이터베이스 구조(layout)
<br>

* 모델 수정
```
vi polls/models.py
```
```
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```
* Field 클래스	# 데이터베이스 각 필드
    * question_text 또는 pub_date는 machine-friendly format
    * Question.pub_date은 human-readable
    * CharField 	# 문자 필드
        * max_length 은 필수
    * DateTimeField	# 날짜와 시간(datetime) 필드
    * ForeignKey 를 사용한 관계
        * 예제에서는 각각의 Choice 가 하나의 Question 에 관계됨
        * Django 는 다-대-일(many-to-one), 다-대-다(many-to-many), 일-대-일(one-to-one) 과 같은 모든 일반 데이터베이스의 관계들를 지원합니다.

* 모델의 활성화
   * 데이터베이스 스키마 생성(CREATE TABLE문)
   * Question과 Choice의 객체 접근을 위한 python  데이터베이스 API 생성

* mysite/settings.py 파일을 편집하여 INSTALLED_APPS에서 설정
    * polls앱 추가
```
vi mysite/settings.py
```
```
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

* makemigrations 실행함으로, 모델 변경 사실을 저장함
```
$ python manage.py makemigrations polls
```

* migrate명령은 자동 데이터베이스 스키마 관리해줌
```
$ python manage.py sqlmigrate polls 0001
```
    * 출력결과는 PostgreSQL에서 생성됨
    * 앱의 이름과 모델의 이름(소문자)이 조합되어 자동으로 생성
    * 앱이름 polls와 모델의 이름인 question 과 choice가 합쳐짐
    * 기본 키(ID) 자동으로 추가, 외래 키 필드명에 "_id" 이름을 자동으로 추가

* 데이터베이스에 모델과 관련된 테이블을 생성
```
$ python manage.py migrate
```
    * 적용되지 않은 마이그레이션을 모두 수집해 이를 실행

* 정리
    * (models.py 에서) 모델을 변경합니다.
    * python manage.py makemigrations을 통해 이 변경사항에 대한 마이그레이션을 만드세요.
    * python manage.py migrate 명령을 통해 변경사항을 데이터베이스에 적용하세요.
