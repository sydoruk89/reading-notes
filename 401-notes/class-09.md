# Dunder Methods

## Iteration: __len__, __getitem__, __reversed__

class Account:
    """A simple account class"""

    def __init__(self, owner, amount=0):
        """
        This is the constructor that lets us create
        objects from this class
        """
        self.owner = owner
        self.amount = amount
        self._transactions = []

    def __repr__(self):
        return 'Account({!r}, {!r})'.format(self.owner, self.amount)

    def __str__(self):
        return 'Account of {} with starting amount: {}'.format(
            self.owner, self.amount)

    def add_transaction(self, amount):
        if not isinstance(amount, int):
            raise ValueError('please use int for amount')
        self._transactions.append(amount)

    @property
    def balance(self):
        return self.amount + sum(self._transactions)

    def __len__(self):
        return len(self._transactions)

    def __getitem__(self, position):
        return self._transactions[position]

    def __reversed__(self):
        return self[::-1]

## Operator Overloading for Comparing Accounts: __eq__, __lt__

>>> acc2 = Account('tim', 100)
>>> acc2.add_transaction(20)
>>> acc2.add_transaction(40)
>>> acc2.balance
160

>>> acc2 > acc
TypeError:
"'>' not supported between instances of 'Account' and 'Account'"

from functools import total_ordering

@total_ordering
class Account:
    # ... (see above)

    def __eq__(self, other):
        return self.balance == other.balance

    def __lt__(self, other):
        return self.balance < other.balance

## Operator Overloading for Merging Accounts: __add__

def __add__(self, other):
    owner = '{}&{}'.format(self.owner, other.owner)
    start_amount = self.amount + other.amount
    acc = Account(owner, start_amount)
    for t in list(self) + list(other):
        acc.add_transaction(t)
    return acc

Yes, it is a bit more involved than the other dunder implementations so far. It should show you though that you are in the driver’s seat. You can implement addition however you please. If we wanted to ignore historic transactions—fine, you can also implement it like this:

day4 = sum([day1, day2, day3])

you’ll get an error like

TypeError: unsupported operand type(s) for +: ‘int’ and ‘Day’

This is because the sum method starts with the integer 0, and it tries:

0.__add__(day1)

However, the __add__ method of an integer doesn’t know anything about how to sum a Day instance. Therefore, it tries to call the reverse add method:

day1.__radd__(0)

which is not yet defined! 

def __add__(self, other):
    owner = self.owner + other.owner
    start_amount = self.balance + other.balance
    return Account(owner, start_amount)

    Note this works in both directions because we’re adding objects of the same type. In general, if you would add your object to a builtin (int, str, …) the __add__ method of the builtin wouldn’t know anything about your object. In that case you need to implement the reverse add method (__radd__) as well.
    class Day(object):
    def __init__(self, visits, contacts):
        self.visits = visits
        self.contacts = contacts

    def __add__(self, other):
        total_visits = self.visits + other.visits
        total_contacts = self.contacts + other.contacts
        return Day(total_visits, total_contacts)

    def __radd__(self, other):
        if other == 0:
            return self
        else:
            return self.__add__(other)

    def __str__(self):
        return "Visits: %i, Contacts: %i" % (self.visits, self.contacts)

day1 = Day(10, 1)
day2 = Day(20, 2)
print day1
#Visits: 10, Contacts: 1
print day2
#Visits: 20, Contacts: 2

day3 = day1 + day2
print day3
#Visits: 30, Contacts: 3

day4 = sum([day1, day2, day3])
print day4
#Visits: 60, Contacts: 6