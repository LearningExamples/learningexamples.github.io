---
layout: default
title: Generic Types and Recursion
parent: Java
nav_order: 2
---

# Generic Types and Recursion in Java

Welcome! This tutorial covers two powerful concepts in Java: **Generic Types** and **Recursion**. Generics allow you to write flexible, type-safe code that works with different data types, while recursion provides an elegant way to solve complex problems by breaking them down into smaller, self-similar pieces. Let's dive in! 🧑‍💻

---

## Part 1: Generic Types ⚙️

Generics are like templates for your classes, interfaces, and methods. They allow you to define an algorithm or data structure with a "placeholder" for a data type, which is then specified when you use it.

The primary benefits of generics are:
* **Stronger type-checking at compile time:** You catch invalid type errors during development, not at runtime.
* **Elimination of casts:** You don't need to manually cast objects, which makes code cleaner and safer.
* **Enabling programmers to implement generic algorithms:** You can write code that works on collections of different types without rewriting it for each type.

### Generic Classes

A generic class is defined using angle brackets `<>` containing a type parameter. The most common placeholder is `T` for "Type".

Let's create a `Box` class that can hold an object of any type.



```java
// A simple generic class
public class Box<T> {
    // T stands for "Type"
    private T content;

    public void set(T content) {
        this.content = content;
    }

    public T get() {
        return content;
    }
}
````

**How to Use It:**

When you create an instance of `Box`, you specify the actual type you want it to hold.

```java
public class Main {
    public static void main(String[] args) {
        // Create a Box to hold an Integer
        Box<Integer> integerBox = new Box<>();
        integerBox.set(123);
        Integer myInt = integerBox.get(); // No cast needed!
        System.out.println("Integer value: " + myInt);

        // Create a Box to hold a String
        Box<String> stringBox = new Box<>();
        stringBox.set("Hello World");
        String myString = stringBox.get(); // No cast needed!
        System.out.println("String value: " + myString);
    }
}
```

### Generic Methods

You can also create methods that are generic, even if they are inside a non-generic class. The type parameter is declared before the method's return type.

```java
public class Utility {
    // A generic method to print the elements of any array
    public static <E> void printArray(E[] inputArray) {
        for (E element : inputArray) {
            System.out.printf("%s ", element);
        }
        System.out.println();
    }

    public static void main(String[] args) {
        // Create arrays of Integer, Double, and Character
        Integer[] intArray = { 1, 2, 3, 4, 5 };
        Double[] doubleArray = { 1.1, 2.2, 3.3, 4.4 };
        Character[] charArray = { 'H', 'E', 'L', 'L', 'O' };

        System.out.println("Array integerArray contains:");
        printArray(intArray); // Pass an Integer array

        System.out.println("\nArray doubleArray contains:");
        printArray(doubleArray); // Pass a Double array

        System.out.println("\nArray characterArray contains:");
        printArray(charArray); // Pass a Character array
    }
}
```

-----

## Part 2: Recursion 🧠

Recursion is a technique where a method calls itself to solve a problem. Think of it like a set of Russian nesting dolls; each doll contains a smaller, identical version of itself until you reach the smallest one.

A recursive method must have two parts:

1.  **Base Case:** A condition that **stops** the recursion. Without a base case, the method would call itself forever, leading to a `StackOverflowError`.
2.  **Recursive Step:** The part of the method that calls itself, but with a slightly modified input that moves it closer to the base case.

### Classic Example: The Factorial Function

The factorial of a non-negative integer `n`, denoted by `n!`, is the product of all positive integers less than or equal to `n`. For example, `4! = 4 * 3 * 2 * 1 = 24`.

The problem can be defined recursively:

  * `n! = n * (n-1)!` (Recursive Step)
  * `0! = 1` (Base Case)

Here is the implementation in Java:

```java
public class Factorial {
    public static long calculateFactorial(int n) {
        // Base Case: if n is 0, factorial is 1. This stops the recursion.
        if (n <= 0) {
            return 1;
        }
        // Recursive Step: n * factorial of (n-1)
        else {
            return n * calculateFactorial(n - 1);
        }
    }

    public static void main(String[] args) {
        int number = 4;
        long result = calculateFactorial(number);
        System.out.println("The factorial of " + number + " is " + result); // Output: The factorial of 4 is 24
    }
}
```

**How it works for `calculateFactorial(4)`:**

1.  `calculateFactorial(4)` calls `calculateFactorial(3)` and waits.
2.  `calculateFactorial(3)` calls `calculateFactorial(2)` and waits.
3.  `calculateFactorial(2)` calls `calculateFactorial(1)` and waits.
4.  `calculateFactorial(1)` calls `calculateFactorial(0)`.
5.  `calculateFactorial(0)` hits the **base case** and returns `1`.
6.  `calculateFactorial(1)` gets `1` back and returns `1 * 1 = 1`.
7.  `calculateFactorial(2)` gets `1` back and returns `2 * 1 = 2`.
8.  `calculateFactorial(3)` gets `2` back and returns `3 * 2 = 6`.
9.  `calculateFactorial(4)` gets `6` back and returns `4 * 6 = 24`.

-----

## Part 3: Combining Generics and Recursion ✨

You can combine generics and recursion to create powerful, type-safe, and flexible algorithms, especially when working with data structures like trees or lists.

Let's create a generic recursive method to search for an item in a list.

### Example: Recursive Generic Search in a List

This method will search for a `target` element within a `List<T>`. It checks the first element, and if it's not a match, it recursively calls itself on the rest of the list.

```java
import java.util.List;
import java.util.ArrayList;
import java.util.Arrays;

public class GenericRecursiveSearch {

    /**
     * Recursively searches for a target element in a list.
     * @param <T> The generic type of the elements in the list.
     * @param list The list to search in.
     * @param target The element to search for.
     * @return true if the target is found, false otherwise.
     */
    public static <T> boolean contains(List<T> list, T target) {
        // Base Case 1: The list is empty, so the target cannot be in it.
        if (list.isEmpty()) {
            return false;
        }
        
        // Base Case 2: The first element of the list is our target.
        if (list.get(0).equals(target)) {
            return true;
        }
        
        // Recursive Step: Call the same method on a sublist (from the 2nd element to the end).
        return contains(list.subList(1, list.size()), target);
    }

    public static void main(String[] args) {
        // Search in a list of Strings
        List<String> names = new ArrayList<>(Arrays.asList("Alice", "Bob", "Charlie", "David"));
        boolean foundCharlie = contains(names, "Charlie");
        boolean foundEve = contains(names, "Eve");
        System.out.println("Found Charlie? " + foundCharlie); // true
        System.out.println("Found Eve? " + foundEve);         // false

        // Search in a list of Integers
        List<Integer> numbers = new ArrayList<>(Arrays.asList(10, 20, 30, 40, 50));
        boolean found30 = contains(numbers, 30);
        boolean found99 = contains(numbers, 99);
        System.out.println("Found 30? " + found30); // true
        System.out.println("Found 99? " + found99); // false
    }
}
```

In this example:

  * The method `contains` is **generic**, defined with `<T>`, so it can work with `List<String>`, `List<Integer>`, or a list of any other object type.
  * The logic is **recursive**, breaking the problem of searching a list into checking the first element and then solving the smaller problem of searching the *rest* of the list.

By mastering generics and recursion, you unlock the ability to write more abstract, reusable, and elegant solutions in Java. Happy coding\!



### Homework


Given an m x n 2D binary grid which represents a map of '1's (land) and '0's (water), return the number of islands. 
An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.


```java

public class NumberOfIslandsWithVisited {
	
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) {
            return 0;
        }
        
        int m = grid.length;
        int n = grid[0].length;
        boolean[][] visited = new boolean[m][n];
        int count = 0 ;
        
     // Traverse each cell  and not visited, start DFS
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
            	if (grid[i][j] == '1' && !visited[i][j]) {
            		count++;
            		dfs(grid, visited, i,j);
            	}
            	
           }
        }
        
        return count;
    }

    // Recursive DFS using visited
    private void dfs(char[][] grid, boolean[][] visited, int i, int j) {
        // Out of bounds or water or already visited
        if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length 
            || grid[i][j] == '0' || visited[i][j]) {
            return;
        }
        
        // Mark current cell as visited
        visited[i][j] = true;
    
        // Recursive 
        // Explore neighbors (up, down, left, right)
        dfs(grid, visited, i + 1, j);
        dfs(grid, visited, i - 1, j);
        dfs(grid, visited, i, j + 1);
        dfs(grid, visited, i, j - 1);   
    
    }

	
	// Example usage
    public static void main(String[] args) {
        NumberOfIslandsWithVisited solution = new NumberOfIslandsWithVisited();
        char[][] grid = {
            {'1','1','0','0','0'},
            {'1','1','0','0','0'},
            {'0','0','1','0','0'},
            {'0','0','0','1','1'}
        };
        
        System.out.println("Number of islands: " + solution.numIslands(grid));
    }

}


```