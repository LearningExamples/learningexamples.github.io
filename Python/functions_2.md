---
title: Python Functions 2 Tutorial
parent: Python
nav_order: 10
---


# 🐍 Python Functions, Part 2: Arguments, References, and Good Habits

Welcome back! 👋
In the first functions tutorial we covered the basics: `def`, parameters, `return`, scope, `*args`, `**kwargs`, and passing functions around.

This post picks up where that one left off. We'll look at the details that trip up almost every beginner:

* the difference between **calling** a function and **referring** to it
* functions that return **nothing** (`None`), and placeholders with `pass`
* functions that **call other functions**
* **default values** and **keyword arguments**
* what really happens when you pass a **list** into a function (this one is important!)

Let's go! 🚀

---

## 📞 1. Calling a Function vs. Referring to It

Parentheses make all the difference.

* `my_fun()` **runs** the function and gives you back whatever it returns.
* `my_fun` (no parentheses) is the **function object itself**.

```python
def my_fun():
    return 2

x = my_fun()   # calls the function -> x holds the returned value
y = my_fun     # no parentheses   -> y IS the function

print(x)
print(y)
print(type(x))
print(type(y))

# y is a function, so we can call it
print("calling y():", y())
```

**Output:**

```
2
<function my_fun at 0x7f72343ab060>
<class 'int'>
<class 'function'>
calling y(): 2
```

`x` is just the number `2`, so trying to call it fails:

```python
x()   # TypeError: 'int' object is not callable
```

> If you ever see `<function ... at 0x...>` printed where you expected a value, you forgot the parentheses!
{: .note }

---

## 🕳️ 2. Functions That Return Nothing (`None`)

Every Python function returns *something*. If you don't say what, it returns `None`.
That happens when:

* there is **no** `return` statement,
* there is a bare `return` with no value, or
* the body is just `pass`.

```python
def say_hello(x, y):
    print("Hello")
    return          # a bare return sends back None

result = say_hello(3, 9)
print(f"{result=}")
```

**Output:**

```
Hello
result=None
```

Compare that with a function that actually returns a value:

```python
def add_them(x, y):
    print("Hello")
    z = x + y
    return z

result = add_them(3, 9)
print(f"{result=}")
```

**Output:**

```
Hello
result=12
```

### 🚧 Placeholders: `pass` and `NotImplementedError`

When you're planning a program, it's handy to write the function *names* first and fill in the bodies later.
`pass` lets you create an empty function that does nothing:

```python
def steps_to_feet(num_steps):
    feet_per_step = 3
    feet = num_steps * feet_per_step
    return feet

def steps_to_calories(num_steps):
    pass   # TODO: write this later

steps = 33
print(f"Feet: {steps_to_feet(steps)}")
print(f"Calories: {steps_to_calories(steps)}")
```

**Output:**

```
Feet: 99
Calories: None
```

The problem with `pass` is that it fails **silently**: the program keeps going with `None`.
If you'd rather get a loud reminder that the code isn't finished yet, raise `NotImplementedError`:

```python
import math

def get_points(num_points):
    """Get num_points from the user. Return a list of (x, y) tuples."""
    raise NotImplementedError

def side_length(p1, p2):
    return math.sqrt((p2[0] - p1[0])**2 + (p2[1] - p1[1])**2)

def get_perimeter_length(points):
    perimeter = side_length(points[0], points[1])
    perimeter += side_length(points[0], points[2])
    perimeter += side_length(points[1], points[2])
    return perimeter

coordinates = get_points(3)   # 💥 NotImplementedError
print(f"Perimeter of triangle: {get_perimeter_length(coordinates)}")
```

This is a nice way to design a program **top-down**: write the overall plan as small function calls, then implement them one at a time.

---

## 🔗 3. Functions Calling Other Functions

A function can call another function. When it does, Python **jumps into** the inner function, runs it to the end, and then **comes back** to exactly where it left off.

```python
def my_fun(x, y):
    print(f"{x=}")
    print(f"{y=}")
    return x * y

def my_second(x, y):
    print("I am line 1 in my_second")
    w = my_fun(x, y)        # jump into my_fun, then come back with the result
    print(f"{w=}")
    print("I am line 4 in my_second")
    return -1

val = my_second(10, 20)
print(f"{val=}")
```

**Output:**

```
I am line 1 in my_second
x=10
y=20
w=200
I am line 4 in my_second
val=-1
```

Notice how the lines from `my_fun` appear *in the middle* of `my_second`'s output. That's the jump-in, jump-out behaviour.

You can also feed the result of one function straight into another:

```python
def func_1(x):
    return x * x

def func_2(w):
    y = func_1(w)     # 10 * 10 = 100
    return y * y      # 100 * 100

print(func_2(10))
```

**Output:**

```
10000
```

And a function can call another one many times, for example in a loop:

```python
def first_function():
    print("This is the first function.")

def second_function():
    print("This is the second function, calling the first function.")
    for i in range(3):
        first_function()

second_function()
```

**Output:**

```
This is the second function, calling the first function.
This is the first function.
This is the first function.
This is the first function.
```

---

## 🦆 4. One Function, Many Types

Python doesn't ask what *type* your arguments are. It only cares whether the operations inside the function make sense for them. (This is sometimes called **duck typing**: "if it walks like a duck and quacks like a duck…")

```python
def add(x, y):
    print(f"x is {x} and y is {y}")
    return x + y

print(f"add(5, 7) is {add(5, 7)}")
print(f"add('Tora', 'Bora') is {add('Tora', 'Bora')}")
print(f"add([1, 2], [3, 4]) is {add([1, 2], [3, 4])}")
```

**Output:**

```
x is 5 and y is 7
add(5, 7) is 12
x is Tora and y is Bora
add('Tora', 'Bora') is ToraBora
x is [1, 2] and y is [3, 4]
add([1, 2], [3, 4]) is [1, 2, 3, 4]
```

The same `+` adds numbers, joins strings, and concatenates lists.
But mixing types that don't support `+` together still fails:

```python
add(2, "hello")   # TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

---

## 🔁 5. Loops and Branches Inside Functions

Functions become really useful when they wrap up a loop or a decision.

### 📈 A step function

```python
def my_step_function(x):
    if x <= -5:
        y = -5
    elif x <= 0:      # we already know x > -5 here
        y = x
    elif x < 3:       # we already know x > 0 here
        y = 0
    elif x <= 5:      # we already know x >= 3 here
        y = -x
    else:
        y = -5
    return y

for x in range(-6, 7):
    print(f"x: {x:>2}   f(x): {my_step_function(x):>2}")
```

**Output:**

```
x: -6   f(x): -5
x: -5   f(x): -5
x: -4   f(x): -4
x: -3   f(x): -3
x: -2   f(x): -2
x: -1   f(x): -1
x:  0   f(x):  0
x:  1   f(x):  0
x:  2   f(x):  0
x:  3   f(x): -3
x:  4   f(x): -4
x:  5   f(x): -5
x:  6   f(x): -5
```

💡 In an `if/elif` chain, every `elif` already knows the conditions above it were `False`, so you don't need to write `x > -5 and x <= 0`. Just `x <= 0` is enough.

### 🔢 Power with a loop

```python
def my_pow(x, y):
    """Returns x to the power y using a loop."""
    result = 1
    for i in range(y):
        result = result * x
    return result

print(my_pow(7, 3))   # 7 * 7 * 7
```

**Output:**

```
343
```

### 🔺 Drawing shapes

```python
def print_pyramid(rows, char='*'):
    """Prints a pyramid with the given number of rows."""
    for i in range(rows):
        print(' ' * (rows - i - 1), end='')   # leading spaces
        print(char * (2 * i + 1))             # the characters

print_pyramid(5, '#')
```

**Output:**

```
    #
   ###
  #####
 #######
#########
```

A function can also *choose* which other function to call:

```python
def my_printer(n):
    for i in range(n // 2):
        print("O" * i)
    for i in range(n // 2, 0, -1):
        print("O" * i)

def draw(x):
    if x < 7:
        my_printer(x)
    else:
        print_pyramid(x)

draw(6)
```

**Output** (the first line is empty because `"O" * 0` is an empty string):

```

O
OO
OOO
OO
O
```

---

## 📝 6. Constants and Docstrings

Values that never change are often written in `ALL_CAPS` at the top of a file. Functions can read them like any other global variable.

```python
CM_PER_INCH = 2.54
INCHES_PER_FOOT = 12

def height_US_to_cm(feet, inches):
    """Converts a height in feet and inches to centimeters.

    Args:
        feet: whole feet.
        inches: remaining inches.

    Returns:
        The height in centimeters.
    """
    total_inches = feet * INCHES_PER_FOOT + inches
    cm = total_inches * CM_PER_INCH
    return cm

print(f"Centimeters: {height_US_to_cm(6, 4)}")
```

**Output:**

```
Centimeters: 193.04
```

The triple-quoted text right under `def` is the **docstring**. Python stores it, and `help()` shows it to anyone using your function:

```python
help(height_US_to_cm)
```

**Output:**

```
Help on function height_US_to_cm in module __main__:

height_US_to_cm(feet, inches)
    Converts a height in feet and inches to centimeters.

    Args:
        feet: whole feet.
        inches: remaining inches.

    Returns:
        The height in centimeters.
```

---

## ⚙️ 7. Default Values and Keyword Arguments

### Default values

A parameter with `=` has a **default value**. If the caller doesn't pass it, the default is used.

```python
def usd_cad(d, rate=1.4):   # if no rate is passed, rate is 1.4
    return d * rate

x = 100
print(f"{x} usd is {usd_cad(x)}")              # default rate
print(f"{x} usd is {usd_cad(x, 1.9)}")         # positional
print(f"{x} usd is {usd_cad(x, rate=1.9)}")    # keyword
print(f"{x} usd is {usd_cad(rate=1.9, d=x)}")  # keywords, any order
```

**Output:**

```
100 usd is 140.0
100 usd is 190.0
100 usd is 190.0
100 usd is 190.0
```

### Keyword arguments

When you call a function, you can name the parameters: `rate=1.9`. These are **keyword arguments**, and they have two big advantages:

1. The call is easier to read.
2. You can skip some defaults and change only the one you care about.

```python
def my_fun(x, w, y=999, z=100):
    print(f"{x = }, {w = }, {y = }, {z = }")

my_fun(1, 2)
my_fun(1, 2, 12)
my_fun(1, 2, 12, -10)
my_fun(1, 2, z=-10)    # keep y's default, change only z
```

**Output:**

```
x = 1, w = 2, y = 999, z = 100
x = 1, w = 2, y = 12, z = 100
x = 1, w = 2, y = 12, z = -10
x = 1, w = 2, y = 999, z = -10
```

The last call is the only way to change `z` without also passing `y`.

Defaults also work well for on/off switches:

```python
def print_date(day, month, year, american=True):
    if american:
        print(f"{month}/{day}/{year}")
    else:
        print(f"{day}/{month}/{year}")

print_date(30, 7, 2012)                  # 7/30/2012
print_date(30, 7, 2012, american=False)  # 30/7/2012
```

### 📏 The rules

> 1. **When defining:** parameters with defaults must come **after** parameters without defaults. `def f(a=1, b):` is a `SyntaxError`.
> 2. **When calling:** positional arguments must come **before** keyword arguments. `usd_cad(d=x, 1.9)` is a `SyntaxError`.
> 3. **Don't fill a parameter twice.** `usd_cad(1.9, d=x)` raises `TypeError: usd_cad() got multiple values for argument 'd'`.
{: .warning }

---

## 🧠 8. How Arguments Are Passed: Immutable vs. Mutable (Important!)

This is one of the most important ideas in this whole post.

When you call a function, Python doesn't copy your data. It gives the function a **reference to the same object**. What happens next depends on whether the object can change.

### Case 1: an integer (immutable)

```python
def my_fun(x):
    print(f"\tx: {x}")
    x = 5               # x now points to a NEW object; y is untouched
    print(f"\tx: {x}")

y = 10
print(f"y: {y}")
my_fun(y)
print(f"y: {y}")
```

**Output:**

```
y: 10
	x: 10
	x: 5
y: 10
```

`x = 5` just makes the local name `x` point somewhere else. The caller's `y` never changes.

### Case 2: a list (mutable)

```python
def my_fun(x):
    print(f"\tx: {x}")
    x[0] = 10           # changes the SAME list that y points to
    print(f"\tx: {x}")

y = [1, 2, 3]
print(f"y: {y}")
my_fun(y)
print(f"y: {y}")
```

**Output:**

```
y: [1, 2, 3]
	x: [1, 2, 3]
	x: [10, 2, 3]
y: [10, 2, 3]
```

This time `y` **did** change. `x` and `y` are two names for one list, and `x[0] = 10` modified that list.

### A practical example: swapping items

```python
def print_swap(list_to_modify):
    temp = list_to_modify[0]
    list_to_modify[0] = list_to_modify[-1]
    list_to_modify[-1] = temp
    print(f"Swapped: {list_to_modify}")

items_list = [1, 2, 3, 4, 5]
print(f"Original: {items_list}")
print_swap(items_list)
print(f"Original: {items_list}")
```

**Output:**

```
Original: [1, 2, 3, 4, 5]
Swapped: [5, 2, 3, 4, 1]
Original: [5, 2, 3, 4, 1]
```

Surprise! The "original" list was changed too.

### ✅ The fix: pass a copy

If you don't want the function to touch your list, give it a **copy**. Either of these works:

```python
print_swap(items_list[:])      # way 1: slice the whole list
print_swap(items_list.copy())  # way 2: the copy() method
```

```python
items_list = [1, 2, 3, 4, 5]
print(f"Original: {items_list}")
print_swap(items_list[:])
print(f"Original: {items_list}")
```

**Output:**

```
Original: [1, 2, 3, 4, 5]
Swapped: [5, 2, 3, 4, 1]
Original: [1, 2, 3, 4, 5]
```

> **Modifying** a mutable object inside a function (`x[0] = 10`, `x.append(...)`) is visible to the caller.
> **Reassigning** the parameter (`x = [99]`) is not. It only changes the local name.
{: .note }

### 🧪 Bonus: `+=` on tuples vs. lists

You can watch this happen with `id()`, which tells you an object's identity (roughly, its memory address).

```python
a = (1, 2, 4)
print(id(a))
a += (1, 2)      # tuples are immutable, so this builds a NEW tuple
print(id(a))     # different id
print(a)         # (1, 2, 4, 1, 2)

b = [1, 2, 4]
print(id(b))
b += [1, 2]      # lists are mutable, so this changes the SAME list
print(id(b))     # same id
print(b)         # [1, 2, 4, 1, 2]
```

The two results look the same, but the tuple version created a brand-new object while the list version extended the existing one. And you still can't do `a[1] = 3` on the tuple: `TypeError: 'tuple' object does not support item assignment`.

---

## 🗂️ 9. Peeking at the Global Namespace

Every variable and function you define at the top level of your program lives in the **global namespace**. `globals()` returns it as a dictionary:

```python
my_var = "This is a variable"

def my_func():
    pass

print("my_var" in globals())    # True
print("my_func" in globals())   # True
```

This is mostly a curiosity, but it shows that a function name is just another name in a dictionary, like any variable.

---

## 📋 Quick Reference Cheat Sheet

| Concept | Example | Remember |
|---|---|---|
| Call vs. reference | `f()` vs. `f` | Parentheses run the function |
| No return value | `return`, `pass`, or nothing | The result is `None` |
| Unfinished function | `raise NotImplementedError` | Fails loudly instead of returning `None` |
| Docstring | `"""..."""` under `def` | Shown by `help(f)` |
| Default value | `def f(a, b=2):` | Defaults go **last** |
| Keyword argument | `f(1, b=5)` | Positional **before** keyword |
| Passing an int/str | `f(10)` | The function can't change the caller's value |
| Passing a list/dict | `f(my_list)` | The function **can** change it |
| Protect your list | `f(my_list[:])` or `f(my_list.copy())` | The function gets a copy |

---

## 🎉 Conclusion

You now know what's really going on under the hood when you call a function:

* `f()` runs the function and `f` *is* the function
* every function returns something, even if that something is `None`
* `pass` and `NotImplementedError` help you build programs step by step
* default values and keyword arguments make functions flexible and readable
* lists and dictionaries are passed **by reference**, so copy them if you need to protect the original

Master these, and your functions will behave exactly the way you expect. 🙌

Happy coding! 💻✨
