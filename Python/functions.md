---
title: Python Functions Tutorial
parent: Python
nav_order: 8
---


# 🐍 Mastering Python Functions: Your Ultimate Guide

Hey everyone! 👋 Welcome to the blog.
Today, we’re diving deep into one of the most fundamental and powerful concepts in Python — **functions**. 🚀

Functions are reusable blocks of code that perform specific tasks. Think of them as mini-programs within your main program. Using them makes your code more **organized**, **readable**, **reusable**, and **easier to debug**.

Let’s get started!

---

## 🧩 1. The Basics: Defining and Calling Functions

You define a function using the `def` keyword, followed by the function’s name, parentheses `()`, and a colon `:`.
The code *inside* the function must be indented.

### 🧮 A Simple Sum

Let’s start with a simple function that sums up all the numbers in a list.

```python
def sum_list(numbers):
    """Calculates the sum of a list of numbers using a for loop.

    Args:
        numbers: A list of numbers.

    Returns:
        The sum of the numbers in the list.
    """
    total = 0
    for number in numbers:
        total += number  # same as total = total + number
    return total

my_list = [1, 2, 3, 4, 5]
result = sum_list(my_list)
print(f"The sum of the list {my_list} is: {result}")
```

**Output:**

```
The sum of the list [1, 2, 3, 4, 5] is: 15
```

Here, `numbers` is a **parameter** (input), and `total` is **returned** (output).
The triple quotes `"""..."""` form a **docstring**, which documents the function.

---

### 💰 A Practical Example: Calculating eBay Fees

Functions are perfect for encapsulating business logic.
Here’s a more complex example that calculates eBay fees based on a tiered percentage system:

```python
def calc_ebay_fee(sell_price):
    """Calculates the total eBay fee for fixed-price items
    (books, movies, music, or video games) based on the selling price.

    The fee includes a $0.50 listing fee plus tiered percentages:
    - 13% for amounts up to $50.00
    - 5% for $50.01–$1000.00
    - 2% for $1000.01 and above
    """
    p50 = 0.13
    p50_to_1000 = 0.05
    p1000 = 0.02
    fee = 0.50

    if sell_price <= 50:
        fee += sell_price * p50
    elif sell_price <= 1000:
        fee += (50 * p50) + ((sell_price - 50) * p50_to_1000)
    else:
        fee += (50 * p50) + ((1000 - 50) * p50_to_1000) + ((sell_price - 1000) * p1000)

    return fee

selling_price = 37.5
print(f"eBay fee: ${calc_ebay_fee(selling_price)}")
```

**Output:**

```
eBay fee: $5.375
```

All that logic is neatly tucked away in one place — now you can reuse `calc_ebay_fee()` anytime.

---

## 🔁 2. The `return` Statement

The `return` keyword does two things:

1. **Exits** the function immediately.
2. **Sends a value back** to where the function was called.

### 🚪 Return Exits Immediately

Any code after a `return` statement won’t run.

```python
def print_fun():
    print("Hello 1")
    return -1
    print("Hello 2")  # never runs
    print("Hello 3")  # never runs

x = print_fun()
print(f"The function returned: {x}")
```

**Output:**

```
Hello 1
The function returned: -1
```

---

### 🎯 Returning Multiple Values

Need to return more than one value?
Just separate them with commas — Python packs them into a **tuple**.

```python
def get_multiple_values(a, b):
    """Returns the sum and product of two numbers."""
    return a + b, a * b

sum_result, product_result = get_multiple_values(5, 3)

print(f"The sum is: {sum_result}")
print(f"The product is: {product_result}")
```

**Output:**

```
The sum is: 8
The product is: 15
```

You can also assign the result to one variable (a tuple) or ignore values with `_`.

```python
B = get_multiple_values(5, 3)
print(f"B is a tuple: {B}")
print(f"Access B[0]: {B[0]} \t B[1]: {B[1]}")

A, _ = get_multiple_values(5, 3)
print(f"Just the sum: {A}")
```

---

## 🌐 3. Understanding Variable Scope

Scope defines where a variable can be accessed.

* **Global Scope:** Variables defined outside any function — accessible everywhere.
* **Local Scope:** Variables defined *inside* a function — accessible only there.

### 🔒 Local vs. Global

```python
def avg(a, b):
    tmp = (a + b) / 2.0  # local variable
    print(f"Inside function, tmp is: {tmp}")
    return tmp

a, b = 5, 10
tmp = a + b  # global variable

print(f"Avg: {avg(a, b)}")
print(f"Outside function, tmp is still: {tmp}")
```

**Output:**

```
Inside function, tmp is: 7.5
Avg: 7.5
Outside function, tmp is still: 15
```

The local `tmp` didn’t affect the global one.

---

### ⚠️ Modifying a Global Variable

If you *must* modify a global variable, use the `global` keyword.

```python
another_global_variable = 10
print(f"Outside, before call: {another_global_variable}")

def modify_global():
    global another_global_variable
    another_global_variable = 20
    print(f"Inside function: {another_global_variable}")

modify_global()
print(f"Outside, after call: {another_global_variable}")
```

**Output:**

```
Outside, before call: 10
Inside function: 20
Outside, after call: 20
```

Use this sparingly — returning values is usually cleaner.

---

## ⚙️ 4. Super-Flexible Arguments

Python lets you define functions that handle variable numbers of arguments.

### 🧍 Default Arguments

You can provide a default value for parameters, making them optional.

```python
def greet_person(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet_person("Alice")      # uses default
greet_person("Bob", "Hi")  # overrides default
```

**Output:**

```
Hello, Alice!
Hi, Bob!
```

---

### 🧩 `*args`: Variable Positional Arguments

Use `*args` to handle any number of positional arguments.

```python
def my_sum(*args):
    total = 0
    for arg in args:
        total += arg
    return total

print(my_sum(1, 2, 3))
print(my_sum(10, 20))
print(my_sum())
```

**Output:**

```
6
30
0
```

Fun example:

```python
def print_sandwich(bread, meat, *args):
    print(f"{meat} on {bread}", end=" ")
    if args:
        print("with", *args)
    else:
        print()

print_sandwich("white", "ham", "lettuce", "tomato")
print_sandwich("white", "turkey")
```

---

### 🧠 `**kwargs`: Variable Keyword Arguments

Use `**kwargs` to handle any number of **named** arguments (stored in a dictionary).

```python
def my_cal(s):
    print(f"Calculating calories for: {s}")

def print_fancy_sandwich(meat, bread, **kwargs):
    print(f"{meat} on {bread}")
    for category, extra in kwargs.items():
        if category == "sauce":
            my_cal(extra)
        print(f"   {category}: {extra}")
    print()

print_fancy_sandwich("turkey", "sourdough")
print_fancy_sandwich("turkey", "sourdough", sauce="mayo")
print_fancy_sandwich("ham", "wheat", sauce="mustard", veggie1="tomato", veggie2="lettuce")
```

**Output:**

```
turkey on sourdough

turkey on sourdough
Calculating calories for: mayo
   sauce: mayo

ham on wheat
Calculating calories for: mustard
   sauce: mustard
   veggie1: tomato
   veggie2: lettuce
```

---

## 🧱 5. Functions Are First-Class Objects

In Python, **functions are objects** — you can assign them to variables or pass them around like data.

### 🔁 Assigning Functions to Variables

```python
def myFun():
    print("Hello World")

myFun()  # normal call

bob = myFun  # assign function (no parentheses!)
bob()        # same effect
```

---

### 🧭 Passing Functions as Arguments

You can pass functions as parameters to other functions — super powerful.

```python
def apply_function(func, argument):
    func(argument)

def greet(name):
    print(f"Hello, {name}!")

def farewell(name):
    print(f"Goodbye, {name}!")

apply_function(greet, "Alice")
apply_function(farewell, "Bob")
```

**Output:**

```
Hello, Alice!
Goodbye, Bob!
```

You can even conditionally choose which function to run:

```python
def function_a():
    print("This is function A")

def function_b():
    print("This is function B")

condition = False

selected_function = function_a if condition else function_b
selected_function()
```

**Output:**

```
This is function B
```

---

## 🎉 Conclusion

Phew! That was a lot — but you made it. 🙌

We covered:

* Function basics (`def`, parameters, and `return`)
* Variable scope
* Flexible arguments (`*args`, `**kwargs`)
* First-class function behavior

Functions are the **building blocks** of clean, professional Python code.
Start using them to organize your projects, and you’ll never look back.

Happy coding! 💻✨
