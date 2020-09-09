# Django REST Framework & Docker
* [Docker](https://wsvincent.com/beginners-guide-to-docker/)
* [Django REST 1](https://djangoforapis.com/library-website-and-api/)
* [Django REST 2](https://learndjango.com/tutorials/official-django-rest-framework-tutorial-beginners)
## Images and Containers
Images and containers are the two fundamental concepts to grasp when you start with Docker. An image is a snapshot in time of what a project contains. A container is a running instance of the image.

When we ran hello-world we used an official Docker image. We did not have to create the image ourself. But typically we will create custom images and we do so using a Dockerfile. We also will use docker-compose.yml files to run the containers.

A baking analogy we can use here is as follows:

A Dockerfile is the recipe for a cake
An image is a snapshot of the recipe at a given time
A docker-compose.yml says how to make the cake
And the container is the actual, baked cake
There are a large number of official images available on Docker Hub for common software like different flavors of Linux, programming languages, and so on. For example, the Hello World image is there.

Images
Ok, let’s create our own image for something more “real.” How about the Python programming language? We can create an image and container just for Python and later add on to it.

First create a local directory on your computer for our code. I’ve chosen to make a code folder on my Desktop (I’m using an Apple computer) and then a python3.7 folder within that.

$ cd ~/Desktop
$ mkdir code && cd code
$ mkdir python3.7 && cd python3.7
Now we need to define the image with a Dockerfile. This is similar to a Pipenv or a requirements.txt file; it is a list of all the requirements needed to build our image. It is simpler to have them all in one place rather than install each manually line-by-line.

If you are on a Mac, you can create a new Dockerfile on the command line using the touch command.

$ touch Dockerfile
With your text editor add the following single line of code to the Dockerfile.

## Dockerfile
FROM python:3.7-alpine
Dockerfiles are read from top-to-bottom. The first instruction must be the FROM command which lets us import a base image to use for our image. This base image could be another Docker image or one we create entirely from scratch.

In this case we’ll be using the official Docker image for Python 3.7, specifically the alpine version which includes only the bare minimum needed to run Python. The alpine version takes up 78MB of space versus full Python’s 923MB so it is a good option to start with!

## Image Builds
With our instructions complete it’s time to “build” or create the image for the first time. Don’t forget that period . at the end of the command!

## Image Layers
Every image is made up of one or more image layers. The base layer is often a flavor of Linux, like alpine. When we built the image for python:3.7-alpine this image had the id b2c276538711. 

## Containers
Now that we have our Python image how do we run it? Well just as a Dockerfile is a list of image instructions there is also a docker-compose.yml file that is a list of container instructions.

To demonstrate a real-world image and container example we will now “Dockerize” a basic Django web app. You might not fully understand every step but you’ll see how Docker can work to run a web application completely on its own.

## Dockerized Django
We’ll now create a Dockerfile for our image which will completely replace our local dev environment, so this will have Python 3 and Django. Then we’ll add a docker-compose.yml for the container instructions. Make each file via the command line.

## Django REST Framework
Django REST Framework is added just like any other third-party app. Make sure to quit the local server Control + c if it is still running. Then on the command line type the below.

(library) $ pipenv install djangorestframework~=3.11.0
Add it to the INSTALLED_APPS config in our settings.py file. I like to make a distinction between third-party apps and local apps as follows since the number of apps grows quickly in most projects.

config/settings.py \
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # 3rd party
    'rest_framework', # new

    # Local
    'books',
]

## Views
Within our views.py file, update it to look like the following:

# api/views.py
from rest_framework import generics

from books.models import Book
from .serializers import BookSerializer


class BookAPIView(generics.ListAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

On the top lines we import Django REST Framework’s generics class of views, the models from our books app, and serializers from our api app (we will make the serializers next).

Then we create a BookAPIView that uses ListAPIView to create a read-only endpoint for all book instances. There are many generic views available and we will explore them further in later chapters.

The only two steps required in our view are to specify the queryset which is all available books, and then the serializer_class which will be BookSerializer.

## Serializers
A serializer translates data into a format that is easy to consume over the internet, typically JSON, and is displayed at an API endpoint. We will also cover serializers and JSON in more depth in following chapters. For now I want to demonstrate how easy it is to create a serializer with Django REST Framework to convert Django models to JSON.

Make a serializers.py file within our api app.

(library) $ touch api/serializers.py
Then update it as follows in a text editor.

### api/serializers.py
from rest_framework import serializers

from books.models import Book


class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = ('title', 'subtitle', 'author', 'isbn')
On the top lines we import Django REST Framework’s serializers class and the Book model from our books app. We extend Django REST Framework’s ModelSerializer into a BookSerializer class that specifies our database model Book and the database fields we wish to expose: title, subtitle, author, and isbn.
