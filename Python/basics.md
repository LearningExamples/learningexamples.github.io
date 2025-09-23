---
title: Python Basics Tutorial
parent: Python
nav_order: 1
---


# Python Basics Tutorial: From Variables to Math Magic

Learning Python is like learning a new language — except instead of ordering coffee in Paris, you’re teaching a computer to follow your instructions. In this tutorial, we’ll walk through some fundamental Python concepts that every beginner should know. Along the way, you’ll see code examples, practical applications, and a few fun tricks.

By the end, you’ll have a strong grasp of:

-   Variables and arithmetic
    
-   Printing vs. displaying data
    
-   Real-world math applications
    
-   Reassigning and updating variables
    
-   Integer division and modulo
    
-   Using Python’s built-in `math` module
    

Let’s dive in!

----------

## 1. Variables and Basic Arithmetic

In Python, a **variable** is just a name we assign to a value. Think of it like a labeled box: whatever we put inside can be reused later.

```python
x = 8
y = 3

c = x + y
print(c)

z = 3 * (x + 10 / y)
print(z)

```

### What’s Happening Here?

-   `x` is assigned the value `8`.
    
-   `y` is assigned the value `3`.
    
-   `c = x + y` calculates `8 + 3`, so `c` becomes `11`.
    
-   `z` combines multiplication and division: `3 * (8 + 10/3)`.
    

Python follows the usual **order of operations** (PEMDAS: Parentheses, Exponents, Multiplication/Division, Addition/Subtraction).

➡️ **Try it yourself:** Change the values of `x` and `y` to see how the results change.

----------

## 2. Printing vs. Displaying Data

In Python, there are a couple of ways to output data. The most common is `print()`. But when working in environments like Jupyter notebooks, `display()` can give more structured and visually friendly results.

```python
import numpy as np
import pandas as pd

# Create a random matrix
matrix = np.random.rand(5, 5)

# Print the matrix
print("Printing the matrix:")
print(matrix)

# Display the matrix as a pandas DataFrame for better formatting
print("\nDisplaying the matrix:")
display(pd.DataFrame(matrix))

```

### What’s the Difference?

-   **`print()`**: Spits out plain text. Great for simple outputs.
    
-   **`display()`**: Renders objects in a more readable form (like a table for a DataFrame).
    

➡️ **Tip:** Use `print()` for quick debugging, but `display()` when you want nicely formatted tables or plots.

----------

## 3. A Practical Example: Leasing a Car

Programming shines when you apply it to real problems. Let’s say you’re calculating the total cost of leasing a car:

```python
# Computes the total cost of leasing a car given the down payment,
# monthly rate, and number of months

down_payment = 7
payment_per_month = 10
num_months = 19

total_cost = down_payment + (payment_per_month * num_months)

print (f"Total cost: ${total_cost:.2f}")

```

### What’s Going On?

-   The **down payment** is $7.
    
-   Each month costs $10, and there are 19 months.
    
-   The formula adds everything up.
    

The result prints as:

```
Total cost: $197.00

```

➡️ **Notice the formatting:** `{total_cost:.2f}` ensures the number is shown with exactly two decimal places, just like real currency.

----------

## 4. Reassignment and Augmented Assignment

Variables in Python aren’t fixed forever. You can reassign them or use shortcuts to update their values.

```python
x = 10
w = 12

# Reassigning x
x = x + w
print(f"After reassignment (x = x + w): {x}")

# Using augmented assignment
x = 10 # Reset x for the next example
x += w
print(f"After augmented assignment (x += w): {x}")

```

### The Key Difference:

-   `x = x + w` means “take the old value of `x`, add `w`, and store it back in `x`.”
    
-   `x += w` is a shorthand way of writing the same thing.
    

➡️ **Tip:** Python has similar shortcuts for subtraction (`-=`), multiplication (`*=`), and division (`/=`).

----------

## 5. Integer Division and Modulo

Sometimes we don’t want decimals — we just want the whole-number result of a division. That’s where **integer division** (`//`) comes in. The **modulo operator** (`%`) tells us the remainder.

```python
x = 17
y = 5

# Integer division
print(f"{x} // {y} = {x // y}")  # 17 divided by 5 is 3 remainder 2

# Modulo (remainder)
print(f"{x} % {y} = {x % y}")    # remainder is 2

```

➡️ This is especially useful in real life — for example, if you’re packing boxes and want to know how many are full and how many items are left over.

----------

## 6. The Math Module: Unlocking More Power

Python’s built-in `math` module gives us access to tons of useful functions.

```python
import math

print("Square root of 16:", math.sqrt(16))
print("Cosine of 0:", math.cos(0))
print("Factorial of 5:", math.factorial(5))

```

### Why Use It?

-   `math.sqrt()` → square root
    
-   `math.cos()` → trigonometry
    
-   `math.factorial()` → factorial (useful in probability)
    

➡️ There are many more: logarithms, constants like `math.pi`, and advanced trigonometry functions.

----------

## Wrapping Up

We’ve covered a lot of ground:

-   Assigning values to variables
    
-   Doing arithmetic with Python
    
-   Printing vs. displaying results
    
-   Applying math to real-world problems
    
-   Reassigning variables efficiently
    
-   Integer division and modulo
    
-   Unlocking the power of the `math` module
    

Python is like a Swiss army knife — the more tools you learn, the more problems you can solve. Practice these basics until they feel second nature, and you’ll be ready to move on to more complex programming challenges.

➡️ **Your Next Step:** Try writing a small program that uses variables, arithmetic, and the math module — maybe something like calculating the area of a circle, or figuring out compound interest.

Happy coding! 🚀
