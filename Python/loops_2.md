---
title: Python Loops, breaks and continue 
parent: Python
nav_order: 7
---

# Nested Loops, `break`, and `continue` — A Practical Tutorial

This tutorial explains nested loops in Python and how `break` and `continue` behave in both simple and nested cases.

---

## Table of contents

* What are nested loops?
* Examples: nested `for` and `while`
* `break` — stop a loop early
* `continue` — skip to next iteration
* Behavior of `break` / `continue` in nested loops
* `else` clause on loops
* Useful patterns: `enumerate`, unpacking tuples
* Common pitfalls and tips

---

## What are nested loops?

A nested loop is simply a loop inside another loop. The inner loop completes all its iterations for every single iteration of the outer loop. Nested loops are commonly used for processing 2D structures (lists of lists, matrices) or combinations of items.

### Example data

```python
fruits = [
    ["apple", "banana", "cherry"],
    ["date", "elderberry", "fig"],
    ["grape", "honeydew", "kiwi"]
]
print(fruits)
```

### Simple nested `for` loop (iterate lists of lists)

```python
for fruit_list in fruits:
    print("My outer loop ........")
    for fruit in fruit_list:
        print("\t\tmy loop produce:", fruit)
    print("end of outer loop")
```

Output (conceptual): each `fruit_list` prints, then each `fruit` inside it prints on its own line.

---

## Mixing `while` and `for` loops

You can mix `while` and `for` loops. Here are two equivalent ways to iterate the 2D `fruits` list.

### `while` outside, `for` inside

```python
print("Example 1: While loop outside, for loop inside")
i = 0
while i < len(fruits):
    print(f"Outer loop (while) iteration: {i}")
    for fruit in fruits[i]:
        print(f"\tInner loop (for) fruit: {fruit}")
    i += 1
```

### `for` outside, `while` inside

```python
print("Example 2: For loop outside, while loop inside")
for fruit_list in fruits:
    print(f"Outer loop (for) processing list: {fruit_list}")
    j = 0
    while j < len(fruit_list):
        print(f"\tInner loop (while) fruit: {fruit_list[j]}")
        j += 1
```

Both produce the same logical result: the inner loop outputs all items of each sub-list.

---

## `break` — stop a loop early

`break` exits the nearest enclosing loop immediately.

### `break` inside a `for`

```python
print("Example with break:")
for i in range(10):
    if i == 5:
        break  # Exit the loop when i is 5
    print(i)
```

This prints `0` through `4`, then stops.

### `break` inside a `while`

```python
print("Example with break in a while loop:")
i = 0
while i < 10:
    if i == 5:
        break
    print(i)
    i += 1
```

Again, it exits when `i == 5`.

---

## `continue` — skip to the next iteration

`continue` skips the rest of the current iteration and continues with the next one of the nearest enclosing loop.

### `continue` in a `for`

```python
print("Example with continue:")
for i in range(5):
    print("+++++++++++++++ before my if i=", i)
    if i == 2:
        continue  # Skip the rest of this iteration when i==2
    print(" after my if i=", i)
```

When `i == 2`, the second `print` is skipped.

### `continue` in a `while` (careful with increments)

If you `continue` inside a `while` loop, make sure you update the loop variable before `continue`, otherwise you risk an infinite loop.

```python
print("Example with continue in a while loop:")
i = 0
while i < 5:
    print("in the looop and i= ", i)
    if i == 2:
        i += 1  # increment before continue to avoid infinite loop
        continue
    print(i)
    i += 1
```

Common pitfall:

```python
# BAD: this will get stuck because i is not incremented when i == 2
if i == 2:
    continue
```

---

## `break` and `continue` in nested loops

`break` and `continue` only affect the innermost loop they are placed in.

### `break` in nested loops (breaks inner loop only)

```python
print("Example with break in a nested loop:")
for i in range(3):
    for j in range(3):
        if i == 1 and j == 1:
            break  # breaks out of the inner loop only
        print(f"i: {i}, j: {j}")
    print("End of inner loop for i =", i)
```

When `i == 1 and j == 1`, the inner loop stops, and execution continues with the next iteration of the outer loop (or the outer loop body after the inner loop).

There are also examples where the outer loop itself breaks:

```python
print("Example with break in a nested loop (outer loop exits):")
for i in range(3):
    if i == 1:
        break  # Exit the outer loop when i is 1
    for j in range(3):
        print(f"i: {i}, j: {j}")
    print("End of inner loop for i =", i)
```

This stops everything when `i == 1` because the `break` is in the outer loop.

### `continue` in nested loops

`continue` skips to the next iteration of the loop where it's used.

Inner-loop `continue` example:

```python
print("Example with continue in a nested loop:")
for i in range(3):
    for j in range(3):
        if i == 1 and j == 1:
            continue  # skips remaining inner-loop body for this j
        print(f"i: {i}, j: {j}")
    print("End of inner loop for i =", i)
```

Outer-loop `continue` example (skips inner loop entirely for a specific outer iteration):

```python
print("Example with continue in a nested loop (outer skip):")
for i in range(3):
    if i == 1:
        continue  # Skip processing this i (inner loop won't run for i==1)
    for j in range(3):
        print(f"i: {i}, j: {j}")
    print("End of inner loop for i =", i)
```

---

## Loop `else` clause

Both `for` and `while` loops can have an `else` block that runs when the loop completes normally (i.e., it was not terminated by `break`). The `else` block runs after the loop finishes.

```python
print("Example with for loop and else block:")
for i in range(5):
    print(i)
else:
    print("Loop finished successfully (iterated through the sequence)")

print("Example with while loop and else block:")
i = 0
while i < 5:
    print(i)
    i += 1
else:
    print("Loop finished successfully (condition became false)")
```

If a `break` occurs, the `else` is skipped.

---

## Handy Python patterns: `enumerate` and unpacking

`enumerate` helps you get both the index and value when iterating:

```python
print("Example with enumerate:")
my_list = ["a", "b", "c"]
for index, value in enumerate(my_list):
    print(f"Index: {index}, Value: {value}")

my_list_of_tuples = [(1, 'one'), (2, 'two')]
for number, word in my_list_of_tuples:
    print(f"Number: {number}, Word: {word}")
```

Tuple unpacking is concise and readable:

```python
print("Example with unpacking:")
my_tuple = (1, 2, 3)
# manual indices
a = my_tuple[0]
b = my_tuple[1]
c = my_tuple[2]
print(f"a: {a}, b: {b}, c: {c}")

# unpacking
a, b, c = my_tuple
print(f"a: {a}, b: {b}, c: {c}")

# ignore values with underscore
a, b, _ = my_tuple
print(f"a: {a}, b: {b}")
```

---

## Common pitfalls & tips

* **Infinite loops with `continue` in `while`**: If you `continue` without updating the loop variable, you may never change the condition and get stuck.
* **`break` only exits the nearest loop**: To break out of multiple nested loops you must either use flags, exceptions, or restructure with functions/returns.
* **Prefer `for` loops for iterables**: `for` is clearer and less error-prone than `while` when you simply want to iterate over an iterable.
* **Use `enumerate` instead of manual index tracking**: It's more Pythonic and less bug-prone.
* **Keep nested loops shallow when possible**: Deeply nested loops can be slow (O(n^k)) and hard to read; look for combinatorial or vectorized approaches if performance matters.

