---
title: 'Making Decisions in Python: A Guide to Conditional Logic'
parent: Python
nav_order: 5
---

# Making Decisions in Python: A Guide to Conditional Logic

So far, you've learned how to store data in variables and perform basic operations. But the real power of programming is unlocked when you can make your code think and make decisions. This is where your code starts getting smart.

Conditional logic allows a program to execute certain blocks of code only if a specific condition is met. It's like giving your program a brain 🧠. The main tools for this in Python are the `if`, `elif`, and `else` statements. Let's dive in!

---

## The Core Idea: `if`, `elif`, and `else`

Imagine you're writing a program to determine car insurance prices. The price heavily depends on the driver's age. You need a way to say, "**if** the user is this age, set this price, **else if** they are another age, set a different price, and **otherwise**, for everyone else, set a default price."

This is exactly what the `if-elif-else` structure does. It's a chain of conditions that Python checks one by one, from top to bottom.

**The Golden Rule:** In an `if-elif-else` chain, **only ONE** block of code will ever run—the very first one whose condition evaluates to `True`. Once Python finds a true condition, it executes that block and skips the rest of the chain completely.

Let's see it in action with our insurance example.

```python
# We always initialize our variable before the if-statement.
# This prevents errors if none of the conditions are met.
insurance_price = -1 

user_age = 28

if user_age <= 15:          # Condition 1: Is the user 15 or younger?
  print("Sorry, you are too young to be insured.")
  insurance_price = 0

elif user_age < 25:        # Condition 2: If not, is the user younger than 25?
  print("You are in the 16-24 age bracket.")
  insurance_price = 4800

elif user_age < 40:         # Condition 3: If not, is the user younger than 40? (This will be TRUE)
  print("You are in the 25-39 age bracket.")
  insurance_price = 2350

else:                      # Otherwise: This runs if all previous conditions were False.
  print("You are in the 40+ age bracket.")
  insurance_price = 2100

print(f"Your calculated annual price is: ${insurance_price}")
````

**Output:**

```
You are in the 25-39 age bracket.
Your calculated annual price is: $2350
```

Because `user_age` is 28, the first two conditions (`<= 15` and `< 25`) were false. The third condition (`< 40`) was true, so its code block was executed, and the final `else` was skipped.

-----

## Building Conditions: Comparison Operators

The conditions we test in an `if` statement are built using **comparison operators**. They compare two values and return a Boolean value: either `True` or `False`.

Here are the main ones:

  - `a < b`: Is `a` less than `b`?
  - `a > b`: Is `a` greater than `b`?
  - `a <= b`: Is `a` less than or equal to `b`?
  - `a >= b`: Is `a` greater than or equal to `b`?
  - `a == b`: Is `a` **exactly equal** to `b`? (Note the double equals\!)
  - `a != b`: Is `a` **not equal** to `b`?

You can even chain them for more readable range checks:

```python
x = 10

# These expressions evaluate to True or False
print(f"Is x < 5? {x < 5}")                 # Evaluates to False
print(f"Is 5 < x < 12? {5 < x < 12}")   # This is a cool Python feature! It's the same as (x > 5 and x < 12), evaluates to True
```

**Output:**

```
Is x < 5? False
Is 5 < x < 12? True
```

-----

## A Classic Example: The Modulo Operator (`%`)

A perfect way to practice `if-else` is to check if a number is even or odd. The best tool for this job is the **modulo operator (`%`)**, which gives you the remainder of a division.

  - An **even** number divided by 2 has a remainder of `0`.
  - An **odd** number divided by 2 has a remainder of `1`.

Let's use this with a simple `if-else` statement.

```python
user_num = 7

div_remainder = user_num % 2 # 7 divided by 2 is 3 with a remainder of 1

if div_remainder == 0: # Is the remainder equal to 0? (False for 7)
    print(f"{user_num} is even.")
else: # If the 'if' condition was false, execute this block.
    print(f"{user_num} is odd.")
```

**Output:**

```
7 is odd.
```

You could also check if the remainder is *not equal to* zero using `!=`:

```python
user_num = 7
div_remainder = user_num % 2

if div_remainder != 0: # Is the remainder not equal to 0? (True for 7)
    print(f"{user_num} is odd.")
else:
    print(f"{user_num} is even.")
```

-----

## A Common Pitfall: `if-elif-else` vs. Multiple `if`s

This is one of the most common sources of bugs for new programmers. 🪲 What's the difference between chaining conditions with `elif` and just writing a series of separate `if` statements?

  - **`if-elif-else`**: Evaluates conditions in order and executes **only the first one** that is `True`. It's for choosing one path out of several mutually exclusive options.
  - **Multiple `if`s**: Each `if` statement is evaluated **independently**. This means multiple `if` blocks can execute if their conditions are all met. It's for checking several different, unrelated conditions.

Let's see this critical difference with a deceptive example.

### Case 1: The `if-elif-else` Chain

Here, only one block can possibly run.

```python
user_input = 10

if user_input <= 11:
  print("AAAA")
  user_input = 20
elif user_input >= 12:
  print("BBBB")
else:
  print("CCCC")
```

**Output:**

```
AAAA
```

The first condition (`10 <= 11`) is true, so "AAAA" is printed. The program then skips the `elif` and `else` entirely. It doesn't matter that we changed `user_input` to 20 inside the block.

### Case 2: Separate `if` Statements

Now, watch what happens when we use two separate `if` statements.

```python
user_input = 10

# --- First, independent 'if' statement ---
if user_input <= 11:
  print("AAAA")
  # CRITICAL: We modify the variable here!
  user_input = 20

# --- Second, independent 'if' statement ---
# Python now checks this condition with the NEW value of user_input (20)
if user_input >= 12:
  print("BBBB")
else:
  print("CCCC")
```

**Output:**

```
AAAA
BBBB
```

This is a completely different result\!

1.  The first `if` condition (`10 <= 11`) is true. "AAAA" is printed, and `user_input` is updated to `20`.
2.  The program continues. It now evaluates the *second* `if` statement.
3.  The condition is now `20 >= 12`, which is also true\! So, "BBBB" is also printed.

> **Key Takeaway:** Use `if-elif-else` when you need to select just one out of many options. Use multiple, separate `if` statements when you need to check for several different things that could all happen independently. ✅

-----

## Combining Conditions: Logical Operators (`and`, `or`, `not`)

What if a decision depends on more than one condition? For example, "you can drive if you are over 18 **and** you have a license." Python's logical operators are here to help.

  - `and`: Returns `True` only if **both** conditions are `True`.
  - `or`: Returns `True` if **at least one** of the conditions is `True`.
  - `not`: Inverts the boolean value (`True` becomes `False`, `False` becomes `True`).

<!-- end list -->

```python
# Basic examples
print(f"True or False: {True or False}")   # True
print(f"True and False: {True and False}") # False
print(f"not True: {not True}")             # False

# A practical example
age = 30
has_license = True

if age >= 18 and has_license:
  print("You can drive.")

is_sunny = False
if not is_sunny:
  print("It's not sunny today, better bring a jacket.")
```

**Output:**

```
True or False: True
True and False: False
not True: False
You can drive.
It's not sunny today, better bring a jacket.
```

-----

## Nested Conditionals

You can also place `if` statements inside other `if` statements. This is called nesting. It's useful for checking a secondary condition only if a primary condition is met first.

```python
temperature = 25
is_raining = True

if temperature > 20:
  print("It's warm today.")
  
  # This inner 'if' only gets checked because the outer 'if' was true
  if is_raining:
    print("It's also raining, so bring an umbrella. ☂️")
  else:
    print("It's not raining, enjoy the weather!")
    
else:
  print("It's cool today.")
```

**Output:**

```
It's warm today.
It's also raining, so bring an umbrella. ☂️
```

> **Pro Tip:** While nesting works, it can sometimes make code harder to read. Often, you can achieve the same result more cleanly using logical operators. The example above could be rewritten like this:

```python
temperature = 25
is_raining = True

if temperature > 20 and is_raining:
  print("It's warm and raining today, so bring an umbrella. ☂️")
elif temperature > 20 and not is_raining:
  print("It's warm today and not raining!")
else:
  print("It's cool today.")
```

-----

## Checking for Membership: The `in` and `not in` Operators

A very common and "Pythonic" task is to check if an item exists within a collection (like a list, a string, or a dictionary). The `in` and `not in` operators make this incredibly easy and readable.

### Checking a List

Let's see if a player is on a team roster.

```python
barcelona_fc_roster = ["Alves", "Messi", "Fabregas"]

name = input("Enter a player's name to check: ")

if name in barcelona_fc_roster:
    print(f"Found {name} on the roster!")
else:
    print(f"Could not find {name} on the roster.")
```

### Checking a String

You can also check if a smaller string (a substring) exists within a larger one.

```python
request_str = "GET index.html HTTP/1.1"

if "/1.1" in request_str:
    print("HTTP protocol 1.1 was used.")

if "HTTPS" not in request_str:
    print("Warning: This was an unsecured connection.")
```

### Checking a Dictionary (A Special Case\!)

Checking a dictionary with `in` has a specific behavior: **by default, it only checks the keys.**

```python
my_dict = {"A": 1, "B": 2, "C": 3}

# Check if a KEY exists
if "B" in my_dict:
   print("Found 'B' as a key.") # This will be True

# Check if a VALUE exists - this requires using .values()
if 3 in my_dict.values():
    print("Found 3 in the dictionary's values.")

# Check if a KEY-VALUE PAIR exists - this requires using .items()
if ("C", 3) in my_dict.items():
    print("Found the item ('C', 3) in the dictionary.")
```

**Output:**

```
Found 'B' as a key.
Found 3 in the dictionary's values.
Found the item ('C', 3) in the dictionary.
```

-----

## Conclusion

Great job\! 🚀 You've just covered some of the most important concepts in Python: using conditional logic with `if`, `elif`, and `else` to make your programs intelligent. You've also learned how to build powerful conditions with comparison, logical, and membership operators.

