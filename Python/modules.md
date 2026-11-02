---
title: Python Modules
parent: Python
nav_order: 14
---


# 📦 Python Modules: The Art of Organizing Code

As your programs grow, putting everything in one file gets messy fast. **Modules** solve this: a module is simply a `.py` file full of functions and variables that you can **import** and reuse anywhere.

Python already ships with hundreds of useful modules, thousands more are a quick `pip install` away, and you can write your own.

In this tutorial you'll learn:

* the different ways to `import` a module
* when to use `import x`, `import x as y`, and `from x import y`
* why `from x import *` causes trouble
* how to install third-party packages
* how to write your **own** module
* what `if __name__ == "__main__":` means
* the "reload trap" in Jupyter and Colab notebooks

Let's get organized! 🚀

---

## 1. Four Situations You'll Run Into 🗺️

When you want to use some code, it will be in one of these four situations:

1. **Built in and already loaded.** Things like `print()`, `len()`, and string methods. Just use them.
2. **Part of the standard library, but not loaded.** Modules like `math`, `random`, `os`, `csv`. They come with Python, but you must `import` them first.
3. **Not installed.** Third-party packages like `numpy`, `pandas`, or `plotly`. Install them once, then import them.
4. **Doesn't exist yet.** You write it yourself as your own module.

---

## 2. `import`: The Standard Way 📥

```python
import math

radius = 5

# Access things inside the module with "dot notation": module.name
area = math.pi * (radius ** 2)
root = math.sqrt(16)

print(f"The area is: {area:.2f}")
print(f"The square root of 16 is: {root}")
```

**Output:**

```
The area is: 78.54
The square root of 16 is: 4.0
```

The `math.` prefix tells anyone reading your code exactly where `pi` and `sqrt` come from.

The `random` module works the same way:

```python
import random

roll = random.randint(1, 6)                         # roll a 6-sided die

options = ["Study Calculus", "Eat Pizza", "Sleep"]
choice = random.choice(options)                     # pick one item

print(f"You rolled a {roll}")
print(f"Decision: {choice}")
```

**Output** (yours will be different, since it's random!):

```
You rolled a 2
Decision: Study Calculus
```

---

## 3. `import ... as ...`: Giving a Module a Nickname 🏷️

If a module's name is long, or you use it constantly, give it a shorter **alias**:

```python
import math as m

area = m.pi * (5 ** 2)
root = m.sqrt(16)
```

Some aliases are so common that everyone uses them:

```python
import numpy as np                 # np.array(...)
import pandas as pd                # pd.DataFrame(...)
import matplotlib.pyplot as plt    # plt.plot(...)
```

> You can pick any alias (`import math as bob` works!), but sticking to the community conventions makes your code instantly readable to other people.
{: .note }

---

## 4. `from ... import ...`: Grabbing Specific Names 🎣

If you only need a couple of things, import them directly. Then you can skip the prefix:

```python
from math import pi, sqrt

area = pi * (5 ** 2)     # no "math." needed
root = sqrt(16)
```

Only the names you listed are available. Everything else in the module is not:

```python
sin(30)        # NameError: name 'sin' is not defined
math.sin(30)   # NameError: name 'math' is not defined (we never imported "math" itself)
```

### ⚠️ Why `from math import *` is a bad idea

`*` imports **every** name from the module:

```python
from math import *

print(log(10))   # works without "math."
```

**Output:**

```
2.302585092994046
```

It looks convenient, but it fills your program with hundreds of names you can't see, and it hides where each one came from. It also makes name clashes easy. Two modules can have functions with the same name, and the **last import silently wins**:

```python
from math import sqrt
from numpy import sqrt    # this replaces math's sqrt without any warning!

print(sqrt([4, 9]))
```

**Output:**

```
[2. 3.]
```

This only works because `sqrt` is now NumPy's version. `math.sqrt([4, 9])` would raise `TypeError: must be real number, not list`. In a big program, a silent swap like that can be a nightmare to debug.

### Which style should I use?

| Style | Syntax | Pros / Cons |
|---|---|---|
| Standard | `import math` | **Best practice.** It's always clear where a function comes from (`math.sqrt`). |
| Alias | `import numpy as np` | Saves typing. Standard in data science (`np.array`). |
| Specific | `from math import sqrt` | Short to type (`sqrt(25)`). Good for small scripts, but can clash with your own names. |
| Everything | `from math import *` | ❌ Avoid. Hides where names come from and causes silent clashes. |

---

## 5. Installing Third-Party Packages 🛒

Packages that don't come with Python are installed with **pip**, Python's package installer.

In a terminal:

```
pip install numpy
```

In a Jupyter or Google Colab notebook cell, add an exclamation mark so the command runs in the system shell:

```
!pip install plotly
```

You only need to install a package **once** per environment. After that, `import` it as usual:

```python
import numpy as np

my_array = np.array([1, 2, 3, 4, 5])
print(f"Original array: {my_array}")
print(f"Squared array: {my_array ** 2}")    # math on every item at once
print(f"Sum of elements: {np.sum(my_array)}")
```

**Output:**

```
Original array: [1 2 3 4 5]
Squared array: [ 1  4  9 16 25]
Sum of elements: 15
```

(There's much more on NumPy in the NumPy, Pandas and Matplotlib tutorial.)

---

## 6. Writing Your Own Module 🛠️

Any `.py` file is a module. Say you create a file called `geometry_tools.py`:

```python
# geometry_tools.py

def rectangle_area(length, width):
    return length * width

def triangle_area(base, height):
    return 0.5 * base * height

print("Module 'geometry_tools' has been imported successfully!")
```

Then, in another file **in the same folder** (say `main.py`), import it by its file name, without the `.py`:

```python
# main.py
import geometry_tools

rect = geometry_tools.rectangle_area(10, 5)
print(f"Rectangle Area: {rect}")

tri = geometry_tools.triangle_area(10, 5)
print(f"Triangle Area: {tri}")
```

**Output:**

```
Module 'geometry_tools' has been imported successfully!
Rectangle Area: 50
Triangle Area: 25.0
```

Notice the first line. **Importing a module runs all of its top-level code**, including that `print`.

### 📓 Creating a module file inside a notebook

In Jupyter or Colab, you can write a cell's contents into a file with the `%%writefile` magic. It must be the very first line of the cell:

```python
%%writefile geometry_tools.py

def rectangle_area(length, width):
    return length * width

def triangle_area(base, height):
    return 0.5 * base * height
```

**Output:**

```
Writing geometry_tools.py
```

The code is **saved, not run**. After that, `import geometry_tools` works in any other cell. You can also upload an existing `.py` file (like a `my_math.py` from your instructor) into the notebook's folder and import it the same way.

---

## 7. `if __name__ == "__main__":` 🚪

Often you want a module to do two jobs:

* provide functions when **imported** by another program
* run some tests or a demo when you **run the file directly**

Python helps with a special variable called `__name__`:

* When you **run** a file directly, `__name__` is `"__main__"`.
* When a file is **imported**, its `__name__` is the module's name (for example `"mathy_tools"`).

You can check this yourself:

```python
print(__name__)
```

**Output** (in a notebook or a script you run directly):

```
__main__
```

So we wrap the "only when run directly" code in an `if`:

```python
# mathy_tools.py

def rectangle_area(length, width):
    return length * width

def triangle_area(base, height):
    return 0.5 * base * height

if __name__ == "__main__":
    # runs only with:  python mathy_tools.py
    # does NOT run on: import mathy_tools
    print("Testing mathy_tools...")
    print("rectangle_area(3, 4) =", rectangle_area(3, 4))
```

Now importing it is silent, and only the functions are used:

```python
import mathy_tools

print(mathy_tools.rectangle_area(3, 4))
```

**Output:**

```
12
```

---

## 8. The Reload Trap (Crucial for Notebooks) 🪤

This is the number one module issue in Colab and Jupyter:

1. You import `geometry_tools`.
2. You find a bug, fix it in `geometry_tools.py`, and save.
3. You run `import geometry_tools` again...
4. ...and **nothing changes**. The old, buggy version is still running!

Why? Python **caches** a module the first time it's imported, and every later `import` of the same name just reuses the cached copy.

**The fix:** force Python to re-read the file with `importlib.reload()`:

```python
import importlib
import geometry_tools

importlib.reload(geometry_tools)    # re-read the file from disk

rect = geometry_tools.rectangle_area(10, 5)
print(f"Rectangle Area: {rect}")
```

The other option is to restart the notebook's runtime, which clears the cache (and all your variables).

---

## 📋 Quick Reference Cheat Sheet

| I want to... | Write this |
|---|---|
| Use a standard module | `import math` then `math.sqrt(9)` |
| Use a shorter name | `import numpy as np` then `np.array(...)` |
| Use one function without a prefix | `from math import sqrt` then `sqrt(9)` |
| Install a package (terminal) | `pip install numpy` |
| Install a package (notebook) | `!pip install numpy` |
| Make my own module | Save functions in `my_module.py`, then `import my_module` |
| Write a file from a notebook cell | `%%writefile my_module.py` on the first line |
| Run code only when executed directly | `if __name__ == "__main__":` |
| Pick up changes to my module | `importlib.reload(my_module)` |

---

## 🎉 Conclusion

You now know how to organize and reuse code like a pro! 🙌

* `import module` is the clearest, safest style
* aliases (`as np`) save typing, and `from x import y` grabs single names
* avoid `from x import *`
* install extra packages with `pip`
* any `.py` file can be your own module
* use `if __name__ == "__main__":` for code that should only run directly
* in notebooks, remember `importlib.reload()`

Happy coding! 💻✨
