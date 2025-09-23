---
title: Python Data Structures, Lists,  Tuples,  Sets,  and Dictionaries
parent: Python
nav_order: 3
---



# Python's Core Data Structures: **Lists**, **Tuples**, **Sets**, and **Dictionaries**🚀

This guide will walk you through the four fundamental data structures: **Lists**, **Tuples**, **Sets**, and **Dictionaries**. We'll cover how to create them, their unique characteristics, and when to use each one.

---

## 1. Lists: The Versatile Workhorse 🧰

A **list** is the most common data structure in Python. It's an **ordered**, **changeable** collection that allows **duplicate** members. Think of it as a shopping list you can modify on the fly.

### Creating and Accessing Lists

You create a list by placing items inside square brackets `[]`. You can access any item by its position, or **index**.

> Python uses **zero-based indexing**, which means the first item is at index `0`, the second at index `1`, and so on. This is a crucial concept in many programming languages.
{: .note .info }

```python
# A list of integers
my_fun_list = [3, 22, 43, 22, 66, 78]

# Accessing items by their index
print(my_fun_list[0]) # Prints the first item: 3
print(my_fun_list[1]) # Prints the second item: 22

# You can also use negative indexing to start from the end
print(my_fun_list[-1]) # Prints the last item: 78
```

### Modifying and Inspecting Lists

Because lists are **mutable** (changeable), Python provides many methods to modify them.

```python
my_fun_list = [3, 20, 3, 3, 22, 43]

# Add an item to the end
my_fun_list.append(100)
print(my_fun_list)
#=> [3, 20, 3, 3, 22, 43, 100]

# Remove the first occurrence of a specific value
my_fun_list.remove(3)
print(my_fun_list)
#=> [20, 3, 3, 22, 43, 100]

# Remove an item by its index and get its value
popped_item = my_fun_list.pop(4) # Removes item at index 4
print(f"Popped item: {popped_item}") #=> Popped item: 43
print(my_fun_list) #=> [20, 3, 3, 22, 100]

# Check if a value exists in the list
print(22 in my_fun_list) #=> True
```

### More Useful List Operations

```python
my_fun_list = [3, 20, 3, 3, 22, 43, 22, 66, 78, 44, 1000]

# Get the number of items
print(f"Length: {len(my_fun_list)}") #=> Length: 11

# Count how many times a value appears
print(f"Count of 3: {my_fun_list.count(3)}") #=> Count of 3: 3

# Sort the list in place (modifies the original list)
my_fun_list.sort()
print(f"Sorted: {my_fun_list}")
#=> Sorted: [3, 3, 3, 20, 22, 22, 43, 44, 66, 78, 1000]
```

---

## 2. Tuples: The Immutable Sibling 🏛️

A **tuple** is an **ordered** collection, just like a list, but it's **immutable**—once it's created, you cannot change it. Tuples are created with parentheses `()`. They're great for data that should not be modified, like geographic coordinates or configuration settings.

```python
white_house_coordinates = (38.8977, 77.0366)

print(f"Latitude: {white_house_coordinates[0]}")
#=> Latitude: 38.8977

# The following line will cause an error because tuples are immutable.
white_house_coordinates[1] = 50
```

> Attempting to modify the tuple raises a `TypeError`. This is not a bug; it's Python's way of enforcing data integrity for immutable types.  
> `TypeError: 'tuple' object does not support item assignment`
{: .warning }

### Super-Powered Tuples: `namedtuple`

For more readable code, you can use `namedtuple` from the `collections` library to give names to your tuple elements.

```python
from collections import namedtuple

# Create a 'Car' blueprint for our named tuple
Car = namedtuple("Car", ["make", "model", "price"])

chevy_impala = Car("Chevrolet", "Impala", 37495)

# Access elements by name instead of index
print(f"The {chevy_impala.model} costs ${chevy_impala.price}.")
#=> The Impala costs $37495.
```

---

## 3. Sets: The Unique & Unordered Collection 🧊

A **set** is an **unordered** collection of **unique** items. Sets are perfect for removing duplicates from a list or performing mathematical set operations like union, intersection, and difference.

```python
first_names = ["Alba", "Hema", "Ron", "Alba", "Musa", "Ron", "Ron"]
print(f"Original list: {first_names}")
#=> Original list: ['Alba', 'Hema', 'Ron', 'Alba', 'Musa', 'Ron', 'Ron']

# Creating a set automatically removes duplicates
names_set = set(first_names)
print(f"Set: {names_set}")
#=> Set: {'Musa', 'Ron', 'Hema', 'Alba'}
```

### Set Operations

The real power of sets is in their mathematical operations.

```python
set1 = {1, 2, 3, 4, 5}
set2 = {4, 5, 6, 7, 8}

# Intersection: Items present in BOTH sets
print(f"Intersection: {set1.intersection(set2)}") #=> Intersection: {4, 5}

# Union: All unique items from ALL sets
print(f"Union: {set1.union(set2)}") #=> Union: {1, 2, 3, 4, 5, 6, 7, 8}

# Difference: Items in set1 but NOT in set2
print(f"Difference: {set1.difference(set2)}") #=> Difference: {1, 2, 3}
```

---

## 4. Dictionaries: The Key-Value Store 📖

A **dictionary** stores data in **key-value pairs**. Each unique **key** maps to a **value**. This is incredibly useful for storing and retrieving structured data. Dictionaries are created with curly braces `{}` containing `key: value` pairs.

> As of Python 3.7, standard Python dictionaries remember insertion order. However, in older versions or other Python implementations, you shouldn't rely on their order.
{: .note }

```python
# A dictionary mapping drinks to their caffeine content in mg
caffeine_content_mg = {
    "Red Bull": 111,
    "Espresso": 64,
    "Regular Coffee": 95
}

# Access a value by its key
print(f"Caffeine in Espresso: {caffeine_content_mg['Espresso']} mg")
#=> Caffeine in Espresso: 64 mg

# Add a new key-value pair
caffeine_content_mg["Green Tea"] = 28
print(caffeine_content_mg)
#=> {'Red Bull': 111, 'Espresso': 64, 'Regular Coffee': 95, 'Green Tea': 28}

# Remove a key-value pair
del caffeine_content_mg["Red Bull"]
```

---

## Quick Reference Cheat Sheet

Use this table to help you decide which data structure fits your needs.

| Feature         | List `[]`                 | Tuple `()`                | Set `{}`                  | Dictionary `{key: value}`  |
|-----------------|---------------------------|---------------------------|---------------------------|-----------------------------|
| **Ordered**     | ✅ Yes                    | ✅ Yes                    | ❌ No                     | ✅ Yes (since Python 3.7)   |
| **Mutable**     | ✅ Yes (Changeable)       | ❌ No (Immutable)         | ✅ Yes (Changeable)       | ✅ Yes (Changeable)         |
| **Duplicates**  | ✅ Yes (Allowed)          | ✅ Yes (Allowed)          | ❌ No (Unique items)      | ❌ No (Unique keys)         |
| **Primary Use** | General-purpose sequences | Protecting data, fixed set | Uniqueness, math ops      | Storing key-value pairs     |