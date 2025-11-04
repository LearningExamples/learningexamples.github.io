---
layout: default
title: Mastering Design Patterns
parent: Java
nav_order: 7
---

# Mastering Design Patterns: A Practical Guide to Strategy and Decorator

Welcome to our introduction to Design Patterns! Design patterns are **reusable, time-tested solutions** to commonly occurring problems in software design. They aren't finished code, but rather templates or blueprints for how to solve a problem.

Using patterns helps make your code more **flexible, reusable, and maintainable**. Today, we'll dive into two of the most popular and useful patterns: the **Strategy Pattern** and the **Decorator Pattern**, with practical, runnable Java examples.

## 🎯 1. The Strategy Pattern

The Strategy Pattern is a **behavioral design pattern**. Its core idea is to let you define a **family of algorithms (or "strategies")**, put each algorithm into its own separate class, and make their objects interchangeable.

This means the main part of your application (called the "Context") doesn't need to know the *specifics* of how an algorithm works. It just needs to know *which* strategy to use. This allows you to change the algorithm at **runtime**.

**Analogy:** Think of a navigation app. You (the "Context") want to get from Point A to Point B. You can choose a "strategy" at runtime: *Walking*, *Biking*, or *Driving*. The app's main logic doesn't change, but the chosen strategy completely changes the resulting path and time.

### Example: Runtime List Sorting

Let's tackle your request: We have a list of random numbers. We want to let the user decide *at runtime* whether to sort them in ascending (increasing) or descending (decreasing) order.

#### Step 1: Define the Strategy Interface
First, we define the "contract" that all our sorting strategies must follow.

```java
// File: SortStrategy.java
import java.util.List;

/**
 * The Strategy interface declares operations common to all supported versions
 * of some algorithm. The Context uses this interface to call the algorithm
 * defined by Concrete Strategies.
 */
public interface SortStrategy {
    void sort(List<Integer> list);
}
````

#### Step 2: Create Concrete Strategies

Next, we create separate classes for each of our algorithms (ascending and descending sort), implementing the `SortStrategy` interface.

```java
// File: AscendingSort.java
import java.util.Collections;
import java.util.List;

/**
 * Concrete Strategy: Implements the ascending sort algorithm.
 */
public class AscendingSort implements SortStrategy {
    @Override
    public void sort(List<Integer> list) {
        System.out.println("Sorting list in ascending order...");
        Collections.sort(list);
    }
}
```

```java
// File: DescendingSort.java
import java.util.Collections;
import java.util.List;

/**
 * Concrete Strategy: Implements the descending sort algorithm.
 */
public class DescendingSort implements SortStrategy {
    @Override
    public void sort(List<Integer> list) {
        System.out.println("Sorting list in descending order...");
        Collections.sort(list, Collections.reverseOrder());
    }
}
```

#### Step 3: Create the Context

The "Context" is the class that holds our data (the list) and a reference to the *current* strategy. It doesn't know *how* the sort works, only that it *can* sort.

```java
// File: SortedListContext.java
import java.util.List;

/**
 * The Context defines the interface of interest to clients.
 * It maintains a reference to one of the Strategy objects.
 */
public class SortedListContext {
    private List<Integer> numbers;
    private SortStrategy strategy; // The reference to the current strategy

    public SortedListContext(List<Integer> numbers) {
        this.numbers = numbers;
    }

    // Method to set the strategy at runtime
    public void setSortStrategy(SortStrategy strategy) {
        this.strategy = strategy;
    }

    // The context delegates the work to the strategy object
    public void sort() {
        if (strategy == null) {
            System.out.println("No sort strategy set!");
            return;
        }
        strategy.sort(numbers);
    }

    public void printList() {
        System.out.println(numbers);
    }
}
```

#### Step 4: The Client (Main Program)

Finally, our main program will create the context, generate random numbers, and ask the user which strategy to apply.

```java
// File: StrategyDemo.java
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.Scanner;

/**
 * The Client. Creates the context, sets the strategy based on user input,
 * and executes it.
 */
public class StrategyDemo {
    public static void main(String[] args) {
        // 1. Setup: Create the context and add random numbers
        List<Integer> numbers = new ArrayList<>();
        Random random = new Random();
        for (int i = 0; i < 10; i++) {
            numbers.add(random.nextInt(100)); // Numbers between 0 and 99
        }
        
        SortedListContext context = new SortedListContext(numbers);

        System.out.println("--- Original List ---");
        context.printList();

        // 2. Get user input at runtime
        Scanner scanner = new Scanner(System.in);
        System.out.print("\nHow do you want to sort? (asc/desc): ");
        String choice = scanner.nextLine();

        // 3. Set the strategy based on input
        if ("asc".equalsIgnoreCase(choice)) {
            context.setSortStrategy(new AscendingSort());
        } else if ("desc".equalsIgnoreCase(choice)) {
            context.setSortStrategy(new DescendingSort());
        } else {
            System.out.println("Invalid choice. No sorting will be performed.");
        }

        // 4. Execute the chosen strategy
        context.sort();

        // 5. Print the result
        System.out.println("--- Sorted List ---");
        context.printList();
        
        scanner.close();
    }
}
```

**Why is this good?** We can add new sorting algorithms (e.G., `QuickSort`, `MergeSort`) just by creating new classes that implement `SortStrategy`. The `StrategyDemo` and `SortedListContext` classes **never have to change**.

-----

## 🎁 2. The Decorator Pattern

The Decorator Pattern is a **structural design pattern**. It allows you to **attach new behaviors or responsibilities to objects dynamically** by placing these objects inside special "wrapper" objects.

This pattern is great for adhering to the **Open/Closed Principle** (open for extension, closed for modification). You can add new functionality without altering the existing object's code.

**Analogy:** Think of ordering a coffee. You start with a `SimpleCoffee` (the component). You can then "decorate" it with `Milk` (a decorator), then "decorate" that with `Caramel` (another decorator). Each decorator adds its own cost and description to the base coffee, and you can mix and match them in any order.

### Example: Building a Custom Computer

Let's use your example: We start with a base computer ($500) and want to add accessories (Monitor, GPU, Keyboard), updating the total price and description as we go.

#### Step 1: Define the Component Interface

This is the common interface for both our base object and our decorators.

```java
// File: Computer.java

/**
 * The Component interface defines methods that will be implemented
 * by both the base object and all its decorators.
 */
public interface Computer {
    String getDescription();
    double getPrice();
}
```

#### Step 2: Create the Concrete Component

This is our base object—the starting point.

```java
// File: BaseComputer.java

/**
 * The Concrete Component is the base object we want to decorate.
 */
public class BaseComputer implements Computer {
    @Override
    public String getDescription() {
        return "Base Computer";
    }

    @Override
    public double getPrice() {
        return 500.00; // Base price
    }
}
```

#### Step 3: Create the Abstract Decorator

This abstract class is the key. It *implements* the `Computer` interface (so it can be treated as a `Computer`) and *has* a `Computer` object (the one it's "wrapping"). It delegates calls to the wrapped object.

```java
// File: ComputerDecorator.java

/**
 * The abstract Decorator class. It implements the Component interface
 * and holds a reference to a Component object.
 */
public abstract class ComputerDecorator implements Computer {
    // Reference to the wrapped component (can be a BaseComputer or another decorator)
    protected Computer decoratedComputer;

    public ComputerDecorator(Computer computer) {
        this.decoratedComputer = computer;
    }

    // Delegate the calls to the wrapped component
    @Override
    public String getDescription() {
        return decoratedComputer.getDescription();
    }

    @Override
    public double getPrice() {
        return decoratedComputer.getPrice();
    }
}
```

#### Step 4: Create Concrete Decorators

These are our "accessories." They extend the abstract decorator and add their *own* behavior (description and price) *on top* of the wrapped object's behavior.

```java
// File: MonitorDecorator.java
public class MonitorDecorator extends ComputerDecorator {
    public MonitorDecorator(Computer computer) {
        super(computer);
    }

    @Override
    public String getDescription() {
        // Add "Monitor" to the existing description
        return super.getDescription() + ", Monitor";
    }

    @Override
    public double getPrice() {
        // Add the price of the monitor ($250) to the existing price
        return super.getPrice() + 250.00;
    }
}
```

```java
// File: GPUDecorator.java
public class GPUDecorator extends ComputerDecorator {
    public GPUDecorator(Computer computer) {
        super(computer);
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", GPU";
    }

    @Override
    public double getPrice() {
        return super.getPrice() + 400.00; // GPU price
    }
}
```

```java
// File: KeyboardDecorator.java
public class KeyboardDecorator extends ComputerDecorator {
    public KeyboardDecorator(Computer computer) {
        super(computer);
    }

    @Override
    public String getDescription() {
        return super.getDescription() + ", Keyboard";
    }

    @Override
    public double getPrice() {
        return super.getPrice() + 75.00; // Keyboard price
    }
}
```

#### Step 5: The Client (Main Program)

Now, let's "build" our computer by wrapping the base component with decorators.

```java
// File: DecoratorDemo.java

/**
 * The Client. We can "build" our computer by dynamically
 * wrapping the base component with decorators.
 */
public class DecoratorDemo {
    public static void main(String[] args) {
        // 1. Start with a base computer
        Computer myComputer = new BaseComputer();
        System.out.println("Item: " + myComputer.getDescription());
        System.out.println("Price: $" + myComputer.getPrice());

        // 2. Add a Monitor
        myComputer = new MonitorDecorator(myComputer);
        System.out.println("\n--- Adding Monitor ---");
        System.out.println("New Item: Monitor");
        System.out.println("New Price: $" + myComputer.getPrice());
        
        // 3. Add a GPU
        myComputer = new GPUDecorator(myComputer);
        System.out.println("\n--- Adding GPU ---");
        System.out.println("New Item: GPU");
        System.out.println("New Price: $" + myComputer.getPrice());
        
        // 4. Add a Keyboard
        myComputer = new KeyboardDecorator(myComputer);
        System.out.println("\n--- Adding Keyboard ---");
        System.out.println("New Item: Keyboard");
        System.out.println("New Price: $" + myComputer.getPrice());
        
        // 5. Print the final configuration
        System.out.println("\n--- Final Build ---");
        System.out.println("Description: " + myComputer.getDescription());
        System.out.println("Total Price: $" + myComputer.getPrice());
    }
}
```

**Why is this good?** We can easily create new accessories (e.g., `MouseDecorator`, `WebcamDecorator`) without touching the `BaseComputer` or any other decorator. We can also combine them in any order we want\!

## Conclusion

We've covered two essential design patterns:

  * **Strategy:** Best for when you have multiple ways of doing something (algorithms) and you want to switch between them easily, even at runtime.
  * **Decorator:** Best for when you want to add new functionalities or responsibilities to an object without changing its class.

Start looking for these patterns in your own code or in the libraries you use. Happy coding\!
