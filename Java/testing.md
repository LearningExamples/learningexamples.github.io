---
layout: default
title: Unit Testing
parent: Java
nav_order: 6
---

# 🧪 Java Unit Testing Tutorial — A Practical Guide with JUnit and Mockito

If you write code, you’ll write bugs. **Testing** is how we find them.
**Test automation** is how we find them *quickly and reliably.*

This tutorial walks you step-by-step through **unit testing in Java** using **JUnit** and **Mockito** — from basics to good practices, with example code you can run immediately.

---

## 🔍 What Is Test Automation?

**Test Automation** means writing code to test other code.
Instead of manually checking if your program works, you automate the process so tests can be run again and again — fast and consistently.

The most common type is **Unit Testing**, which focuses on small “units” of code (like individual methods or classes) in isolation.

---

## ⚙️ Part 1: Unit Testing with JUnit

**JUnit** is the standard framework for unit testing in Java.

The basic idea is simple:

* For a class named `Foo`, you create a test class named `FooTest`.
* Inside that class, you write *test methods* that check if your code behaves as expected.

### ✅ Common JUnit Assertions

JUnit provides **assertion methods** — statements that verify conditions in your code.
If an assertion fails, the test fails.

Here are the most common ones:

| Assertion                             | Purpose                                      |
| ------------------------------------- | -------------------------------------------- |
| `assertTrue(condition)`               | Fails if condition is false                  |
| `assertFalse(condition)`              | Fails if condition is true                   |
| `assertEquals(expected, actual)`      | Fails if values differ                       |
| `assertNotEquals(unexpected, actual)` | Fails if values match                        |
| `assertSame(expected, actual)`        | Fails if objects are not the *same instance* |
| `assertNotSame(unexpected, actual)`   | Fails if objects are the same instance       |
| `assertNull(object)`                  | Fails if object is not null                  |
| `assertNotNull(object)`               | Fails if object is null                      |
| `fail()`                              | Immediately fails the test                   |

You can also include a custom message for better debugging:

```java
assertEquals("Message shown if test fails", expected, actual);
```

---

### 🧩 Example: Testing a Custom `ArrayList`

**Code under test:** `MyArrayList.java`

```java
public class MyArrayList {
    private Object[] data = new Object[10];
    private int size = 0;

    public void add(Object item) {
        data[size] = item;
        size++;
    }

    public Object get(int index) {
        if (index >= size || index < 0) {
            throw new IndexOutOfBoundsException();
        }
        return data[index];
    }

    public boolean isEmpty() {
        return size == 0;
    }

    public void remove(int index) {
        size--;
    }
}
```

**Test class:** `MyArrayListTest.java`

```java
import org.junit.Test;
import static org.junit.Assert.*;

public class MyArrayListTest {

    @Test
    public void testAddAndGet() {
        MyArrayList list = new MyArrayList();
        list.add(42);
        list.add(-3);
        list.add(15);

        assertEquals(42, list.get(0));
        assertEquals(-3, list.get(1));
        assertEquals(15, list.get(2));
    }

    @Test
    public void testIsEmpty() {
        MyArrayList list = new MyArrayList();

        assertTrue(list.isEmpty());
        list.add(123);
        assertFalse(list.isEmpty());
        list.remove(0);
        assertTrue(list.isEmpty());
    }
}
```

### ▶️ Running Tests

In Eclipse or IntelliJ:

* Right-click the test file → **Run As → JUnit Test**

If the bar is **green**, all tests passed.
If it’s **red**, at least one test failed — check the “Failure Trace” for details.

---

## 🧠 Part 2: Writing Good Tests

### 1. Use Descriptive Messages

Bad (no message):

```java
assertEquals(3, d.getMonth());
```

Good (with message):

```java
assertEquals("Month after adding 14 days", 3, d.getMonth());
```

Messages make it easier to understand *why* a test failed.

---

### 2. Use Descriptive Method Names

Avoid `test1()` and `test2()` — they tell you nothing.
Instead, use clear, behavior-focused names:

```java
@Test public void test_addDays_withinSameMonth() { ... }
@Test public void test_addDays_wrapsToNextMonth() { ... }
```

Good test names act as documentation.

---

### 3. Test for Exceptions

Use `(expected = ...)` to verify that an exception is thrown.

```java
@Test(expected = IndexOutOfBoundsException.class)
public void testGet_onBadIndex() {
    MyArrayList list = new MyArrayList();
    list.add(1);
    list.get(4); // Should throw
}
```

The test **passes** only if the exception is thrown.

---

### 4. Test for Timeouts

To prevent infinite loops or slow code:

```java
@Test(timeout = 5000)
public void test_somePotentiallySlowMethod() {
    // Fails if runs > 5 seconds
}
```

---

## 🧰 Part 3: Test Setup with `@Before` and `@After`

If you repeat setup code in every test, use **fixtures**.

* `@Before`: Runs before *each* test (for setup)
* `@After`: Runs after *each* test (for cleanup)

Example:

```java
public class ShoppingCartTest {

    private ShoppingCart cart;

    @Before
    public void setUp() {
        cart = new ShoppingCart();
    }

    @After
    public void tearDown() {
        cart.clear();
    }

    @Test
    public void testAddItem() {
        cart.addItem("Apple");
        assertEquals(1, cart.getItemCount());
    }

    @Test
    public void testRemoveItem() {
        cart.addItem("Apple");
        cart.removeItem("Apple");
        assertTrue(cart.isEmpty());
    }
}
```

Each test starts with a *fresh* `ShoppingCart` instance.

---

## 🧪 Part 4: Mocking with Mockito

### What Is Mocking?

**Mocking** lets you test a class *in isolation* from its dependencies.

Example:
`PaymentService` depends on a `DatabaseConnection`, but you don’t want your tests to hit a real database.
With **Mockito**, you create a *fake* object (a “mock”) that returns controlled responses.

---

### Example 1: Simple Mock

**Code under test:**

```java
public class MyClass {
    public int addService(int a, int b) {
        return 0;
    }
}
```

**Test class:**

```java
import org.junit.Test;
import org.junit.Before;
import org.junit.runner.RunWith;
import org.mockito.runners.MockitoJUnitRunner;

import static org.junit.Assert.*;
import static org.mockito.Mockito.*;

@RunWith(MockitoJUnitRunner.class)
public class MyTest {

    MyClass p;

    @Before
    public void setUp() {
        p = mock(MyClass.class);
    }

    @Test
    public void testAddTwoAndFive() {
        when(p.addService(2, 5)).thenReturn(7);
        assertEquals(7, p.addService(2, 5));
    }
}
```

---

### Example 2: Mocking a Dependency

**Code under test:**

```java
public class Database {
    public boolean isAvailable() {
        System.out.println("Checking real database...");
        return false;
    }
}

public class Service {
    private Database db;

    public Service(Database db) {
        this.db = db;
    }

    public boolean isDatabaseAvailable() {
        return db.isAvailable();
    }
}
```

**Test class:**

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.runners.MockitoJUnitRunner;
import static org.junit.Assert.*;
import static org.mockito.Mockito.*;

@RunWith(MockitoJUnitRunner.class)
public class ServiceTest {

    @Test
    public void testService_when_db_is_available() {
        Database mockDb = mock(Database.class);
        when(mockDb.isAvailable()).thenReturn(true);

        Service service = new Service(mockDb);
        assertTrue(service.isDatabaseAvailable());
    }

    @Test
    public void testService_when_db_is_NOT_available() {
        Database mockDb = mock(Database.class);
        when(mockDb.isAvailable()).thenReturn(false);

        Service service = new Service(mockDb);
        assertFalse(service.isDatabaseAvailable());
    }
}
```

No real database connection needed — fast, reliable tests.

---

## 📈 Part 5: Test Coverage

To see how much of your code is tested, use **Test Coverage** tools.

In your IDE:

* Right-click → **Coverage As → JUnit Test**

**Green lines** = code was run by tests
**Red lines** = untested code

Your goal isn’t 100% coverage — it’s *meaningful* coverage that gives confidence in your code.

---

## ✅ Summary

By now, you should be able to:

* Write **unit tests** with JUnit
* Use **assertions** effectively
* Structure tests with `@Before` / `@After`
* Mock dependencies using **Mockito**
* Measure **test coverage** in your IDE

Automated testing isn’t just about finding bugs — it’s about building confidence and making your codebase easier to maintain.

