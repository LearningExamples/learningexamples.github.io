---
title: Python Files and CSV
parent: Python
nav_order: 15
---


# 📂 Working with Files in Python: Text, Folders, Binary, and CSV

Variables disappear when your program ends. **Files** let your data stick around: you can save results, read in data someone else prepared, keep logs, and share information between programs.

In this tutorial you'll learn how to:

* open, read, and close text files
* read a file all at once, as a list of lines, or line by line
* write and append to files (and pick the right **mode**)
* use `with` so files close themselves
* work with folders and paths using the `os` module
* read binary files like images
* read and write **CSV** files with the `csv` module

Let's open some files! 🚀

---

## 1. Opening, Reading, and Closing 📖

First, let's create a small file to play with. (We'll explain the `with` and `"w"` parts in a moment.)

```python
with open("sample_text.txt", "w") as f:
    f.write("This is a sample text file.\n")
    f.write("You can read this content back later.")
```

The basic recipe for reading has three steps: **open**, **read**, **close**.

```python
f = open("sample_text.txt")   # 1. open (reading is the default)
my_data = f.read()            # 2. read EVERYTHING into one string
f.close()                     # 3. close

print(my_data)
print(type(my_data))
```

**Output:**

```
This is a sample text file.
You can read this content back later.
<class 'str'>
```

### The file "cursor"

An open file remembers how far you've read. After `read()` has read everything, the cursor sits at the end, so a second `read()` gives you an empty string:

```python
f = open("sample_text.txt")
first = f.read()
second = f.read()
f.close()

print(f"{second = }")
```

**Output:**

```
second = ''
```

And once a file is closed, you can't read from it any more:

```python
f.read()   # ValueError: I/O operation on closed file.
```

> If you try to open a file that doesn't exist, you get a `FileNotFoundError`. You can catch it with `try` / `except` like any other exception.
{: .note }

---

## 2. Reading Line by Line 📃

### `readlines()`: a list of lines

```python
f = open("sample_text.txt")
my_lines = f.readlines()
f.close()

print(my_lines)
```

**Output:**

```
['This is a sample text file.\n', 'You can read this content back later.']
```

Each line keeps its newline character `\n` at the end (except, here, the last line, because we didn't write one).

### Looping over the file directly

A file object can be used directly in a `for` loop. You get one line per iteration, which is memory-friendly for very large files:

```python
f = open("sample_text.txt")
for line in f:
    print(line, end="")   # the line already ends with \n
f.close()
```

**Output:**

```
This is a sample text file.
You can read this content back later.
```

### Getting rid of `\n` with `strip()`

That trailing `\n` often gets in the way, for example when comparing a line to another string. `strip()` removes it, along with any other whitespace at the start and end:

```python
my_str = "hello\n"
print(my_str, "<- end of print")          # the \n pushes the text to a new line
print(my_str.strip(), "<- end of print")  # stripped
```

**Output:**

```
hello
 <- end of print
hello <- end of print
```

### 🧪 Practice: which words are in range?

Read a file of words (one per line) and two bounds from the user, then report whether each word falls between the bounds alphabetically:

```python
file_name = input()   # e.g. input1.txt
bound_1 = input()     # e.g. ammunition
bound_2 = input()     # e.g. millennium

user_file = open(file_name)
lines = user_file.readlines()     # ['aspiration\n', 'classified\n', ...]
user_file.close()

for line in lines:
    word = line.strip()           # remove the \n before comparing!
    if bound_1 <= word <= bound_2:
        print(f"{word} - in range")
    else:
        print(f"{word} - not in range")
```

If `input1.txt` contains `aspiration`, `classified`, `herald`, `lawman`, and `portrait`, the output is:

```
aspiration - in range
classified - in range
herald - in range
lawman - in range
portrait - not in range
```

Strings are compared letter by letter, like a dictionary: `"portrait"` comes after `"millennium"` because `p` comes after `m`.

---

## 3. Writing and Appending ✍️

Pass a **mode** as the second argument to `open()`:

```python
f = open("sample_text2.txt", "w")     # "w" = write
f.write("This is a sample text file.\n")
f.write("Second line\n")
f.close()
```

> `write()` does **not** add a newline for you. If you want each piece on its own line, include `\n` yourself.
{: .note }

### `"w"` vs. `"a"`

* `"w"` **erases** the file first (or creates it if it's missing), then writes.
* `"a"` **appends**: it keeps what's there and adds to the end.

```python
with open("write_example.txt", "w") as f:
    f.write("Dr. No ?\n")
    f.write("This is another line.")

with open("write_example.txt", "a") as f:
    f.write("\nDr. Gold ?\n")
    f.write("This is another line.")
```

`write_example.txt` now contains:

```
Dr. No ?
This is another line.
Dr. Gold ?
This is another line.
```

If the second block had used `"w"` instead of `"a"`, only the last two lines would be left.

### File modes at a glance

| Mode | Description | Read? | Write? | Creates missing file? | Erases existing contents? |
|---|---|---|---|---|---|
| `"r"` | Read (the default) | ✅ | ❌ | ❌ | ❌ |
| `"w"` | Write | ❌ | ✅ | ✅ | ✅ |
| `"a"` | Append to the end | ❌ | ✅ | ✅ | ❌ |
| `"rb"` | Read in **binary** | ✅ | ❌ | ❌ | ❌ |
| `"wb"` | Write in **binary** | ❌ | ✅ | ✅ | ✅ |

> `"w"` destroys whatever was in the file before, and there is no undo! Double-check your mode before writing to an important file.
{: .warning }

---

## 4. The `with` Statement: Files That Close Themselves 🚪

Forgetting `close()` is a common bug, and if an exception happens before `close()`, the file stays open. The `with` statement fixes both problems: the file is closed **automatically** when the indented block ends, even if an error occurs.

```python
file_name = "my_simple_file.txt"

with open(file_name, "w") as f:
    f.write("Hello, this is a simple text.\nThis is the second line.")
# the file is already closed here, no f.close() needed

with open(file_name, "r") as f:
    read_content = f.read()

print(read_content)
```

**Output:**

```
Hello, this is a simple text.
This is the second line.
```

💡 Use `f` only **inside** the `with` block. Once the block ends, the file is closed.

### Bonus: `flush()`

When you write, Python first collects the text in a memory **buffer** and saves it to disk in chunks, because that's faster. `flush()` forces the buffer to be written right now. That's useful for log files you want to watch while a long program runs:

```python
import time

with open("flush_example.txt", "w") as f:
    f.write("This is the first line. It will be flushed.")
    f.flush()          # written to disk now
    time.sleep(3)      # you could open the file during this pause and see the line
    f.write("This is the second line.\n")
# closing the file (end of "with") flushes everything else
```

---

## 5. Folders and Paths with `os` 🗂️

The `os` module lets your program talk to the operating system: check whether files exist, build paths, and create folders.

### Checking files

```python
import os

print(os.path.exists("sample_text.txt"))    #=> True
print(os.path.exists("nope.txt"))           #=> False

file_info = os.stat("sample_text.txt")
print(file_info.st_size)                    #=> 65  (size in bytes)
```

### Building paths with `os.path.join()`

Windows separates folders with `\` and macOS/Linux use `/`. `os.path.join()` uses the right one automatically, so your code works everywhere:

```python
path = os.path.join("logs", "1997", "8", "29", "log.txt")
print(path)
#=> logs/1997/8/29/log.txt      (macOS / Linux)
#=> logs\1997\8\29\log.txt      (Windows)
```

Every piece must be a string. Passing a number raises `TypeError: join() argument must be str, bytes, or os.PathLike object, not 'int'`, so convert with `str()` first.

### Example: one log file per day

```python
import os
import datetime

curr_day = datetime.date(1997, 8, 29)

for i in range(3):
    year = str(curr_day.year)
    month = str(curr_day.month)
    day = str(curr_day.day)

    file_path = os.path.join("logs", year, month, day, "log.txt")

    # create the folders (exist_ok=True: no error if they already exist)
    os.makedirs(os.path.dirname(file_path), exist_ok=True)

    with open(file_path, "w") as f:
        f.write(f"This is a log entry for {curr_day}\n")

    with open(file_path) as f:
        print(f"{file_path}: {f.read()}", end="")

    curr_day = curr_day + datetime.timedelta(days=1)   # move to the next day
```

**Output** (on macOS/Linux):

```
logs/1997/8/29/log.txt: This is a log entry for 1997-08-29
logs/1997/8/30/log.txt: This is a log entry for 1997-08-30
logs/1997/8/31/log.txt: This is a log entry for 1997-08-31
```

* `os.path.dirname(file_path)` gives the folder part (`logs/1997/8/29`).
* `os.makedirs()` creates that folder **and** any missing parent folders.
* `datetime.timedelta(days=1)` adds one day and correctly rolls over months and years.

---

## 6. Binary Files 🖼️

Not every file is text. Images, audio, and many data formats are **binary**: raw bytes that aren't meant to be read as characters. Add `"b"` to the mode to work with them.

First, let's make a small image to test with. This uses the third-party **Pillow** library (`pip install pillow`):

```python
from PIL import Image, ImageDraw

img = Image.new("RGB", (200, 200), color="white")        # blank white image
draw = ImageDraw.Draw(img)
draw.ellipse((50, 50, 150, 150), fill="red", outline="red")   # a red circle
img.save("star.bmp")
```

Now read it back as raw bytes:

```python
with open("star.bmp", "rb") as f:    # "rb" = read binary
    contents = f.read()

print(type(contents))
print(contents[:2])
```

**Output:**

```
<class 'bytes'>
b'BM'
```

Binary data comes back as a `bytes` object (notice the `b` before the quotes). Every BMP file starts with the two bytes `BM`.

### Going further: packing values into bytes with `struct` (optional)

The `struct` module converts Python values to and from exact byte layouts, which is how many binary file formats are built:

```python
import struct

# '<' little-endian, 'i' 4-byte int, 'f' 4-byte float, '?' 1-byte bool, '10s' 10-byte string
format_string = "<if?10s"

packed_data = struct.pack(format_string, 12345, 3.14159, True, b"hello")
print(f"Packed data: {packed_data}")
print(f"Size: {len(packed_data)} bytes")

values = struct.unpack(format_string, packed_data)
print(values[0], values[1], values[2], values[3].decode().strip("\x00"))
```

**Output:**

```
Packed data: b'90\x00\x00\xd0\x0fI@\x01hello\x00\x00\x00\x00\x00'
Size: 19 bytes
12345 3.141590118408203 True hello
```

Notice that the float came back as `3.141590118408203`: a 4-byte float can't store every digit exactly.

---

## 7. CSV Files 📊

**CSV** (Comma-Separated Values) is the most common format for tables of data. Each line is a row, and commas separate the columns:

```
name,hw1,hw2,midterm,final
Petr Little,9,8,85,78
Sam Tarley,10,10,99,100
Joff King,4,2,55,61
```

You *could* split each line on `","` yourself, but the built-in `csv` module handles the tricky cases for you (like a comma inside a quoted name).

### Writing a CSV file

```python
import csv

data = [
    ["name", "hw1", "hw2", "midterm", "final"],   # header row
    ["Petr Little", 9, 8, 85, 78],
    ["Sam Tarley", 10, 10, 99, 100],
    ["Joff King", 4, 2, 55, 61],
]

with open("grades.csv", "w", newline="") as csvfile:
    csv_writer = csv.writer(csvfile)
    csv_writer.writerows(data)        # write all rows at once

print("Data successfully written to grades.csv")
```

* `writerow(row)` writes **one** row, and `writerows(rows)` writes a list of rows. You could also call `writerow()` in a loop.
* Always open CSV files with `newline=""`. Without it, you can get blank lines between rows on Windows.

### Reading a CSV file

```python
import csv

with open("grades.csv", "r", newline="") as csvfile:
    csv_reader = csv.reader(csvfile)
    for row in csv_reader:
        print(row)
```

**Output:**

```
['name', 'hw1', 'hw2', 'midterm', 'final']
['Petr Little', '9', '8', '85', '78']
['Sam Tarley', '10', '10', '99', '100']
['Joff King', '4', '2', '55', '61']
```

> Every value comes back as a **string**, even the numbers. Convert them with `int()` or `float()` before doing math.
{: .warning }

### Skipping the header and doing math

Two common ways to skip the header row:

```python
import csv

# Way 1: count the rows
total = 0
with open("grades.csv", "r") as csvfile:
    grades_reader = csv.reader(csvfile, delimiter=",")
    row_num = 1
    for row in grades_reader:
        if row_num == 1:
            row_num += 1          # skip the header
        else:
            scores = [float(i) for i in row[1:]]   # everything after the name
            total += sum(scores)
print(total)
```

**Output:**

```
521.0
```

```python
import csv

# Way 2: next() reads one row and moves past it
with open("grades.csv", "r", newline="") as csvfile:
    reader = csv.reader(csvfile)
    header = next(reader)                  # ['name', 'hw1', 'hw2', 'midterm', 'final']
    for row in reader:
        name = row[0]
        scores = [float(s) for s in row[1:]]
        print(name, sum(scores))
```

**Output:**

```
Petr Little 180.0
Sam Tarley 219.0
Joff King 122.0
```

💡 The reader also counts lines for you: after looping, `csv_reader.line_num` tells you how many lines were read.

For bigger data, the **pandas** library can read a whole CSV into a table in one line (`pd.read_csv("grades.csv")`). It's covered in the NumPy, Pandas and Matplotlib tutorial.

---

## 📋 Quick Reference Cheat Sheet

| Task | Code |
|---|---|
| Read whole file | `with open(name) as f: text = f.read()` |
| Read list of lines | `lines = f.readlines()` |
| Loop over lines | `for line in f:` |
| Remove the newline | `line.strip()` |
| Write (erase first) | `with open(name, "w") as f: f.write("text\n")` |
| Append | `with open(name, "a") as f: f.write("more\n")` |
| Force write to disk | `f.flush()` |
| File exists? | `os.path.exists(name)` |
| File size | `os.stat(name).st_size` |
| Build a path | `os.path.join("folder", "file.txt")` |
| Create folders | `os.makedirs(path, exist_ok=True)` |
| Read binary | `open(name, "rb")` |
| Write CSV | `csv.writer(f).writerows(rows)` |
| Read CSV | `for row in csv.reader(f):` |
| Skip CSV header | `next(reader)` |

---

## 🎉 Conclusion

Your programs can now remember things! 🙌

* `open()`, `read()`, `readlines()`, and `close()` are the basics of reading
* `"w"` overwrites, `"a"` appends, and `"b"` switches to binary
* `with open(...) as f:` closes the file for you
* `os` handles paths and folders across operating systems
* the `csv` module reads and writes tables, but remember that values come back as strings

Happy coding! 💻✨
