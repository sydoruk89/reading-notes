# Classes and Objects. Pytest Fixtures and Coverage

## Accessing Object Variables
class MyClass:
    variable = "blah"

    def function(self):
        print("This is a message inside the class.")

myobjectx = MyClass()

myobjectx.variable

## Accessing Object Functions
class MyClass:
    variable = "blah"

    def function(self):
        print("This is a message inside the class.")

myobjectx = MyClass()

myobjectx.function()

## Thinking Recursively in Python
def fibonacci_recursive(n):
    print("Calculating F", "(", n, ")", sep="", end=", ")

    # Base case
    if n == 0:
        return 0
    elif n == 1:
        return 1

    # Recursive case
    else:
        return fibonacci_recursive(n-1) + fibonacci_recursive(n-2)

Calculating F(5), Calculating F(4), Calculating F(3), Calculating F(2), Calculating F(1), 
Calculating F(0), Calculating F(1), Calculating F(2), Calculating F(1), Calculating F(0), 
Calculating F(3), Calculating F(2), Calculating F(1), Calculating F(0), Calculating F(1)

from functools import lru_cache

@lru_cache(maxsize=None)
def fibonacci_recursive(n):
    print("Calculating F", "(", n, ")", sep="", end=", ")

    # Base case
    if n == 0:
        return 0
    elif n == 1:
        return 1

    # Recursive case
    else:
        return fibonacci_recursive(n-1) + fibonacci_recursive(n-2)

>>> fibonacci_recursive(5)
Calculating F(5), Calculating F(4), Calculating F(3), Calculating F(2), Calculating F(1), Calculating F(0)

<b>lru_cache</b> is a decorator that caches the results. Thus, we avoid recomputation by explicitly checking for the value before trying to compute it. One thing to keep in mind about lru_cache is that since it uses a dictionary to cache results, the positional and keyword arguments (which serve as keys in that dictionary) to the function must be hashable.

## Pytest Fixtures

If you're going to test this function, you'll need to pass it a file-like object. In my last article, I showed how you could use a StringIO object for such a thing, and that remains the case. But rather than defining global variables in your test file, you can create a fixture that'll provide your test with the appropriate object at the right time.
@pytest.fixture
def simple_file():
   return StringIO('\n'.join(['abc', 'def', 'ghi', 'jkl']))

At the same time, fixtures are used differently from global variables. For example, let's say you want to include this fixture in one of your tests. You then can mention it in the test's parameter list. Then, inside the test, you can access the fixture by name. For example:
def test_reverse_lines(simple_file):
   assert reverse_lines(simple_file) == ['cba\n', 'fed\n',
 ↪'ihg\n', 'lkj\n']

 But what if you want to set up an object and then use it multiple times without creating it again? You can do that by setting the fixture's "scope". For example, if you set the scope of the fixture to be "module", it'll be available throughout your tests but will execute only a single time. You can do this by passing the scope parameter to the @pytest.fixture decorator:

@pytest.fixture(scope='module')
def simple_file():
   return StringIO('\n'.join(['abc', 'def', 'ghi', 'jkl']))

# Coverage
If you don't say anything more than that, you'll get a coverage report for every part of the Python library that your program used, so I strongly suggest you provide an argument to --cov, specifying which program(s) you want to test. And, you should indicate the directory into which the report should be written. So in this case, you would say:

def only_odd_mul(x, y):
   if x%2 and y%2:
       return x * y
   else:
       raise NoEvenNumbersHereException(f'{x} and/or {y}
 ↪not odd')

pytest --cov=mymul .

Once you've done this, you'll need to turn the coverage report into something human-readable. I suggest using HTML, although other output formats are available:

coverage html

This creates a directory called htmlcov. Open the index.html file in this directory using your browser, and you'll get a web-based report showing (in red) where your program still lacks coverage. Sure enough, in this case, it showed that the even-number path wasn't covered. Let's add a test to do this:

def test_even_numbers():
   with pytest.raises(NoEvenNumbersHereException):
       only_odd_mul(2,4)


# conftest.py: sharing fixture functions¶
If during implementing your tests you realize that you want to use a fixture function from multiple test files you can move it to a conftest.py file. You don’t need to import the fixture you want to use in a test, it automatically gets discovered by pytest. The discovery of fixture functions starts at test classes, then test modules, then conftest.py files and finally builtin and third party plugins.

# Scope: sharing a fixture instance across tests in a class, module or session¶
We can add a scope="module" parameter to the @pytest.fixture invocation to cause the decorated smtp_connection fixture function to only be invoked once per test module (the default is to invoke once per test function).

import pytest
import smtplib


@pytest.fixture(scope="module")
def smtp_connection():
    return smtplib.SMTP("smtp.gmail.com", 587, timeout=5)

The name of the fixture again is smtp_connection and you can access its result by listing the name smtp_connection as an input parameter in any test or fixture function (in or below the directory where conftest.py is located):



def test_ehlo(smtp_connection):
    response, msg = smtp_connection.ehlo()
    assert response == 250
    assert b"smtp.gmail.com" in msg
    assert 0  # for demo purposes


def test_noop(smtp_connection):
    response, msg = smtp_connection.noop()
    assert response == 250
    assert 0  # for demo purposes

If you decide that you rather want to have a session-scoped smtp_connection instance, you can simply declare it:

@pytest.fixture(scope="session")
def smtp_connection():
    # the returned fixture value will be shared for
    # all tests needing it
    ...

# Fixture finalization / executing teardown code

Pytest supports execution of fixture specific finalization code when the fixture goes out of scope. By using a yield statement instead of return, all the code after the yield statement serves as the teardown code:

import smtplib
import pytest


@pytest.fixture(scope="module")
def smtp_connection():
    smtp_connection = smtplib.SMTP("smtp.gmail.com", 587, timeout=5)
    yield smtp_connection  # provide the fixture value
    print("teardown smtp")
    smtp_connection.close()

Note that we can also seamlessly use the yield syntax with with statements:


import smtplib
import pytest


@pytest.fixture(scope="module")
def smtp_connection():
    with smtplib.SMTP("smtp.gmail.com", 587, timeout=5) as smtp_connection:
        yield smtp_connection  # provide the fixture value

Here’s the smtp_connection fixture changed to use addfinalizer for cleanup:

import smtplib
import pytest


@pytest.fixture(scope="module")
def smtp_connection(request):
    smtp_connection = smtplib.SMTP("smtp.gmail.com", 587, timeout=5)

    def fin():
        print("teardown smtp_connection")
        smtp_connection.close()

    request.addfinalizer(fin)

# Factories as fixtures
The “factory as fixture” pattern can help in situations where the result of a fixture is needed multiple times in a single test. Instead of returning data directly, the fixture instead returns a function which generates the data. This function can then be called multiple times in the test.

Factories can have parameters as needed:

@pytest.fixture
def make_customer_record():
    def _make_customer_record(name):
        return {"name": name, "orders": []}

    return _make_customer_record


def test_customer_records(make_customer_record):
    customer_1 = make_customer_record("Lisa")
    customer_2 = make_customer_record("Mike")
    customer_3 = make_customer_record("Meredith")

# Parametrizing fixtures
Extending the previous example, we can flag the fixture to create two smtp_connection fixture instances which will cause all tests using the fixture to run twice. The fixture function gets access to each parameter through the special request object:

import pytest
import smtplib


@pytest.fixture(scope="module", params=["smtp.gmail.com", "mail.python.org"])
def smtp_connection(request):
    smtp_connection = smtplib.SMTP(request.param, 587, timeout=5)
    yield smtp_connection
    print("finalizing {}".format(smtp_connection))
    smtp_connection.close()

# Using marks with parametrized fixtures

import pytest


@pytest.fixture(params=[0, 1, pytest.param(2, marks=pytest.mark.skip)])
def data_set(request):
    return request.param


def test_data(data_set):
    pass
Running this test will skip the invocation of data_set with value 2.

# Modularity: using fixtures from a fixture function
import pytest


class App:
    def __init__(self, smtp_connection):
        self.smtp_connection = smtp_connection


@pytest.fixture(scope="module")
def app(smtp_connection):
    return App(smtp_connection)


def test_smtp_connection_exists(app):
    assert app.smtp_connection