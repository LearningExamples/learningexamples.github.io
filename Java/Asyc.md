---
layout: default
title: Synchronous vs Asynchronous
parent: Java
nav_order: 5
---


# Java Concurrency: Synchronous vs Asynchronous

---

## 1 — The concepts

* **Synchronous (blocking):**

  * Tasks are executed **one after another**.
  * A task must complete before the next one starts.
  * Thread waits for I/O or computation before continuing.
  * Simpler to reason about, but less responsive under heavy workloads.

* **Asynchronous (non-blocking):**

  * Tasks can be executed **concurrently** or **in the background**.
  * The caller doesn’t wait for the result; instead, it gets a **future/promise/callback**.
  * More complex to design, but improves throughput and responsiveness.

---

## 2 — Synchronous example

Here, tasks run **one after the other**. Each "task" simulates a slow operation (like reading from disk or a network call).

```java
public class SyncExample {
    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        doTask("Task 1");
        doTask("Task 2");
        doTask("Task 3");
        long end = System.currentTimeMillis();
        System.out.println("All tasks finished in " + (end - start) + " ms");
    }

    private static void doTask(String name) {
        System.out.println(name + " started");
        try {
            Thread.sleep(1000); // simulate slow I/O
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        System.out.println(name + " finished");
    }
}
```

**Output (sequential, ~3 seconds total):**

```
Task 1 started
Task 1 finished
Task 2 started
Task 2 finished
Task 3 started
Task 3 finished
All tasks finished in ~3000 ms
```

---

## 3 — Asynchronous example (using `ExecutorService` + `Future`)

```java
import java.util.concurrent.*;

public class AsyncExample {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        ExecutorService executor = Executors.newFixedThreadPool(3);

        Future<String> f1 = executor.submit(() -> doTask("Task 1"));
        Future<String> f2 = executor.submit(() -> doTask("Task 2"));
        Future<String> f3 = executor.submit(() -> doTask("Task 3"));

        // Wait for all results
        System.out.println(f1.get());
        System.out.println(f2.get());
        System.out.println(f3.get());

        executor.shutdown();
    }

    private static String doTask(String name) {
        System.out.println(name + " started (async)");
        try {
            Thread.sleep(1000); // simulate slow I/O
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        return name + " finished";
    }
}
```

**Output (parallel, ~1 second total):**

```
Task 1 started (async)
Task 2 started (async)
Task 3 started (async)
Task 1 finished
Task 2 finished
Task 3 finished
```

---

## 4 — Modern async style with `CompletableFuture`

`CompletableFuture` is more flexible: you can chain operations, run them in parallel, and combine results.

```java
import java.util.concurrent.*;

public class CompletableFutureExample {
    public static void main(String[] args) {
        CompletableFuture<String> f1 = CompletableFuture.supplyAsync(() -> doTask("Task 1"));
        CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> doTask("Task 2"));
        CompletableFuture<String> f3 = CompletableFuture.supplyAsync(() -> doTask("Task 3"));

        CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2, f3);

        all.thenRun(() -> {
            try {
                System.out.println(f1.get());
                System.out.println(f2.get());
                System.out.println(f3.get());
            } catch (Exception e) {
                e.printStackTrace();
            }
        }).join(); // wait for all tasks to finish
    }

    private static String doTask(String name) {
        System.out.println(name + " started (completableFuture)");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
        return name + " finished";
    }
}
```

---

## 5 — Synchronous vs Asynchronous (at a glance)

| Feature         | Synchronous (blocking)                  | Asynchronous (non-blocking)                    |
| --------------- | --------------------------------------- | ---------------------------------------------- |
| Execution order | One after another (serial)              | Tasks can run in parallel / background         |
| Resource usage  | Simpler, but wastes time waiting        | Better CPU utilization under I/O waits         |
| Complexity      | Easy to code/read/debug                 | Harder (callbacks, futures, exceptions)        |
| Performance     | Slower under many concurrent operations | Faster under I/O-bound workloads               |
| Use cases       | Simple programs, small scripts          | Web servers, GUI apps, large-scale concurrency |

---

## 6 — Exercise: Simulate a web service

### Problem

Simulate a "web service" where 5 clients request data.

* In the **synchronous** version, requests are handled **one at a time**.
* In the **asynchronous** version, requests are handled **concurrently**.

Each request takes ~1 second (simulate with `Thread.sleep(1000)`).

**Question:** How long will synchronous vs asynchronous take?

---

## 7 — Solution

### Synchronous web server

```java
public class SyncServer {
    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        for (int i = 1; i <= 5; i++) {
            handleRequest(i);
        }
        long end = System.currentTimeMillis();
        System.out.println("All requests handled in " + (end - start) + " ms");
    }

    private static void handleRequest(int clientId) {
        System.out.println("Handling request from client " + clientId);
        try { Thread.sleep(1000); } catch (InterruptedException e) {}
        System.out.println("Done with client " + clientId);
    }
}
```

**Total time ~5000 ms** (5 seconds).

---

### Asynchronous web server

```java
import java.util.concurrent.*;

public class AsyncServer {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService pool = Executors.newFixedThreadPool(5);

        long start = System.currentTimeMillis();
        CountDownLatch latch = new CountDownLatch(5);

        for (int i = 1; i <= 5; i++) {
            int clientId = i;
            pool.submit(() -> {
                handleRequest(clientId);
                latch.countDown();
            });
        }

        latch.await(); // wait for all clients to finish
        long end = System.currentTimeMillis();
        System.out.println("All requests handled in " + (end - start) + " ms");

        pool.shutdown();
    }

    private static void handleRequest(int clientId) {
        System.out.println("Handling request from client " + clientId + " (async)");
        try { Thread.sleep(1000); } catch (InterruptedException e) {}
        System.out.println("Done with client " + clientId);
    }
}
```

**Total time ~1000 ms** (1 second, since all run in parallel).

---

# 8 — Further exercises

1. Modify the async server to **limit concurrency** to 2 clients at a time (hint: use `Executors.newFixedThreadPool(2)` or a `Semaphore`).
2. Use `CompletableFuture` to handle requests and then **merge results** into a single report.
3. Extend the example to simulate **mix of fast and slow clients** and compare sync vs async behavior.
