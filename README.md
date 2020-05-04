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

# Para verifivcar as migraçoes 
```
(appdjango) appdjango $ mng showmigrations
admin
 [ ] 0001_initial
 [ ] 0002_logentry_remove_auto_add
 [ ] 0003_logentry_add_action_flag_choices
auth
 [ ] 0001_initial
 [ ] 0002_alter_permission_name_max_length
 [ ] 0003_alter_user_email_max_length
 [ ] 0004_alter_user_username_opts
 [ ] 0005_alter_user_last_login_null
 [ ] 0006_require_contenttypes_0002
 [ ] 0007_alter_validators_add_error_messages
 [ ] 0008_alter_user_username_max_length
 [ ] 0009_alter_user_last_name_max_length
 [ ] 0010_alter_group_name_max_length
 [ ] 0011_update_proxy_permissions
contenttypes
 [ ] 0001_initial
 [ ] 0002_remove_content_type_name
sessions
 [ ] 0001_initial
```

# Paera criar as migraçoes 
```
(appdjango) appdjango $ mng migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying sessions.0001_initial... OK
```

# Inserir no file polls/models.ps
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

# Inserir n setting 
```
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
     ...
]  
```

# Rodar o comando makemigartions
```
(appdjango) appdjango $ mng makemigrations polls
Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice

```

# Executando o comando 
```
(appdjango) appdjango $ mng sqlmigrate polls 0001
BEGIN;
--
-- Create model Question
--
CREATE TABLE "polls_question" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "question_text" varchar(200) NOT NULL, \
"pub_date" datetime NOT NULL);
--
-- Create model Choice
--
CREATE TABLE "polls_choice" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "choice_text" varchar(200) NOT NULL, \
"votes" integer NOT NULL, "question_id" integer NOT NULL REFERENCES "polls_question\
" ("id") DEFERRABLE INITIALLY DEFERRED);
CREATE INDEX "polls_choice_question_id_c5b4b260" ON "polls_choice" ("question_id");
COMMIT;
```
# Executando o comando migrate
```
(appdjango) appdjango $ mng migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0001_initial... OK
```

# no shell, explore a API de banco de dados:
```
(appdjango) appdjango $ mng shell
Python 3.8.0 (default, Feb  3 2020, 16:24:25) 
[GCC 7.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from polls.models import Choice, Question
>>> Question.objects.all()
<QuerySet []>
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())
>>> q.save()
>>> q.id
1
>>> q.question_text
"What's new?"
>>> q.pub_date
datetime.datetime(2020, 5, 4, 22, 23, 30, 50256, tzinfo=<UTC>)
>>> q.question_text = "What's up?"
>>> q.save
<bound method Model.save of <Question: Question object (1)>>
>>> q.save()
>>> Question.objects.a
Question.objects.aggregate(    Question.objects.all(          Question.objects.annotate(     Question.objects.auto_created  
>>> Question.objects.all()
<QuerySet [<Question: Question object (1)>]>
```