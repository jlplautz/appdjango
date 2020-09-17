# appdjango

# Aplicação baseada na documentação Django

[![Updates](https://pyup.io/repos/github/jlplautz/appdjango/shield.svg)](https://pyup.io/repos/github/jlplautz/appdjango/)
[![Python 3](https://pyup.io/repos/github/jlplautz/appdjango/python-3-shield.svg)](https://pyup.io/repos/github/jlplautz/appdjango/)
![Python package](https://github.com/jlplautz/appdjango/workflows/Python%20package/badge.svg)


```
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

# In the urls.py file from project was inserted the follow lines
```
from django.urls import path, include

path('polls/', include('polls.urls')),
```
# was executed makemigrations polls
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
# Python interactive shell by running python manage.py shell
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

# Creating an admin user¶
- python manage.py createsuperuser

# Make the poll app modifiable in the admin
```
from django.contrib import admin
from .models import Question

admin.site.register(Question)
```

# Escrevendo mais views


# Escrevendo nosso primeiro teste
```
Felizmente, há um pequeno bug na aplicação polls para corrigirmos imediatamente: 
O método Question.was_published_recently() retorna True se a Question foi publicada dentro do último dia 
(o que está correto), mas também se o campo pub_date de Question está no futuro (o que certamente não é o caso).

$ python manage.py shell
>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
>>> # create a Question instance with pub_date 30 days in the future
>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))
>>> # was it published recently?
>>> future_question.was_published_recently()
True
```

# Executando o teste
$ python manage.py test polls
```
(appdjango) appdjango $ python manage.py test
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionModelTests)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/plautz/PycharmProjects/appdjango/polls/tests.py", 
         line 18, in test_was_published_recently_with_future_question
    self.assertIs(future_question.was_published_recently(), False)
AssertionError: True is not False

----------------------------------------------------------------------
Ran 1 test in 0.002s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

# Corrigindo o erro - polls/models.py
```
def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

# Esccrever um teste para uma VIEW

```
(appdjango) appdjango $ python manage.py shell
Python 3.8.0 (default, Feb  3 2020, 16:24:25) 
[GCC 7.4.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
>>> from django.test import Client
>>> client = Client()
>>> response = client.get('/')
Not Found: /
>>> response.status_code
404
>>> from django.urls import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
b'<!DOCTYPE html>\n
<html lang="en">\n
<head>\n
    <meta charset="UTF-8">\n
    <title>TEMPLATE-INDEX</title>\n</head>\n
<body>\n
    \n
        <ul>\n
            \n
                <li><a href="/polls/5/">Is there a new question?</a></li>\n
            \n                <li><a href="/polls/1/">What&#x27;s up?</a></li>\n
            \n
        </ul>\n
    \n
\n
</body>\n
</html>'
>>> response.context['latest_question_list']
<QuerySet [<Question: Is there a new question?>, <Question: What's up?>]>
>>> 
```

# Melhorando a nossa VIEW
```
  - class-based views, baseado no ListView
    polls/views.py
    class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """
        Return the last five published questions.
        """
        return Question.objects.order_by('-pub_date')[:5]
    
    OBS: o metodp get_queryset() para comparar a data com o timezone.com()
```

# Criado os testes com a Views
  - class QuestionIndexViewTests(TestCase):
     - def test_no_questions(self):
     - def test_past_question(self):
     - def test_future_question(self):
     - def test_future_question_and_past_question(self):
     - def test_two_past_questions(self):
  
  Vejamos algumas delas mais de perto.

O primeiro é uma função de atalho para questão, create_question, para retirar algumas repetições do processo 
de criar questões.

**test_no_questions** não cria nenhuma questão, mas verifica a mensagem:”Nenhuma enquete disponível.” e verifica 
que latest_question_list está vazia. Note que a classe django.test.TestCase fornece alguns métodos adicionais 
de assertividade. Nestes exemplos nós usamos assertContains() e assertQuerysetEqual().

Em **test_past_question**, criamos uma questão e verificamos se esta aparece na lista.
Em **test_future_question**, nós criamos uma pub_date no futuro. O banco de dados é resetado para cada método de 
teste, quer dizer que a primeira questão não está mais lá, então novamente o index não deve ter nenhuma questão.

E assim por diante. De fato, estamos usando os testes para contar uma história de entradas no site de administração 
e da experiencia do usuário, e verificando se para cada estado e para cada nova alteração no estado do sistema, 
o resultado esperado é publicado.


# Testando do DetailView

- Inserido no polls/views.py
  ```
      def get_queryset(self):
        """
        Excludes any questions that aren't published yet.
        """
        return Question.objects.filter(pub_date__lte=timezone.now())

  ```
  
  -   Inserido os testes polls/tests.py
    class QuestionDetailViewTests(TestCase):
         def test_future_question(self):
         def test_past_question(self):
          
# Personalizando aparencia da sua aplicação
- Criar um diretorio static -> polls/static
- Criar um diretorio static -> polls/static/polls/style.css
  - pools/static/pools/style.css
    li a {
        color: green;
    }
- inserir no file polls/templates/polls/index.html
  ```
  {% load static %}
  <link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
  ```
- Criar um diretorio static -> polls/static/polls/images/
- inserir no file polls/static/polls/style.css
  ```
  body {
    background: white url("images/background.gif") no-repeat;
  }
  ``` 