---
layout: default
title: Observer Pattern
parent: Java
nav_order: 8
---
## 📰 What is the Observer Pattern?

The **Observer Pattern** is a behavioral design pattern that defines a one-to-many dependency between objects. When one object (the **Subject**) changes its state, all its dependents (the **Observers**) are notified and updated automatically.

Think of it like a magazine subscription:

  * **Subject:** The publisher (e.g., "Tech Weekly" magazine).
  * **Observers:** The subscribers.
  * **Action:** When the publisher releases a new issue (a state change), they automatically send it to all subscribers on their list. Subscribers don't have to check the newsstand every day; the update comes to them.

This pattern promotes **loose coupling**. The Subject only knows it has a list of Observers, not *what* they are or *what* they do with the information.

-----

## 🔬 The Example: A Temperature Server

We will build the exact system you described:

1.  A **Subject** (`TemperatureServer`) that generates a new temperature every second.
2.  Multiple **Observers** (`TemperatureListener`) that print the new temperature as soon as they are notified.

### Step 1: Define the Core Interfaces

First, we define the contracts for our two main components. This is the foundation of the pattern.

#### `Observer.java`

This interface defines what every "subscriber" must be able to do. It needs one method, `update()`, which the Subject will call.

```java
// Observer.java
public interface Observer {
    void update(int temperature);
}
```

#### `Subject.java`

This interface defines what the "publisher" must be able to do: manage its subscribers and notify them.

```java
// Subject.java
public interface Subject {
    void registerObserver(Observer o);
    void unregisterObserver(Observer o);
    void notifyObservers();
}
```

-----

### Step 2: Implement the Concrete Subject (TemperatureServer)

Now, let's build our `TemperatureServer`. It will implement the `Subject` interface and also contain the logic to run on a loop.

  * It holds a `List` of its registered observers.
  * `registerObserver()` and `unregisterObserver()` simply add/remove from that list.
  * `notifyObservers()` loops through the list and calls `update()` on each one.
  * `setTemperature()` is the key method. When the temperature changes, it updates its internal state *and then* calls `notifyObservers()` to broadcast the change.
  * `startServer()` will launch a new thread to simulate the 1-second updates.

<!-- end list -->

```java
// TemperatureServer.java
import java.util.ArrayList;
import java.util.List;
import java.util.Random;

public class TemperatureServer implements Subject {
    
    private List<Observer> observers;
    private int currentTemperature;
    
    public TemperatureServer() {
        this.observers = new ArrayList<>();
    }
    
    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }
    
    @Override
    public void unregisterObserver(Observer o) {
        observers.remove(o);
    }
    
    @Override
    public void notifyObservers() {
        // Notify all registered observers
        for (Observer observer : observers) {
            observer.update(this.currentTemperature);
        }
    }
    
    // This is the method that triggers the notification
    public void setTemperature(int newTemperature) {
        System.out.println("\n[Server] New temperature set: " + newTemperature + "°C");
        this.currentTemperature = newTemperature;
        notifyObservers(); // Broadcast the change!
    }
    
    // Method to start the server simulation
    public void startServer() {
        Random random = new Random();
        
        // Run the server in a separate thread
        new Thread(() -> {
            while (true) {
                // Simulate a new temperature reading (0-49)
                int newTemp = random.nextInt(50);
                this.setTemperature(newTemp);
                
                try {
                    // Wait for 1 second
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
```

-----

### Step 3: Implement the Concrete Observer (TemperatureListener)

This is our "subscriber." It's a simple class that implements the `Observer` interface. We'll give it a name so we can tell our 5 listeners apart in the console.

Its `update()` method simply prints the new temperature it received from the server.

```java
// TemperatureListener.java
public class TemperatureListener implements Observer {
    
    private String listenerName;
    
    public TemperatureListener(String listenerName) {
        this.listenerName = listenerName;
    }
    
    @Override
    public void update(int temperature) {
        // Reacts to the update
        System.out.println("[" + this.listenerName + "] Received temperature update: " + temperature + "°C");
    }
}
```

-----

### Step 4: Run the Simulation

Finally, let's create a `main` class to put it all together.

1.  Create one `TemperatureServer`.
2.  Create five `TemperatureListener` instances.
3.  **Register** all five listeners with the server.
4.  Start the server.

<!-- end list -->

```java
// Demo.java
public class Demo {
    public static void main(String[] args) {
        // 1. Create the Subject
        TemperatureServer server = new TemperatureServer();
        
        // 2. Create 5 Observers
        Observer listener1 = new TemperatureListener("Listener 1 (Living Room)");
        Observer listener2 = new TemperatureListener("Listener 2 (Bedroom)");
        Observer listener3 = new TemperatureListener("Listener 3 (Kitchen)");
        Observer listener4 = new TemperatureListener("Listener 4 (Garage)");
        Observer listener5 = new TemperatureListener("Listener 5 (Mobile App)");
        
        // 3. Register the Observers with the Subject
        server.registerObserver(listener1);
        server.registerObserver(listener2);
        server.registerObserver(listener3);
        server.registerObserver(listener4);
        server.registerObserver(listener5);
        
        // 4. Start the server to begin broadcasting
        System.out.println("Starting temperature server...");
        server.startServer();
        
        // We can even demonstrate unregistering one after a few seconds
        // (This part is optional, just to show it works)
        try {
            Thread.sleep(5000); // Let it run for 5 seconds
            System.out.println("\n*** [Listener 3 (Kitchen)] is unregistering... ***\n");
            server.unregisterObserver(listener3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

### Example Output:

When you run `Demo.java`, you will see output like this, repeating every second:

```
Starting temperature server...

[Server] New temperature set: 22°C
[Listener 1 (Living Room)] Received temperature update: 22°C
[Listener 2 (Bedroom)] Received temperature update: 22°C
[Listener 3 (Kitchen)] Received temperature update: 22°C
[Listener 4 (Garage)] Received temperature update: 22°C
[Listener 5 (Mobile App)] Received temperature update: 22°C

[Server] New temperature set: 19°C
[Listener 1 (Living Room)] Received temperature update: 19°C
[Listener 2 (Bedroom)] Received temperature update: 19°C
[Listener 3 (Kitchen)] Received temperature update: 19°C
[Listener 4 (Garage)] Received temperature update: 19°C
[Listener 5 (Mobile App)] Received temperature update: 19°C

... (this repeats 3 more times) ...

*** [Listener 3 (Kitchen)] is unregistering... ***

[Server] New temperature set: 35°C
[Listener 1 (Living Room)] Received temperature update: 35°C
[Listener 2 (Bedroom)] Received temperature update: 35°C
[Listener 4 (Garage)] Received temperature update: 35°C
[Listener 5 (Mobile App)] Received temperature update: 35°C

[Server] New temperature set: 31°C
[Listener 1 (Living Room)] Received temperature update: 31°C
[Listener 2 (Bedroom)] Received temperature update: 31°C
[Listener 4 (Garage)] Received temperature update: 31°C
[Listener 5 (Mobile App)] Received temperature update: 31°C
```

Notice how after the 5-second mark, "Listener 3 (Kitchen)" no longer receives updates.

-----

### A Note on Modern Java

The manual implementation we just did is the best way to *learn* the pattern.

  * **Deprecated Way:** Java used to have a built-in `java.util.Observable` class and `java.util.Observer` interface. **These are deprecated** (since Java 9) and should not be used in new code. They were inflexible (forcing you to extend a `class` instead of implement an `interface`).
  * **Modern Way:** For property changes in modern Java (especially in GUI/Swing apps), the standard approach is to use `java.beans.PropertyChangeSupport` and `java.beans.PropertyChangeListener`. This is a more robust, standardized version of the pattern.

