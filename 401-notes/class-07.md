# Python Scope & the LEGB Rule

## Python Scope vs Namespace
In Python, the concept of scope is closely related to the concept of the namespace. As you’ve learned so far, a Python scope determines where in your program a name is visible. Python scopes are implemented as dictionaries that map names to objects. These dictionaries are commonly called namespaces. These are the concrete mechanisms that Python uses to store names. They’re stored in a special attribute called .__dict__.

Names at the top level of a module are stored in the module’s namespace. In other words, they’re stored in the module’s .__dict__ attribute. Take a look at the following code:

>>> import sys
>>> sys.__dict__.keys()
dict_keys(['__name__', '__doc__', '__package__',..., 'argv', 'ps1', 'ps2'])

After you import sys, you can use .keys() to inspect the keys of sys.__dict__. This returns a list with all the names defined at the top level of the module. In this case, you can say that .__dict__ holds the namespace of sys and is a concrete representation of the module scope.

If you know how .__dict__ and namespaces work in Python, then you can reference ps1 in at least two different ways:

Using the dot notation on the module’s name in the form module.name
Using a subscription operation on .__dict__ in the form module.__dict__['name']
>>> sys.ps1
'>>> '
>>> sys.__dict__['ps1']
'>>> '

## Using the LEGB Rule for Python Scope
The letters in LEGB stand for Local, Enclosing, Global, and Built-in. Here’s a quick overview of what these terms mean:
* Local (or function) scope is the code block or body of any Python function or lambda expression. This Python scope contains the names that you define inside the function. These names will only be visible from the code of the function. It’s created at function call, not at function definition, so you’ll have as many different local scopes as function calls. This is true even if you call the same function multiple times, or recursively. Each call will result in a new local scope being created.

* Enclosing (or nonlocal) scope is a special scope that only exists for nested functions. If the local scope is an inner or nested function, then the enclosing scope is the scope of the outer or enclosing function. This scope contains the names that you define in the enclosing function. The names in the enclosing scope are visible from the code of the inner and enclosing functions.

* Global (or module) scope is the top-most scope in a Python program, script, or module. This Python scope contains all of the names that you define at the top level of a program or a module. Names in this Python scope are visible from everywhere in your code.

* Built-in scope is a special Python scope that’s created or loaded whenever you run a script or open an interactive session. This scope contains names such as keywords, functions, exceptions, and other attributes that are built into Python. Names in this Python scope are also available from everywhere in your code. It’s automatically loaded by Python when you run a program or script.

## Functions: The Local Scope
>>> def square(base):
...     result = base ** 2
...     print(f'The square of {base} is: {result}')
...
>>> square(10)
The square of 10 is: 100
>>> result  # Isn't accessible from outside square()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    result
NameError: name 'result' is not defined
>>> base  # Isn't accessible from outside square()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    base
NameError: name 'base' is not defined
>>> square(20)
The square of 20 is: 400

You can inspect the names and parameters of a function using .__code__, which is an attribute that holds information on the function’s internal code. Take a look at the code below:

>>> square.__code__.co_varnames
('base', 'result')
>>> square.__code__.co_argcount
1
>>> square.__code__.co_consts
(None, 2, 'The square of ', ' is: ')
>>> square.__code__.co_name
'square'

## Modules: The Global Scope
To inspect the names within your main global scope, you can use dir(). If you call dir() without arguments, then you’ll get the list of names that live in your current global scope. 
Global names can be updated or modified from any place in your global Python scope. Beyond that, the global statement can be used to modify global names from almost any place in your code, as you’ll see in The global Statement.

Modifying global names is generally considered bad programming practice because it can lead to code that is:

* Difficult to debug: Almost any statement in the program can change the value of a global name.
* Hard to understand: You need to be aware of all the statements that access and modify global names.
* Impossible to reuse: The code is dependent on global names that are specific to a concrete program.

Good programming practice recommends using local names rather than global names. Here are some tips:
* Write self-contained functions that rely on local names rather than global ones.
* Try to use unique objects names, no matter what scope you’re in.
* Avoid global name modifications throughout your programs.
* Avoid cross-module name modifications.
* Use global names as constants that don’t change during your program’s execution.

## builtins: The Built-In Scope
Notice that the names in builtins are always loaded into your global Python scope with the special name __builtins__, as you can see in the following code:

>>> dir()
['__annotations__', '__builtins__',..., '__package__', '__spec__']
>>> dir(__builtins__)
['ArithmeticError', 'AssertionError',..., 'tuple', 'type', 'vars', 'zip']

## The global Statement

If you want this code to work the way you expect here, then you can use a global statement as follows:

>>> counter = 0  # A global name
>>> def update_counter():
...     global counter  # Declare counter as global
...     counter = counter + 1  # Successfully update the counter
...
>>> update_counter()
>>> counter
1
>>> update_counter()
>>> counter
2
>>> update_counter()
>>> counter
3

The use of global is considered bad practice in general. If you find yourself using global to fix problems like the one above, then stop and think if there is a better way to write your code.

For example, you can try to write a self-contained function that relies on local names rather than on global names as follows:

>>> global_counter = 0  # A global name
>>> def update_counter(counter):
...     return counter + 1  # Rely on a local name
...
>>> global_counter = update_counter(global_counter)
>>> global_counter
1
>>> global_counter = update_counter(global_counter)
>>> global_counter
2
>>> global_counter = update_counter(global_counter)
>>> global_counter
3

## The nonlocal Statement
The nonlocal Statement
Similarly to global names, nonlocal names can be accessed from inner functions, but not assigned or updated. If you want to modify them, then you need to use a nonlocal statement. With a nonlocal statement, you can define a list of names that are going to be treated as nonlocal.
>>> def func():
...     var = 100  # A nonlocal variable
...     def nested():
...         nonlocal var  # Declare var as nonlocal
...         var += 100
...
...     nested()
...     print(var)
...
>>> func()
200

<b>Unlike global, you can’t use nonlocal outside of a nested or enclosed function. To be more precise, you can’t use a nonlocal statement in either the global scope or in a local scope.</b>
In contrast to global, you can’t use nonlocal to create lazy nonlocal names. Names must already exist in the enclosing Python scope if you want to use them as nonlocal names. This means that you can’t create nonlocal names by declaring them in a nonlocal statement in a nested function. Take a look at the following code example:

>>> def func():
...     def nested():
...         nonlocal lazy_var  # Try to create a nonlocal lazy name
...
  File "<stdin>", line 3
SyntaxError: no binding for nonlocal 'lazy_var' found

## Using Enclosing Scopes as Closures
Closures are a special use case of the enclosing Python scope. When you handle a nested function as data, the statements that make up that function are packaged together with the environment in which they execute. The resulting object is known as a closure. In other words, a closure is an inner or nested function that carries information about its enclosing scope, even though this scope has completed its execution.

>>> def power_factory(exp):
...     def power(base):
...         return base ** exp
...     return power
...
>>> square = power_factory(2)
>>> square(10)
100
>>> cube = power_factory(3)
>>> cube(10)
1000
>>> cube(5)
125
>>> square(15)
225

Variables like exp are called free variables. They are variables that are used in a code block but not defined there. Free variables are the mechanism that closures use to retain state information between calls.

In this case, you can use a closure factory to generate a closure that remembers the previous measurements in the sample. 
>>> def mean():
...     sample = []
...     def _mean(number):
...         sample.append(number)
...         return sum(sample) / len(sample)
...     return _mean
...
>>> current_mean = mean()
>>> current_mean(10)
10.0
>>> current_mean(15)
12.5
>>> current_mean(12)
12.333333333333334
>>> current_mean(11)
12.0
>>> current_mean(13)
12.2

Notice that if your data stream gets too large, then this function can become a problem in terms of memory usage. That’s because with each call to current_mean, sample will hold a bigger and bigger list of values. Take a look at the following code for an alternative implementation using nonlocal:

>>> def mean():
...     total = 0
...     length = 0
...     def _mean(number):
...         nonlocal total, length
...         total += number
...         length += 1
...         return total / length
...     return _mean
...
>>> current_mean = mean()
>>> current_mean(10)
10.0
>>> current_mean(15)
12.5
>>> current_mean(12)
12.333333333333334
>>> current_mean(11)
12.0
>>> current_mean(13)
12.2

Finally, you can find some examples of using closures in the Python standard library. For example, functools provides a function named partial() that makes use of the closure technique to create new function objects that can be called using predefined arguments. Here’s an example:

>>> from functools import partial
>>> def power(exp, base):
...     return base ** exp
...
>>> square = partial(power, 2)
>>> square(10)
100

## Class and Instance Attributes Scope
Unlike functions, the class local scope isn’t created at call time, but at execution time. Each class object has its own .__dict__ attribute that holds the class scope or namespace where all the class attributes live. Check out this code:

>>> class A:
...     attr = 100
...
>>> A.__dict__.keys()
dict_keys(['__module__', 'attr', '__dict__', '__weakref__', '__doc__'])

>>> class A:
...     attr = 100
...     print(attr)  # Access class attributes directly
...
100
>>> A.attr  # Access a class attribute from outside the class
100
>>> attr  # Isn't defined outside A
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    attr
NameError: name 'attr' is not defined

To create, update, or access any instance attribute from inside the class, you need to use self along with the dot notation. Here, self is a special attribute that represents the current instance. On the other hand, to update or access any instance attribute from outside the class, you need to create an instance and then use the dot notation. Here’s how this works:

>>> class A:
...     def __init__(self, var):
...         self.var = var  # Create a new instance attribute
...         self.var *= 2  # Update the instance attribute
...
>>> obj = A(100)
>>> obj.__dict__
{'var': 200}
>>> obj.var
200

>>> class A:
...     def __init__(self, var):
...         self.var = var
...
...     def duplicate_var(self):
...         return self.var * 2
...
>>> obj = A(100)
>>> obj.var
100
>>> obj.duplicate_var()
200
>>> A.var
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    A.var
AttributeError: type object 'A' has no attribute 'var'

<b>Here, you modify A to add a new method called duplicate_var(). Then, you create an instance of A by passing in 100 to the class initializer. After that, you can now call duplicate_var() on obj to duplicate the value stored in self.var. Finally, if you try to access var using the class object instead of an instance, then you’ll get an AttributeError because instance attributes can’t be accessed using class objects.</b>

>>> class A:
...     var = 100
...     def print_var(self):
...         print(var)  # Try to access a class attribute directly -> A.var should be

>>> class A:
...     var = 100
...     def __init__(self):
...         self.var = 200
...
...     def access_attr(self):
...         # Use dot notation to access class and instance attributes
...         print(f'The instance attribute is: {self.var}')
...         print(f'The class attribute is: {A.var}')
...
>>> obj = A()
>>> obj.access_attr()
>>> obj = A()
>>> obj.access_attr()
The instance attribute is: 200
The class attribute is: 100
>>> A.var  # Access class attributes
100
>>> A().var # Access instance attributes
200
>>> A.__dict__.keys()
dict_keys(['__module__', 'var', '__init__',..., '__getattribute__'])
>>> A().__dict__.keys()
dict_keys(['var'])

# globals()
In Python, globals() is a built-in function that returns a reference to the current global scope or namespace dictionary. This dictionary always stores the names of the current module. This means that if you call globals() in a given module, then you’ll get a dictionary containing all the names that you’ve defined in that module, right before the call to globals(). Here’s an example:

>>> globals()
{'__name__': '__main__',..., '__builtins__': <module 'builtins' (built-in)>}
>>> my_var = 100
>>> globals()
{'__name__': '__main__',..., 'my_var': 100}

Another example of how to use globals() would be to inspect the list of special names in the global scope. Take a look at the following list comprehension:

>>> [name for name in globals() if name.startswith('__')]
['__name__', '__doc__', '__package__',..., '__annotations__', '__builtins__']

# locals()
Another function related to Python scope and namespaces is locals(). This function updates and returns a dictionary that holds a copy of the current state of the local Python scope or namespace. When you call locals() in a function block, you get all the names assigned in the local or function scope up to the point where you call locals(). Here’s an example:

>>> def func(arg):
...     var = 100
...     print(locals())
...     another = 200
...
>>> func(300)
{'var': 100, 'arg': 300}


## vars()

vars() is a Python built-in function that returns the .__dict__ attribute of a module, class, instance, or any other object which has a dictionary attribute. Remember that .__dict__ is a special dictionary that Python uses to implement namespaces. Take a look at the following examples:

>>> import sys
>>> vars(sys) # With a module object
{'__name__': 'sys',..., 'ps1': '>>> ', 'ps2': '... '}
>>> vars(sys) is sys.__dict__
True
>>> class MyClass:
...     def __init__(self, var):
...         self.var = var
...
>>> obj = MyClass(100)
>>> vars(obj)  # With a user-defined object
{'var': 100}
>>> vars(MyClass)  # With a class
mappingproxy({'__module__': '__main__',..., '__doc__': None})

Without any argument, vars() acts like locals() and returns a dictionary with all the names in the local Python scope:

>>> vars()
{'__name__': '__main__',..., '__builtins__': <module 'builtins' (built-in)>}
>>> vars() is locals()
True


## dir()

You can use dir() without arguments to get the list of names in the current Python scope. If you call dir() with an argument, then the function attempts to return a list of valid attributes for that object:

>>> dir()  # With no arguments
['__annotations__', '__builtins__',..., '__package__', '__spec__']
>>> dir(zip)  # With a function object
['__class__', '__delattr__',..., '__str__', '__subclasshook__']
>>> import sys
>>> dir(sys)  # With a module object
['__displayhook__', '__doc__',..., 'version_info', 'warnoptions']
>>> var = 100
>>> dir(var)  # With an integer variable
['__abs__', '__add__',..., 'imag', 'numerator', 'real', 'to_bytes']