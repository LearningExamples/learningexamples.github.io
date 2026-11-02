---
title: Python String 3 Tutorial
parent: Python
nav_order: 9.5
---


# 🔍 Python Strings, Part 3: Checking, Comparing, Splitting, and Joining

The previous string tutorial covered creating strings, slicing, the main string methods, and f-string formatting.
This one goes a step further, into the questions you'll ask about strings every day:

* *Is* this text a number? Is it all uppercase? Is it empty space?
* *Where* is this word, and how many times does it appear?
* Which of these two strings comes first alphabetically?
* How do I cleanly break text apart and glue it back together?

Along the way we'll look at a few gotchas that surprise almost everyone. Let's go! 🚀

---

## 🔒 1. A Quick Reminder: Strings Can't Be Changed

A list lets you replace an item in place. A string doesn't:

```python
array = [1, 2, 3, 4, 5]
array[0] = 1000
print(array)          #=> [1000, 2, 3, 4, 5]

mystring = "hello world"
print(mystring[0])    #=> h
mystring[0] = "H"     # TypeError: 'str' object does not support item assignment
```

Every string method in this tutorial returns a **new** string (or a number, a boolean, or a list). The original is never touched. To "change" a string, build a new one and assign it:

```python
mystring = "H" + mystring[1:]
print(mystring)       #=> Hello world
```

---

## ✂️ 2. Slicing Edge Cases

You already know `text[start:stop:step]`. Here are a few behaviours that are worth knowing.

```python
#      0123456789...
url = "http://en.wikipedia.org/wiki/Turing"

print(url[7:23])     #=> en.wikipedia.org
print(url[7:-12])    #=> en.wikipedia.org   (negative stop: count from the end)
print(url[10:-5])    #=> wikipedia.org/wiki/T
print(url[-6:])      #=> Turing
```

### When `start` is past `stop`, you get an empty string

```python
print(url[10:5])     #=> ''   (nothing between 10 and 5 going forward)
print(url[20:-16])   #=> ''   (-16 is index 19, which is before 20)
```

### Slices never go "out of range"

Indexing a position that doesn't exist is an error, but **slicing** past the end just stops at the end:

```python
print(len(url))      #=> 35
print(url[30:100])   #=> uring
print(url[100:])     #=> ''
print(url[100])      # IndexError: string index out of range
```

### A slice is a separate string

Slicing copies the characters into a new string. Changing the original variable afterwards doesn't affect the slice:

```python
my_str = "The cat jumped the brown cow"
animal = my_str[4:7]
print(f"The animal is a {animal}")         #=> The animal is a cat

my_str = "The fox jumped the brown llama"
print(f"The animal is still a {animal}")   #=> The animal is still a cat
```

---

## ➕ 3. A Classic Gotcha: Strings That Look Like Numbers

Text that *looks* like a number is still text. `+` joins strings, it doesn't add them:

```python
s1 = "2"
s2 = "4"

print(s1 + s2)             #=> 24   (joined, not added!)
print(int(s1) + int(s2))   #=> 6
print(s1 * 3)              #=> 222
```

And mixing a string with a number is an error:

```python
print("2" + 4)   # TypeError: can only concatenate str (not "int") to str
```

> This shows up all the time with `input()`, which **always** returns a string. Convert with `int()` or `float()` before doing math.
{: .note }

---

## ✅ 4. Checking What a String Contains

These methods all return `True` or `False`, which makes them perfect for `if` statements.

| Method | Returns `True` if... |
|---|---|
| `s.isalnum()` | every character is a letter or a digit |
| `s.isalpha()` | every character is a letter |
| `s.isdigit()` | every character is a digit `0`–`9` |
| `s.islower()` | every letter is lowercase (and there's at least one letter) |
| `s.isupper()` | every letter is uppercase (and there's at least one letter) |
| `s.isspace()` | every character is whitespace (spaces, tabs, newlines) |
| `s.isascii()` | every character is a basic ASCII character |
| `s.startswith(x)` | the string begins with `x` |
| `s.endswith(x)` | the string ends with `x` |

```python
print("HelloWorld123".isalnum())     #=> True
print("Hello World 123".isalnum())   #=> False  (a space is not a letter or digit)

print("123".isdigit())               #=> True
print("Hello123".isdigit())          #=> False
print("-5".isdigit())                #=> False  ('-' is not a digit)
print("7.5".isdigit())               #=> False  ('.' is not a digit)
print("".isdigit())                  #=> False  (an empty string has no digits)

print("hello world".islower())       #=> True   (the space is ignored)
print("Hello world".islower())       #=> False
print("HELLO WORLD".isupper())       #=> True
print("Hello World".isupper())       #=> False

print("   ".isspace())               #=> True
print("  a  ".isspace())             #=> False

print("Hello World".startswith("Hello"))   #=> True
print("Hello World".startswith("World"))   #=> False
print("Hello World".endswith("World"))     #=> True
```

### Practical use: checking input before converting it

```python
age_text = input("Enter your age: ").strip()   # strip() first: " 25".isdigit() is False

if age_text.isdigit():
    age = int(age_text)
    print(f"Next year you'll be {age + 1}.")
else:
    print("Please type a whole number, like 25.")
```

Because `isdigit()` rejects `-5` and `7.5`, it only works for whole, non-negative numbers. For anything more flexible, use `try` / `except` with `float()` (see the exception handling tutorial).

### Practical use: counting kinds of characters

```python
text = "Hello World 123!"

letters = 0
digits = 0
spaces = 0
others = 0

for c in text:
    if c.isalpha():
        letters += 1
    elif c.isdigit():
        digits += 1
    elif c.isspace():
        spaces += 1
    else:
        others += 1

print(f"letters={letters}, digits={digits}, spaces={spaces}, others={others}")
```

**Output:**

```
letters=10, digits=3, spaces=2, others=1
```

---

## 🔎 5. Searching Inside a String

### `in` and `not in`

The quickest check is the membership operator. It's **case-sensitive** and looks for the exact sequence of characters:

```python
print("www" in "www.google.com")   #=> True
print("www" in "ww.google.com")    #=> False
print("Google" in "www.google.com")   #=> False  (capital G)
print("https" not in "http://site.com")   #=> True
```

### `find()` with a start and an end

`find(x)` returns the index of the first match, or `-1` if there is none. You can also tell it **where to look**:

* `find(x, start)` starts searching at index `start`
* `find(x, start, end)` searches only between `start` and `end`

```python
my_string = "programming is fun and programming is useful"

print(my_string.find("is"))                     #=> 12
print(my_string.find("python"))                 #=> -1
print(my_string.find("programming", 15))        #=> 23
print(my_string.find("programming", 15, 30))    #=> -1
print(my_string.rfind("programming"))           #=> 23  (search from the right)
print(my_string.count("programming"))           #=> 2
print(my_string.count("bob"))                   #=> 0
```

Why is the `15, 30` search `-1`? The second `"programming"` starts at 23 but ends at index 33, so it doesn't fit **completely** inside the 15–30 window.

`index()` works like `find()`, but raises an error instead of returning `-1`:

```python
my_string.index("python")   # ValueError: substring not found
```

### Finding every match

Combine `find()` with a loop, starting each new search one character after the last match:

```python
my_string = "programming is fun and programming is useful"

pos = my_string.find("is")
while pos != -1:
    print(f"found 'is' at index {pos}")
    pos = my_string.find("is", pos + 1)
```

**Output:**

```
found 'is' at index 12
found 'is' at index 35
```

---

## ⚖️ 6. Comparing Strings

### Equality is case-sensitive

```python
print("apple" == "apple")                    #=> True
print("Apple" == "apple")                    #=> False
print("Apple".lower() == "apple".lower())    #=> True   (compare without case)
```

### `<` and `>` compare alphabetically... mostly

Python compares strings **character by character**, from left to right, using each character's code (the number `ord()` gives you). The first difference decides the result.

```python
print("Yankee Sierra" > "Amy Wise")      #=> True   ('Y' comes after 'A')
print("Yankee Sierra" > "Yankee Zulu")   #=> False  (equal until 'S' vs 'Z')
print("ariday" < "bbc")                  #=> True   ('a' < 'b'; the rest doesn't matter)
print("app" < "apple")                   #=> True   (a prefix comes first)
```

Two surprises come from those character codes:

```python
print(ord("Z"), ord("a"))   #=> 90 97
print("Zebra" < "apple")    #=> True   (ALL uppercase letters come before lowercase!)
print("10" < "9")           #=> True   ('1' < '9', compared as text, not as numbers!)
```

> To compare alphabetically regardless of case, compare `a.lower()` with `b.lower()`. To compare numbers, convert them with `int()` or `float()` first.
{: .warning }

### `==` vs. `is`

`==` asks "do these have the **same value**?", while `is` asks "are these the **same object** in memory?". For strings, you almost always want `==`:

```python
student_name = input("Enter a name: ")   # the user types: Bob Adams

print(student_name == "Bob Adams")   #=> True
print(student_name is "Bob Adams")   #=> False
```

The text typed by the user is a **new** string object, so `is` is `False` even though the values match. Python even warns you about this mistake:

```
SyntaxWarning: "is" with 'str' literal. Did you mean "=="?
```

💡 Use `is` only for checking against `None` (`if result is None:`). For everything else, use `==`.

---

## 🔪 7. Splitting, in More Depth

### No argument vs. `" "`

`split()` with **no argument** splits on any run of whitespace and ignores extra spaces. `split(" ")` splits on every single space, so double spaces give you empty strings:

```python
text = "one  two   three"

print(text.split())      #=> ['one', 'two', 'three']
print(text.split(" "))   #=> ['one', '', 'two', '', '', 'three']
```

### Cleaning up after a split

The delimiter is removed, but any spaces next to it stay:

```python
data = "apple ,banana ,cherry"

fruits = data.split(",")
print(fruits)    #=> ['apple ', 'banana ', 'cherry']

fruits = [f.strip() for f in data.split(",")]
print(fruits)    #=> ['apple', 'banana', 'cherry']
```

### Limiting the number of splits

The second argument, `maxsplit`, stops after that many splits. Everything left over stays together:

```python
log_line = "ERROR: File not found: myfile.txt"

print(log_line.split(":"))      #=> ['ERROR', ' File not found', ' myfile.txt']
print(log_line.split(":", 1))   #=> ['ERROR', ' File not found: myfile.txt']
```

This is handy when only the **first** delimiter matters, like separating a label from its message.

### Picking the right delimiter

If the delimiter isn't in the string, you get the whole string back inside a list:

```python
my_str = "Hello_bob,WHAT_are_you_doing_HERE!!"

print(my_str.split())      #=> ['Hello_bob,WHAT_are_you_doing_HERE!!']
print(my_str.split("_"))   #=> ['Hello', 'bob,WHAT', 'are', 'you', 'doing', 'HERE!!']
```

### Splitting on two different delimiters

`split()` only takes one delimiter at a time, so split on the first, then split each piece on the second. `extend()` adds the pieces to one flat list:

```python
my_str = "Hello_bob,WHAT_are_you_doing_HERE!!"

final_tokens = []
for item in my_str.split("_"):
    final_tokens.extend(item.split(","))

print(final_tokens)   #=> ['Hello', 'bob', 'WHAT', 'are', 'you', 'doing', 'HERE!!']
```

---

## 🔗 8. Joining, in More Depth

`separator.join(items)` glues a list (or tuple) of strings together, putting the separator **between** them:

```python
my_tokens = ["bob", "Alex", "A", "montreal"]

print("_".join(my_tokens))    #=> bob_Alex_A_montreal
print("**".join(my_tokens))   #=> bob**Alex**A**montreal
print("-".join(("user1", "user2", "user3")))   #=> user1-user2-user3
```

### Why not a loop with `+`?

You *can* build the same string with a loop, but it's easy to end up with an extra separator:

```python
my_new_str = ""
for token in my_tokens:
    my_new_str += token + "_"

print(my_new_str)   #=> bob_Alex_A_montreal_    (an extra "_" at the end!)
```

`join()` only puts separators *between* items, so it never has that problem. It's also shorter and faster.

### `join()` needs strings

```python
nums = [1, 2, 3]
print("-".join(nums))   # TypeError: sequence item 0: expected str instance, int found

print("-".join([str(n) for n in nums]))   #=> 1-2-3
```

---

## 🔄 9. `split()` + `join()` vs. `replace()`

Splitting on one separator and joining with another is a common way to **swap separators**:

```python
data_string = "item1;item2;item3;item4"

items = data_string.split(";")
print(", ".join(items))               #=> item1, item2, item3, item4

print(data_string.replace(";", ", "))  #=> item1, item2, item3, item4
```

For simple swaps, both give the same result. There's one useful difference: `split()` with no argument **collapses** runs of spaces, while `replace()` changes every single one.

```python
messy = "This   is  a sentence"

print("_".join(messy.split()))   #=> This_is_a_sentence
print(messy.replace(" ", "_"))   #=> This___is__a_sentence
```

---

## ↩️ 10. Reversing

Strings and lists can both be reversed with the `[::-1]` slice:

```python
print("hello"[::-1])       #=> olleh

my_list = [1, 2, 3, 4, 5]
print(my_list[::-1])       #=> [5, 4, 3, 2, 1]   (a new list)
print(my_list)             #=> [1, 2, 3, 4, 5]   (unchanged)

my_list.reverse()          # lists can also reverse IN PLACE
print(my_list)             #=> [5, 4, 3, 2, 1]
```

Strings have no `reverse()` method, because they can't be changed in place:

```python
"hello".reverse()   # AttributeError: 'str' object has no attribute 'reverse'
```

---

## 📋 Quick Reference Cheat Sheet

| I want to... | Use | Example result |
|---|---|---|
| Check for digits only | `s.isdigit()` | `"123"` → `True` |
| Check letters/digits only | `s.isalnum()` | `"a b"` → `False` |
| Check case | `s.islower()`, `s.isupper()` | `"HI"` → `isupper()` is `True` |
| Check for only whitespace | `s.isspace()` | `"   "` → `True` |
| Check the start / end | `s.startswith(x)`, `s.endswith(x)` | `"a.pdf".endswith(".pdf")` → `True` |
| Check for a substring | `x in s` | `"www" in "www.a.com"` → `True` |
| Find within a range | `s.find(x, start, end)` | `-1` if not fully inside |
| Find from the right | `s.rfind(x)` | last match |
| Compare ignoring case | `a.lower() == b.lower()` | `"Hi"` vs. `"hi"` → `True` |
| Compare values | `==` (never `is`) | |
| Split on whitespace | `s.split()` | extra spaces ignored |
| Split only once | `s.split(":", 1)` | 2 pieces at most |
| Join with a separator | `"_".join(items)` | items must be strings |
| Swap separators | `new.join(s.split(old))` or `s.replace(old, new)` | |
| Reverse | `s[::-1]` | |

---

## 🏁 Conclusion

Strings are simple on the surface, but these details make the difference between code that *usually* works and code that *always* works. 🙌

We covered:

* slicing edge cases (empty slices, no out-of-range errors)
* why `"2" + "4"` is `"24"`
* the `is...()` methods for validating text
* `in`, `find()` with ranges, and finding every match
* how string comparison really works, and why to use `==` instead of `is`
* `split()` with `maxsplit`, and cleaning up the pieces
* `join()`, and why it beats building strings with `+` in a loop
* reversing with `[::-1]`

Happy coding! 💻✨
