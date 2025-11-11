---
layout: default
title: Java Functional Programming
parent: Java
nav_order: 9
---



# ☕ A Simple Guide to Java Functional Programming

Welcome to this guide to **Functional Programming (FP)** in **Java**!

Java has traditionally been a very Object-Oriented (OOP) language. Since **Java 8**, however, it has adopted many powerful features from the functional programming world.

This tutorial will take you from the **“why” and “what” (easy)** to the **“how” and “where” (more advanced)** — with clear examples, explanations, and challenges.

---

## 🧩 Part 1: The Core Idea (Easy)

### 💡 What is Functional Programming?

In simple terms:

* **OOP (Object-Oriented)** is about modeling the world with *objects* that have *state* (fields) and *behavior* (methods). You change the state of objects.
* **FP (Functional Programming)** is about modeling the world with *functions* that transform *data*. You avoid changing state and focus on *what* you want done, not *how* to do it (declarative vs. imperative).

---

### 🚀 The “Hello, World!” of FP — Lambda Expressions

A **lambda expression** is an anonymous function — a function without a name — that you can pass around as if it were data.

#### 🧱 Before Java 8 (Anonymous Inner Class)

```java
Runnable myRunnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello, old way!");
    }
};

new Thread(myRunnable).start();
```

#### ✅ After Java 8 (Lambda Expression)

```java
Runnable myRunnable = () -> System.out.println("Hello, lambda!");
new Thread(myRunnable).start();
```

#### 🧠 Lambda Syntax

| Pattern                      | Example       | Meaning                            |
| ---------------------------- | ------------- | ---------------------------------- |
| `() -> value`                | `() -> "Hi!"` | No params, returns a value         |
| `x -> x * 2`                 | `(5) -> 10`   | One param, returns computed result |
| `(x, y) -> x + y`            | `(2,3) -> 5`  | Multiple params                    |
| `s -> System.out.println(s)` | `("Hi")`      | Executes a statement (no return)   |

---

## ⚙️ Part 2: Functional Interfaces (Medium-Easy)

A **functional interface** has **exactly one abstract method**.
Lambdas *must* be assigned to a functional interface type.

### 🧩 The Big Four (from `java.util.function`)

| Interface        | Method              | Type          | Purpose            | Example               |
| ---------------- | ------------------- | ------------- | ------------------ | --------------------- |
| `Predicate<T>`   | `boolean test(T t)` | `T → boolean` | Tests a condition  | `n -> n > 10`         |
| `Function<T, R>` | `R apply(T t)`      | `T → R`       | Transforms data    | `s -> s.length()`     |
| `Consumer<T>`    | `void accept(T t)`  | `T → void`    | Performs an action | `System.out::println` |
| `Supplier<T>`    | `T get()`           | `() → T`      | Supplies a value   | `() -> Math.random()` |

---

### 💻 Example: Using Functional Interfaces

```java
import java.util.function.*;

public class FunctionalInterfacesDemo {
    public static void main(String[] args) {
        Predicate<Integer> isGreaterThan10 = n -> n > 10;
        Function<String, Integer> getLength = s -> s.length();
        Consumer<String> printer = s -> System.out.println("Message: " + s);
        Supplier<Double> random = Math::random;

        System.out.println(isGreaterThan10.test(20)); // true
        System.out.println(getLength.apply("hello")); // 5
        printer.accept("I am a consumer!");           // Prints message
        System.out.println(random.get());             // Random number
    }
}
```

---

## 🌊 Part 3: Streams API (Medium-Hard)

Streams are where FP in Java *shines*.
A **Stream** is *not* a data structure — it’s a *pipeline* for processing data.

### 🧠 Stream Pipeline

```
Source → Intermediate Operations → Terminal Operation
```

---

### 🔸 Before and After Example

**Task:** Get all names starting with "A", convert to uppercase, collect in a list.

#### Imperative Style (Before Java 8)

```java
import java.util.*;

List<String> names = List.of("Alice", "Bob", "Anna", "Charlie", "Alex");
List<String> result = new ArrayList<>();

for (String name : names) {
    if (name.startsWith("A")) {
        result.add(name.toUpperCase());
    }
}
System.out.println(result); // [ALICE, ANNA, ALEX]
```

#### Declarative FP Style (After Java 8)

```java
import java.util.*;
import java.util.stream.Collectors;

List<String> result = List.of("Alice", "Bob", "Anna", "Charlie", "Alex")
        .stream()
        .filter(n -> n.startsWith("A"))
        .map(String::toUpperCase)
        .collect(Collectors.toList());

System.out.println(result); // [ALICE, ANNA, ALEX]
```

---

### 🧰 Common Stream Operations

| Type                 | Methods                             | Example         |
| -------------------- | ----------------------------------- | --------------- |
| **Intermediate**     | `filter()`, `map()`, `sorted()`     | Transform data  |
| **Terminal**         | `collect()`, `count()`, `forEach()` | Produce results |
| **Short-Circuiting** | `limit()`, `anyMatch()`             | Stop early      |

---

### 🧮 Example: `filter`, `map`, `reduce`

```java
import java.util.List;

int sumOfEvenSquares = List.of(1,2,3,4,5,6)
    .stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .reduce(0, Integer::sum);

System.out.println(sumOfEvenSquares); // 56
```

---

## 💡 Part 4: Advanced Concepts (Hard)

### 🔸 1. `Optional<T>` — Handling Missing Values Safely

```java
import java.util.Optional;

Optional<String> email = Optional.ofNullable(null);

System.out.println(email.orElse("default@example.com")); // default@example.com

email.ifPresent(e -> System.out.println("Found: " + e));
```

---

### 🔸 2. Method References — Shorthand for Lambdas

| Type        | Lambda                     | Method Reference      |
| ----------- | -------------------------- | --------------------- |
| Static      | `s -> Integer.parseInt(s)` | `Integer::parseInt`   |
| Instance    | `s -> s.toUpperCase()`     | `String::toUpperCase` |
| Constructor | `() -> new ArrayList<>()`  | `ArrayList::new`      |

Example:

```java
List<String> names = List.of("Alice", "Bob", "Anna");
names.stream().map(String::toUpperCase).forEach(System.out::println);
```

---

### 🔸 3. Collecting & Grouping

```java
import java.util.*;
import java.util.stream.Collectors;

List<String> names = List.of("Alice", "Bob", "Anna", "Charlie", "Alex");

Map<Character, List<String>> grouped = names.stream()
        .collect(Collectors.groupingBy(name -> name.charAt(0)));

System.out.println(grouped);
// {A=[Alice, Anna, Alex], B=[Bob], C=[Charlie]}
```

---

### 🔸 4. Function Composition

```java
import java.util.function.Function;

Function<Integer, Integer> doubleIt = x -> x * 2;
Function<Integer, Integer> squareIt = x -> x * x;

Function<Integer, Integer> combined = doubleIt.andThen(squareIt);
System.out.println(combined.apply(3)); // (3*2)^2 = 36
```

---

## ⚠️ Common Pitfalls

1. Streams can only be **used once**.
2. Don’t use `map()` for side effects — use `forEach()` instead.
3. `Optional` is *not* a replacement for every `null` — it’s for API boundaries.
4. Mixing stateful variables inside streams can lead to bugs.

---

## 🧩 Practice Challenges

### 🧠 Easy

1. Create a lambda that prints `"Hello Functional Java!"`.
2. Use a `Predicate<Integer>` to test if a number is positive.
3. Use a `Consumer<String>` to print strings in uppercase.

### ⚙️ Medium

1. Use a Stream to return all even numbers squared from a list.
2. Convert a list of Celsius temperatures to Fahrenheit using `map()`.
3. Use `Optional` to safely print an email if present.

### 🚀 Hard

1. Use `Collectors.groupingBy()` to group products by category.
2. Combine two `Predicate`s using `.and()` and `.or()`.
3. Use `reduce()` to calculate factorial of a number list.

---

## 🧠 Java FP Quick Reference

| Concept    | Example                           | Description               |
| ---------- | --------------------------------- | ------------------------- |
| Lambda     | `(x, y) -> x + y`                 | Inline anonymous function |
| Predicate  | `x -> x > 10`                     | Boolean condition         |
| Function   | `x -> x * 2`                      | Value transformer         |
| Consumer   | `System.out::println`             | Performs an action        |
| Supplier   | `() -> Math.random()`             | Supplies a value          |
| Stream     | `.filter().map().collect()`       | Functional data pipeline  |
| Optional   | `Optional.ofNullable(x)`          | Safe null handling        |
| Method Ref | `String::toUpperCase`             | Shorter lambda syntax     |
| Collectors | `groupingBy`, `joining`, `toList` | Stream aggregation        |

---

## 🚀 Next Steps

* Learn about **parallel streams** for performance.
* Explore **Reactive Streams** (`RxJava`, `Reactor`).
* Study **pure functions**, **immutability**, and **higher-order functions**.
* Refactor old Java code using lambdas and streams!

---

## ✅ Conclusion

You’ve learned:

1. **Lambdas** — concise anonymous functions.
2. **Functional Interfaces** — the types that lambdas implement.
3. **Streams** — pipelines for transforming data.
4. **Optionals** — null safety done right.
5. **Method References** — elegant shorthand syntax.
6. **Function Composition & Collectors** — advanced FP patterns.

> 💬 The best way to master this is to **practice**:
> Try rewriting loops, filters, and transformations using streams and lambdas.

