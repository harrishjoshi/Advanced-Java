# Object-Oriented Programming

---

## Table of Contents

1. [What is Inheritance?](#what-is-inheritance)
2. [Polymorphism](#polymorphism)
3. [Method Overriding](#method-overriding)
4. [Method Overloading](#method-overloading)
5. [Abstraction](#abstraction)
6. [Abstract Classes](#abstract-classes)
7. [Abstract Methods](#abstract-methods)
8. [Interfaces](#interfaces)
9. [Hands-on Practice](#hands-on-practice)
10. [Summary](#summary)

---

## What is Inheritance?

### Simple Definition

**Inheritance** is a mechanism in OOP where a new class (subclass) **inherits** properties and methods from an existing class (superclass). It’s like a child inheriting traits from a parent.

### Real-World Analogy: Car Models

- A **Car** is a general blueprint (superclass).
- A **SportsCar** is a specific type of car that inherits the basic car features but adds specialized ones like higher speed or a turbo engine.
- The SportsCar can use everything from the Car class but also has its own unique features.

### Code Example

```java
// Superclass
public class Car {
    protected String brand;
    protected String color;
    protected int year;

    public Car(String brand, String color, int year) {
        this.brand = brand;
        this.color = color;
        this.year = year;
    }

    public void start() {
        System.out.println("Car is starting...");
    }

    public void stop() {
        System.out.println("Car has stopped.");
    }

    public void displayInfo() {
        System.out.println("Car: " + brand + ", Color: " + color + ", Year: " + year);
    }
}

// Subclass inheriting from Car
public class SportsCar extends Car {
    private int maxSpeed;

    public SportsCar(String brand, String color, int year, int maxSpeed) {
        super(brand, color, year); // Call superclass constructor
        this.maxSpeed = maxSpeed;
    }

    public void boost() {
        System.out.println("SportsCar is boosting to " + maxSpeed + " km/h!");
    }
}

// Using the classes
public class Main {
    public static void main(String[] args) {
        SportsCar sportsCar = new SportsCar("Ferrari", "Red", 2023, 350);
        sportsCar.displayInfo(); // Inherited from Car
        sportsCar.start();      // Inherited from Car
        sportsCar.boost();      // Specific to SportsCar
        // Output: Car: Ferrari, Color: Red, Year: 2023
        //         Car is starting...
        //         SportsCar is boosting to 350 km/h!
    }
}
```

### Key Points

- Use `extends` keyword to inherit a class.
- Subclasses inherit properties and methods from the superclass.
- Use `super()` to call the superclass constructor or methods.
- Inheritance promotes **code reuse** and **hierarchy**.

---

## Polymorphism

### Simple Definition

**Polymorphism** means "many forms" and allows objects of different classes to be treated as objects of a common superclass. It’s like treating all cars (sedans, sports cars) as just "cars" when driving.

### Real-World Analogy: Driving Any Car

- Different car types (sedans, sports cars, SUVs) can all be driven using the same controls (steering wheel, pedals).
- You interact with them as "cars," but each type behaves slightly differently.

### Code Example

```java
public class Car {
    public void drive() {
        System.out.println("Car is being driven.");
    }
}

public class SportsCar extends Car {
    @Override
    public void drive() {
        System.out.println("SportsCar is being driven fast!");
    }
}

public class SUV extends Car {
    @Override
    public void drive() {
        System.out.println("SUV is being driven off-road!");
    }
}

public class Main {
    public static void main(String[] args) {
        Car myCar1 = new Car();          // Regular Car
        Car myCar2 = new SportsCar();    // SportsCar as Car
        Car myCar3 = new SUV();          // SUV as Car

        myCar1.drive(); // Output: Car is being driven.
        myCar2.drive(); // Output: SportsCar is being driven fast!
        myCar3.drive(); // Output: SUV is being driven off-road!
    }
}
```

### Key Points

- Polymorphism allows a subclass to be treated as its superclass.
- Methods can behave differently based on the actual object type.
- Enables flexibility and extensibility in code design.

---

## Method Overriding

### Simple Definition

**Method overriding** is when a subclass provides a specific implementation of a method already defined in its superclass. It’s like customizing how a sports car starts compared to a regular car.

### Real-World Analogy: Car Start Button

- A regular car starts with a standard ignition.
- A sports car might have a "sport mode" start that revs the engine louder.
- Both are "starting" the car, but the sports car does it differently.

### Code Example

```java
public class Car {
    public void start() {
        System.out.println("Car starts with standard ignition.");
    }
}

public class SportsCar extends Car {
    @Override
    public void start() {
        System.out.println("SportsCar starts with sport mode!");
    }
}

public class Main {
    public static void main(String[] args) {
        Car car = new Car();
        SportsCar sportsCar = new SportsCar();

        car.start();       // Output: Car starts with standard ignition.
        sportsCar.start(); // Output: SportsCar starts with sport mode!
    }
}
```

### Key Points

- Use `@Override` annotation to indicate method overriding.
- Method in subclass must have the same name, return type, and parameters as in the superclass.
- Allows subclasses to customize inherited behavior.

---

## Method Overloading

### Simple Definition

**Method overloading** is defining multiple methods with the **same name** but **different parameters** (number, type, or order) in the same class. It’s like having multiple ways to start a car (key, button, remote).

### Real-World Analogy: Car Start Options

- A car might have a method to start with a key, another with a push-button, or one with a remote.
- All are "start" actions but take different inputs.

### Code Example

```java
public class Car {
    // Method overloading - same method name, different parameters
    public void start() {
        System.out.println("Car starts with standard key.");
    }

    public void start(String startType) {
        System.out.println("Car starts with " + startType + ".");
    }

    public void start(String startType, int delay) {
        System.out.println("Car starts with " + startType + " after " + delay + " seconds.");
    }
}

public class Main {
    public static void main(String[] args) {
        Car car = new Car();
        car.start();                    // Output: Car starts with standard key.
        car.start("push-button");       // Output: Car starts with push-button.
        car.start("remote", 5);         // Output: Car starts with remote after 5 seconds.
    }
}
```

### Key Points

- Methods must have the same name but different parameter lists.
- Return type can differ but isn’t enough alone to overload.
- Improves code readability and flexibility.

---

## Abstraction

### Simple Definition

**Abstraction** is hiding complex details and showing only the necessary parts to the user. It’s like using a car’s dashboard without needing to understand the engine’s inner workings.

### Real-World Analogy: Car Controls

- You control a car using simple interfaces (steering wheel, pedals).
- The complex mechanics (engine, transmission) are hidden from you.
- You focus on driving, not how the engine works.

### Code Example

```java
// Abstract class (cannot be instantiated directly)
public abstract class Car {
    protected String brand;

    public Car(String brand) {
        this.brand = brand;
    }

    // Abstract method (must be implemented by subclasses)
    public abstract void drive();

    // Concrete method
    public void stop() {
        System.out.println(brand + " car has stopped.");
    }
}

public class SportsCar extends Car {
    public SportsCar(String brand) {
        super(brand);
    }

    @Override
    public void drive() {
        System.out.println(brand + " SportsCar is driving fast!");
    }
}

public class Main {
    public static void main(String[] args) {
        // Car car = new Car("Toyota"); // Error: Cannot instantiate abstract class
        SportsCar sportsCar = new SportsCar("Ferrari");
        sportsCar.drive(); // Output: Ferrari SportsCar is driving fast!
        sportsCar.stop();  // Output: Ferrari car has stopped.
    }
}
```

### Key Points

- Abstraction reduces complexity by hiding details.
- Use `abstract` keyword for classes and methods.
- Focuses on **what** an object does, not **how** it does it.

---

## Abstract Classes

### Simple Definition

An **abstract class** is a class that cannot be instantiated directly and may contain abstract methods (without implementation). It’s like a partial blueprint for cars that requires specific details to be filled in.

### Real-World Analogy: Generic Car Blueprint

- A generic car blueprint defines common features (wheels, engine).
- Specific car types (sports car, SUV) must provide details like engine size or design.
- You can’t build a "generic car"; you need a specific type.

### Code Example

```java
public abstract class Car {
    protected String brand;

    public Car(String brand) {
        this.brand = brand;
    }

    // Abstract method
    public abstract void assemble();

    // Concrete method
    public void testDrive() {
        System.out.println(brand + " car is being test-driven.");
    }
}

public class SportsCar extends Car {
    public SportsCar(String brand) {
        super(brand);
    }

    @Override
    public void assemble() {
        System.out.println("Assembling " + brand + " SportsCar with turbo engine.");
    }
}

public class Main {
    public static void main(String[] args) {
        SportsCar sportsCar = new SportsCar("Porsche");
        sportsCar.assemble();   // Output: Assembling Porsche SportsCar with turbo engine.
        sportsCar.testDrive();  // Output: Porsche car is being test-driven.
    }
}
```

### Key Points

- Use `abstract` keyword for classes that can’t be instantiated.
- Can include both abstract and concrete methods.
- Subclasses must implement all abstract methods.

---

## Abstract Methods

### Simple Definition

An **abstract method** is a method declared without an implementation in an abstract class. Subclasses must provide the implementation. It’s like specifying that all cars must have a "drive" function, but each car type defines how it drives.

### Real-World Analogy: Mandatory Car Feature

- A car blueprint might require all cars to have a "start" function.
- Each car type (sports car, SUV) defines how the start function works.

### Code Example

```java
public abstract class Car {
    public abstract void accelerate(); // No implementation

    public void honk() {
        System.out.println("Car honks: Beep beep!");
    }
}

public class SportsCar extends Car {
    @Override
    public void accelerate() {
        System.out.println("SportsCar accelerates rapidly!");
    }
}

public class Main {
    public static void main(String[] args) {
        SportsCar sportsCar = new SportsCar();
        sportsCar.accelerate(); // Output: SportsCar accelerates rapidly!
        sportsCar.honk();        // Output: Car honks: Beep beep!
    }
}
```

### Key Points

- Declared with `abstract` keyword and no body.
- Must be in an abstract class.
- Subclasses must override abstract methods.

---

## Interfaces

### Simple Definition

An **interface** is a contract that defines methods a class must implement. It’s like a car manufacturer agreeing to include standard safety features (brakes, airbags) in every car.

### Real-World Analogy: Car Safety Standards

- All cars must follow safety standards (e.g., have brakes, airbags).
- Each car brand implements these standards in its own way.
- The interface ensures consistency across different car types.

### Code Example

```java
public interface Drivable {
    void drive(); // Implicitly public and abstract
    void brake();
}

public class Car implements Drivable {
    private String brand;

    public Car(String brand) {
        this.brand = brand;
    }

    @Override
    public void drive() {
        System.out.println(brand + " car is driving.");
    }

    @Override
    public void brake() {
        System.out.println(brand + " car is braking.");
    }
}

public class Main {
    public static void main(String[] args) {
        Car car = new Car("Toyota");
        car.drive(); // Output: Toyota car is driving.
        car.brake(); // Output: Toyota car is braking.
    }
}
```

### Key Points

- Use `interface` keyword to define.
- Classes implement interfaces using `implements`.
- All methods in an interface are implicitly `public` and `abstract`.
- Promotes loose coupling and standardization.

---

## Hands-on Practice

### Exercise 1: ElectricCar Class with Inheritance

Create an `ElectricCar` class that extends `Car` with:

- Private property: `batteryLevel` (percentage, 0-100)
- Constructor to initialize `brand`, `color`, `year`, and `batteryLevel`
- Override `start()` to display "Electric motor starting..."
- Method `chargeBattery(double percentage)` to increase battery level (with validation)
- Static counter for total electric cars created
- Method `displayInfo()` to show all properties

**Solution**

```java
public class Car {
    protected String brand;
    protected String color;
    protected int year;

    public Car(String brand, String color, int year) {
        this.brand = brand;
        this.color = color;
        this.year = year;
    }

    public void start() {
        System.out.println("Car is starting...");
    }

    public void displayInfo() {
        System.out.println("Car: " + brand + ", Color: " + color + ", Year: " + year);
    }
}

public class ElectricCar extends Car {
    private static int totalElectricCars = 0;
    private double batteryLevel;

    public ElectricCar(String brand, String color, int year, double batteryLevel) {
        super(brand, color, year);
        this.batteryLevel = batteryLevel;
        totalElectricCars++;
    }

    @Override
    public void start() {
        System.out.println("Electric motor starting...");
    }

    public void chargeBattery(double percentage) {
        if (percentage > 0 && percentage <= 100) {
            batteryLevel += percentage;
            if (batteryLevel > 100) batteryLevel = 100;
            System.out.println("Battery charged by " + percentage + "%. Current level: " + batteryLevel + "%");
        } else {
            System.out.println("Invalid charge percentage!");
        }
    }

    @Override
    public void displayInfo() {
        System.out.println("Electric Car: " + brand + ", Color: " + color + ", Year: " + year + ", Battery: " + batteryLevel + "%");
    }

    public static int getTotalElectricCars() {
        return totalElectricCars;
    }
}

public class Main {
    public static void main(String[] args) {
        ElectricCar tesla = new ElectricCar("Tesla", "White", 2023, 50.0);
        tesla.displayInfo();        // Output: Electric Car: Tesla, Color: White, Year: 2023, Battery: 50.0%
        tesla.start();             // Output: Electric motor starting...
        tesla.chargeBattery(30);   // Output: Battery charged by 30%. Current level: 80.0%
        System.out.println("Total electric cars: " + ElectricCar.getTotalElectricCars());
        // Output: Total electric cars: 1
    }
}
```

### Exercise 2: Drivable Interface

Create a `Drivable` interface and implement it in a `HybridCar` class with:

- Interface methods: `drive()`, `brake()`
- Private properties: `brand`, `fuelLevel`, `batteryLevel`
- Constructor to initialize all properties
- Static method to display total hybrid cars created
- Method to display car details

**Solution**

```java
public interface Drivable {
    void drive();
    void brake();
}

public class HybridCar implements Drivable {
    private static int totalHybridCars = 0;
    private String brand;
    private double fuelLevel;
    private double batteryLevel;

    public HybridCar(String brand, double fuelLevel, double batteryLevel) {
        this.brand = brand;
        this.fuelLevel = fuelLevel;
        this.batteryLevel = batteryLevel;
        totalHybridCars++;
    }

    @Override
    public void drive() {
        System.out.println(brand + " hybrid car is driving using " +
                (batteryLevel > 0 ? "battery" : "fuel"));
    }

    @Override
    public void brake() {
        System.out.println(brand + " hybrid car is braking.");
    }

    public void displayDetails() {
        System.out.println("Hybrid Car: " + brand + ", Fuel: " + fuelLevel + "L, Battery: " + batteryLevel + "%");
    }

    public static int getTotalHybridCars() {
        return totalHybridCars;
    }
}

public class Main {
    public static void main(String[] args) {
        HybridCar prius = new HybridCar("Toyota Prius", 40.0, 60.0);
        prius.displayDetails(); // Output: Hybrid Car: Toyota Prius, Fuel: 40.0L, Battery: 60.0%
        prius.drive();         // Output: Toyota Prius hybrid car is driving using battery
        prius.brake();         // Output: Toyota Prius hybrid car is braking.
        System.out.println("Total hybrid cars: " + HybridCar.getTotalHybridCars());
        // Output: Total hybrid cars: 1
    }
}
```

---

## Summary

### What We Learned

**Inheritance**

- Allows a subclass to inherit properties and methods from a superclass.
- Promotes code reuse and hierarchical organization.
- Example: SportsCar inherits from Car.

**Polymorphism**

- Treats different subclass objects as their superclass type.
- Enables flexible method calls based on object type.
- Example: Driving different car types uniformly.

**Method Overriding**

- Subclasses provide specific implementations of superclass methods.
- Customizes behavior for specific subclasses.
- Example: SportsCar overrides Car’s `start()` method.

**Method Overloading**

- Multiple methods with the same name but different parameters.
- Improves flexibility and readability.
- Example: Different `start()` methods for various inputs.

**Abstraction**

- Hides complex details, exposing only necessary functionality.
- Simplifies interaction with objects.
- Example: Car’s dashboard hides engine complexity.

**Abstract Classes**

- Partial blueprints that cannot be instantiated.
- Contain abstract and concrete methods.
- Example: Car with abstract `assemble()` method.

**Abstract Methods**

- Methods without implementation in abstract classes.
- Subclasses must provide implementation.
- Example: `accelerate()` defined by subclasses.

**Interfaces**

- Define a contract of methods for classes to implement.
- Ensure consistency across unrelated classes.
- Example: `Drivable` interface for car behaviors.

### Key Benefits of Advanced OOP

- **Code Reusability**: Inheritance and interfaces reduce duplication.
- **Flexibility**: Polymorphism and overloading allow versatile code.
- **Simplicity**: Abstraction hides unnecessary details.
- **Standardization**: Interfaces ensure consistent behavior.
- **Maintainability**: Organized code is easier to update.

_Happy coding!_
