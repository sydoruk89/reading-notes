# Random Module in Python
import random
print random.randint(0, 5)

A random number between 0 and 100:

import random
random.random() * 100

## Choice 
Generate a random value from the sequence sequence.

import random
myList = [2, 109, False, 10, "Lorem", 482, "Ipsum"]
random.choice(myList)

## Shuffle
The shuffle function, shuffles the elements in list in place, so they are in a random order.
from random import shuffle
x = [[i] for i in range(10)]
shuffle(x)
Output:
#print x  gives  [[9], [2], [7], [0], [4], [5], [3], [1], [8], [6]]

## Randrange
Generate a randomly selected element from range(start, stop, step)
random.randrange(start, stop[, step])
import random
for i in range(3):
    print random.randrange(0, 101, 5)

## random.sample(population, k)
Return a k length list of unique elements chosen from the population sequence or set. Used for random sampling without replacement. Returns a new list containing elements from the population while leaving the original population unchanged.

# random.random()
Return the next random floating point number in the range [0.0, 1.0).

# random.uniform(a, b)
Return a random floating point number N such that a <= N <= b for a <= b and b <= N <= a for b < a.

# There are basically three types of dependency injection:
* constructor injection: the dependencies are provided through a class constructor.
* setter injection: the client exposes a setter method that the injector uses to inject the dependency.
* interface injection: the dependency provides an injector method that will inject the dependency into any client passed to it. Clients must implement an interface that exposes a setter method that accepts the dependency.

# So now its the dependency injection’s responsibility to:

Create the objects
Know which classes require those objects
And provide them all those objects


# Benefits of using DI
* Helps in Unit testing.
* Boiler plate code is reduced, as initializing of dependencies is done by the injector component.
* Extending the application becomes easier.
* Helps to enable loose coupling, which is important in application programming.
# Disadvantages of DI
* It’s a bit complex to learn, and if overused can lead to management issues and other problems.
* Many compile time errors are pushed to run-time.
* Dependency injection frameworks are implemented with reflection or dynamic programming. This can hinder use of IDE automation, such as “find references”, “show call hierarchy” and safe refactoring.