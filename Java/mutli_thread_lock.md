---
layout: default
title: Multi-threading in Java (Lock Locking)
parent: Java
nav_order: 4
---



## `ReentrantLock`

A `ReentrantLock` is an explicit and flexible lock. It's part of the `java.util.concurrent.locks` package. The "reentrant" part means that a thread that already holds the lock can acquire it again without blocking itself. This is useful in recursive programming.

The standard way to use a `ReentrantLock` is with a `try`/`finally` block to guarantee that the lock is always released, even if an exception occurs.

### How It Works

1.  **Create a Lock**: You create an instance of `ReentrantLock`.
2.  **Lock**: Before accessing the shared resource, a thread calls the `lock()` method. If the lock is available, the thread acquires it and continues. If another thread already holds the lock, the current thread will wait until it's released.
3.  **Unlock**: After the thread is done with the shared resource, it *must* call the `unlock()` method inside a `finally` block. This is crucial to prevent deadlocks.

### Example: A Shared Counter

Here's a simple counter that is safely incremented by multiple threads using `ReentrantLock`.

```java
import java.util.concurrent.locks.ReentrantLock;

class SharedCounter {
    private int count = 0;
    private final ReentrantLock lock = new ReentrantLock();

    public void increment() {
        lock.lock(); // Acquire the lock
        try {
            count++;
            System.out.println(Thread.currentThread().getName() + " incremented count to: " + count);
        } finally {
            lock.unlock(); // Always release the lock in a finally block
        }
    }

    public int getCount() {
        return count;
    }
}

public class ReentrantLockExample {
    public static void main(String[] args) throws InterruptedException {
        SharedCounter counter = new SharedCounter();

        // Create multiple threads that will increment the counter
        Thread t1 = new Thread(counter::increment, "Thread-1");
        Thread t2 = new Thread(counter::increment, "Thread-2");
        Thread t3 = new Thread(counter::increment, "Thread-3");

        t1.start();
        t2.start();
        t3.start();

        // Wait for all threads to finish
        t1.join();
        t2.join();
        t3.join();

        System.out.println("Final count is: " + counter.getCount());
    }
}
```

-----

## `Semaphore`

A `Semaphore` is different from a regular lock. Instead of allowing only one thread access, a semaphore maintains a set of **permits**. It's used to control access to a pool of resources.

  - If a thread wants to access a resource, it must acquire a permit from the semaphore by calling `acquire()`.
  - If a permit is available, the thread takes it and proceeds.
  - If no permits are available, the thread blocks until one is released.
  - When the thread is finished, it releases the permit by calling `release()`, allowing another waiting thread to proceed.

Think of it like a nightclub with a limited capacity. The bouncer (the semaphore) only lets in a certain number of people (permits). 🕺💃

### Example: Limited Resource Pool

Imagine you have a connection pool that can only handle 3 active connections at a time. A semaphore is perfect for this.

```java
import java.util.concurrent.Semaphore;

class ConnectionPool {
    // Only 3 permits available; only 3 threads can "connect" at once.
    private final Semaphore semaphore = new Semaphore(3, true); // true for fairness

    public void connect() {
        try {
            semaphore.acquire(); // Acquire a permit
            System.out.println(Thread.currentThread().getName() + " has connected.");
            // Simulate doing work with the connection
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            System.out.println(Thread.currentThread().getName() + " is disconnecting...");
            semaphore.release(); // Release the permit
        }
    }
}

public class SemaphoreExample {
    public static void main(String[] args) {
        ConnectionPool pool = new ConnectionPool();

        // Create 5 threads trying to use the pool of 3 connections
        for (int i = 1; i <= 5; i++) {
            Thread thread = new Thread(pool::connect, "User-" + i);
            thread.start();
        }
    }
}
```

You'll notice in the output that only three users can be connected at any given time.

-----

## `synchronized` Keyword

The `synchronized` keyword is Java's built-in, intrinsic locking mechanism. It's simpler to use than `ReentrantLock` but offers less flexibility. You can use it to lock a block of code or an entire method.

When a method is declared `synchronized`, it acts as if the code is wrapped in a `synchronized(this)` block. The lock is automatically acquired when a thread enters the method and released when the thread exits (either normally or via an exception).

### Example: A Shared Counter (Synchronized Version)

This example achieves the same result as the `ReentrantLock` example but with less code.

```java
class SynchronizedCounter {
    private int count = 0;

    // The 'synchronized' keyword locks the method.
    // The lock is on the instance of the object ('this').
    public synchronized void increment() {
        count++;
        System.out.println(Thread.currentThread().getName() + " incremented count to: " + count);
    }

    public int getCount() {
        return count;
    }
}

public class SynchronizedExample {
    public static void main(String[] args) throws InterruptedException {
        SynchronizedCounter counter = new SynchronizedCounter();

        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        };

        Thread t1 = new Thread(task, "Thread-1");
        Thread t2 = new Thread(task, "Thread-2");

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final count is: " + counter.getCount());
    }
}
```

The lock is acquired on the `counter` object instance. This ensures that only one thread can execute the `increment()` method on that specific instance at a time.

-----

## Exercise: ATM Machine Simulation 🏧

### Problem

Simulate an ATM machine where multiple users can access the same bank account. These users will randomly deposit or withdraw money.

### Requirements

1.  Create a `BankAccount` class with a `balance`. It should have `deposit()` and `withdraw()` methods.
2.  The `withdraw()` method must check for sufficient funds. A user cannot withdraw more money than is available in the account.
3.  Create a `User` class that implements `Runnable`. Each user thread will perform a series of random deposit and withdrawal operations.
4.  Use a locking mechanism (`ReentrantLock`, `synchronized`, or `Semaphore`) to protect the bank account's balance from race conditions. Ensure the final balance is correct.
5.  In your main class, create one `BankAccount` instance and multiple `User` threads. Start the threads and wait for them to complete, then print the final balance.

-----

## Solution

This solution uses `ReentrantLock` because it's explicit and clearly shows where the critical section begins and ends.

### `BankAccount.java`

```java
import java.util.concurrent.locks.ReentrantLock;

public class BankAccount {
    private double balance;
    private final ReentrantLock lock = new ReentrantLock();

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    // Deposit money into the account
    public void deposit(double amount) {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " is depositing: $" + amount);
            balance += amount;
            System.out.println("New balance after deposit: $" + balance);
        } finally {
            lock.unlock();
        }
    }

    // Withdraw money from the account
    public void withdraw(double amount) {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " is trying to withdraw: $" + amount);
            if (balance >= amount) {
                balance -= amount;
                System.out.println(Thread.currentThread().getName() + " successfully withdrew. New balance: $" + balance);
            } else {
                System.out.println(Thread.currentThread().getName() + " FAILED to withdraw. Insufficient funds. Current balance: $" + balance);
            }
        } finally {
            lock.unlock();
        }
    }

    public double getBalance() {
        return balance;
    }
}
```

### `User.java`

```java
import java.util.Random;

public class User implements Runnable {
    private final BankAccount account;
    private final Random random = new Random();

    public User(BankAccount account) {
        this.account = account;
    }

    @Override
    public void run() {
        // Each user performs 5 transactions
        for (int i = 0; i < 5; i++) {
            // Randomly choose to deposit or withdraw
            boolean isDeposit = random.nextBoolean();
            double amount = random.nextInt(100) + 1; // Amount between $1 and $100

            if (isDeposit) {
                account.deposit(amount);
            } else {
                account.withdraw(amount);
            }

            try {
                // Simulate time between transactions
                Thread.sleep(random.nextInt(100));
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
    }
}
```

### `AtmSimulation.java`

```java
public class AtmSimulation {
    public static void main(String[] args) throws InterruptedException {
        // A single bank account with an initial balance of $1000
        BankAccount sharedAccount = new BankAccount(1000.00);
        System.out.println("Initial account balance: $" + sharedAccount.getBalance());
        System.out.println("-------------------------------------------");


        // Create and start three user threads
        Thread user1 = new Thread(new User(sharedAccount), "User-Alice");
        Thread user2 = new Thread(new User(sharedAccount), "User-Bob");
        Thread user3 = new Thread(new User(sharedAccount), "User-Charlie");

        user1.start();
        user2.start();
        user3.start();

        // Wait for all users to finish their transactions
        user1.join();
        user2.join();
        user3.join();
        
        System.out.println("-------------------------------------------");
        System.out.printf("All transactions are complete. Final balance: $%.2f%n", sharedAccount.getBalance());
    }
}
