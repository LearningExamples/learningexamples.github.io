---
title: Python Lists and Dictionaries 2
parent: Python
nav_order: 11
---


# 🧰 Lists and Dictionaries, Part 2: The Deep Dive

The first data-structures guide introduced **lists**, **tuples**, **sets**, and **dictionaries**.
This one zooms in on the two you'll use the most, **lists** and **dictionaries**, and covers the tools that make them powerful:

* every way to add, remove, and rearrange list items
* the classic bugs when you change a list inside a loop
* nested lists (lists of lists)
* **list comprehensions**
* `sort()` vs. `sorted()`, and custom sort keys
* safe dictionary access with `get()` and `pop()`
* nested dictionaries

Let's dive in! 🚀

---

## 1. Creating Lists 🏗️

You can write a list directly with square brackets, or build one from another object with `list()`.

```python
my_list = [1, 2, 3, "Bob"]          # lists can mix types
print(my_list)                       #=> [1, 2, 3, 'Bob']

letters = list("hello")              # from a string: one item per character
print(letters)                       #=> ['h', 'e', 'l', 'l', 'o']

from_tuple = list((1, 2, 3))         # from a tuple
print(from_tuple)                    #=> [1, 2, 3]
```

---

## 2. Accessing and Slicing ✂️

Slicing works on lists exactly like it does on strings: `my_list[start:stop:step]`, where `stop` is **not** included.

```python
my_list = ["a", "b", "c", "d", "e", "f", "g", "h"]

print(my_list[0])       #=> a
print(my_list[-1])      #=> h
print(my_list[2:5])     #=> ['c', 'd', 'e']
print(my_list[2:])      #=> ['c', 'd', 'e', 'f', 'g', 'h']
print(my_list[:5])      #=> ['a', 'b', 'c', 'd', 'e']
print(my_list[2:7:2])   #=> ['c', 'e', 'g']
print(my_list[::3])     #=> ['a', 'd', 'g']
print(my_list[-5:-2])   #=> ['d', 'e', 'f']
print(my_list[::-1])    #=> ['h', 'g', 'f', 'e', 'd', 'c', 'b', 'a']
print(my_list[:])       #=> a full copy of the list
```

Asking for an index that doesn't exist raises an error:

```python
print(my_list[11])   # IndexError: list index out of range
```

> `my_list[:]` creates a **copy**. This is the easiest way to protect a list before handing it to a function that might change it.
{: .note }

---

## 3. Changing a List ✏️

Lists are **mutable**, so all of these change the list **in place**.

### Replace and delete

```python
my_list = [1, 2, 3, "a", "b", "c"]

my_list[0] = 100        # replace the value at index 0
print(my_list)          #=> [100, 2, 3, 'a', 'b', 'c']

del my_list[2]          # delete the item at index 2
print(my_list)          #=> [100, 2, 'a', 'b', 'c']
```

### Insert at a position

`insert(index, value)` puts the value at that index and shifts everything after it one place to the right.

```python
letters = ["a", "b", "c", "d"]

letters.insert(2, "z")
print(letters)          #=> ['a', 'b', 'z', 'c', 'd']

letters.insert(100, "end")   # index past the end? it just goes at the end
print(letters)          #=> ['a', 'b', 'z', 'c', 'd', 'end']
```

### `append()` vs. `extend()` vs. `+`

These three look similar but behave differently.

```python
list1 = [1, 2, 3]
list2 = [4, 5, 6]

a = list1.copy()
a.append(list2)         # adds list2 as ONE item
print(a, len(a))        #=> [1, 2, 3, [4, 5, 6]] 4

b = list1.copy()
b.extend(list2)         # adds each item of list2
print(b, len(b))        #=> [1, 2, 3, 4, 5, 6] 6
```

`extend()` changes the existing list, while `+` builds a **new** list. You can see it with `id()`:

```python
my_list_1 = ["a", "b", "c", "d"]
my_list_2 = ["e", "f", "g", "h"]

print(id(my_list_1))
my_list_1.extend(my_list_2)
print(id(my_list_1))          # same id: same list, now longer

my_list_1 = ["a", "b", "c", "d"]
print(id(my_list_1))
my_list_1 = my_list_1 + my_list_2
print(id(my_list_1))          # different id: + made a new list
```

| Method | What it adds | Changes the original? |
|---|---|---|
| `a.append(x)` | `x` as a single item | ✅ Yes |
| `a.extend(x)` | each item of `x` | ✅ Yes |
| `a + x` | each item of `x` | ❌ No, it returns a new list |

### `remove()` vs. `pop()`

* `remove(value)` deletes the **first** matching value and returns **`None`**.
* `pop()` deletes the **last** item and **returns it**.
* `pop(index)` deletes the item at that index and **returns it**.

```python
fruits = ["apple", "elderberry", "cherry", "date", "banana"]

removed = fruits.remove("banana")
print(fruits)      #=> ['apple', 'elderberry', 'cherry', 'date']
print(removed)     #=> None

last = fruits.pop()
print(fruits)      #=> ['apple', 'elderberry', 'cherry']
print(last)        #=> date

second = fruits.pop(1)
print(fruits)      #=> ['apple', 'cherry']
print(second)      #=> elderberry
```

> `remove()` raises a `ValueError` if the value isn't in the list, and `pop()` raises an `IndexError` if the index is out of range.
{: .warning }

### Sort, reverse, find, count

```python
nums = [3, 1, 4, 1, 5, 9, 2, 6]

nums.sort()
print(nums)                  #=> [1, 1, 2, 3, 4, 5, 6, 9]
nums.reverse()
print(nums)                  #=> [9, 6, 5, 4, 3, 2, 1, 1]

words = ["banana", "apple", "cherry"]
words.sort()
print(words)                 #=> ['apple', 'banana', 'cherry']

letters = ["a", "b", "c", "a", "d", "a"]
print(letters.index("b"))    #=> 1
print(letters.index("a"))    #=> 0   (only the FIRST match)
print(letters.count("a"))    #=> 3
```

---

## 4. Built-in Functions for Lists 🧮

```python
my_list = [1, 2, 3, 4, 5]

print(len(my_list))   #=> 5
print(sum(my_list))   #=> 15
print(min(my_list))   #=> 1
print(max(my_list))   #=> 5
```

`all()` and `any()` check the "truthiness" of every item:

* `all()` is `True` only if **every** item is truthy.
* `any()` is `True` if **at least one** item is truthy.

```python
print(all([True, True, False]))   #=> False
print(all([True, True, True]))    #=> True
print(all([1, 2, 0, 4]))          #=> False  (0 counts as False)
print(any([True, True, False]))   #=> True

print(all([]))                    #=> True   (no item is False)
print(any([]))                    #=> False  (no item is True)
```

---

## 5. Looping Over Lists 🔁

There are three common styles. Pick the one that gives you what you need.

```python
my_list = ["apple", "banana", "cherry"]

# 1. Just the values
for item in my_list:
    print(item)

# 2. Just the indexes (use the index to get the value)
for i in range(len(my_list)):
    print(f"Element at index {i}: {my_list[i]}")

# 3. Both at once (the most Pythonic way)
for index, item in enumerate(my_list):
    print(f"Index: {index}, Item: {item}")
```

### Turning text into a list of numbers

A very common task: the user types numbers separated by commas, and you need real integers.

```python
user_input = "1,9,9,9,0,1,0,1"

tokens = user_input.split(",")     # still strings!
print(tokens)                      #=> ['1', '9', '9', '9', '0', '1', '0', '1']

nums = []
for token in tokens:
    nums.append(int(token) + 10)   # convert, then use
print(nums)                        #=> [11, 19, 19, 19, 10, 11, 10, 11]
```

---

## 6. Changing a List Inside a Loop ⚠️

### ✅ Changing values: loop over the indexes

```python
my_list = [1, 2, 3, 4, 5]

for i in range(len(my_list)):
    my_list[i] = my_list[i] * 10

print(my_list)   #=> [10, 20, 30, 40, 50]
```

Here's a common mistake. This version does **nothing** to the list:

```python
my_list = [1, 2, 3, 4, 5]

for item in my_list:
    item = item * 10   # only changes the loop variable, not the list!

print(my_list)   #=> [1, 2, 3, 4, 5]
```

The index style also lets you change only *some* items:

```python
letters = ["a", "b", "c", "d", "e"]

for i in range(len(letters)):
    if i % 2 == 0:                       # even indexes only
        letters[i] = letters[i].upper()

print(letters)   #=> ['A', 'b', 'C', 'd', 'E']
```

### ❌ Removing items while looping forward

Removing from a list while you loop over it **shifts** the remaining items, so the loop skips some of them:

```python
nums = [3, 6, 9, 10]

for item in nums:
    if item % 3 == 0:
        nums.remove(item)

print(nums)   #=> [6, 10]   😱 6 was never checked!
```

What happened: after `3` was removed, `6` slid into index 0, but the loop had already moved on to index 1.

### ✅ Fix 1: loop backwards

If you go from the **end** to the start, removing an item never shifts the ones you haven't looked at yet.
For example, remove the positions where two lists have the same value:

```python
list1 = [10, 5, 8, 12, 5]
list2 = [10, 3, 8, 15, 5]

if len(list1) != len(list2):
    print("Lists have different lengths, cannot compare element-wise.")
else:
    for i in range(len(list1) - 1, -1, -1):   # last index down to 0
        if list1[i] - list2[i] == 0:
            list1.pop(i)
            list2.pop(i)

print(list1)   #=> [5, 12]
print(list2)   #=> [3, 15]
```

### ✅ Fix 2: build a new list

Often the cleanest solution is to keep the items you want in a new list:

```python
list1 = [10, 5, 8, 12, 5]
list2 = [10, 3, 8, 15, 5]

new_list1 = []
for item in list1:
    if item not in list2:
        new_list1.append(item)

list1 = new_list1
print(list1)   #=> [12]
```

---

## 7. Nested Lists (Lists of Lists) 🪆

A list can contain other lists. Use one index per level: `outer[i][j]`.

```python
dealership = [["BMW", "KIA"], ["FORD", "KIA", "Toyota"], ["MAZDA", "HONDA"], ["NISSAN"]]

print(len(dealership))      #=> 4
print(dealership[0])        #=> ['BMW', 'KIA']
print(len(dealership[0]))   #=> 2
print(dealership[1][2])     #=> Toyota
```

### Looping with nested loops

```python
for dealer in dealership:
    print(f"{dealer = }")
    for car in dealer:
        print(f"\t{car = }")
```

**Output:**

```
dealer = ['BMW', 'KIA']
	car = 'BMW'
	car = 'KIA'
dealer = ['FORD', 'KIA', 'Toyota']
	car = 'FORD'
	car = 'KIA'
	car = 'Toyota'
dealer = ['MAZDA', 'HONDA']
	car = 'MAZDA'
	car = 'HONDA'
dealer = ['NISSAN']
	car = 'NISSAN'
```

If you need the positions, loop over the indexes. Notice that the inner `range` uses `len(grid[i])`, because each row can have a different length:

```python
grid = [[1, 2, 3], ["a", "b", "c"], [True, False]]

for i in range(len(grid)):
    for j in range(len(grid[i])):
        print(f"grid[{i}][{j}] = {grid[i][j]}")
```

**Output:**

```
grid[0][0] = 1
grid[0][1] = 2
grid[0][2] = 3
grid[1][0] = a
grid[1][1] = b
grid[1][2] = c
grid[2][0] = True
grid[2][1] = False
```

### Going deeper: 3D lists

```python
cube = [[[1, 2, 3], ["a", "b", "c"]], [[True, False], [4, 5, 6]]]

print(cube[1])          #=> [[True, False], [4, 5, 6]]
print(cube[1][1])       #=> [4, 5, 6]
print(cube[1][1][1])    #=> 5
```

### Changing a nested list

```python
list_of_lists = [[1, 2, 3], ["a", "b", "c"], [True, False]]

list_of_lists[0][1] = 100            # change one item
list_of_lists.append([4, 5, 6])      # add a whole new row
list_of_lists[1].extend(["d", "e"])  # grow one row

print(list_of_lists)
#=> [[1, 100, 3], ['a', 'b', 'c', 'd', 'e'], [True, False], [4, 5, 6]]
```

And with nested loops you can change items based on their position:

```python
my_list = [[1, 2, 3], [4, 5], [7, 8, 9, 10, 11, 12]]

for i in range(len(my_list)):
    for j in range(len(my_list[i])):
        if j > i:
            my_list[i][j] = my_list[i][j] * 10
        else:
            my_list[i][j] = my_list[i][j] * 2

print(my_list)   #=> [[2, 20, 30], [8, 10], [14, 16, 18, 100, 110, 120]]
```

---

## 8. List Comprehensions ⚡

A **list comprehension** builds a new list in a single line. The pattern is:

```
[expression  for item in iterable  if condition]
```

### The basic form

```python
list_x = [1, 2, 3, 4, 5]

squares = [n * n for n in list_x]
print(squares)   #=> [1, 4, 9, 16, 25]
```

That one line does the same job as this loop:

```python
squares = []
for n in list_x:
    squares.append(n * n)
```

### Converting types

```python
list_str = ["1", "2", "3", "4", "5"]
list_int = [int(s) for s in list_str]
print(list_int)   #=> [1, 2, 3, 4, 5]
```

### Filtering with `if`

```python
import math

list_x = [1, -4, -9, 16, 25]
roots = [math.sqrt(n) for n in list_x if n >= 0]   # skip negatives
print(roots)   #=> [1.0, 4.0, 5.0]
```

### Reading numbers from the user

```python
# The user types: 10 11 12 13 14 15
numbers = [int(i) for i in input("Enter numbers: ").split()]

odd_numbers = [i for i in numbers if i % 2 != 0]
print(f"Odd numbers only: {odd_numbers}")   #=> Odd numbers only: [11, 13, 15]
```

### Comprehensions with nested lists

```python
my_list = [[5, 10, 15], [2, 3, 16], [100]]

flat = [item for row in my_list for item in row]   # flatten
print(flat)       #=> [5, 10, 15, 2, 3, 16, 100]

row_max = [max(row) for row in my_list]             # one value per row
print(row_max)    #=> [15, 16, 100]

big_rows = [row for row in my_list if sum(row) > 25]  # keep some rows
print(big_rows)   #=> [[5, 10, 15], [100]]
```

> In the flatten example, read the two `for` parts from left to right, just like nested loops: "for each row, for each item in that row".
{: .note }

---

## 9. Sorting: `sort()` vs. `sorted()` 🔀

* `my_list.sort()` sorts the list **in place** and returns **`None`**.
* `sorted(my_list)` returns a **new** sorted list and leaves the original alone.

```python
my_list = [3, 1, 4, 9, 2]
result = my_list.sort()
print(result)     #=> None
print(my_list)    #=> [1, 2, 3, 4, 9]

my_list = [3, 1, 4, 9, 2]
new_list = sorted(my_list)
print(new_list)   #=> [1, 2, 3, 4, 9]
print(my_list)    #=> [3, 1, 4, 9, 2]   (unchanged)
```

> A classic bug: `my_list = my_list.sort()`. Because `sort()` returns `None`, this throws away your list!
{: .warning }

`sorted()` is handy for comparing two lists without caring about order:

```python
list_a = [3, 1, 4, 1, 5, 9, 2, 6]
list_b = [9, 6, 5, 4, 3, 2, 1, 1]

if sorted(list_a) == sorted(list_b):
    print("Same items, different order.")   # this one prints
```

### Custom order with `key`

`key` is a **function** that Python calls on each item. Items are then sorted by whatever that function returns.

```python
# Sort lists by their largest number
my_list = [[1, 5, 10], [2, 3, 5], [0, 0, 100]]
print(sorted(my_list))            #=> [[0, 0, 100], [1, 5, 10], [2, 3, 5]]
print(sorted(my_list, key=max))   #=> [[2, 3, 5], [1, 5, 10], [0, 0, 100]]
```

Without a key, lists are compared item by item (`0 < 1 < 2`). With `key=max`, they're compared by `10`, `5`, and `100`.

Uppercase letters sort **before** lowercase ones, which often isn't what you want. Use `key=str.lower` to ignore case:

```python
names = ["alice", "ALEX", "Bob", "brad"]

print(sorted(names))                               #=> ['ALEX', 'Bob', 'alice', 'brad']
print(sorted(names, key=str.lower))                #=> ['ALEX', 'alice', 'Bob', 'brad']
print(sorted(names, key=str.lower, reverse=True))  #=> ['brad', 'Bob', 'alice', 'ALEX']
```

You can pass **your own function** as the key:

```python
def get_length(item):
    return len(item)

fruits = ["apple", "banana", "cherry", "date"]
fruits.sort(key=get_length)
print(fruits)   #=> ['date', 'apple', 'banana', 'cherry']
```

### Sneak peek: `lambda`

A `lambda` is a tiny function written inline. It's perfect for short sort keys (we'll cover it properly later):

```python
fruits = ["apple", "banana", "cherry", "date"]
fruits.sort(key=lambda item: len(item))   # same as get_length
print(fruits)   #=> ['date', 'apple', 'banana', 'cherry']

pairs = [(1, "banana"), (2, "apple"), (3, "cherry")]
pairs.sort(key=lambda x: x[1])            # sort by the 2nd element
print(pairs)    #=> [(2, 'apple'), (1, 'banana'), (3, 'cherry')]

people = [{"name": "Alice", "age": 30}, {"name": "Bob", "age": 25}, {"name": "Charlie", "age": 35}]
people.sort(key=lambda p: p["age"])       # sort by a dictionary key
print([p["name"] for p in people])        #=> ['Bob', 'Alice', 'Charlie']
```

---

## 10. Dictionaries in Depth 📖

A dictionary maps **keys** to **values**.

```python
student_grades = {
    "Alice": 95,
    "Bob": 88,
    "Charlie": 75,
    "David": 92,
}
```

### Read, add, and update

```python
print(student_grades["Alice"])    #=> 95

student_grades["Eve"] = 81        # new key   -> added
student_grades["Alice"] = 97      # existing key -> updated
```

Square brackets with a missing key crash the program:

```python
student_grades["Zed"]   # KeyError: 'Zed'
```

### Safe reading with `get()`

`get()` returns `None` (or a default you choose) instead of crashing:

```python
print(student_grades.get("Bob"))           #=> 88
print(student_grades.get("Zed"))           #=> None
print(student_grades.get("Zed", "N/A"))    #=> N/A
```

### Removing: `del`, `pop()`, `clear()`

```python
del student_grades["Bob"]                  # remove, no return value

removed = student_grades.pop("Charlie")    # remove AND return the value
print(removed)                             #=> 75

# pop() with a default doesn't crash on a missing key
print(student_grades.pop("Zed", "Student not found"))   #=> Student not found

print(student_grades)                      #=> {'Alice': 97, 'David': 92, 'Eve': 81}

student_grades.clear()                     # empty the whole dictionary
print(student_grades)                      #=> {}
```

### `pop()` vs. `get()`

Both give you the value. The difference is what happens to the dictionary:

```python
my_dict = {"Ahmad": 1, "Jane": 42, "Charlie": 75}
x = my_dict.pop("Jane")
print(my_dict)    #=> {'Ahmad': 1, 'Charlie': 75}   (Jane is gone)

my_dict = {"Ahmad": 1, "Jane": 42, "Charlie": 75}
x = my_dict.get("Jane")
print(my_dict)    #=> {'Ahmad': 1, 'Jane': 42, 'Charlie': 75}   (Jane stays)
```

| | `d.get(key)` | `d.pop(key)` |
|---|---|---|
| Returns the value | ✅ | ✅ |
| Removes the key | ❌ | ✅ |
| Missing key, no default | returns `None` | raises `KeyError` |
| Missing key, with default | returns the default | returns the default |

### Merging with `update()`

`update()` adds new keys and overwrites existing ones:

```python
my_dict = {"Ahmad": 1, "Jane": 42}
my_dict.update({"John": 50, "Ahmad": 2})
print(my_dict)   #=> {'Ahmad': 2, 'Jane': 42, 'John': 50}
```

### Looping over a dictionary

```python
my_dict = {"Ahmad": 1, "Jane": 42, "Charlie": 75}

for name in my_dict:                    # keys (the default)
    print(name, my_dict[name])

for name in my_dict.keys():             # keys, written explicitly
    print(name, my_dict.get(name))

for grade in my_dict.values():          # values only
    print(grade)

for name, grade in my_dict.items():     # key AND value
    print(f"Name: {name}, Grade: {grade}")
```

`keys()`, `values()`, and `items()` return special **view** objects. Wrap them in `list()` when you need a real list:

```python
my_dict = {"apple": 1, "banana": 2, "cherry": 3}

print(my_dict.keys())            #=> dict_keys(['apple', 'banana', 'cherry'])
print(list(my_dict.keys()))      #=> ['apple', 'banana', 'cherry']
print(list(my_dict.values()))    #=> [1, 2, 3]
print(list(my_dict.items()))     #=> [('apple', 1), ('banana', 2), ('cherry', 3)]
```

---

## 11. Nested Dictionaries 🗃️

A dictionary's values can be dictionaries too. This is a great way to store records.

```python
people = {
    "person1": {"name": "Alice", "age": 30, "city": "New York"},
    "person2": {"name": "Bob", "age": 25, "city": "London"},
    "person3": {"name": "Charlie", "age": 35, "city": "Paris"},
}

print(people["person1"]["name"])   #=> Alice
print(people["person2"]["age"])    #=> 25
```

### Looping through every level

```python
for person, details in people.items():
    print(f"{person}:")
    for key, value in details.items():
        print(f"\t{key}: {value}")
```

**Output:**

```
person1:
	name: Alice
	age: 30
	city: New York
person2:
	name: Bob
	age: 25
	city: London
person3:
	name: Charlie
	age: 35
	city: Paris
```

### Changing and adding

```python
people["person1"]["age"] = 31                    # change an inner value
people["person2"]["occupation"] = "Engineer"     # add an inner key
people["person4"] = {"name": "David", "age": 28, "city": "Tokyo"}   # add a new record
```

### When the values aren't all the same type

Nothing forces every value to be a dictionary. If one of them is just a string, calling `.items()` on it would crash with an `AttributeError`, so check the type first:

```python
people["person6"] = "N/A"

for person, details in people.items():
    print(f"{person}:")
    if type(details) == dict:
        for key, value in details.items():
            print(f"\t{key}: {value}")
    else:
        print(f"\t{details}")
```

💡 `isinstance(details, dict)` is another (and more common) way to write the same check.

---

## 📋 Quick Reference Cheat Sheet

### Lists

| Operation | Example | Returns | Changes the list? |
|---|---|---|---|
| Add to end | `a.append(x)` | `None` | ✅ |
| Add many | `a.extend([x, y])` | `None` | ✅ |
| Insert | `a.insert(i, x)` | `None` | ✅ |
| Remove by value | `a.remove(x)` | `None` | ✅ |
| Remove by index | `a.pop(i)` | the item | ✅ |
| Delete by index | `del a[i]` | — | ✅ |
| Sort in place | `a.sort()` | `None` | ✅ |
| Sorted copy | `sorted(a)` | new list | ❌ |
| Reverse in place | `a.reverse()` | `None` | ✅ |
| Reversed copy | `a[::-1]` | new list | ❌ |
| Copy | `a[:]` or `a.copy()` | new list | ❌ |
| Find | `a.index(x)` | first index | ❌ |
| Count | `a.count(x)` | a number | ❌ |
| Build from loop | `[f(x) for x in a if cond]` | new list | ❌ |

### Dictionaries

| Operation | Example | Missing key? |
|---|---|---|
| Read | `d[key]` | `KeyError` |
| Safe read | `d.get(key, default)` | returns `default` |
| Add or update | `d[key] = value` | adds it |
| Remove | `del d[key]` | `KeyError` |
| Remove and return | `d.pop(key, default)` | returns `default` |
| Merge | `d.update(other)` | adds it |
| Empty | `d.clear()` | — |
| Loop | `for k, v in d.items():` | — |

---

## 🎉 Conclusion

That was a big one! 🙌 You now know how to:

* add, insert, remove, and reorder list items, and which methods return what
* avoid the "remove while looping" bug
* work with nested lists using nested loops
* write list comprehensions
* sort with `sort()`, `sorted()`, and custom `key` functions
* read dictionaries safely with `get()` and `pop()`
* store and loop through nested dictionaries

Lists and dictionaries are the workhorses of Python. Practice them often and everything else gets easier.

Happy coding! 💻✨
