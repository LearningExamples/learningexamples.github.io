---
title: Python Classes and Objects
parent: Python
nav_order: 12
---


# 🏭 Python Classes and Objects: An Introduction to OOP

So far we've stored data in variables, lists, and dictionaries, and we've organized code into functions.
**Object-Oriented Programming (OOP)** lets us bundle the two together: data *and* the functions that work on that data, in one neat package called a **class**.

By the end of this tutorial you'll know how to:

* define a class and create objects from it
* write a constructor (`__init__`) and use `self`
* tell **class attributes** apart from **instance attributes**
* write methods, getters, and setters
* control how your objects print with `__str__`
* make your objects work with `==`, `<`, `+`, `-`, and friends

Let's build some classes! 🚀

---

## 1. Classes vs. Objects 🍪

Think of a **class** as a cookie cutter and an **object** as a cookie.

* The **class** is the blueprint. It describes what every car *has* (a model, a price) and what every car *can do* (honk).
* An **object** (also called an **instance**) is one actual car built from that blueprint.

You write the class once, then create as many objects as you like.

| Term | Meaning | Example |
|---|---|---|
| **Class** | The blueprint | `class Car:` |
| **Object / instance** | One thing built from the blueprint | `my_car = Car()` |
| **Attribute** | A variable that belongs to an object | `my_car.price` |
| **Method** | A function that belongs to a class | `my_car.honk()` |
| **Constructor** | The method that sets up a new object | `def __init__(self):` |
| **`self`** | The object the method is working on | `self.price = 100` |

---

## 2. Your First Class 🚗

```python
class Car:
    """A simple class for cars."""

    # The constructor: runs automatically every time a Car is created
    def __init__(self):
        self.model = "KIA"
        self.price = 100

    def show_car(self):
        """Prints the car model and price."""
        print("The car model:", self.model)
        print("The car price:", self.price)

    def honk(self):
        print("honk")

    def fun_honk(self, x):   # a method with an extra parameter
        for i in range(x):
            print("honk")
```

### Creating and using an object

```python
my_car = Car()                 # create an object (calls __init__)

print(f"{my_car.model = }")    # read attributes with a dot
print(f"{my_car.price = }")
my_car.honk()                  # call a method with a dot

# attributes can be changed
my_car.model = "Toyota"
my_car.price = 200

my_car.fun_honk(2)
my_car.show_car()
```

**Output:**

```
my_car.model = 'KIA'
my_car.price = 100
honk
honk
honk
The car model: Toyota
The car price: 200
```

### What is `self`?

Every method's first parameter is `self`. When you write `my_car.fun_honk(2)`, Python actually runs `Car.fun_honk(my_car, 2)`:

* `self` becomes `my_car`
* `x` becomes `2`

That's how a method knows **which** car's `model` and `price` to use. You never pass `self` yourself; Python fills it in.

### Each object is independent

```python
my_car_2 = Car()
my_car_2.show_car()
```

**Output:**

```
The car model: KIA
The car price: 100
```

Changing `my_car` earlier had no effect on `my_car_2`. Each object keeps its own attributes.

---

## 3. Constructors with Parameters 🔧

A constructor that always sets `"KIA"` isn't very useful. Let's let the caller choose:

```python
class NewCar:
    """A class for cars."""

    def __init__(self, model, price):
        """The constructor.

        Args:
            model: the model of the car.
            price: the price of the car.
        """
        self.model = model
        self.price = price

    def show_car(self):
        print("The car model:", self.model)
        print("The car price:", self.price)

my_car = NewCar("BOB", 12000)
my_car.show_car()
```

**Output:**

```
The car model: BOB
The car price: 12000
```

> Notice the pattern `self.model = model`. The **parameter** `model` disappears when `__init__` ends, so we copy it into the **attribute** `self.model`, which lives as long as the object does.
{: .note }

### Default values

Constructors are regular functions, so they can have default values and keyword arguments:

```python
class Dog:
    def __init__(self, name, size, location="Montreal", color="red"):
        self.name = name
        self.size = size
        self.location = location
        self.color = color

    def print_dog(self):
        print("the dog name is", self.name)
        print("the dog size is", self.size)
        print("the dog location is", self.location)
        print("the dog color is", self.color)

dog1 = Dog("bob", 10, location="Toronto")   # keep the default color
dog1.print_dog()

dog2 = Dog("bob2", 20, color="orange")      # keep the default location
dog2.print_dog()
```

**Output:**

```
the dog name is bob
the dog size is 10
the dog location is Toronto
the dog color is red
the dog name is bob2
the dog size is 20
the dog location is Montreal
the dog color is orange
```

Parameters **without** defaults are required:

```python
dog3 = Dog("hello")
# TypeError: Dog.__init__() missing 1 required positional argument: 'size'
```

> Once a parameter has a default, every parameter after it needs a default too. That's why `location` and `color` come last.
{: .warning }

---

## 4. Methods That Call Other Methods 📣

Inside a class, a method can call another method of the same object through `self`:

```python
class NewCar:
    def __init__(self, model, price=12, honk_sound="GRRR"):
        self.model = model
        self.price = price
        self.honk_sound = honk_sound

    def honk(self):
        print(self.honk_sound)

    def many_honk(self, x):
        for i in range(x):
            self.honk()            # calling another method

my_car = NewCar("BYD")
my_car.many_honk(3)

my_car.honk_sound = "HONK"         # change the attribute...
my_car.many_honk(2)                # ...and the behaviour changes too
```

**Output:**

```
GRRR
GRRR
GRRR
HONK
HONK
```

---

## 5. Class Attributes vs. Instance Attributes 🛞

* **Instance attributes** are created with `self.something` (usually in `__init__`). Every object gets its own copy.
* **Class attributes** are written directly inside the class body. They are **shared** by all objects of that class.

```python
class Motor:
    number_of_wheels = 2            # class attribute: shared by every Motor

    def __init__(self, model, price):
        self.model = model          # instance attributes: one per object
        self.price = price

    def honk2(self, p):
        print("the model is", self.model)
        print("Honk" * p)

bike1 = Motor("toyota", 110)
bike2 = Motor("honda", 120)

print(bike1.number_of_wheels)   #=> 2
print(bike2.number_of_wheels)   #=> 2
print(Motor.number_of_wheels)   #=> 2  (read through the class itself)
```

Class attributes can be read through the **class name**, but instance attributes can't, because they only exist on objects:

```python
print(Motor.price)   # AttributeError: type object 'Motor' has no attribute 'price'
```

### Watch out: assigning through an object

Assigning to a class attribute through an object creates a new **instance** attribute on that object only. It doesn't change the shared one:

```python
bike2.number_of_wheels = 3      # bike2 now has its OWN number_of_wheels

print(bike1.number_of_wheels)   #=> 2
print(bike2.number_of_wheels)   #=> 3
print(Motor.number_of_wheels)   #=> 2

Motor.number_of_wheels = 4      # change the shared class attribute
print(bike1.number_of_wheels)   #=> 4  (bike1 uses the class value)
print(bike2.number_of_wheels)   #=> 3  (bike2's own value hides it)
```

---

## 6. Getters, Setters, and Parameters vs. Attributes 🏃

**Getters** and **setters** are methods whose only job is to read or change an attribute. They give you one place to add checks later (for example, "speed can't be negative").

```python
class Runner:
    race_distance = 42.195          # class attribute

    def __init__(self, name, age, speed):
        self.name = name
        self.age = age
        self.speed = speed

    def set_speed(self, speed):     # setter
        self.speed = speed

    def get_speed(self):            # getter
        return self.speed

    def best_speed(self, speed):
        print("the object's speed is:", self.speed)        # the attribute
        print("the best speed is:", speed)                 # the parameter
        print("the race distance is:", self.race_distance)

    def run(self):
        print("I am running --", self.name)

m1 = Runner("bob", 18, 100)
m1.set_speed(1000)
print(m1.get_speed())
m1.best_speed(4321)
m1.run()
```

**Output:**

```
1000
the object's speed is: 1000
the best speed is: 4321
the race distance is: 42.195
I am running -- bob
```

Inside `best_speed`, `speed` and `self.speed` are two **different** variables:

* `speed` is the value passed into this one call (`4321`).
* `self.speed` is the value stored on the object (`1000`).

---

## 7. Putting It Together: A Bank Account 🏦

Here's a more realistic class. It also shows a common naming convention: a method whose name starts with an underscore (`_log_transaction`) is meant for **internal use only**, as a helper for the class's other methods.

```python
class BankAccount:
    def __init__(self, account_holder, initial_balance=0):
        self.account_holder = account_holder
        self.balance = initial_balance
        print(f"Account created for {self.account_holder} with initial balance ${self.balance:.2f}")

    def _log_transaction(self, transaction_type, amount):   # "private" helper
        print(f"  [Log] {transaction_type} of ${amount:.2f} processed.")

    def deposit(self, amount):
        if amount > 0:
            self.balance += amount
            self._log_transaction("Deposit", amount)
            print(f"Deposited ${amount:.2f}. New balance: ${self.balance:.2f}")
        else:
            print("Deposit amount must be positive.")

    def withdraw(self, amount):
        if amount > 0:
            if self.balance >= amount:
                self.balance -= amount
                self._log_transaction("Withdrawal", amount)
                print(f"Withdrew ${amount:.2f}. New balance: ${self.balance:.2f}")
            else:
                print(f"Insufficient funds. Current balance: ${self.balance:.2f}")
        else:
            print("Withdrawal amount must be positive.")

    def get_balance(self):
        print(f"Current balance for {self.account_holder}: ${self.balance:.2f}")
        return self.balance

my_account = BankAccount("Alice Wonderland", 500.00)
my_account.deposit(200.00)
my_account.withdraw(150.00)
my_account.withdraw(600.00)    # not enough money
my_account.get_balance()
```

**Output:**

```
Account created for Alice Wonderland with initial balance $500.00
  [Log] Deposit of $200.00 processed.
Deposited $200.00. New balance: $700.00
  [Log] Withdrawal of $150.00 processed.
Withdrew $150.00. New balance: $550.00
Insufficient funds. Current balance: $550.00
Current balance for Alice Wonderland: $550.00
```

The class protects its own data: you can't withdraw more than you have, and you can't deposit a negative amount.

---

## 8. Making Objects Print Nicely with `__str__` 🖨️

Printing an object gives you something not very helpful by default:

```python
class Toy:
    def __init__(self, name, price, min_age):
        self.name = name
        self.price = price
        self.min_age = min_age

truck = Toy("Monster Truck XX", 14.99, 5)
print(truck)
```

**Output:**

```
<__main__.Toy object at 0x7adef05f5eb0>
```

Add a `__str__` method that **returns** a string, and Python uses it whenever the object is printed, passed to `str()`, or placed in an f-string:

```python
class Toy:
    def __init__(self, name, price, min_age):
        self.name = name
        self.price = price
        self.min_age = min_age

    def __str__(self):
        return f"{self.name} costs only ${self.price:.2f}. Not for children under {self.min_age}!"

truck = Toy("Monster Truck AA", 14.99, 5)
print(truck)
```

**Output:**

```
Monster Truck AA costs only $14.99. Not for children under 5!
```

`__str__` can reuse other methods:

```python
class Motor:
    def __init__(self, make, model, year):
        self.make = make
        self.model = model
        self.year = year

    def get_descriptive(self):
        return str(self.year) + " " + self.make + " " + self.model

    def __str__(self):
        return self.get_descriptive()

m1 = Motor("honda", "civic", 2023)
m1.make = "accord"
print(m1)          #=> 2023 accord civic
print(str(m1))     #=> 2023 accord civic
```

> `__str__` must **return** a string. If it prints instead of returning, `print(obj)` will fail with a `TypeError`.
{: .warning }

---

## 9. Operator Overloading: Teaching Python New Tricks ➕

Methods with double underscores on both sides (`__like_this__`) are called **magic methods** or **dunder methods**. Python calls them automatically when you use an operator on your objects.

### Equality: `__eq__`

Without `__eq__`, `==` only checks whether two variables are the **same object**. With it, *you* decide what "equal" means:

```python
class Student:
    def __init__(self, name, grade, num_inter):
        self.name = name
        self.grade = grade
        self.num_inter = num_inter

    def __str__(self):
        return f"{self.name} (grade {self.grade})"

    def __eq__(self, other):
        return self.grade == other.grade and self.num_inter == other.num_inter

s1 = Student("bob", 110, 3)
s2 = Student("alice", 110, 3)

print(s1)          #=> bob (grade 110)
print(s1 == s2)    #=> True  (same grade and same number of interactions)
```

### Comparisons: `__lt__` and `__gt__`

```python
class Quarterback:
    def __init__(self, yrds, tds, cmps, atts, ints, wins):
        self.wins = wins
        # NCAA passer rating
        self.rating = ((8.4 * yrds) + (330 * tds) + (100 * cmps) - (200 * ints)) / atts

    def __lt__(self, other):    # self < other
        return self.rating < other.rating or self.wins < other.wins

    def __gt__(self, other):    # self > other
        return self.rating > other.rating and self.wins > other.wins

peyton = Quarterback(yrds=4700, atts=679, cmps=450, tds=33, ints=17, wins=10)
eli = Quarterback(yrds=4002, atts=539, cmps=339, tds=31, ints=25, wins=9)

if peyton > eli:          # calls peyton.__gt__(eli)
    print("Peyton is the better QB")
elif peyton < eli:        # calls peyton.__lt__(eli)
    print("Eli is the better QB")
else:
    print("It is not clear who the better QB is...")
```

**Output:**

```
Peyton is the better QB
```

Peyton has both the higher rating (about 135.4 vs. 135.0) **and** more wins, so `__gt__` returns `True`.

### Arithmetic: `__sub__` with a type check

Here's a 24-hour time class where subtracting two times gives the distance between them. It uses `isinstance()` to make sure the other operand is also a `Time24`:

```python
class Time24:
    def __init__(self, hours, minutes):
        self.hours = hours
        self.minutes = minutes

    def __str__(self):
        return f"{self.hours:02d}:{self.minutes:02d}"   # always 2 digits

    def __gt__(self, other):
        if self.hours > other.hours:
            return True
        if self.hours == other.hours and self.minutes > other.minutes:
            return True
        return False

    def __sub__(self, other):
        """Absolute distance between two times."""
        if not isinstance(other, Time24):
            raise TypeError(f"cannot subtract {type(other).__name__} from Time24")

        if self > other:          # uses our own __gt__!
            larger, smaller = self, other
        else:
            larger, smaller = other, self

        hrs = larger.hours - smaller.hours
        mins = larger.minutes - smaller.minutes
        if mins < 0:              # borrow an hour
            mins += 60
            hrs -= 1

        return Time24(hrs, mins)  # return a brand-new Time24

time1 = Time24(3, 15)
time2 = Time24(2, 12)
print(f"Time difference: {time1 - time2}")
print(f"Time difference: {time2 - time1}")
print(f"Time difference: {Time24(3, 5) - Time24(1, 50)}")
```

**Output:**

```
Time difference: 01:03
Time difference: 01:03
Time difference: 01:15
```

Without the `isinstance()` check, `time1 - 5` would crash in a confusing way (`AttributeError: 'int' object has no attribute 'hours'`). With it, the error message tells you exactly what went wrong.

💡 To read the times from the user, split the input on `":"`:

```python
t1 = input("Enter time1 (hours:minutes): ")   # e.g. 3:15
tokens = t1.split(":")
time1 = Time24(int(tokens[0]), int(tokens[1]))
```

### A complete example: a 2D vector

```python
import math

class Vector:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"Vector({self.x}, {self.y})"

    def magnitude(self):
        return math.sqrt(self.x**2 + self.y**2)

    # comparisons (by length)
    def __lt__(self, other):      # v1 < v2
        return self.magnitude() < other.magnitude()

    def __ge__(self, other):      # v1 >= v2
        return self.magnitude() >= other.magnitude()

    # arithmetic
    def __add__(self, other):     # v1 + v2
        if isinstance(other, Vector):
            return Vector(self.x + other.x, self.y + other.y)
        raise TypeError("Can only add a Vector to another Vector")

    def __sub__(self, other):     # v1 - v2
        if isinstance(other, Vector):
            return Vector(self.x - other.x, self.y - other.y)
        raise TypeError("Can only subtract a Vector from another Vector")

    def __mul__(self, scalar):    # v1 * 2
        if isinstance(scalar, (int, float)):
            return Vector(self.x * scalar, self.y * scalar)
        raise TypeError("Can only multiply a Vector by a scalar (int or float)")

v1 = Vector(3, 4)   # length 5
v2 = Vector(1, 2)   # length ~2.24
v3 = Vector(6, 8)   # length 10

print(f"v1 < v2: {v1 < v2}")
print(f"v1 >= v2: {v1 >= v2}")
print(f"v1 < v3: {v1 < v3}")
print(f"v1 + v2: {v1 + v2}")
print(f"v1 - v2: {v1 - v2}")
print(f"v1 * 2: {v1 * 2}")
print(f"v2 * 0.5: {v2 * 0.5}")
```

**Output:**

```
v1 < v2: False
v1 >= v2: True
v1 < v3: True
v1 + v2: Vector(4, 6)
v1 - v2: Vector(2, 2)
v1 * 2: Vector(6, 8)
v2 * 0.5: Vector(0.5, 1.0)
```

---

## 📋 Quick Reference: Magic Methods

| Method | Called when you write |
|---|---|
| `__init__(self, ...)` | `MyClass(...)` (creating an object) |
| `__str__(self)` | `print(obj)`, `str(obj)`, `f"{obj}"` |
| `__eq__(self, other)` | `a == b` |
| `__ne__(self, other)` | `a != b` |
| `__lt__(self, other)` | `a < b` |
| `__le__(self, other)` | `a <= b` |
| `__gt__(self, other)` | `a > b` |
| `__ge__(self, other)` | `a >= b` |
| `__add__(self, other)` | `a + b` |
| `__sub__(self, other)` | `a - b` |
| `__mul__(self, other)` | `a * b` |
| `__truediv__(self, other)` | `a / b` |
| `__floordiv__(self, other)` | `a // b` |
| `__mod__(self, other)` | `a % b` |
| `__pow__(self, other)` | `a ** b` |
| `__abs__(self)` | `abs(a)` |
| `__int__(self)` | `int(a)` |
| `__float__(self)` | `float(a)` |

There are also `__and__` and `__or__`, for the `&` and `|` operators. The keywords `and` and `or` can't be overloaded.

---

## 🎉 Conclusion

You've just taken your first steps into object-oriented programming! 🙌

We covered:

* **classes** (blueprints) and **objects** (things built from them)
* the **constructor** `__init__` and the meaning of `self`
* **instance attributes** vs. shared **class attributes**
* methods, getters, setters, and internal helper methods
* `__str__` for readable printing
* **operator overloading** with `__eq__`, `__lt__`, `__gt__`, `__add__`, `__sub__`, and more

Try modelling something from your own life as a class: a playlist, a recipe, a game character. You'll be surprised how natural it feels.

Happy coding! 💻✨
