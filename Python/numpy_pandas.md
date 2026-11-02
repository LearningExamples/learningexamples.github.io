---
title: NumPy, Pandas and Matplotlib
parent: Python
nav_order: 16
---


# 📊 Data Tools in Python: NumPy, Pandas, and Matplotlib

Plain Python lists and loops can handle almost anything, but for **numbers and tables** there are three libraries nearly every Python programmer uses:

* **NumPy** for fast arrays and math on many numbers at once
* **Pandas** for tables of data (think "spreadsheets in Python")
* **Matplotlib** for drawing charts

They're built to work together: NumPy is the engine, Pandas organizes the data, and Matplotlib shows it.

If you don't have them yet, install them with `pip install numpy pandas matplotlib` (they come pre-installed in Google Colab). By convention, everyone imports them like this:

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

Let's crunch some data! 🚀

---

# Part 1: NumPy 🔢

## 1. Arrays vs. Lists

A NumPy **array** looks a lot like a list, but it's designed for math. Convert a list with `np.array()`:

```python
import numpy as np

list1 = [1, 2, 3]
list2 = [4, 5, 6]

array1 = np.array(list1)
array2 = np.array(list2)

print(f"Array 1: {array1}")
print(f"Array 2: {array2}")
```

**Output:**

```
Array 1: [1 2 3]
Array 2: [4 5 6]
```

(Arrays print without commas.)

### The big difference: operators work element by element

```python
# Lists
print(list1 + list2)     # concatenates
print(list1 * 2)         # repeats

# Arrays
print(array1 + array2)   # adds matching elements
print(array1 * array2)   # multiplies matching elements
print(array1 * 2)        # multiplies EVERY element by 2
```

**Output:**

```
[1, 2, 3, 4, 5, 6]
[1, 2, 3, 1, 2, 3]
[5 7 9]
[ 4 10 18]
[2 4 6]
```

| Operation | List | NumPy array |
|---|---|---|
| `a + b` | Joins the two lists | Adds element by element |
| `a * 2` | Repeats the list | Doubles every element |
| `a * b` | ❌ `TypeError` | Multiplies element by element |
| Different sizes with `+` | Works (just joins) | ❌ `ValueError` |

```python
np.array([1, 2, 3]) + np.array([1, 2])
# ValueError: operands could not be broadcast together with shapes (3,) (2,)
```

> With arrays you don't need a loop to do math on every item. That's faster to write **and** much faster to run.
{: .note }

---

## 2. Shape, Size, and Dimensions 📐

Arrays can have many dimensions. A 2D array is like a table with rows and columns.

```python
my_array = np.array([[1, 2, 3], [21, 22, 23]])

print(my_array)
print(f"shape: {my_array.shape}")   # (rows, columns)
print(f"size:  {my_array.size}")    # total number of elements
print(f"ndim:  {my_array.ndim}")    # number of dimensions
```

**Output:**

```
[[ 1  2  3]
 [21 22 23]]
shape: (2, 3)
size:  6
ndim:  2
```

A 3D array of random numbers between 0 and 1, with 3 layers, 4 rows, and 2 columns:

```python
random_3d = np.random.rand(3, 4, 2)

print(f"shape: {random_3d.shape}")   #=> (3, 4, 2)
print(f"size:  {random_3d.size}")    #=> 24
print(f"ndim:  {random_3d.ndim}")    #=> 3
```

---

## 3. Creating Arrays 🏗️

```python
print(np.array([1, 2, 3, 4, 5]))   # from a list
print(np.zeros((2, 3)))            # 2 rows x 3 columns of 0.0
print(np.ones((3, 2)))             # 3 rows x 2 columns of 1.0
print(np.full((2, 2), 19))         # 2 x 2, every value is 19
```

**Output:**

```
[1 2 3 4 5]
[[0. 0. 0.]
 [0. 0. 0.]]
[[1. 1.]
 [1. 1.]
 [1. 1.]]
[[19 19]
 [19 19]]
```

Pass a tuple with three numbers to get a 3D array, for example `np.zeros((2, 3, 4))`.

---

## 4. Indexing and Slicing 🎯

With a list of lists you write `my_list[1][2]`. With a NumPy array you can write `my_array[1, 2]`: **row first, then column**.

```python
sample = np.array([[10, 11, 12],
                   [13, 14, 15],
                   [16, 17, 18]])

print(sample[0, 1])       # row 0, column 1
print(sample[1, :])       # all of row 1
print(sample[:, 2])       # all of column 2
print(sample[0:2, 0:2])   # rows 0-1, columns 0-1
```

**Output:**

```
11
[13 14 15]
[12 15 18]
[[10 11]
 [13 14]]
```

`:` on its own means "everything along this dimension". Grabbing a whole **column** like this is something plain lists can't do easily.

---

## 5. Reshaping and Rearranging 🔄

```python
my_array = np.array([[10, 11, 12, 11],
                     [16, 17, 18, 11]])    # shape (2, 4)

print(my_array.reshape(4, 2))   # same 8 numbers, new shape (4, 2)
print(my_array.transpose())     # rows become columns
print(my_array.ravel())         # flatten to 1D
```

**Output:**

```
[[10 11]
 [12 11]
 [16 17]
 [18 11]]
[[10 16]
 [11 17]
 [12 18]
 [11 11]]
[10 11 12 11 16 17 18 11]
```

The new shape must hold the same number of elements: `reshape(3, 3)` on 8 numbers raises a `ValueError`.

### Deleting and sorting

```python
sample = np.array([[10, 11, 12],
                   [13, 14, 15],
                   [16, 17, 18]])

smaller = np.delete(sample, obj=1, axis=0)   # delete row 1 (axis=1 would delete a column)
print(smaller)
print(sample)                                # unchanged: np.delete returns a copy

arr = np.array([2, 4, 1, 3])
arr.sort()                                   # sorts in place
print(arr)
```

**Output:**

```
[[10 11 12]
 [16 17 18]]
[[10 11 12]
 [13 14 15]
 [16 17 18]]
[1 2 3 4]
```

---

## 6. Math on Whole Arrays 🧮

NumPy's math functions work on every element at once:

```python
array1 = np.array([1, 2, 3])

print(np.sin(array1))     # sine of each element
print(np.sqrt(array1))    # square root of each element
print(np.max(array1))     # largest value
print(np.sum(array1))     # total
print(np.mean(array1))    # average
print(np.var(array1))     # variance
```

**Output:**

```
[0.84147098 0.90929743 0.14112001]
[1.         1.41421356 1.73205081]
3
6
2.0
0.6666666666666666
```

Compare that with `math.sqrt()`, which only accepts **one** number at a time.

---

# Part 2: Pandas 🐼

## 7. DataFrames

A **DataFrame** is a table with labelled **columns** and an **index** (row labels).
The easiest way to make one is from a dictionary, where each key becomes a column:

```python
import pandas as pd

data = {
    "Name": ["Alice", "Bob", "Charlie", "David"],
    "Age": [25, 30, 35, 40],
    "City": ["New York", "Los Angeles", "Chicago", "Houston"],
}

df = pd.DataFrame(data)
print(df)
```

**Output:**

```
      Name  Age         City
0    Alice   25     New York
1      Bob   30  Los Angeles
2  Charlie   35      Chicago
3    David   40      Houston
```

The numbers `0` to `3` on the left are the **index**. Pandas created them automatically.

You can also build a DataFrame from a list of rows and name the columns and index yourself:

```python
sample_df = pd.DataFrame(
    data=[["abc", 3.3, 28, True], ["xyz", -0.55, 0, False]],
    columns=["Label1", "Label2", "Label3", "Label4"],
    index=[0, 1],
)
print(sample_df)
print(f"shape: {sample_df.shape}")
```

**Output:**

```
  Label1  Label2  Label3  Label4
0    abc    3.30      28    True
1    xyz   -0.55       0   False
shape: (2, 4)
```

### Peeking at the data

```python
df.head(2)    # first 2 rows (default is 5)
df.tail(2)    # last 2 rows
```

In a notebook, the value on the **last line** of a cell is displayed automatically as a nice table. Anywhere else, wrap it in `print()` or `display()`:

```python
display(df.tail(2))   # without display() this wouldn't show, because it isn't the last line
print("hello")
```

---

## 8. Reading and Writing CSV Files 💾

```python
df.to_csv("people.csv", index=False)   # index=False: don't save the 0,1,2,3 column

df_from_csv = pd.read_csv("people.csv")
print(df_from_csv)
```

`people.csv` looks like this:

```
Name,Age,City
Alice,25,New York
Bob,30,Los Angeles
Charlie,35,Chicago
David,40,Houston
```

Unlike the `csv` module, `read_csv()` converts number columns to real numbers for you.

💡 Need a bigger dataset to practise with? NumPy can generate one:

```python
import numpy as np

dummy = pd.DataFrame({
    "ID": np.arange(1, 21),                                   # 1 to 20
    "Name": [f"Person_{i}" for i in range(1, 21)],
    "Age": np.random.randint(20, 60, 20),                     # 20 random ages
    "City": np.random.choice(["New York", "Chicago", "Miami"], 20),
})
dummy.to_csv("dummy_data.csv", index=False)
```

---

## 9. Selecting Data: `iloc` and `loc` 🔍

There are two ways to pick rows and columns:

* **`iloc`** selects by **position** (0, 1, 2, ...), like list indexing. The end of a slice is **excluded**.
* **`loc`** selects by **label** (index values and column names). The end of a slice is **included**.

### `iloc`: by position

```python
print(df.iloc[1:3])          # rows at positions 1 and 2, all columns
print(df.iloc[1:3, 0:2])     # same rows, first two columns
```

**Output:**

```
      Name  Age         City
1      Bob   30  Los Angeles
2  Charlie   35      Chicago
      Name  Age
1      Bob   30
2  Charlie   35
```

You can also pass lists of positions, as in `df.iloc[[0, 3], :]` or `df.iloc[:, [0, 2]]`.

### `loc`: by label

```python
print(df.loc[0:2, ["Name", "Age"]])   # rows labelled 0, 1, AND 2
print(df.loc[2])                      # one row
```

**Output:**

```
      Name  Age
0    Alice   25
1      Bob   30
2  Charlie   35
Name    Charlie
Age          35
City    Chicago
Name: 2, dtype: object
```

A single row comes back as a **Series** (a one-column, labelled list).

| | `df.iloc[...]` | `df.loc[...]` |
|---|---|---|
| Selects by | Position | Label |
| `0:2` includes | rows 0, 1 | rows 0, 1, **2** |
| Columns written as | numbers (`0:2`) | names (`["Name", "Age"]`) |

### Picking columns

```python
print(df[["Name", "Age"]])   # double brackets: a list of column names
```

**Output:**

```
      Name  Age
0    Alice   25
1      Bob   30
2  Charlie   35
3    David   40
```

### Filtering rows with a condition

Put a condition inside the brackets to keep only the matching rows:

```python
print(df[df["Age"] > 30])
print(df.loc[df["Age"] > 30, ["Name", "City"]])   # filter rows AND pick columns
```

**Output:**

```
      Name  Age     City
2  Charlie   35  Chicago
3    David   40  Houston
      Name     City
2  Charlie  Chicago
3    David  Houston
```

To combine conditions, use `&` (and), `|` (or), and `~` (not) instead of the words `and`, `or`, and `not`, and wrap each condition in parentheses:

```python
print(df[(df["Age"] > 25) & (df["City"] != "Chicago")])
```

**Output:**

```
    Name  Age         City
1    Bob   30  Los Angeles
3  David   40      Houston
```

---

## 10. Changing DataFrames ✏️

> Most DataFrame methods return a **new** DataFrame and leave the original alone. To keep the result, assign it to a variable: `df = df.drop(...)`.
{: .note }

### Dropping rows and columns

```python
print(df.drop(index=0))            # remove the row labelled 0
print(df.drop(columns=["City"]))   # remove the City column
```

**Output:**

```
      Name  Age         City
1      Bob   30  Los Angeles
2  Charlie   35      Chicago
3    David   40      Houston
      Name  Age
0    Alice   25
1      Bob   30
2  Charlie   35
3    David   40
```

### Removing duplicates

```python
df_dups = pd.DataFrame({
    "ID": [1, 2, 3, 1, 4],
    "Name": ["Alice", "Bob", "Charlie", "Alice", "David"],
    "Score": [85, 90, 78, 85, 92],
})

print(df_dups.drop_duplicates(subset=["Name", "Score"]))
```

**Output:**

```
   ID     Name  Score
0   1    Alice     85
1   2      Bob     90
2   3  Charlie     78
4   4    David     92
```

The row labelled `3` repeated Alice's name **and** score, so it was dropped.

### Inserting a column

`insert()` is one of the few methods that changes the DataFrame **in place**, so we work on a copy:

```python
df_with_rank = df.copy()
df_with_rank.insert(loc=1, column="Rank", value=[1, 2, 3, 4])
print(df_with_rank)
```

**Output:**

```
      Name  Rank  Age         City
0    Alice     1   25     New York
1      Bob     2   30  Los Angeles
2  Charlie     3   35      Chicago
3    David     4   40      Houston
```

### Replacing values

```python
df_replaced = df.copy()
df_replaced["City"] = df_replaced["City"].replace("Houston", "Dallas")
print(df_replaced)
```

**Output:**

```
      Name  Age         City
0    Alice   25     New York
1      Bob   30  Los Angeles
2  Charlie   35      Chicago
3    David   40       Dallas
```

### Sorting

```python
print(df.sort_values(by="Age", ascending=False))
```

**Output:**

```
      Name  Age         City
3    David   40      Houston
2  Charlie   35      Chicago
1      Bob   30  Los Angeles
0    Alice   25     New York
```

Notice that each row keeps its original index label.

---

# Part 3: Matplotlib 📈

## 11. Your First Line Plot

```python
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd

# 1. Generate data with NumPy
x = np.linspace(0, 10, 100)                       # 100 evenly spaced points from 0 to 10
y = np.sin(x) + np.random.normal(0, 0.1, 100)     # a sine wave plus a little random noise

# 2. Put it in a DataFrame
df_plot = pd.DataFrame({"X_values": x, "Y_values": y})

# 3. Plot it
plt.figure(figsize=(8, 4))                         # width, height in inches
plt.plot(df_plot["X_values"], df_plot["Y_values"], label="Sine wave with noise", color="skyblue")
plt.title("My First Plot")
plt.xlabel("X-axis (time)")
plt.ylabel("Y-axis (my energy)")
plt.grid(True)
plt.legend()
plt.show()
```

This draws a wavy light-blue line with a title, axis labels, a grid, and a legend in the corner.

| Function | What it does |
|---|---|
| `plt.figure(figsize=(w, h))` | Start a new figure of a given size |
| `plt.plot(x, y)` | Draw a line through the points |
| `plt.title("...")` | Add a title |
| `plt.xlabel("...")`, `plt.ylabel("...")` | Label the axes |
| `plt.grid(True)` | Show grid lines |
| `plt.legend()` | Show the `label=` of each line |
| `plt.show()` | Display the figure |

💡 Changing `figsize` changes the shape of the chart: `(12, 6)` is wide, and `(6, 8)` is tall and narrow.

---

## 12. Several Lines on One Plot

Every `plt.plot()` call before `plt.show()` draws onto the **same** figure:

```python
y_cos = np.cos(x) + np.random.normal(0, 0.1, 100)

plt.figure(figsize=(10, 6))
plt.plot(x, y, label="Sine Wave with Noise", color="blue", linestyle="--")
plt.plot(x, y_cos, label="Cosine Wave with Noise", color="red", linestyle="--")
plt.title("Overlapping Sine and Cosine Waves")
plt.xlabel("X-axis")
plt.ylabel("Y-axis")
plt.grid(True)
plt.legend()     # the legend tells the two lines apart
plt.show()
```

If you call `plt.show()` (or start a new `plt.figure()`) between the two `plot` calls, you get two separate charts instead.

---

## 13. Scatter Plots, Text, and Annotations

```python
x = np.linspace(0, 10, 50)
y = np.sin(x) + np.random.normal(0, 0.2, 50)
df_plot = pd.DataFrame({"X_values": x, "Y_values": y})

plt.figure(figsize=(10, 6))

# dots instead of a line; marker sets the shape, s sets the size
plt.scatter(df_plot["X_values"], df_plot["Y_values"], marker=">", s=100, color="purple", label="Data Points")

plt.title("Scatter Plot of Sine Wave with Noise")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")

# plain text at position (x=1, y=0.8)
plt.text(1, 0.8, "Important Area", fontsize=12, color="green")

# an arrow pointing at the highest point
max_index = df_plot["Y_values"].idxmax()     # the index of the largest value
max_x = df_plot["X_values"][max_index]
max_y = df_plot["Y_values"][max_index]
plt.annotate(
    f"Peak: ({max_x:.2f}, {max_y:.2f})",
    xy=(max_x, max_y),                        # where the arrow points
    xytext=(max_x + 1.5, max_y + 0.5),        # where the text sits
    arrowprops=dict(facecolor="black", shrink=0.05),
)

plt.legend(loc="upper right")
plt.grid(True)
plt.show()
```

* `plt.scatter()` draws individual points. Try markers like `"o"`, `"*"`, `"s"` (square), or `">"`.
* `plt.text(x, y, s)` writes text at a data position.
* `plt.annotate(s, xy, xytext)` writes text with an arrow pointing at `xy`.

---

## 14. Side-by-Side Charts with Subplots

`plt.subplot(rows, columns, number)` splits one figure into a grid and selects which cell to draw in:

```python
x = np.linspace(0, 10, 100)
y_sin = np.sin(x) + np.random.normal(0, 0.1, 100)
y_cos = np.cos(x) + np.random.normal(0, 0.1, 100)

plt.figure(figsize=(12, 5))

plt.subplot(1, 2, 1)       # 1 row, 2 columns, plot #1 (left)
plt.plot(x, y_sin, color="skyblue", label="Sine Wave")
plt.title("Sine Wave")
plt.grid(True)
plt.legend()

plt.subplot(1, 2, 2)       # 1 row, 2 columns, plot #2 (right)
plt.plot(x, y_cos, color="lightcoral", label="Cosine Wave")
plt.title("Cosine Wave")
plt.grid(True)
plt.legend()

plt.tight_layout()         # stop the labels from overlapping
plt.show()
```

---

## 📋 Quick Reference Cheat Sheet

### NumPy

| Task | Code |
|---|---|
| List to array | `np.array([1, 2, 3])` |
| Zeros / ones / fill | `np.zeros((2, 3))`, `np.ones((3, 2))`, `np.full((2, 2), 7)` |
| Evenly spaced numbers | `np.linspace(0, 10, 100)` |
| Random numbers | `np.random.rand(3, 4)`, `np.random.randint(20, 60, 20)` |
| Shape info | `a.shape`, `a.size`, `a.ndim` |
| Element / row / column | `a[r, c]`, `a[r, :]`, `a[:, c]` |
| Reshape / flip / flatten | `a.reshape(4, 2)`, `a.transpose()`, `a.ravel()` |
| Math | `np.sin(a)`, `np.sqrt(a)`, `np.sum(a)`, `np.mean(a)`, `np.max(a)` |

### Pandas

| Task | Code |
|---|---|
| From a dictionary | `pd.DataFrame({"col": [...]})` |
| Read / write CSV | `pd.read_csv("f.csv")`, `df.to_csv("f.csv", index=False)` |
| First / last rows | `df.head(n)`, `df.tail(n)` |
| By position | `df.iloc[0:3, 1:3]` |
| By label | `df.loc[0:2, ["Name", "Age"]]` |
| Pick columns | `df[["Name", "Age"]]` |
| Filter rows | `df[df["Age"] > 30]` |
| Drop | `df.drop(index=0)`, `df.drop(columns=["City"])` |
| Remove duplicates | `df.drop_duplicates(subset=[...])` |
| Add a column | `df.insert(loc, column, value)` |
| Replace values | `df["City"].replace("old", "new")` |
| Sort | `df.sort_values(by="Age", ascending=False)` |

### Matplotlib

| Task | Code |
|---|---|
| Line plot | `plt.plot(x, y, label="...")` |
| Scatter plot | `plt.scatter(x, y, marker="o")` |
| Labels and title | `plt.title()`, `plt.xlabel()`, `plt.ylabel()` |
| Legend / grid | `plt.legend()`, `plt.grid(True)` |
| Size | `plt.figure(figsize=(w, h))` |
| Side-by-side | `plt.subplot(1, 2, 1)` ... `plt.tight_layout()` |
| Show it | `plt.show()` |

---

## 🎉 Conclusion

You've just met the three libraries behind most data work in Python! 🙌

* **NumPy** arrays do math on every element at once, with no loops needed
* **Pandas** DataFrames make it easy to load, select, filter, and clean tables
* **Matplotlib** turns those numbers into line plots, scatter plots, and subplots

A great next step: take a CSV file you care about (sports stats, grades, weather), load it with `pd.read_csv()`, filter it, and plot something interesting.

Happy coding! 💻✨
