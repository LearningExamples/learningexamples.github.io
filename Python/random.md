---
title: Python Random
parent: Python
nav_order: 2
---

## Table of contents

- [Table of contents](#table-of-contents)
- [Overview \& learning goals](#overview--learning-goals)
- [Random numbers in Python (`random` module)](#random-numbers-in-python-random-module)
  - [`random.random()` and `random.uniform(a, b)`](#randomrandom-and-randomuniforma-b)
  - [`randint(a, b)` vs `randrange(start, stop[, step])`](#randinta-b-vs-randrangestart-stop-step)
  - [`choice()`, `choices()`, `sample()`, `shuffle()`](#choice-choices-sample-shuffle)
  - [`seed()` and reproducibility](#seed-and-reproducibility)
  - [Simulating random variables: coin flips, dice, and expectations](#simulating-random-variables-coin-flips-dice-and-expectations)
  - [Monte Carlo estimate of π (short recipe)](#monte-carlo-estimate-of-π-short-recipe)
  - [Quick recipes \& gotchas](#quick-recipes--gotchas)
- [Strings: basics, escapes, raw strings, `ord()` / `chr()`](#strings-basics-escapes-raw-strings-ord--chr)
  - [Escape sequences and raw strings](#escape-sequences-and-raw-strings)
  - [`ord()` and `chr()`](#ord-and-chr)
  - [Indexing and slicing](#indexing-and-slicing)
  - [Concatenation, repetition, membership, length](#concatenation-repetition-membership-length)
- [String methods (editing) — the toolbox](#string-methods-editing--the-toolbox)
  - [Case conversion](#case-conversion)
  - [Trimming (strip) and splitting](#trimming-strip-and-splitting)
  - [Searching and replacing](#searching-and-replacing)
  - [Tests and properties](#tests-and-properties)
  - [Joining lists into strings](#joining-lists-into-strings)
  - [Method chaining](#method-chaining)
- [Printing and formatting](#printing-and-formatting)
  - [`print()` parameters](#print-parameters)
  - [f-strings (formatted string literals)](#f-strings-formatted-string-literals)
  - [Format specifiers (useful examples)](#format-specifiers-useful-examples)
  - [`repr()` vs `str()` and printing raw values](#repr-vs-str-and-printing-raw-values)
- [Combined examples \& small projects](#combined-examples--small-projects)
  - [1) Random password generator](#1-random-password-generator)
  - [2) Simulating many dice rolls and printing a nice table](#2-simulating-many-dice-rolls-and-printing-a-nice-table)
  - [3) Monte Carlo estimate of π (again, longer example)](#3-monte-carlo-estimate-of-π-again-longer-example)
- [Exercises (with solutions)](#exercises-with-solutions)
  - [Exercise 1 — password strength check](#exercise-1--password-strength-check)
  - [Exercise 2 — random string editing](#exercise-2--random-string-editing)
  - [Exercise 3 — expected value via simulation](#exercise-3--expected-value-via-simulation)
- [Summary \& next steps](#summary--next-steps)

---

## Overview & learning goals

This post expands on the short code examples from the Lecture 5 notebook and turns them into a longer, more approachable tutorial. After reading and experimenting with the examples you should be able to:

* Use Python's `random` module for common random-generation tasks.
* Understand the difference between continuous (`random()`, `uniform()`) and discrete (`randint`, `randrange`) random outputs.
* Use string editing methods to clean and transform text.
* Format values for human-friendly printing using f-strings and format specifiers.
* Combine these ideas to build small programs like password generators and Monte Carlo simulations.

---

## Random numbers in Python (`random` module)

Start by importing the module:

```python
import random
```

### `random.random()` and `random.uniform(a, b)`

* `random.random()` returns a floating point number in the half-open interval `[0.0, 1.0)`.
* `random.uniform(a, b)` returns a floating point number in `[a, b]` (both endpoints possible depending on float rounding).

Examples:

```python
# standard 0-1 float
print(random.random())          # e.g. 0.719446...

# scaled float in 0-100
x = 100 * random.random()
print(x)                        # e.g. 72.944...

# uniform between -1 and 1
print(random.uniform(-1, 1))
```

Converting the float to an integer using `int()` truncates toward zero:

```python
x = 73.9
print(int(x))  # 73
```

If you want to round instead, use `round()`.

### `randint(a, b)` vs `randrange(start, stop[, step])`

* `random.randint(a, b)` returns an integer **including** both endpoints `a` and `b`.
* `random.randrange(start, stop, step)` works like `range`: it returns an integer from `start` (inclusive) to `stop` (exclusive), optionally with a `step`.

Examples:

```python
print(random.randint(50, 70))       # integer between 50 and 70 inclusive
print(random.randrange(0, 20, 2))   # even integer from 0 up to 18
```

Common gotcha: `randrange(0, 20, 2)` can never return 20. `randint(0, 20)` can return 20.

### `choice()`, `choices()`, `sample()`, `shuffle()`

* `random.choice(seq)` picks a single element from a non-empty sequence.
* `random.choices(seq, k=n)` picks `n` elements **with replacement** (duplicates allowed) and can accept weights.
* `random.sample(seq, k=n)` picks `n` **distinct** elements (without replacement).
* `random.shuffle(list_)` shuffles a list **in-place**.

Examples:

```python
colors = ['red', 'green', 'blue']
print(random.choice(colors))            # one color
print(random.choices(colors, k=5))      # e.g. ['blue','blue','red','green','blue']
print(random.sample(colors, 2))         # two distinct colors

lst = [1,2,3,4,5]
random.shuffle(lst)
print(lst)  # order changed
```

### `seed()` and reproducibility

`random.seed(some_seed)` makes the sequence of random numbers deterministic — this is essential for reproducible examples, debugging, and tests.

```python
random.seed(1222)
print(random.random())
random.seed(1222)
print(random.random())  # same output as above
```

If you comment out `seed()` (or call with `None`) you get different outputs each run.

### Simulating random variables: coin flips, dice, and expectations

Coin flip (fair): use `random.choice(['H','T'])` or `random.random() < 0.5`.

```python
# single coin flip
print('H' if random.random() < 0.5 else 'T')

# simulate 1000 coin flips and count heads
n = 1000
heads = sum(1 for _ in range(n) if random.random() < 0.5)
print(heads)
```

Dice roll:

```python
# fair six-sided die
print(random.randint(1, 6))
```

Estimate a probability using Monte Carlo (approximate):

```python
# estimate probability of getting at least one six in 4 dice rolls
def trial():
    return any(random.randint(1,6) == 6 for _ in range(4))

trials = 100000
count = sum(1 for _ in range(trials) if trial())
print(count / trials)  # should be close to analytic value 1 - (5/6)^4
```

### Monte Carlo estimate of π (short recipe)

One popular toy example: sample uniform points in the square `[-1,1] x [-1,1]` and count how many fall inside the unit circle `x^2 + y^2 <= 1`. The ratio approximates π/4.

```python
def estimate_pi(n):
    inside = 0
    for _ in range(n):
        x = random.uniform(-1,1)
        y = random.uniform(-1,1)
        if x*x + y*y <= 1:
            inside += 1
    return 4 * inside / n

print(estimate_pi(100_000))
```

This is a great demonstration of randomness, convergence, and variance: more samples → better estimate.

### Quick recipes & gotchas

* Use `random.sample` when you need unique random picks (e.g. lottery numbers).
* Avoid using `random.seed()` in library code — it is fine in scripts and examples. For reproducible experiments, set seeds in your test harness or notebook cell.
* `random.shuffle()` modifies the list in place — if you need a shuffled copy, do `lst_copy = lst[:]` then `random.shuffle(lst_copy)`.

---

## Strings: basics, escapes, raw strings, `ord()` / `chr()`

Strings are sequences of Unicode characters.

### Escape sequences and raw strings

```python
my_string = "This is a \n \"normal\" string\n"
my_raw_string = r"This is a \n \"raw\" string"

print(my_string)
print(my_raw_string)
```

* `\n` is a newline in a normal string. In a *raw string* (prefix `r`), backslashes are not processed, so `r"\n"` is two characters: a backslash and `n`.
* Raw strings are handy for regular expressions and Windows paths (but note: a raw string cannot end with a single backslash `r"...\"`).

### `ord()` and `chr()`

* `ord('A')` → returns the Unicode code point (integer) of the character `'A'` (65).
* `chr(65)` → returns the character for the code point 65, `'A'`.

```python
print(ord('A'))  # 65
print(chr(65))   # 'A'
```

### Indexing and slicing

Strings act like lists of characters:

```python
x = "Hello"
print(x[0])      # 'H'
print(x[-1])     # 'o'
print(x[1:4])    # 'ell'
```

Important: strings are immutable. `x[0] = 'h'` raises an error. To change a string, build a new one.

### Concatenation, repetition, membership, length

```python
s = "hi" + " " + "there"   # 'hi there'
print('ha' * 3)                 # 'hahaha'
print('ell' in 'Hello')         # True
print(len('Appl e'))            # counts characters including spaces
```

---

## String methods (editing) — the toolbox

Python strings come with many helpful methods. Here are the most useful ones with examples.

### Case conversion

```python
s = "Hello World"
print(s.upper())      # 'HELLO WORLD'
print(s.lower())      # 'hello world'
print(s.title())      # 'Hello World' (title case)
print(s.swapcase())   # 'hELLO wORLD'
```

### Trimming (strip) and splitting

```python
s = "   padded text\n"
print(s.strip())      # remove leading/trailing whitespace
print(s.rstrip())
print(s.lstrip())

# split by whitespace
words = "one two  three".split()
print(words)          # ['one', 'two', 'three']

# split with a specific delimiter
csv = "a,b,c,d".split(',')
print(csv)
```

### Searching and replacing

```python
s = "The quick brown fox"
print(s.find('quick'))   # index of first match or -1
print(s.index('quick'))  # same but raises ValueError if not found

print(s.replace('quick', 'slow'))
```

### Tests and properties

```python
print("abc".isalpha())     # True
print("123".isdigit())     # True (note: unicode digits count too)
print("abc123".isalnum())  # True
```

### Joining lists into strings

```python
items = ['apple', 'banana', 'cherry']
print(', '.join(items))   # 'apple, banana, cherry'
```

### Method chaining

```python
s = "   hello  world \n"
clean = s.strip().replace(' ', '_').upper()
print(clean)  # 'HELLO_WORLD'
```

This pattern is extremely readable once you get used to it: perform transformations step-by-step.

---

## Printing and formatting

`print()` is more powerful than most beginners expect.

### `print()` parameters

* `sep` — string put between multiple arguments (default `' '`).
* `end` — string appended at the end (default `'\n'`).
* `file` — where to send output (default `sys.stdout`).
* `flush` — whether to forcibly flush the stream.

```python
print('a', 'b', 'c', sep='|', end='\n---\n')
```

### f-strings (formatted string literals)

The modern, concise way to format strings (Python 3.6+):

```python
x = 18
y = 10
print(f"{x} divided by {y} is {x/y}")
print(f"{x}/{y} = {x/y:.3f}")   # 3 decimal places
```

F-strings can evaluate expressions and even show debug info:

```python
print(f"{2**2=}")  # prints '2**2=4'
```

### Format specifiers (useful examples)

* `{:d}` — decimal integer
* `{:b}` — binary
* `{:x}` — hex (lowercase)
* `{:o}` — octal
* `{:e}` or `{:.1e}` — scientific notation
* `{:0>4d}` — pad integer with zeros on the left to width 4
* `{:>10}`, `{:^10}`, `{: <10}` — alignment (right, center, left)
* `{:,}` — thousands separator

Examples:

```python
number = 32
print(f"{number:b}")        # binary
print(f"{number:x}")        # hex

number = 1234567
print(f"{number:,}")        # '1,234,567'

number = 44
print(f"{number:e}")        # scientific notation
print(f"{number:0.1e}")     # 1 decimal digit in exponent form
```

### `repr()` vs `str()` and printing raw values

Use `repr(obj)` when you want the unambiguous representation (good for debugging), and `str(obj)` when you want a human-friendly string.

```python
s = "Hello\n"
print(str(s))   # prints with newline interpreted
print(repr(s))  # prints with escapes visible: '"Hello\\n"'
```

---

## Combined examples & small projects

### 1) Random password generator

```python
import random, string

def make_password(length=12, use_punct=True):
    pool = list(string.ascii_letters + string.digits)
    if use_punct:
        pool += list(string.punctuation)
    # pick with replacement
    return ''.join(random.choice(pool) for _ in range(length))

print(make_password(16))
```

This produces passwords like `fG3$w0Q!aB9pZ2xY`.

### 2) Simulating many dice rolls and printing a nice table

```python
from collections import Counter

def roll_dice(n_rolls=10000):
    counts = Counter(random.randint(1,6) for _ in range(n_rolls))
    for face in range(1,7):
        print(f"Face {face}: {counts[face]} times ({counts[face]/n_rolls:.3%})")

roll_dice(100000)
```

### 3) Monte Carlo estimate of π (again, longer example)

```python
def estimate_pi(n):
    inside = 0
    for _ in range(n):
        x = random.random()*2 - 1
        y = random.random()*2 - 1
        if x*x + y*y <= 1:
            inside += 1
    return 4 * inside / n

for n in [1000, 10_000, 100_000, 1_000_000]:
    print(n, estimate_pi(n))
```

Try plotting how the estimate converges (use `matplotlib` locally).

---

## Exercises (with solutions)

### Exercise 1 — password strength check

**Task:** write a function `is_strong(password)` that returns `True` if the password is at least 8 characters long, contains at least one uppercase letter, one lowercase letter, one digit, and one punctuation character.

**Solution:**

```python
import string

def is_strong(password):
    if len(password) < 8:
        return False
    has_upper = any(c.isupper() for c in password)
    has_lower = any(c.islower() for c in password)
    has_digit = any(c.isdigit() for c in password)
    has_punct = any(c in string.punctuation for c in password)
    return has_upper and has_lower and has_digit and has_punct

# quick tests
print(is_strong('aB3$xxxx'))  # True
print(is_strong('weak'))      # False
```

### Exercise 2 — random string editing

**Task:** given a string that may contain extra whitespace and mixed case, normalize it to lower-case, remove leading/trailing spaces, collapse any internal run of whitespace to a single space, and replace spaces with underscores.

**Solution:**

```python
import re

def normalize(s):
    s = s.strip().lower()
    s = re.sub(r"\s+", " ", s)  # collapse whitespace runs
    return s.replace(' ', '_')

print(normalize('  This   is   MIXED  '))  # 'this_is_mixed'
```

### Exercise 3 — expected value via simulation

**Task:** simulate rolling two six-sided dice 1,000,000 times and estimate the expected value (mean) of the sum.

**Solution:**

```python
import random

trials = 1_000_000
s = 0
for _ in range(trials):
    s += random.randint(1,6) + random.randint(1,6)
print(s / trials)  # should be close to 7.0
```

## Summary & next steps

You've seen how to generate random values, control reproducibility with `seed`, and use string methods to clean and format text. You learned f-strings and several useful format specifiers. Try combining these topics into small projects: a text-based game that uses randomness, log-formatters that output aligned columns, or a data-cleaning mini-tool that reads messy text and outputs normalized CSV.

If you want, I can also:

* produce a version of this post with runnable Jupyter cells exported as a notebook;
* add visualizations (histograms) using matplotlib for the Monte Carlo examples;
* turn the exercises into a short worksheet with automated tests.

Would you like me to generate any of those next?
