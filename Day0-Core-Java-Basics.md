# Core Java Basics

---

## Table of Contents

1. [Introduction to Java](#introduction-to-java)
2. [Primitive Data Types](#primitive-data-types)
3. [String Definition](#string-definition)
4. [Defining Variables](#defining-variables)
5. [Conditions](#conditions)
6. [Loops](#loops)
7. [Basic Input/Output](#basic-input-output)
8. [Hands-on Practice](#hands-on-practice)
9. [Summary](#summary)

---

## Introduction to Java

### Simple Definition

**Java** is a high-level, object-oriented programming language designed to be platform-independent, meaning you can write code once and run it anywhere with a Java Virtual Machine (JVM).

### Real-World Analogy

Think of Java like a **universal recipe book**:

- The recipes (code) can be used in any kitchen (platform) with the right equipment (JVM).
- Each recipe has clear instructions (syntax) that anyone can follow.

### Key Points

- Java is **simple**, **secure**, and **portable**.
- Uses `class` to define programs and `main` method as the entry point.
- Code is written in `.java` files and compiled into `.class` files (bytecode).

### Code Example

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

---

## Primitive Data Types

### Simple Definition

**Primitive data types** are the basic building blocks in Java for storing simple values like numbers, characters, or true/false values.

### Real-World Analogy

Think of primitive data types as **basic ingredients** in a kitchen:

- Each ingredient (like sugar, flour, or salt) has a specific purpose.
- You combine them to create complex dishes (programs).

### Common Primitive Data Types

| Type       | Description                     | Size       | Example Values         |
|------------|---------------------------------|------------|-----------------------|
| `int`      | Integer numbers                | 4 bytes    | -5, 0, 42             |
| `double`   | Decimal numbers                | 8 bytes    | 3.14, -0.001, 42.0    |
| `boolean`  | True or false values           | 1 bit      | true, false           |
| `char`     | Single characters              | 2 bytes    | 'A', '1', '@'         |
| `byte`     | Small integers (-128 to 127)   | 1 byte     | -100, 0, 100          |
| `short`    | Short integers                 | 2 bytes    | -32000, 0, 32000      |
| `long`     | Large integers                 | 8 bytes    | 123456789L, -999L     |
| `float`    | Smaller decimal numbers        | 4 bytes    | 3.14f, -0.01f         |

### Code Example

```java
public class DataTypes {
    public static void main(String[] args) {
        int age = 25;              // Integer
        double price = 19.99;      // Decimal
        boolean isStudent = true;   // True/False
        char grade = 'A';          // Single character
        System.out.println("Age: " + age + ", Price: " + price + ", Grade: " + grade);
    }
}
```

### Key Points

- Primitives store **simple values**, not objects.
- Each type has a **specific size** and **range**.
- Use meaningful types for the data you need (e.g., `int` for age, `double` for prices).

---

## String Definition

### Simple Definition

A **String** is a non-primitive data type in Java used to store a sequence of characters, like a word, sentence, or any text.

### Real-World Analogy

Think of a **String** as a **sentence written on a whiteboard**:

- It can hold any text, from a single word to a full paragraph.
- You can manipulate it (e.g., add more words, change letters, or erase parts).

### Code Example

```java
public class Strings {
    public static void main(String[] args) {
        String greeting = "Hello, World!"; // String literal
        String name = new String("Alice"); // String object

        // Common String operations
        System.out.println("Greeting: " + greeting);
        System.out.println("Length: " + greeting.length()); // 13
        System.out.println("Uppercase: " + greeting.toUpperCase()); // HELLO, WORLD!
        System.out.println("Contains 'World': " + greeting.contains("World")); // true
        System.out.println("Concatenated: " + greeting + " " + name); // Hello, World! Alice
    }
}
```

### Key Points

- **String** is a class, not a primitive type, but widely used like one.
- Created using double quotes (e.g., `"text"`) or `new String()`.
- Common methods: `length()`, `toUpperCase()`, `toLowerCase()`, `contains()`, `substring()`.
- Strings are **immutable** (cannot be changed once created; operations create new Strings).
- Use `+` for concatenation (joining strings).

---

## Defining Variables

### Simple Definition

A **variable** is a named storage location in memory that holds a value, like a labeled jar in a kitchen that stores ingredients.

### Real-World Analogy

Think of variables as **labeled containers**:

- The label (variable name) tells you what's inside.
- The container holds one type of item (data type) at a time.
- You can change the contents but not the label.

### Code Example

```java
public class Variables {
    public static void main(String[] args) {
        // Declare and initialize variables
        int count = 10;            // Integer variable
        double temperature = 23.5; // Decimal variable
        String name = "Alice";     // String variable
        boolean isOpen = false;    // Boolean variable

        // Print variables
        System.out.println("Count: " + count);
        System.out.println("Temperature: " + temperature);
        System.out.println("Name: " + name);
        System.out.println("Is Open: " + isOpen);
    }
}
```

### Key Points

- Syntax: `type variableName = value;`
- Variable names should be **meaningful** and follow **camelCase** (e.g., `studentName`).
- Variables must be **declared** with a type before use.
- `String` is a non-primitive type, unlike `int` or `double`.

---

## Conditions

### Simple Definition

**Conditions** allow your program to make decisions based on whether something is true or false, like choosing what to wear based on the weather.

### Real-World Analogy

Think of conditions as a **decision tree**:

- If it's raining, take an umbrella.
- If it's sunny, wear sunglasses.
- Otherwise, just carry on.

### Code Example

```java
public class Conditions {
    public static void main(String[] args) {
        int temperature = 20;
        String weather = "rainy";

        // If-else condition
        if (temperature > 25) {
            System.out.println("It's hot, wear shorts!");
        } else if (temperature > 15) {
            System.out.println("It's pleasant, wear a jacket.");
        } else {
            System.out.println("It's cold, wear a coat!");
        }

        // Using String in condition
        if (weather.equals("rainy")) {
            System.out.println("Take an umbrella!");
        }
    }
}
```

### Key Points

- Use `if`, `else if`, and `else` for decision-making.
- Use `equals()` for String comparisons, not `==`.
- Conditions use **comparison operators**: `==`, `!=`, `>`, `<`, `>=`, `<=`.
- Combine conditions with **logical operators**: `&&` (and), `||` (or), `!` (not).

---

## Loops

### Simple Definition

**Loops** let your program repeat actions multiple times, like washing dishes until the sink is empty.

### Real-World Analogy

Think of loops as a **repetitive task**:

- **For loop**: Washing exactly 5 plates.
- **While loop**: Washing plates until the sink is empty.
- **Do-while loop**: Wash at least one plate, then check if more need washing.

### Code Example

```java
public class Loops {
    public static void main(String[] args) {
        // For loop: Repeat 5 times
        for (int i = 1; i <= 5; i++) {
            System.out.println("Count: " + i);
        }

        // While loop: Repeat while condition is true
        int count = 1;
        while (count <= 3) {
            System.out.println("While count: " + count);
            count++;
        }

        // Do-while loop: Execute at least once
        String message = "Hello";
        int index = 0;
        do {
            System.out.println("Character: " + message.charAt(index));
            index++;
        } while (index < message.length());
    }
}
```

### Key Points

- **For loop**: Use when you know the number of repetitions.
- **While loop**: Use when repetitions depend on a condition.
- **Do-while loop**: Ensures code runs at least once.
- Use `break` to exit a loop early, `continue` to skip to the next iteration.

---

## Basic Input/Output

### Simple Definition

**Input/Output (I/O)** allows your program to interact with users by reading input (e.g., from the keyboard) and displaying output (e.g., to the console).

### Real-World Analogy

Think of I/O as a **conversation**:

- **Output**: The program "speaks" by printing messages.
- **Input**: The program "listens" to user responses via a scanner.

### Code Example

```java
import java.util.Scanner;

public class InputOutput {
    public static void main(String[] args) {
        // Create Scanner for input
        Scanner scanner = new Scanner(System.in);

        // Get user input
        System.out.print("Enter your name: ");
        String name = scanner.nextLine();

        System.out.print("Enter your age: ");
        int age = scanner.nextInt();

        // Display output
        System.out.println("Hello, " + name + "! You are " + age + " years old.");

        // Close scanner
        scanner.close();
    }
}
```

### Key Points

- Use `System.out.println()` for output.
- Use `Scanner` class (from `java.util`) for input.
- Always close the scanner to prevent resource leaks.
- Handle different input types (`nextLine()`, `nextInt()`, `nextDouble()`, etc.).

---
## Hands-on Practice

### Exercise 1: Simple Calculator
Create a `Calculator` class with:

- Methods for basic operations: `add`, `subtract`, `multiply`, `divide`.
- Use `Scanner` to take two numbers as input from the user.
- Display the results of all operations.

```java
import java.util.Scanner;

public class Calculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter first number: ");
        double num1 = scanner.nextDouble();

        System.out.print("Enter second number: ");
        double num2 = scanner.nextDouble();

        System.out.println("Addition: " + (num1 + num2));
        System.out.println("Subtraction: " + (num1 - num2));
        System.out.println("Multiplication: " + (num1 * num2));
        if (num2 != 0) {
            System.out.println("Division: " + (num1 / num2));
        } else {
            System.out.println("Division: Cannot divide by zero!");
        }

        scanner.close();
    }
}
```

### Exercise 2: Number Guessing Game
Create a program that:

- Generates a random number between 1 and 100.
- Asks the user to guess the number.
- Provides hints ("Too high" or "Too low") until the correct number is guessed.
- Counts the number of attempts.

```java
import java.util.Scanner;
import java.util.Random;

public class NumberGuessingGame {
    public static void main(String[] args) {
        Random random = new Random();
        int number = random.nextInt(100) + 1; // Random number between 1 and 100
        Scanner scanner = new Scanner(System.in);
        int attempts = 0;
        int guess;

        System.out.println("Guess the number between 1 and 100!");

        do {
            System.out.print("Enter your guess: ");
            guess = scanner.nextInt();
            attempts++;

            if (guess > number) {
                System.out.println("Too high!");
            } else if (guess < number) {
                System.out.println("Too low!");
            } else {
                System.out.println("Correct! You got it in " + attempts + " attempts!");
            }
        } while (guess != number);

        scanner.close();
    }
}
```

---

## Summary

### What We Learned

**Primitive Data Types**

- Basic building blocks (`int`, `double`, `boolean`, `char`, etc.).
- Store simple values with fixed sizes and ranges.

**Defining Variables**

- Named storage for data.
- Use meaningful names and correct types.
- Syntax: `type variableName = value;`.

**Conditions**

- Make decisions with `if`, `else if`, `else`.
- Use comparison and logical operators for complex conditions.

**Loops**

- Repeat tasks with `for`, `while`, or `do-while`.
- Control flow with `break` and `continue`.

**Basic Input/Output**

- Use `System.out.println()` for output.
- Use `Scanner` for user input.
- Handle different input types appropriately.

_Happy coding!_
