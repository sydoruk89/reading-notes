# [Dunder Methods](https://dbader.org/blog/python-dunder-methods)

## Object Representation: __str__, __repr__
__repr__: The “official” string representation of an object. This is how you would make an object of the class. The goal of __repr__ is to be unambiguous.

__str__: The “informal” or nicely printable string representation of an object. This is for the enduser.

# [Python Iterators](https://dbader.org/blog/python-iterators)

# [Python Decorators](https://realpython.com/primer-on-python-decorators/)

def my_decorator(func):\
    def wrapper():\
        print("Something is happening before the function is called.")\
        func()\
        print("Something is happening after the function is called.")\
    return wrapper

@my_decorator\
def say_whee():\
    print("Whee!")

## Decorating Functions With Arguments

from decorators import do_twice

@do_twice
def greet(name):
    print(f"Hello {name}")

>>> greet("World")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: wrapper_do_twice() takes 0 positional arguments but 1 was given

The solution is to use *args and **kwargs in the inner wrapper function. Then it will accept an arbitrary number of positional and keyword arguments. Rewrite decorators.py as follows:

def do_twice(func):
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        func(*args, **kwargs)
    return wrapper_do_twice

## Returning Values From Decorated Functions#
What happens to the return value of decorated functions? Well, that’s up to the decorator to decide. Let’s say you decorate a simple function as follows:

from decorators import do_twice

@do_twice
def return_greeting(name):
    print("Creating greeting")
    return f"Hi {name}"
Try to use it:

>>> hi_adam = return_greeting("Adam")
Creating greeting
Creating greeting
>>> print(hi_adam)
None
Oops, your decorator ate the return value from the function.

Because the do_twice_wrapper() doesn’t explicitly return a value, the call return_greeting("Adam") ended up returning None.

To fix this, you need to make sure the wrapper function returns the return value of the decorated function. Change your decorators.py file:

def do_twice(func):
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        return func(*args, **kwargs)
    return wrapper_do_twice
The return value from the last execution of the function is returned:

>>> return_greeting("Adam")
Creating greeting
Creating greeting
'Hi Adam'

However, after being decorated, say_whee() has gotten very confused about its identity. It now reports being the wrapper_do_twice() inner function inside the do_twice() decorator. Although technically true, this is not very useful information.

To fix this, decorators should use the @functools.wraps decorator, which will preserve information about the original function. Update decorators.py again:

import functools

def do_twice(func):
    @functools.wraps(func)
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        return func(*args, **kwargs)
    return wrapper_do_twice