# [Django Custom User](https://learndjango.com/tutorials/django-custom-user-model)

## [Django Login and Logout Tutorial](https://learndjango.com/tutorials/django-login-and-logout-tutorial)
## AbstractUser vs AbstractBaseUser
There are two modern ways to create a custom user model in Django: AbstractUser and AbstractBaseUser. In both cases we can subclass them to extend existing functionality however AbstractBaseUser requires much, much more work. Seriously, don't mess with it unless you really know what you're doing. And if you did, you wouldn't be reading this tutorial, would you?

So we'll use AbstractUser which actually subclasses AbstractBaseUser but provides more default configuration.

## Custom User Model
Creating our initial custom user model requires four steps:

update settings.py
create a new CustomUser model
create new UserCreation and UserChangeForm
update the admin
In settings.py we'll add the users app and use the AUTH_USER_MODEL config to tell Django to use our new custom user model in place of the built-in User model. We'll call our custom user model CustomUser.

Within INSTALLED_APPS add users.apps.UsersConfig at the bottom. Then at the bottom of the entire file, add the AUTH_USER_MODEL config.

# config/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'users.apps.UsersConfig', # new
]
...
AUTH_USER_MODEL = 'users.CustomUser' # new

Now update models.py with a new User model which we'll call CustomUser.

# users/models.py
from django.contrib.auth.models import AbstractUser
from django.db import models

class CustomUser(AbstractUser):
    pass
    # add additional fields in here

    def __str__(self):
        return self.username

(users) $ touch users/forms.py
We'll update it with the following code to largely subclass the existing forms.

### users/forms.py
from django import forms
from django.contrib.auth.forms import UserCreationForm, UserChangeForm
from .models import CustomUser

class CustomUserCreationForm(UserCreationForm):

    class Meta:
        model = CustomUser
        fields = ('username', 'email')

class CustomUserChangeForm(UserChangeForm):

    class Meta:
        model = CustomUser
        fields = ('username', 'email')

Finally we update admin.py since the Admin is highly coupled to the default User model.

### users/admin.py
from django.contrib import admin
from django.contrib.auth import get_user_model
from django.contrib.auth.admin import UserAdmin

from .forms import CustomUserCreationForm, CustomUserChangeForm
from .models import CustomUser

class CustomUserAdmin(UserAdmin):
    add_form = CustomUserCreationForm
    form = CustomUserChangeForm
    model = CustomUser
    list_display = ['email', 'username',]

admin.site.register(CustomUser, CustomUserAdmin)
And we're done! We can now run makemigrations and migrate for the first time to create a new database that uses the custom user model.

(users) $ python manage.py makemigrations users
(users) $ python manage.py migrate

### config/settings.py
LOGIN_REDIRECT_URL = 'home'
LOGOUT_REDIRECT_URL = 'home'

(users) $ mkdir templates
(users) $ mkdir templates/registration
Then create four templates:

(users) $ touch templates/registration/login.html
(users) $ touch templates/base.html
(users) $ touch templates/home.html
(users) $ touch templates/signup.html

<!-- templates/home.html -->
{% extends 'base.html' %}

{% block title %}Home{% endblock %}

{% block content %}
{% if user.is_authenticated %}
  Hi {{ user.username }}!
  \<p><a href="{% url 'logout' %}">logout</a></p> \
{% else %}
  \<p>You are not logged in</p> \
  \<a href="{% url 'login' %}">login</a> \
  \<a href="{% url 'signup' %}">signup</a> \
{% endif %}
{% endblock %}

### config/urls.py
from django.contrib import admin
from django.urls import path, include
from django.views.generic.base import TemplateView

urlpatterns = [
    path('', TemplateView.as_view(template_name='home.html'), name='home'),
    path('admin/', admin.site.urls),
    path('users/', include('users.urls')),
    path('users/', include('django.contrib.auth.urls')),
]

## users/urls.py
from django.urls import path
from .views import SignUpView

urlpatterns = [
    path('signup/', SignUpView.as_view(), name='signup'),
]

## users/views.py
from django.urls import reverse_lazy
from django.views.generic.edit import CreateView

from .forms import CustomUserCreationForm

class SignUpView(CreateView):
    form_class = CustomUserCreationForm
    success_url = reverse_lazy('login')
    template_name = 'signup.html'