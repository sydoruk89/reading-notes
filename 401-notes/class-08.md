# List Comprehensions. Primer on Python Decorators
[Examples](https://github.com/realpython/materials/blob/master/primer-on-python-decorators/examples.py)

new_list = []
for i in old_list:
    if filter(i):
        new_list.append(expressions(i))

new_list = [expression(i) for i in old_list if filter(i)]

<b>Show the first letter of each word</b>

listOfWords = ["this","is","a","list","of","words"]

items = [ word[0] for word in listOfWords ]

print items

<b>Lower/Upper case converter</b>

>>> [x.lower() for x in ["A","B","C"]]
['a', 'b', 'c']

>>> [x.upper() for x in ["a","b","c"]]
['A', 'B', 'C']

<b>Print numbers only from a given string</b>

string = "Hello 12345 World"
numbers = [x for x in string if x.isdigit()]
print numbers

>> ['1', '2', '3', '4', '5']


<b>Parsing a file using list comprehension</b>

fh = open("test.txt", "r")

result = [i for i in fh if "line3" in i]

print result

<b>Using list comprehension in functions</b>
def double(x):
  return x*2
>>> print double(10)
20
>>> [double(x) for x in range(10)]

print double
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

## First-Class Objects
In Python, functions are first-class objects. This means that functions can be passed around and used as arguments, just like any other object (string, int, float, list, and so on).
In Python, functions are first-class objects. This means that functions can be passed around and used as arguments, just like any other object (string, int, float, list, and so on).
def say_hello(name):
    return f"Hello {name}"

def be_awesome(name):
    return f"Yo {name}, together we are the awesomest!"

def greet_bob(greeter_func):
    return greeter_func("Bob")

>>> greet_bob(say_hello)
'Hello Bob'

>>> greet_bob(be_awesome)
'Yo Bob, together we are the awesomest!'

## Inner Functions

def parent():
    print("Printing from the parent() function")

    def first_child():
        print("Printing from the first_child() function")

    def second_child():
        print("Printing from the second_child() function")

    second_child()
    first_child()

>>> parent()
Printing from the parent() function
Printing from the second_child() function
Printing from the first_child() function

## Returning Functions From Functions

def parent(num):
    def first_child():
        return "Hi, I am Emma"

    def second_child():
        return "Call me Liam"

    if num == 1:
        return first_child
    else:
        return second_child

## Simple Decorators

def my_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")
    return wrapper

def say_whee():
    print("Whee!")

say_whee = my_decorator(say_whee)

>>> say_whee()
Something is happening before the function is called.
Whee!
Something is happening after the function is called.

## Pie syntax
def my_decorator(func):
    def wrapper():
        print("Something is happening before the function is called.")
        func()
        print("Something is happening after the function is called.")
    return wrapper

@my_decorator
def say_whee():
    print("Whee!")

say_whee = my_decorator(say_whee)

Create a file called decorators.py with the following content:

def do_twice(func):
    def wrapper_do_twice():
        func()
        func()
    return wrapper_do_twice

You can now use this new decorator in other files by doing a regular import:

from decorators import do_twice

@do_twice
def say_whee():
    print("Whee!")

>>> say_whee()
Whee!
Whee!

## Decorating Functions With Arguments
from decorators import do_twice

@do_twice
def greet(name):
    print(f"Hello {name}")
>>> greet("World")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: wrapper_do_twice() takes 0 positional arguments but 1 was

def do_twice(func):
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        func(*args, **kwargs)
    return wrapper_do_twice

<b>Now both your say_whee() and greet() examples works:</b>

>>> say_whee()
Whee!
Whee!

>>> greet("World")
Hello World
Hello World

## Returning Values From Decorated Functions

from decorators import do_twice

@do_twice
def return_greeting(name):
    print("Creating greeting")
    return f"Hi {name}"

>>> hi_adam = return_greeting("Adam")
Creating greeting
Creating greeting
>>> print(hi_adam)
None
Oops, your decorator ate the return value from the function.

<b>To fix this, you need to make sure the wrapper function returns the return value of the decorated function. Change your decorators.py file:</b>

def do_twice(func):
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        return func(*args, **kwargs)
    return wrapper_do_twice

>>> return_greeting("Adam")
Creating greeting
Creating greeting
'Hi Adam'

>>> say_whee
<function do_twice.<locals>.wrapper_do_twice at 0x7f43700e52f0>

>>> say_whee.__name__
'wrapper_do_twice'

>>> help(say_whee)
Help on function wrapper_do_twice in module decorators:

wrapper_do_twice()

<b>To fix this, decorators should use the @functools.wraps decorator, which will preserve information about the original function. Update decorators.py again:</b>

import functools

def do_twice(func):
    @functools.wraps(func)
    def wrapper_do_twice(*args, **kwargs):
        func(*args, **kwargs)
        return func(*args, **kwargs)
    return wrapper_do_twice

>>> say_whee
<function say_whee at 0x7ff79a60f2f0>

>>> say_whee.__name__
'say_whee'

>>> help(say_whee)
Help on function say_whee in module whee:

say_whee()

The @functools.wraps decorator uses the function functools.update_wrapper() to update special attributes like __name__ and __doc__ that are used in the introspection.

## A Few Real World Examples

import functools

def decorator(func):
    @functools.wraps(func)
    def wrapper_decorator(*args, **kwargs):
        # Do something before
        value = func(*args, **kwargs)
        # Do something after
        return value
    return wrapper_decorator

## Timing Functions

import functools
import time

def timer(func):
    """Print the runtime of the decorated function"""
    @functools.wraps(func)
    def wrapper_timer(*args, **kwargs):
        start_time = time.perf_counter()    # 1
        value = func(*args, **kwargs)
        end_time = time.perf_counter()      # 2
        run_time = end_time - start_time    # 3
        print(f"Finished {func.__name__!r} in {run_time:.4f} secs")
        return value
    return wrapper_timer

@timer
def waste_some_time(num_times):
    for _ in range(num_times):
        sum([i**2 for i in range(10000)])

>>> waste_some_time(1)
Finished 'waste_some_time' in 0.0010 secs

>>> waste_some_time(999)
Finished 'waste_some_time' in 0.3260 secs

## Is the User Logged In?

from flask import Flask, g, request, redirect, url_for
import functools
app = Flask(__name__)

def login_required(func):
    """Make sure user is logged in before proceeding"""
    @functools.wraps(func)
    def wrapper_login_required(*args, **kwargs):
        if g.user is None:
            return redirect(url_for("login", next=request.url))
        return func(*args, **kwargs)
    return wrapper_login_required

@app.route("/secret")
@login_required
def secret():

## Decorating Classes

from decorators import debug, timer

class TimeWaster:
    @debug
    def __init__(self, max_num):
        self.max_num = max_num

    @timer
    def waste_time(self, num_times):
        for _ in range(num_times):
            sum([i**2 for i in range(self.max_num)])

>>> tw = TimeWaster(1000)
Calling __init__(<time_waster.TimeWaster object at 0x7efccce03908>, 1000)
'__init__' returned None

>>> tw.waste_time(999)
Finished 'waste_time' in 0.3376 secs

## Nesting Decorators
from decorators import debug, do_twice

@debug
@do_twice
def greet(name):
    print(f"Hello {name}")

Think about this as the decorators being executed in the order they are listed. In other words, @debug calls @do_twice, which calls greet(), or debug(do_twice(greet())):

>>> greet("Eva")
Calling greet('Eva')
Hello Eva
Hello Eva
'greet' returned None

## Decorators With Arguments

@repeat(num_times=4)
def greet(name):
    print(f"Hello {name}")
 
>>> greet("World")
Hello World
Hello World
Hello World
Hello World

def repeat(_func=None, *, num_times=2):
    def decorator_repeat(func):
        @functools.wraps(func)
        def wrapper_repeat(*args, **kwargs):
            for _ in range(num_times):
                value = func(*args, **kwargs)
            return value
        return wrapper_repeat

    if _func is None:
        return decorator_repeat
    else:
        return decorator_repeat(_func)

## Classes as Decorators
class Counter:
    def __init__(self, start=0):
        self.count = start

    def __call__(self):
        self.count += 1
        print(f"Current count is {self.count}")

import functools

class CountCalls:
    def __init__(self, func):
        functools.update_wrapper(self, func)
        self.func = func
        self.num_calls = 0

    def __call__(self, *args, **kwargs):
        self.num_calls += 1
        print(f"Call {self.num_calls} of {self.func.__name__!r}")
        return self.func(*args, **kwargs)

@CountCalls
def say_whee():
    print("Whee!")

## Caching Return Values

from decorators import count_calls

@count_calls
def fibonacci(num):
    if num < 2:
        return num
    return fibonacci(num - 1) + fibonacci(num - 2)

import functools
from decorators import count_calls

def cache(func):
    """Keep a cache of previous function calls"""
    @functools.wraps(func)
    def wrapper_cache(*args, **kwargs):
        cache_key = args + tuple(kwargs.items())
        if cache_key not in wrapper_cache.cache:
            wrapper_cache.cache[cache_key] = func(*args, **kwargs)
        return wrapper_cache.cache[cache_key]
    wrapper_cache.cache = dict()
    return wrapper_cache

@cache
@count_calls
def fibonacci(num):
    if num < 2:
        return num
    return fibonacci(num - 1) + fibonacci(num - 2)

This decorator has more features than the one you saw above. You should use @functools.lru_cache instead of writing your own cache decorator:

import functools

@functools.lru_cache(maxsize=4)
def fibonacci(num):
    print(f"Calculating fibonacci({num})")
    if num < 2:
        return num
    return fibonacci(num - 1) + fibonacci(num - 2)

## Validating JSON

@app.route("/grade", methods=["POST"])
def update_grade():
    json_data = request.get_json()
    if "student_id" not in json_data:
        abort(400)
    # Update database
    return "success!"

from flask import Flask, request, abort
import functools
app = Flask(__name__)

def validate_json(*expected_args):                  # 1
    def decorator_validate_json(func):
        @functools.wraps(func)
        def wrapper_validate_json(*args, **kwargs):
            json_object = request.get_json()
            for expected_arg in expected_args:      # 2
                if expected_arg not in json_object:
                    abort(400)
            return func(*args, **kwargs)
        return wrapper_validate_json
    return decorator_validate_json
