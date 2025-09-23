---
layout: default
title: Python Basics - From Input to Conditionals
parent: Python
nav_order: 4
---

# Python Basics: From User Input to Conditional Logic

Today, we're going to walk through some fundamental building blocks of programming in Python. We'll start with how to get information from a user, understand how Python handles different data types.

Let's get started!

---

## Getting User Input and Changing Its Type

One of the most basic things a program can do is interact with a user. In Python, we use the `input()` function for this. But there's a small catch: `input()` **always** gives us the data as a string (text). What if we want to do math with it? We have to convert it! This process is called **type casting**.

### Example 1: From String to Float to Integer

Let's see this in action. We'll ask the user for a number, convert it to a floating-point number (a number with a decimal), and then to an integer.

```python
# Ask the user for input
input_text = input("Enter a number: ")

# Convert the string input to a float (decimal number)
float_variable = float(input_text)

# Convert the float to an integer
int_variable = int(float_variable)

print(f"Original input text: {input_text}")
print(f"Input text converted to a float: {float_variable}")
print(f"Float variable converted to an int: {int_variable}")
````

If you run this code and enter `12.6`, you'll see this output:

```
original input text: 12.6
input text converted to a float: 12.6
float variable converted to an int: 12
```

**Key Takeaways:**

  * `input()` captures user input as a string. `type(input_text)` would be `<class 'str'>`.
  * `float()` converts a string or integer into a floating-point number.
  * `int()` converts a string or float into an integer. Notice that it **truncates** (chops off) the decimal part, it **does not round**. So `12.6` becomes `12`, not `13`.

> **Heads Up\!** ⚠️
> If you try to convert a non-numeric string to a number, you'll get a `ValueError`. For example, `int("hello")` will crash your program. Always be mindful of the data you're trying to convert\!

-----

## A Quick Dive into Number Systems

Computers don't think in the decimal (base-10) system we use every day. Their native language is **binary** (base-2), which uses only two digits: 0 and 1. As a programmer, it's useful to know how to convert between these systems.

### Example 2: Binary ↔ Decimal Conversion

Python makes this super easy with its built-in functions.

```python
# Binary to decimal conversion
binary_number = "1101"
# The '2' tells the int() function the number is in base-2
decimal_number = int(binary_number, 2)
print(f"Binary: {binary_number}, Decimal: {decimal_number}")

# Decimal to binary conversion
decimal_to_convert = 13
# The bin() function converts a decimal to its binary representation
binary_converted = bin(decimal_to_convert)[2:] # We slice with [2:] to remove the '0b' prefix
print(f"Decimal: {decimal_to_convert}, Binary: {binary_converted}")
```

Output:

```
Binary: 1101, Decimal: 13
Decimal: 13, Binary: 1101
```

**How it Works:**

  * To convert **to decimal**, we use `int(number_string, base)`. The base tells Python what number system the string is in.
  * To convert **from decimal**, we use `bin()` for binary, `oct()` for octal, and `hex()` for hexadecimal. These functions add a prefix (`0b`, `0o`, `0x`) to the result, which we often slice off using `[2:]` for a cleaner look.

-----
