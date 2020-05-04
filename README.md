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