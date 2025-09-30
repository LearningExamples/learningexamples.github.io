---
title: Python Loops
parent: Python
nav_order: 6
---

# A Guide to Python Loops (with Extended Examples)

Loops are a fundamental concept in programming that allow you to execute a block of code repeatedly. Mastering them is crucial for writing efficient and effective Python programs.  

This tutorial will walk you through the two main types of loops in Python: **`while`** and **`for`**, using practical examples.



## 1. The `while` Loop

A `while` loop repeatedly executes a block of code as long as a specified condition is `True`.
It's ideal when you **don't know in advance how many times you need to loop**.

### Basic Syntax

```python
while condition:
    # Code to execute as long as the condition is true
```

---

### Example: A Simple Counter

```python
# Simple while loop
count = 4
while count < 5:
    print(f"Count is: {count}")
    count += 1

print("\nWhile loop finished.")
```

**Output:**

```
Count is: 4

While loop finished.
```

---

### Example: User Input Validation

```python
# user input check
my_num = 4
user_num = int(input("Enter a number: "))

while my_num != user_num:
    print("Try again!")
    user_num = int(input("Enter a number: "))

print("Correct!")
```

---

### Example: Factorial Calculation

```python
# Factorial calculation in a while loop
num = 7
factorial = 1
i = 1

while i <= num:
    factorial *= i
    i += 1

print(f"The factorial of {num} is {factorial}")
```

**Output:**

```
The factorial of 7 is 5040
```

---

## 2. The `for` Loop

A `for` loop is used for **iterating over a sequence** (like a list, tuple, dictionary, set, or string).

It's best used when you want to perform an action **for each item in a collection**.

### Basic Syntax

```python
for item in sequence:
    # Code to execute for each item
```

---

### Example: Iterating Over a List

```python
fruits = ["apple", "banana", "cherry"]
for item in fruits:
    print(item)
```

**Output:**

```
apple
banana
cherry
```

---

### Example: Iterating in Reverse

```python
fruits = ["apple", "banana", "cherry"]
print("\nIn reverse order:")
for item in reversed(fruits):
    print(item)
```

**Output:**

```
In reverse order:
cherry
banana
apple
```

---

### Example: Iterating Over a Dictionary

```python
my_dict = {"a": 1, "b": 2, "c": 3}

# Iterating through keys (default behavior)
print("Iterating through keys:")
for key in my_dict:
    print(key)

# Iterating through values using .values()
print("\nIterating through values:")
for value in my_dict.values():
    print(value)

# Iterating through key-value pairs using .items()
print("\nIterating through key-value pairs:")
for key, value in my_dict.items():
    print(f"Key: {key}, Value: {value}")
```

---

### Using the `range()` Function

```python
print("Example 1: range(5)")
for i in range(5):
    print(i)  # Prints 0, 1, 2, 3, 4

print("\nExample 2: range(2, 7)")
for i in range(2, 7):
    print(i)  # Prints 2, 3, 4, 5, 6

print("\nExample 3: range(0, 10, 2)")
for i in range(0, 10, 2):
    print(i)  # Prints 0, 2, 4, 6, 8
```

---

### Nested Loops

```python
shops = [
    {"name": "Fruit Stand", "fruits": ["apple", "banana", "orange"]},
    {"name": "Organic Market", "fruits": ["strawberry", "blueberry", "raspberry"]},
    {"name": "Local Grocer", "fruits": ["grape", "kiwi", "mango"]}
]

print("Here are the fruits from each shop:")
for shop in shops:
    print(f"\n--- {shop['name']} ---")
    for fruit in shop["fruits"]:
        print(fruit)
```

---

## 3. Advanced Loop Control

Python provides statements to control the flow of loops with more precision.

### The `break` Statement

```python
print("\nSimulating a do-while loop:")
i = 0
while True:  # This would be an infinite loop without a break
    print(f"i is: {i}")
    i += 1
    if i >= 5:
        break

print("\nDo-while simulation finished.")
```

---

### The `continue` Statement

```python
# Print only the odd numbers
for number in range(10):
    if number % 2 == 0:
        continue
    print(number)
```

**Output:**

```
1
3
5
7
9
```

---

### The `else` Clause in Loops

```python
my_list = ["apple", "banana", "cherry"]
for item in my_list:
    if item == "grape":
        print("Found the grape!")
        break
else:
    print("Grape not found in the list.")
```

**Output:**

```
Grape not found in the list.
```

---

## Summary: `while` vs `for`

* Use a **`while` loop** when you want to loop based on a **condition being met**, or when you **don’t know how many iterations** are needed.
* Use a **`for` loop** when you want to **iterate over every item in a sequence**.

Understanding both types of loops and when to use them is a key step in becoming a proficient Python programmer.

