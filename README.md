# Django

## Introduction
C/C++을 이용한 다양한 기능 및 알고리즘 구현 예제

<br><br>

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

<br><br>

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

<br><br>

## 데이터베이스 설정 & 설치

<br>

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

<br>

### Django의 DB 생성
* 데이터베이스 테이블 미리 만들기
```
$ python manage.py migrate
```

<br>

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
    * 출력결과는 PostgreSQL에서 생성됨
    * 앱의 이름과 모델의 이름(소문자)이 조합되어 자동으로 생성
    * 앱이름 polls와 모델의 이름인 question 과 choice가 합쳐짐
    * 기본 키(ID) 자동으로 추가, 외래 키 필드명에 "_id" 이름을 자동으로 추가
```
$ python manage.py sqlmigrate polls 0001
```

* 데이터베이스에 모델과 관련된 테이블을 생성
    * 적용되지 않은 마이그레이션을 모두 수집해 이를 실행
```
$ python manage.py migrate
```


* 정리
    * (models.py 에서) 모델을 변경합니다.
    * python manage.py makemigrations을 통해 이 변경사항에 대한 마이그레이션을 만드세요.
    * python manage.py migrate 명령을 통해 변경사항을 데이터베이스에 적용하세요.
    
    
<br><br>    
    
## Django API

<br>

### shell 실행

* Python 쉘을 실행
    * manaage.py는 DJANGO_SETTINGS_MODULE 환경변수
    * 환경변수는 mysite/settings.py 파일에서 사용
```
$ python manage.py shell
```

* 데이터베이스 API
```
>>> from polls.models import Choice, Question  # Import the model classes we just wrote.

# No questions are in the system yet.
>>> Question.objects.all()
<QuerySet []>

# Create a new Question.
# Support for time zones is enabled in the default settings file, so
# Django expects a datetime with tzinfo for pub_date. Use timezone.now()
# instead of datetime.datetime.now() and it will do the right thing.
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())

# Save the object into the database. You have to call save() explicitly.
>>> q.save()

# Now it has an ID.
>>> q.id
1

# Access model field values via Python attributes.
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2012, 2, 26, 13, 0, 0, 775217, tzinfo=<UTC>)

# Change values by changing the attributes, then calling save().
>>> q.question_text = "What's up?"
>>> q.save()

# objects.all() displays all the questions in the database.
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```

* Question모델 수정, __str__() 메소드를 Question과 Choice에 추가
```
$ vi polls/models.py
```
```
from django.db import models

class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text

class Choice(models.Model):
    # ...
    def __str__(self):
        return self.choice_text
```

* Question모델 수정, __str__() 메소드에 커스텀 메소드 추가
    * timezone은 시간대 관련 유틸 참고
```
$ vi polls/models.py
```
```
import datetime
from django.db import models
from django.utils import timezone

class Question(models.Model):
    # ...
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
```

<br>

### shell 실행2

* python manage.py shell 변경 사항

```
>>> from polls.models import Choice, Question

# Make sure our __str__() addition worked.
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>

# Django provides a rich database lookup API that's entirely driven by
# keyword arguments.
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>

# Get the question that was published this year.
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>

# Request an ID that doesn't exist, this will raise an exception.
>>> Question.objects.get(id=2)
Traceback (most recent call last):
    ...
DoesNotExist: Question matching query does not exist.

# Lookup by a primary key is the most common case, so Django provides a
# shortcut for primary-key exact lookups.
# The following is identical to Question.objects.get(id=1).
>>> Question.objects.get(pk=1)
<Question: What's up?>

# Make sure our custom method worked.
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
True

# Give the Question a couple of Choices. The create call constructs a new
# Choice object, does the INSERT statement, adds the choice to the set
# of available choices and returns the new Choice object. Django creates
# a set to hold the "other side" of a ForeignKey relation
# (e.g. a question's choice) which can be accessed via the API.
>>> q = Question.objects.get(pk=1)

# Display any choices from the related object set -- none so far.
>>> q.choice_set.all()
<QuerySet []>

# Create three choices.
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# Choice objects have API access to their related Question objects.
>>> c.question
<Question: What's up?>

# And vice versa: Question objects get access to Choice objects.
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> q.choice_set.count()
3

# The API automatically follows relationships as far as you need.
# Use double underscores to separate relationships.
# This works as many levels deep as you want; there's no limit.
# Find all Choices for any question whose pub_date is in this year
# (reusing the 'current_year' variable we created above).
>>> Choice.objects.filter(question__pub_date__year=current_year)
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>

# Let's delete one of the choices. Use delete() for that.
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
```

<br><br>

## Django 관리자

<br>

### 관리자 관리 

* 관리자 생성
```
$ python manage.py createsuperuser
```

* 내용 입력(ID, E-mail, PW)
```
Username: admin
Email address: admin@example.com
```

<br>

### 관리자 사이트 변경

* 개발 서버 시작
    * http://127.0.0.1:8000/admin/ 으로 접근 가능
```
$ python manage.py runserver
```

* 인덱스 페이지 수정
```
$ vi polls/admin.py
```
```
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

<br><br>

## 뷰(view)

* poll 어플리케이션에 4개의 view를 생성할 계획임
    * 질문 "색인" 페이지	#최근의 질문들을 표시
    * 질문 "세부" 페이지	#질문 내용과, 투표할 수 있는 서식을 표시
    * 질문 "결과" 페이지	#특정 질문에 대한 결과를 표시
    * 투표 기능	#특정 질문에 대해 특정 선택을 할 수 있는 투표 기능을 제공

* URL로 부터 뷰를 얻기위해 'URLconfs'를 사용함
    * URLconf는 URL 패턴을 뷰에 연결



<br>

### 뷰 추가

* polls/views.py에 뷰를 추가
```
$ vi polls/views.py
```
```
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

* polls.urls 모듈에 path() 호출 추가해 연결
```
$ vi polls/urls.py
```
```
from django.urls import path

from . import views

urlpatterns = [
    # ex: /polls/
    path('', views.index, name='index'),
    # ex: /polls/5/
    path('<int:question_id>/', views.detail, name='detail'),
    # ex: /polls/5/results/
    path('<int:question_id>/results/', views.results, name='results'),
    # ex: /polls/5/vote/
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```


<br>

### 뷰 동작
* 요청된 페이지의 내용이 담긴 HttpResponse 객체를 반환, 혹은  Http404 같은 예외를 발생
* 당신이 작성한 뷰는 데이터베이스의 레코드를 읽기
* Django나 Python에서 서드파티로 제공되는 템플릿 시스템을 사용
* PDF 생성, XML 출력, ZIP 파일 생성, python 라이브러리 사용

<br>

* Django에 필요한 것은 HttpResponse 객체 혹은 예외 적용
    * Django 자체 데이터베이스 API를 사용
    * 새로운 index() 뷰 하나를 호출 때
    * 시스템에 저장된 5개의 투표 질문을 콤마로 분리하고 발행일에 따라 출력

```
$ vi polls/views.py
```
```
from django.http import HttpResponse
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

# Leave the rest of the views (detail, results, vote) unchanged
```

### TEMPLATES 설정
* Django가 어떻게 템플릿을 불러오고 렌더링 할 것인가 기술
* 먼저 기본 templates 디렉토리 생성
```
$ mkdir templates
```

* 기본 설정 파일 옵션을 수정 및 index.html 파일 생성
```
$ vi polls/templates/polls/index.html
```
```
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

* 템플릿 이용해, polls/views.py에 index 뷰를 업데이트
```
$ vi polls/views.py
```
```
from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```

<br>

### render()
* 템플릿에 contex 채워 결과를 HttpResponse 객체와 돌려주는 방법
* index() 뷰를 단축 기능으로 작성
```
$ vi polls/views.py
```
```
from django.shortcuts import render
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

<br>

### 404에러 처리
* 질문의 상세 뷰에 오류 일으키기
```
$ vi polls/views.py
```
```
from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

* 뷰는 요청된 질문의 ID가 없는 경우 Http404 예외 발생할 파일 작성
```
$ vi polls/templates/polls/detail.html
```
```
{{ question }}
```

<br>

### get_object_or_404()
* 객체가 존재하지 않을 때, get() 사용해 Http404 예외 발생시키기
    * detail() 뷰를 단축 기능으로 작성
    * get_object_or_404() 함수는 Django 모델을 첫번째 인자로 받음
    * 몇개의 키워드 인수를 모델 관리자의 get() 함수에 넘김
    * 만약 객체가 존재하지 않을 경우, Http404 예외가 발생합니다.

```
$ vi polls/views.py
```
```
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

    * get_object_or_404() 함수처럼 동작하는 get_list_or_404() 함수 있음
    * get() 대신 filter() 를 쓴다는 것이 다름
    * 리스트가 비어있을 경우, Http404 예외를 발생시킴


<br><br>

## 템플릿 시스템 사용

<br>

### 투표 어플리케이션의 detail() 뷰 수정
* context 변수 question이 주어졌을때, polls/detail.html이라는 템플릿이 어떤지 보기

```
$ vi polls/templates/polls/detail.html
```
```
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```

* 템플릿 시스템은 변수의 속성에 접근하기 위해 점-탐색(dot-lookup) 문법을 사용
* {{ question.question_text }} 보면, Django는 먼저 question 객체에 대해 사전형으로 탐색
    * 탐색에 실패하게 되면 속성값으로 탐색
    * 속성 탐색에도 실패한다면 리스트의 인덱스 탐색을 시도
* {% for %} 반복 구문에서 메소드 호출
    * question.choice_set.all은 Python에서 question.choice_set.all() 코드로 해석
    * 반환된 Choice 객체의 반복자는 {% for %}에서 사용


### 템플릿에서 하드코딩된 URL 제거
* polls/index.html 템플릿 변경 전
```
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```

* polls/index.html 템플릿 변경 후
    * polls.urls 모듈의 path() 함수에서 인수의 이름을 정의
    * {% url %} template 태그를 사용하여 url 설정에 정의된 특정한 URL 경로들의 의존성을 제거
    * 이것이 polls.urls 모듈에 서술된 URL 의 정의를 탐색하는 동작
```
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```

* 다음과 같이 'detail' 이라는 이름의 URL 이 어떻게 정의되어 있는지 확인 가능
```
...
# the 'name' value as called by the {% url %} template tag
path('<int:question_id>/', views.detail, name='detail'),
...
```
* 상세 뷰의 URL을 polls/specifics/12/로 바꾸고 싶다면면, 템플릿에서 바꾸는 것이 아니라 polls/urls.py에서 바꿔야 함
```
...
# added the word 'specifics'
path('specifics/<int:question_id>/', views.detail, name='detail'),
...
```

### URL 이름공간 결정
* Django는 이 앱들의 URL을 구별하는 방법
    * polls 앱은 detail이라는 뷰를 가지고 있고, 동일한 프로젝트에 블로그를 위한 앱이 있을 수도 있음

* {% url %} 템플릿태그를 사용할 때, 어떤 앱의 뷰에서 URL을 생성할지 알 수 있을까요?
    * 정답은 URLconf에 이름공간(namespace)을 추가하는 것임

* polls/urls.py 파일에 app_name을 추가하여 어플리케이션의 이름공간을 설정할 수 있음
```
$ vi polls/urls.py
```
```
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```
* polls/index.html 템플릿의 기존 내용 수정
    * 이름공간으로 나뉘진 상세 뷰를 가리키도록 변경
```
$ vi polls/templates/polls/index.html
```
```
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```







<br><br><br>

## Ref.

* https://docs.djangoproject.com/ko/3.0/
