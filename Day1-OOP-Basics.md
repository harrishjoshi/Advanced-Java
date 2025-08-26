# Object-Oriented Programming Basics

---

## Table of Contents

1. [What is OOP?](#what-is-oop)
2. [Classes](#classes)
3. [Objects](#objects)
4. [Constructors](#constructors)
5. [Encapsulation](#encapsulation)
6. [Static Members](#static-members)
7. [Hands-on Practice](#hands-on-practice)
8. [Summary](#summary)

---

## What is OOP?

### Simple Definition

**Object-Oriented Programming (OOP)** is a way of writing code that organizes information and actions into "objects" - like organizing your belongings into different boxes, where each box has a label and contains related items.

### Real-World Analogy

Think of OOP like organizing a **library**:

- **Books** are objects
- **Categories** (Fiction, Science, History) are classes
- Each book has **properties** (title, author, pages)
- Each book can perform **actions** (be borrowed, returned, read)

---

## Classes

### Simple Definition

A **class** is like a **blueprint** or **template** that describes what something should look like and what it can do.

### Real-World Analogy: Car Blueprint

- A **car blueprint** is like a class
- It defines the **specifications** and **features** of cars
- You can use one blueprint to manufacture many cars
- All cars made from the same blueprint have the same basic design

### Code Example

```java
// Class = Blueprint for a Car
public class Car {
    // Properties (what a car has)
    String brand;
    String color;
    int year;

    // Methods (what a car can do)
    public void start() {
        System.out.println("Car is starting...");
    }

    public void stop() {
        System.out.println("Car has stopped.");
    }
}
```

### Key Points

- Classes define **properties** (characteristics)
- Classes define **methods** (actions/behaviors)
- Classes are **templates**, not actual things
- Use `class` keyword to create a class

---

## Objects

### Simple Definition

An **object** is an actual **instance** created from a class - like an actual car manufactured from the car blueprint.

### Real-World Analogy: Car Manufacturing

- **Car blueprint** = Class (shows what a car should look like)
- **Actual Car** = Object (the real car you can drive)
- You can manufacture many cars from one blueprint
- Each car can have different colors, license plates, owners

### Code Example

```java
public class Main {
    public static void main(String[] args) {
        // Creating objects from the Car class
        Car myCar = new Car();        // Object 1
        Car friendsCar = new Car();   // Object 2

        // Each object can have different properties
        myCar.brand = "Toyota";
        myCar.color = "Blue";
        myCar.year = 2020;

        friendsCar.brand = "Honda";
        friendsCar.color = "Red";
        friendsCar.year = 2019;

        // Each object can perform actions
        myCar.start();  // Output: Car is starting...
        friendsCar.stop();  // Output: Car has stopped.
    }
}
```

### Key Points

- Objects are **real instances** of classes
- Use `new` keyword to create objects
- Each object has its own set of properties
- Objects can perform the methods defined in their class

---

## Constructors

### Simple Definition

A **constructor** is a special method that runs automatically when you create a new object. It's like an **initialization ritual** that sets up the object with starting values.

### Real-World Analogy: Setting Up Your New Car

When you buy a new car from the dealer:

1. The dealer prepares it (constructor is called)
2. They register it with your name, set the radio presets, adjust mirrors (setting initial values)
3. Now your car is ready to drive with your personal settings

### Code Example

```java
public class Car {
    String brand;
    String color;
    int year;

    // Constructor - runs when object is created
    public Car(String carBrand, String carColor, int carYear) {
        brand = carBrand;
        color = carColor;
        year = carYear;
        System.out.println("New car created: " + brand + " " + color);
    }

    public void displayInfo() {
        System.out.println("Car: " + brand + " " + color + " (" + year + ")");
    }
}

// Using the constructor
public class Main {
    public static void main(String[] args) {
        // Constructor is called automatically with 'new'
        Car myCar = new Car("Toyota", "Blue", 2020);
        // Output: New car created: Toyota Blue

        myCar.displayInfo();
        // Output: Car: Toyota Blue (2020)
    }
}
```

### Key Points

- Constructor has the **same name** as the class
- Constructor has **no return type** (not even void)
- Called automatically when creating objects with `new`
- Used to set initial values for object properties

---

## Encapsulation

### Simple Definition

**Encapsulation** means **hiding the internal details** and only showing what's necessary. It's like putting sensitive items in a locked box and only giving people a key to what they need.

### Real-World Analogy: Car Dashboard

- You don't need to know how the car's engine works internally
- You just use the steering wheel, pedals, and buttons (public methods)
- The complex engine mechanics are hidden under the hood (private data)
- The dashboard gives you a simple interface to control the car

### Code Example

```java
public class Car {
    // Private properties (hidden from outside)
    private String engineType;
    private double fuelLevel;
    private String ownerName;
    private boolean isRunning;

    // Constructor
    public Car(String owner, String engine) {
        ownerName = owner;
        engineType = engine;
        fuelLevel = 0.0;
        isRunning = false;
    }

    // Public methods (safe ways to access/modify data)
    public void addFuel(double liters) {
        if (liters > 0 && liters <= 100) {
            fuelLevel += liters;
            if (fuelLevel > 100) fuelLevel = 100; // Tank capacity limit
            System.out.println("Added fuel: " + liters + " liters");
        }
    }

    public boolean start() {
        if (fuelLevel > 0 && !isRunning) {
            isRunning = true;
            System.out.println("Car started successfully!");
            return true;
        }
        System.out.println("Cannot start car - check fuel or engine!");
        return false;
    }

    public void stop() {
        if (isRunning) {
            isRunning = false;
            System.out.println("Car stopped.");
        }
    }

    // Getter methods (safe way to read data)
    public double getFuelLevel() {
        return fuelLevel;
    }

    public String getOwnerName() {
        return ownerName;
    }

    public boolean isCarRunning() {
        return isRunning;
    }
}
```

### Using Encapsulated Class

```java
public class Main {
    public static void main(String[] args) {
        Car myCar = new Car("John Doe", "V6");

        // These work (using public methods)
        myCar.addFuel(50);
        myCar.start();
        System.out.println("Fuel level: " + myCar.getFuelLevel() + " liters");

        // This would cause an error (private property)
        // myCar.fuelLevel = 999;  // Can't access directly!
        // myCar.isRunning = true; // Can't access directly!
    }
}
```

### Key Points

- Use `private` for sensitive data
- Use `public` methods to provide controlled access
- **Getters** retrieve data safely
- **Setters** modify data safely
- Protects data from invalid changes

---

## Static Members

### Simple Definition

**Static** members belong to the **class itself**, not to individual objects. They're like shared resources that all objects of that class can use.

### Real-World Analogy: Car Dealership

- **Individual cars** = regular methods (each customer has their own car)
- **Shared service center** = static method (all customers use the same service facilities)
- **Dealership name** = static property (same for all cars sold there)

### Code Example

```java
public class Student {
    // Static properties - shared by all students
    private static final String SCHOOL_NAME = "Sunshine High School";
    private static int totalStudents = 0;

    // Instance properties - unique to each student
    private String name;
    private int grade;

    // Constructor
    public Student(String studentName, int studentGrade) {
        this.name = studentName;
        this.grade = studentGrade;
        totalStudents++; // Increase count when new student is created
    }

    // Instance method - works with individual student
    public void introduce() {
        System.out.println("Hi, I'm " + name + " in grade " + grade +
                         " at " + SCHOOL_NAME);
    }

    // Static method - works with class-level data
    public static void displaySchoolInfo() {
        System.out.println("School: " + SCHOOL_NAME);
        System.out.println("Total students: " + totalStudents);
    }

    // Getters
    public String getName() {
        return name;
    }

    public int getGrade() {
        return grade;
    }

    public static String getSchoolName() {
        return SCHOOL_NAME;
    }

    public static int getTotalStudents() {
        return totalStudents;
    }

    // Setters (if needed)
    public void setGrade(int grade) {
        this.grade = grade;
    }

    // Note: No setter for SCHOOL_NAME since it's final (constant)
}
```

### Using Static Members

```java
public class Main {
    public static void main(String[] args) {
        // Call static method without creating objects
        Student.displaySchoolInfo();
        // Output: School: Sunshine High School
        //         Total students: 0

        // Create some students
        Student alice = new Student("Alice", 10);
        Student bob = new Student("Bob", 11);

        // Each student can introduce themselves (regular method)
        alice.introduce(); // Output: Hi, I'm Alice in grade 10
        bob.introduce();   // Output: Hi, I'm Bob in grade 11

        // Check total students (static method)
        Student.displaySchoolInfo();
        // Output: School: Sunshine High School
        //         Total students: 2

        // Access static method through class name
        System.out.println("Students enrolled: " + Student.getTotalStudents());
    }
}
```

### Key Points

- Static members are shared by **all objects** of the class
- Access static members using **class name**, not object name
- Static methods **cannot** access regular (non-static) properties
- Common uses: counters, utility methods, constants

---

## Hands-on Practice

### Exercise: Create a Simple Person Class

Let's build a complete example together that demonstrates all concepts:

```java
public class Person {
    // Static member - counts total people created
    private static int totalPeople = 0;

    // Private properties (Encapsulation)
    private String name;
    private int age;
    private String email;

    // Constructor
    public Person(String name, int age, String email) {
        this.name = name;
        this.age = age;
        this.email = email;
        totalPeople++; // Increment counter
        System.out.println("New person created: " + name);
    }

    // Getter methods (safe way to read data)
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public String getEmail() {
        return email;
    }

    // Setter methods (safe way to modify data)
    public void setName(String newName) {
        if (newName != null && !newName.trim().isEmpty()) {
            name = newName;
        } else {
            System.out.println("Invalid name!");
        }
    }

    public void setAge(int newAge) {
        if (newAge >= 0 && newAge <= 150) {
            age = newAge;
        } else {
            System.out.println("Invalid age!");
        }
    }

    public void setEmail(String newEmail) {
        if (newEmail != null && newEmail.contains("@")) {
            email = newEmail;
        } else {
            System.out.println("Invalid email!");
        }
    }

    // Regular method
    public void introduce() {
        System.out.println("Hello, I'm " + name + ", " + age + " years old.");
    }

    public void celebrate() {
        System.out.println(name + " is celebrating!");
        age++; // Birthday!
        System.out.println("Happy birthday! Now " + age + " years old.");
    }

    // Static method
    public static int getTotalPeople() {
        return totalPeople;
    }

    public static void displayPopulation() {
        System.out.println("Total people created: " + totalPeople);
    }
}
```

### Testing Our Person Class

```java
public class PersonDemo {
    public static void main(String[] args) {
        // Check initial population
        Person.displayPopulation(); // Output: Total people created: 0

        // Create objects using constructor
        Person alice = new Person("Alice", 25, "alice@email.com");
        Person bob = new Person("Bob", 30, "bob@email.com");

        // Use regular methods
        alice.introduce(); // Output: Hello, I'm Alice, 25 years old.
        bob.introduce();   // Output: Hello, I'm Bob, 30 years old.

        // Use getters to read data
        System.out.println("Alice's email: " + alice.getEmail());

        // Use setters to modify data safely
        alice.setAge(26);
        alice.setEmail("alice.new@email.com");

        // Try invalid data (encapsulation protects us)
        alice.setAge(-5);     // Output: Invalid age!
        alice.setEmail("bad-email"); // Output: Invalid email!

        // Use static method to check population
        Person.displayPopulation(); // Output: Total people created: 2

        // Birthday celebration
        alice.celebrate(); // Output: Alice is celebrating!
                          //         Happy birthday! Now 27 years old.
    }
}
```

### Practice Exercises

**Exercise 1: Book Class**
Create a `Book` class with:

- Private properties: title, author, pages, price
- Constructor to initialize all properties
- Getters for all properties
- Setters with validation (pages > 0, price >= 0)
- A static counter for total books created
- Method `displayInfo()` to show book details

**Exercise 2: Calculator Class**
Create a `Calculator` class with:

- Static methods for: add, subtract, multiply, divide
- Static property to count total calculations performed
- Method to get calculation count

---

## Summary

### What We Learned

**Classes**

- Blueprints/templates for creating objects
- Define properties and methods
- Like cookie cutters for making cookies

**Objects**

- Actual instances created from classes
- Have their own property values
- Can perform class methods

**Constructors**

- Special methods that initialize objects
- Called automatically with `new`
- Set up initial values

**Encapsulation**

- Hide internal details with `private`
- Provide controlled access with `public` methods
- Use getters and setters for safe data access

**Static Members**

- Belong to the class, not individual objects
- Shared by all objects of that class
- Accessed using class name

### Key Benefits of OOP

- **Organization**: Code is organized into logical units
- **Reusability**: Classes can be used to create multiple objects
- **Security**: Encapsulation protects data
- **Maintainability**: Changes are easier to make
- **Real-world modeling**: Code represents real-world concepts

### Remember

- Start simple and gradually add complexity
- Think about real-world analogies
- Always use meaningful names for classes and methods
- Practice encapsulation for data protection
- Use static members for shared functionality

---

_Happy coding!_
