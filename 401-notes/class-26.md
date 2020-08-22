# Django
## [Link](https://docs.djangoproject.com/en/3.1/intro/tutorial01/)
## Creating a project
- django-admin startproject mysite
mysite/ 
    - manage.py \
    mysite/ 
        - __init__.py \
        - settings.py \
        - urls.py \
        - asgi.py \
        - wsgi.py

* The outer mysite/ root directory is a container for your project. Its name doesn’t matter to Django; you can rename it to anything you like.
* manage.py: A command-line utility that lets you interact with this Django project in various ways. You can read all the details about manage.py in django-admin and manage.py.
* The inner mysite/ directory is the actual Python package for your project. Its name is the Python package name you’ll need to use to import anything inside it (e.g. mysite.urls).
* mysite/__init__.py: An empty file that tells Python that this directory should be considered a Python package. If you’re a Python beginner, read more about packages in the official Python docs.
* mysite/settings.py: Settings/configuration for this Django project. Django settings will tell you all about how settings work.
* mysite/urls.py: The URL declarations for this Django project; a “table of contents” of your Django-powered site. You can read more about URLs in URL dispatcher.
* mysite/asgi.py: An entry-point for ASGI-compatible web servers to serve your project. See How to deploy with ASGI for more details.
* mysite/wsgi.py: An entry-point for WSGI-compatible web servers to serve your project. See How to deploy with WSGI for more details.

## The development server
python manage.py runserver

## Creating the Polls app
python manage.py startapp polls

That’ll create a directory polls, which is laid out like this:

- polls/
    * __init__.py
    * admin.py
    * apps.py
    * migrations/
        * __init__.py
    * models.py
    * tests.py
    * views.py

## Write your first view¶
Let’s write the first view. Open the file polls/views.py and put the following Python code in it:

polls/views.py¶
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")

To create a URLconf in the polls directory, create a file called urls.py. 

In the polls/urls.py file include the following code:

polls/urls.py¶
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]

mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]

python manage.py runserver

**python manage.py migrate** \
The migrate command looks at the INSTALLED_APPS setting and creates any necessary database tables according to the database settings in your mysite/settings.py file and the database migrations shipped with the app (we’ll cover those later). You’ll see a message for each migration it applies. If you’re interested, run the command-line client for your database and type \dt (PostgreSQL), SHOW TABLES; (MariaDB, MySQL), .schema (SQLite), or SELECT TABLE_NAME FROM USER_TABLES; (Oracle) to display the tables Django created.

polls/models.py¶
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

Edit the mysite/settings.py file and add that dotted path to the INSTALLED_APPS setting. It’ll look like this:

mysite/settings.py¶
INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

Now Django knows to include the polls app. Let’s run another command:

/ 
$ python manage.py makemigrations polls
You should see something similar to the following:

Migrations for 'polls':
  polls/migrations/0001_initial.py
    - Create model Question
    - Create model Choice