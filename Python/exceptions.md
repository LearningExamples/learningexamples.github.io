---
title: Python Exception Handling
parent: Python
nav_order: 13
---


# 🛡️ Python Exception Handling: Writing Programs That Don't Crash

Every programmer's code meets bad input eventually. A user types `"hello"` where you expected a number, a list is shorter than you thought, or someone divides by zero.
When Python hits a problem like that, it raises an **exception**, and if nobody handles it, the program stops immediately.

In this tutorial you'll learn how to:

* recognize the most common exceptions
* choose between **checking first** and **handling the error**
* use `try` and `except` (including several `except` blocks)
* raise your own exceptions with `raise`
* understand how exceptions travel out of functions
* clean up with `else` and `finally`
* define your own exception types

Let's make our programs bulletproof! 🚀

---

## 1. What Is an Exception? 💥

An **exception** is Python's way of saying "something went wrong while running this line". Here are three you'll see all the time:

```python
int("hello")
# ValueError: invalid literal for int() with base 10: 'hello'
```

```python
arr = [1, 2, 3]
print(arr[10])
# IndexError: list index out of range
```

```python
x = 10 / 0
# ZeroDivisionError: division by zero
```

None of these are *syntax* mistakes. The code is valid Python. The problem only shows up **at runtime**, often because of the data the program received.

---

## 2. Two Strategies for Bad Input 🧭

There are two ways to deal with situations like these:

1. **Defensive programming:** check the values *before* doing the risky operation (`if b != 0: ...`).
2. **Exception handling:** just try the operation, and *catch* the exception if it happens.

Both are valid, and real programs use both. Let's see each one in action.

---

## 3. Your First `try` / `except` 🎯

The pattern is simple:

* put the risky code inside `try`
* put the recovery code inside `except`

```python
try:
    result = 10 / 0
    print(result)            # never runs: the line above raised an exception
except ZeroDivisionError:
    print("Error: Cannot divide by zero!")
```

**Output:**

```
Error: Cannot divide by zero!
```

As soon as an exception happens inside `try`, Python **skips the rest of the `try` block** and jumps to the matching `except`. Then the program carries on normally.

---

## 4. Example: A Salary Calculator 💵

### Version 1: no protection

```python
hourly_rate = 20

while True:
    hours_input = input("Enter hours worked (or 'done' to quit): ")

    if hours_input.lower() == "done" or hours_input.lower() == "quit":
        print("Exiting salary calculator.")
        break

    hours = float(hours_input)
    salary = hours * hourly_rate
    print(f"Calculated salary: ${salary:.2f}")
```

This works as long as the user behaves:

```
Enter hours worked (or 'done' to quit): 22
Calculated salary: $440.00
Enter hours worked (or 'done' to quit): hello
ValueError: could not convert string to float: 'hello'
```

One typo and the whole program crashes. 😬

### Version 2: with `try` / `except`

```python
hourly_rate = 20

while True:
    hours_input = input("Enter hours worked (or 'done' to quit): ")

    if hours_input.lower() == "done" or hours_input.lower() == "quit":
        print("Exiting salary calculator.")
        break

    try:
        hours = float(hours_input)
        salary = hours * hourly_rate
        print(f"Calculated salary: ${salary:.2f}")
    except ValueError:
        print("Invalid input. Please enter a number for hours or 'done' to quit.")
```

**Output:**

```
Enter hours worked (or 'done' to quit): 22
Calculated salary: $440.00
Enter hours worked (or 'done' to quit): abc
Invalid input. Please enter a number for hours or 'done' to quit.
Enter hours worked (or 'done' to quit): done
Exiting salary calculator.
```

Now a bad entry just prints a message and the loop asks again.

> You'll sometimes see a bare `except:` with no exception type. It catches **everything**, including typos in your own code (like a misspelled variable name), which makes bugs very hard to find. Name the exception you expect, as in `except ValueError:`.
{: .warning }

### Version 3: the defensive alternative

Here's the same idea written with `if` checks instead of exceptions. It only accepts whole numbers (optionally negative):

```python
hourly_rate = 20

while True:
    hours_input = input("Enter hours worked (or 'done' to quit): ")

    if hours_input.lower() == "done" or hours_input.lower() == "quit":
        print("Exiting salary calculator.")
        break

    is_valid_integer = False
    processed_hours = 0.0

    if hours_input.startswith("-"):
        # check that everything after the "-" is a digit
        if hours_input[1:].isdigit():
            is_valid_integer = True
            processed_hours = float(hours_input)
    elif hours_input.isdigit():
        is_valid_integer = True
        processed_hours = float(hours_input)

    if is_valid_integer:
        salary = processed_hours * hourly_rate
        print(f"Calculated salary: ${salary:.2f}")
    else:
        print("Invalid input. Please enter a whole number for hours or 'done' to quit.")
```

It works, but it's longer, and it *still* rejects perfectly good input like `7.5`. Letting `float()` do the checking and catching its `ValueError` is usually simpler.

| | Defensive (`if` checks) | Exception handling (`try`) |
|---|---|---|
| **Idea** | "Look before you leap" | "Ask forgiveness, not permission" |
| **Good for** | Simple, predictable checks (`b != 0`) | Complex checks Python already does (`float("7.5")`) |
| **Downside** | Can get long and miss cases | Needs you to know which exception to catch |

---

## 5. Common Exception Types 📚

| Exception | Raised when... |
|---|---|
| `ValueError` | The type is right but the value is wrong, e.g. `int("hello")` |
| `TypeError` | An operation gets the wrong type, e.g. `10 / "two"` |
| `ZeroDivisionError` | You divide by zero |
| `IndexError` | A list index is out of range |
| `KeyError` | A dictionary key doesn't exist |
| `NameError` | You use a variable that was never defined |
| `AttributeError` | An object doesn't have the attribute or method you asked for |
| `FileNotFoundError` | You try to open a file that doesn't exist |
| `EOFError` | `input()` reaches the end of the input without reading anything |

---

## 6. Handling Several Exceptions 🧩

One `try` block can fail in different ways. Add one `except` per exception type to handle each differently.

```python
while True:
    num1 = 12
    num2_input = input("Enter the second number (or 'done' to quit): ")

    if num2_input.lower() in ("done", "quit"):
        print("Exiting calculation.")
        break

    try:
        num2 = float(num2_input)
        result = num1 / num2
        print(f"Result: {result}")
    except ValueError:
        print("Invalid input. Please enter a valid number.")
    except ZeroDivisionError:
        print("You cannot divide by zero.")
    except Exception as e:          # anything else we didn't expect
        print(f"An unexpected error occurred: {e}")
```

Try it with `4`, `0`, and `hello`:

```
Enter the second number (or 'done' to quit): 4
Result: 3.0
Enter the second number (or 'done' to quit): 0
You cannot divide by zero.
Enter the second number (or 'done' to quit): hello
Invalid input. Please enter a valid number.
Enter the second number (or 'done' to quit): done
Exiting calculation.
```

### `as e`: getting the error message

`except SomeError as e:` stores the exception object in `e`. Printing it shows Python's message:

```python
try:
    num = float("hello")
except ValueError as e:
    print(f"Problem: {e}")
```

**Output:**

```
Problem: could not convert string to float: 'hello'
```

> Python checks the `except` blocks **from top to bottom** and runs only the first one that matches. Put specific exceptions first and the catch-all `except Exception` **last**, or it will swallow everything before the specific ones get a chance.
{: .note }

---

## 7. Raising Your Own Exceptions with `raise` 🚩

Sometimes a value is a perfectly valid *type* but makes no sense for *your* program. A negative age is still a number, so Python won't complain. You can raise the exception yourself:

```python
age = int(input("Enter your age: "))

if age < 0:
    raise ValueError("Age cannot be negative.")

print("Age is valid:", age)
```

### Salary calculator, final version

Now we can treat "not a number" and "negative number" the same way:

```python
hourly_rate = 20

while True:
    hours_input = input("Enter hours worked (or 'done' to quit): ")

    if hours_input.lower() == "done" or hours_input.lower() == "quit":
        print("Exiting salary calculator.")
        break

    try:
        hours = float(hours_input)

        if hours < 0:
            raise ValueError("Hours worked cannot be negative.")

        salary = hours * hourly_rate
        print(f"Calculated salary: ${salary:.2f}")
    except ValueError as e:     # catches BOTH the float() error and our own
        print(f"Invalid input: {e}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")
```

**Output:**

```
Enter hours worked (or 'done' to quit): 22
Calculated salary: $440.00
Enter hours worked (or 'done' to quit): hello
Invalid input: could not convert string to float: 'hello'
Enter hours worked (or 'done' to quit): -9
Invalid input: Hours worked cannot be negative.
Enter hours worked (or 'done' to quit): done
Exiting salary calculator.
```

---

## 8. Exceptions and Functions 📤

If an exception happens inside a function and the function doesn't handle it, the exception **travels back up** to whoever called the function. The caller can catch it there.

```python
def divide(a, b):
    return a / b          # no try here

try:
    print(divide(10, 0))  # the exception comes out of divide()...
except ZeroDivisionError:
    print("Cannot divide by zero.")   # ...and is caught here
```

**Output:**

```
Cannot divide by zero.
```

This lets you split the work nicely: the **function** checks the rules and raises, and the **main program** decides what to tell the user.

```python
def calculate_salary_safe(hours, hourly_rate):
    if hours < 0:
        raise ValueError("Hours worked cannot be negative.")
    return hours * hourly_rate


hourly_rate = 20

while True:
    hours_input = input("Enter hours worked (or 'done' to quit): ")

    if hours_input.lower() == "done" or hours_input.lower() == "quit":
        print("Exiting salary calculator.")
        break

    try:
        hours = float(hours_input)
        calculated_salary = calculate_salary_safe(hours, hourly_rate)
        print(f"Calculated salary: ${calculated_salary:.2f}")
    except ValueError as e:
        print(f"Invalid input: {e}")
```

**Output:**

```
Enter hours worked (or 'done' to quit): -6
Invalid input: Hours worked cannot be negative.
Enter hours worked (or 'done' to quit): done
Exiting salary calculator.
```

---

## 9. `else` and `finally` 🧹

A `try` statement can have two more optional parts:

| Block | When does it run? |
|---|---|
| `try` | Always runs first |
| `except` | Only if an exception happened (and matched) |
| `else` | Only if **no** exception happened |
| `finally` | **Every time**, no matter what |

### `else`: the "it worked" branch

```python
try:
    num = int(input("Enter a whole number: "))
except ValueError:
    print("That was not a whole number.")
else:
    print(f"Great! Double your number is {num * 2}")
```

Putting the "success" code in `else` keeps the `try` block small, so you only catch errors from the line you actually expect to fail.

### `finally`: cleanup that always happens

```python
print("--- Starting division attempt ---")

num1 = 10
num2_input = input("Enter a number to divide by (or 'error' to force a TypeError): ")

try:
    if num2_input.lower() == "error":
        result = num1 / "invalid_type"     # force a TypeError
    else:
        num2 = float(num2_input)
        result = num1 / num2
        print(f"Division successful. Result: {result}")
except ZeroDivisionError:
    print("Error: Cannot divide by zero!")
except ValueError:
    print("Error: Invalid input. Please enter a valid number.")
except TypeError:
    print("Error: Detected invalid type for division.")
except Exception as e:
    print(f"An unexpected error occurred: {e}")
finally:
    print("--- Division attempt finished ---")
```

**Output** (entering `0`):

```
--- Starting division attempt ---
Enter a number to divide by (or 'error' to force a TypeError): 0
Error: Cannot divide by zero!
--- Division attempt finished ---
```

Whatever you type, the last line always prints.

### `finally` inside a function

`finally` runs even when the function `return`s from inside `try` or `except`:

```python
def divide_numbers(a, b):
    try:
        result = a / b
        print(f"Division successful. Result: {result}")
        return result
    except ZeroDivisionError:
        print("Error: Cannot divide by zero!")
        return None
    except TypeError:
        print("Error: Both inputs must be numbers!")
        return None
    finally:
        print("--- Division attempt finished ---")

print("Attempt 1: Valid division")
divide_numbers(10, 2)

print("\nAttempt 2: Division by zero")
divide_numbers(10, 0)

print("\nAttempt 3: Invalid input type")
divide_numbers(10, "hello")
```

**Output:**

```
Attempt 1: Valid division
Division successful. Result: 5.0
--- Division attempt finished ---

Attempt 2: Division by zero
Error: Cannot divide by zero!
--- Division attempt finished ---

Attempt 3: Invalid input type
Error: Both inputs must be numbers!
--- Division attempt finished ---
```

💡 `finally` is the right place for things that must happen no matter what, like closing a file or a network connection.

### The same function, written defensively

For comparison, here's a version that uses `if` instead of `try`:

```python
def divide_numbers(a, b):
    if b != 0:
        result = a / b
        print(f"Division successful. Result: {result}")
        return result
    else:
        print("Error: zero!")
        return None
```

It handles division by zero, but `divide_numbers(10, "hello")` still crashes because we never checked the type. The `try` version caught both.

---

## 10. Custom Exception Types 🏷️

For rules that are specific to your program, you can create your own exception type. It makes errors easier to understand and lets callers catch exactly that problem.

```python
class LessThanFiveError(Exception):
    """Raised when a number is smaller than 5."""
    pass
```

The `(Exception)` part means "a `LessThanFiveError` is a kind of `Exception`", so it works with `raise` and `except` just like the built-in ones.

```python
my_num = int(input("Enter number: "))

if my_num < 5:
    raise LessThanFiveError("my_num must be at least 5")
else:
    print(f"my_num: {my_num}")
```

**Output** (entering `4`):

```
LessThanFiveError: my_num must be at least 5
```

### Raising it from a function and catching it

```python
def check_my_val(my_num):
    if my_num < 5:
        raise LessThanFiveError("my_num must be at least 5")

for value in [6, 3]:
    try:
        check_my_val(value)
        print(f"my_num: {value}")
    except LessThanFiveError as e:
        print(f"Problem with {value}: {e}")
```

**Output:**

```
my_num: 6
Problem with 3: my_num must be at least 5
```

---

## 📋 Quick Reference Cheat Sheet

```python
try:
    # code that might fail
    ...
except ValueError:
    # runs if a ValueError happened
    ...
except (TypeError, ZeroDivisionError) as e:
    # one block for several types; e holds the error
    ...
except Exception as e:
    # catch-all for anything unexpected (keep it LAST)
    ...
else:
    # runs only if NO exception happened
    ...
finally:
    # runs ALWAYS
    ...
```

| Keyword | Purpose |
|---|---|
| `try` | Wrap code that might fail |
| `except X` | Handle exception type `X` |
| `except X as e` | Handle it and keep the error object in `e` |
| `else` | Code for the "no error" case |
| `finally` | Cleanup that always runs |
| `raise X("msg")` | Signal an error yourself |
| `class MyError(Exception):` | Create your own exception type |

---

## 🎉 Conclusion

Your programs are now much harder to break! 🙌

Main takeaways:

* exceptions happen at **runtime** when Python can't do what you asked
* `try` / `except` keeps one bad input from crashing the whole program
* use several `except` blocks to handle different problems differently
* `raise` lets you enforce your own rules (like "no negative hours")
* exceptions **propagate** out of functions until something catches them
* `else` runs on success, and `finally` always runs
* custom exceptions make your program's rules clear

Happy coding! 💻✨
