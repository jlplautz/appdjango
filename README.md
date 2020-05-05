# appdjango

Aplicação baseada na documentação Django

![Python package](https://github.com/jlplautz/appdjango/workflows/Python%20package/badge.svg?event=pull_request)
![Python package](https://github.com/jlplautz/appdjango/workflows/Python%20package/badge.svg?event=deployment_status)

```python
    Iniciando app Django
appdjango $ tree
.
├── LICENSE
└── README.md
```

# Comandos executados
- appdjango $ pipenv shell
- (appdjango) appdjango $ pipenv install django
- (appdjango) appdjango $ pipenv install --dev flake8
- (appdjango) appdjango $ pip freeze > requirements.txt

# Criados os arquivos 
- .flake8
``` 
[flake8]
max-line-length = 120
exclude = .venv
```
- .pyup.yml
``` 
requirements:
  - Pipfile
  - Pipfile.lock
```

# Criado github action file
```
# This workflow will install Python dependencies, run tests and lint with a variety of Python versions
# For more information see: https://help.github.com/actions/language-and-framework-guides/using-python-with-github-actions

name: Python package

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  build:

    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8]

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v1
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install flake8 pytest
        if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
    - name: Lint with flake8
      run: |
        # stop the build if there are Python syntax errors or undefined names
        flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
        # exit-zero treats all errors as warnings. The GitHub editor is 127 chars wide
        flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
#    - name: Test with pytest
#      run: |
#        pytest
```

# Criando o projeto e uma aplicação

- (appdjango) appdjango $ django-admin startproject mysite .
- (appdjango) appdjango $ mng startapp polls

(appdjango) appdjango $ tree
```
(appdjango) appdjango $ tree
.
├── db.sqlite3
├── LICENSE
├── manage.py
├── mysite
│   ├── asgi.py
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── Pipfile
├── Pipfile.lock
├── polls
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── README.md
└── requirements.txt

```

# Create your views here.
```
from django.http import HttpResponse

def index(request):
    return HttpResponse("<h1>Hello, world. You're at the polls index.</h1>")
```

# Create your urls.py in the app polls
```
from django.urls import path
from . import views
urlpatterns = [
    path('', views.index, name='index'),
]
```

# In tje urls.py file from projet was inseeted the follow lines 
```
from django.urls import path, include

path('polls/', include('polls.urls')),
```
# foi executado makemigrations polls
- python manage.py makemigrations polls
```
(appdjango) appdjango $ python manage.py makemigrations polls
Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice
```

# foi executado sqlmigrate polls 0001
- python manage.py sqlmigrate polls 0001
```
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "question_text" varchar(200) NOT NULL, "pub_date" datetime NOT NULL);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "choice_text" varchar(200) NOT NULL, "votes" integer NOT NULL, "question_id" integer NOT NULL REFERENCES "polls_question" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
COMMIT;
```
# Python interactive shell by running python manage.py shel
```
(appdjango) appdjango $ python manage.py shell
Python 3.8.0 (default, Feb  3 2020, 16:24:25) 
[GCC 7.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from polls.models import Choice, Question
>>> Question.objects.all()
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(id=1)
<QuerySet [<Question: What's up?>]>
>>> Question.objects.filter(question_text__startswith='What')
<QuerySet [<Question: What's up?>]>
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
<Question: What's up?>
>>> Question.objects.get(id=2)
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/home/plautz/PycharmProjects/appdjango/.venv/lib/python3.8/site-packages/django/db/models/manager.py", line 82, in manager_method
    return getattr(self.get_queryset(), name)(*args, **kwargs)
  File "/home/plautz/PycharmProjects/appdjango/.venv/lib/python3.8/site-packages/django/db/models/query.py", line 415, in get
    raise self.model.DoesNotExist(
polls.models.Question.DoesNotExist: Question matching query does not exist.
>>> Question.objects.get(pk=1)
<Question: What's up?>
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
Traceback (most recent call last):
  File "<console>", line 1, in <module>
AttributeError: 'Question' object has no attribute 'was_published_recently'
>>> q = Question.objects.get(pk=1)
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>]>
>>> q.choice_set.create(choice_text='Not much', votes=0)
<Choice: Not much>
>>> q.choice_set.create(choice_text='The sky', votes=0)
<Choice: The sky>
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)
>>> c.question
<Question: What's up?>
>>> q.choice_set.all()
<QuerySet [<Choice: Not much>, <Choice: The sky>, <Choice: Not much>, <Choice: The sky>, <Choice: Just hacking again>]>
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
(1, {'polls.Choice': 1})
```