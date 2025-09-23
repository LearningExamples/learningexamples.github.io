---
layout: default
title: Multi-threading in Java
parent: Java
nav_order: 3
---

# Multi-threading in Java

Welcome to the tutorial on **Multi-threading**! 🚀 In modern software, the ability to do multiple things at once is crucial for performance and responsiveness. Multi-threading is how Java achieves this concurrency.

Think of a single-threaded application as a single cook in a kitchen who can only do one task at a time: chop vegetables, then cook the sauce, then boil pasta. A multi-threaded application is like having multiple cooks working in the same kitchen simultaneously, each handling a different task. The result? The meal gets prepared much faster.

A **thread** is the smallest unit of execution within a process. A Java application runs by default in one thread (the `main` thread). Multi-threading allows you to create and run multiple threads to perform different tasks concurrently.

---

## Part 1: Creating Threads

There are two primary ways to create a thread in Java.

### Method 1: Extending the `Thread` Class

You can create a class that inherits from the `java.lang.Thread` class and override its `run()` method. The `run()` method contains the code that will be executed by the new thread.

```java
// Method 1: Extending the Thread class
class MyThread extends Thread {
    public void run() {
        try {
            for (int i = 0; i < 5; i++) {
                System.out.println("Thread extending Thread: " + i);
                Thread.sleep(500); // Pause for 500 milliseconds
            }
        } catch (InterruptedException e) {
            System.out.println("Thread was interrupted.");
        }
    }
}
````

To run this, you create an instance of your new class and call its `start()` method.

> **Important:** You must call the `start()` method to begin execution in a new thread. If you call `run()` directly, it will simply execute in the current thread, and no multi-threading will occur.

### Method 2: Implementing the `Runnable` Interface

This is the **preferred method**. You create a class that implements the `Runnable` interface, which has a single method: `run()`.

```java
// Method 2: Implementing the Runnable interface
class MyRunnable implements Runnable {
    public void run() {
        try {
            for (int i = 0; i < 5; i++) {
                System.out.println("Thread implementing Runnable: " + i);
                Thread.sleep(500);
            }
        } catch (InterruptedException e) {
            System.out.println("Runnable was interrupted.");
        }
    }
}
```

To run this, you create an instance of your `MyRunnable` class, pass it to the constructor of a `Thread` object, and then call the `start()` method on the `Thread` object.

```java
public class Main {
    public static void main(String[] args) {
        // Using the class that extends Thread
        MyThread thread1 = new MyThread();
        thread1.start(); // This starts the new thread

        // Using the class that implements Runnable
        MyRunnable myRunnable = new MyRunnable();
        Thread thread2 = new Thread(myRunnable);
        thread2.start(); // This also starts a new thread
    }
}
```

**Why is `Runnable` preferred?** Java does not support multiple inheritance of classes. If your class extends `Thread`, it cannot extend any other class. By implementing `Runnable`, you can still extend another class, making your code more flexible and promoting better design.

-----

## Part 2: The Thread Lifecycle ⚙️

A thread goes through several states during its lifetime. Understanding these states is key to managing threads effectively.

  * **NEW:** The thread object has been created, but the `start()` method has not yet been called. It's not yet alive.
  * **RUNNABLE:** The thread is ready to run. The thread scheduler can select it to be run at any time. It's either running or ready to run.
  * **BLOCKED / WAITING:** The thread is alive but currently not eligible to be run. It's waiting for some event to occur, such as acquiring a lock on an object or being notified by another thread.
  * **TIMED\_WAITING:** The thread is waiting for a specific amount of time. This happens when you call methods like `Thread.sleep()` or `wait()` with a timeout.
  * **TERMINATED:** The thread has completed its execution because its `run()` method has finished, or it has otherwise been terminated.

-----

## Part 3: Synchronization 🔒

When multiple threads access and modify the same data (a shared resource), you can run into problems. This is called a **race condition**. The final result of the data depends on the unpredictable order in which threads are scheduled.

### The Problem: Race Condition

Imagine two threads are trying to increment the same counter.

```java
class Counter {
    private int count = 0;

    public void increment() {
        count++; // This is NOT atomic. It's actually 3 steps: read, increment, write.
    }

    public int getCount() {
        return count;
    }
}
```

If two threads call `increment()` at roughly the same time, they might both read the value `0`, both increment it to `1`, and both write `1` back. The final count will be `1` instead of the expected `2`\!

### The Solution: The `synchronized` Keyword

Java provides the `synchronized` keyword to create "thread-safe" code. When a method is declared as `synchronized`, it can only be accessed by **one thread at a time** for a given object instance. It acquires a "lock" on the object.

```java
class SynchronizedCounter {
    private int count = 0;

    // Only one thread can execute this method at a time on the same instance
    public synchronized void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

public class SyncDemo {
    public static void main(String[] args) throws InterruptedException {
        SynchronizedCounter counter = new SynchronizedCounter();

        // Create two threads that both increment the same counter instance
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });

        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        });

        t1.start();
        t2.start();

        // Wait for both threads to finish
        t1.join();
        t2.join();

        System.out.println("Final count is: " + counter.getCount()); // Always 2000
    }
}
```

By adding `synchronized`, we guarantee that the increment operation is **atomic**, ensuring the final count is always correct.

-----

## Conclusion

Multi-threading is a powerful feature for building high-performance, responsive applications.

**Key Takeaways:**

  * Threads allow for concurrent execution of tasks.
  * The `Runnable` interface is the preferred way to create tasks for threads.
  * A thread progresses through a lifecycle of states (NEW, RUNNABLE, WAITING, TERMINATED).
  * The `synchronized` keyword is essential for preventing race conditions when multiple threads share data.

This is just the beginning\! The `java.util.concurrent` package offers more advanced tools like Executors, Locks, and thread-safe collections to make concurrent programming even more powerful and manageable. Happy coding\!

