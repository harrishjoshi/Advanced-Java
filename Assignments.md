# Java Programming Assignment

## Section 1: First Steps in Java Programming

1. **Hello World Program**  
   Write a program to print "Hello, Java World!".

2. **Variable Declaration and Default Values**  
   Declare variables of types: `byte`, `short`, `int`, `long`, `float`, `double`, `char`, and `boolean`. Print their default values.

3. **Type Casting Demonstration**  
   Write a program to demonstrate type casting between `int`, `double`, and `char`.

4. **Rectangle Area Calculation**  
   Create a program that uses arithmetic operators to calculate the area of a rectangle.

5. **String Concatenation**  
   Write a program to demonstrate String concatenation with numbers and text.

## Section 2: Control Flow Fundamentals

1. **Positive, Negative, or Zero Check**  
   Write a program that checks whether a number is positive, negative, or zero using `if-else`.

2. **Logical Operators**  
   Implement a program using the logical AND (`&&`) and logical OR (`||`) operators.

3. **Ternary Operator**  
   Write a program to demonstrate the use of the ternary operator.

4. **Assignment vs Equality Operators**  
   Create a program that shows the difference between assignment (`=`) and equality (`==`) operators.

## Section 3: Expressions, Statements, Code Blocks & Method Overloading

1. **Even Number Check**  
   Write a method `isEven(int number)` that returns `true` if a number is even.

2. **Method Overloading for Area Calculation**  
   Create a method `calculateArea(int side)` for the area of a square and `calculateArea(int length, int breadth)` for the area of a rectangle. Demonstrate method overloading.

3. **Leap Year Check**  
   Write a program to check whether a given year is a leap year.

4. **Minutes to Years and Days Converter**  
   Write a program `minutesToYearsDaysCalculator` that converts minutes into years and days.

5. **Barking Dog Logic**  
   Implement a method `barkingDog(boolean barking, int hourOfDay)` that returns `true` if a dog is barking before 8 AM or after 10 PM.

## Section 4: Conditional Logic, Loops & Interactive Applications

1. **Number to Word (Switch Statement)**  
   Write a program to print the word representation of numbers 0-9 using a `switch` statement.

2. **Sum of Odd Numbers**  
   Write a program to calculate the sum of all odd numbers between two given numbers.

3. **Palindrome Check**  
   Implement a program that checks if a number is a palindrome.

4. **Factors of a Number**  
   Write a program to print the factors of a given number.

5. **GCD Calculation**  
   Create a program to find the GCD (Greatest Common Divisor) of two numbers.

6. **Sum and Average of N Integers**  
   Write a program to read `N` integers from the user, and print their sum and average.

7. **Largest Prime Factor**  
   Write a program that finds the largest prime factor of a given number.

## Section 5: OOP - Classes & Inheritance

1. **BankAccount Class**  
   Create a `BankAccount` class with fields: account number, balance, customer name, email, and phone number. Implement `deposit` and `withdraw` methods.

2. **Person Class with Validation**  
   Create a `Person` class with `name` and `age`. Add validation so that `age` cannot be negative.

3. **Point Class for Distance Calculation**  
   Write a `Point` class with methods to calculate the distance from the origin and from another point.

4. **Cylinder Class with Inheritance**  
   Create a `Cylinder` class that inherits from a `Circle` class and calculates volume.

5. **Employee Hierarchy**  
   Create an Employee hierarchy: `Worker`, `Employee`, `Manager`. Demonstrate inheritance.

6. **Method Overriding with Animal Classes**  
   Demonstrate method overriding by creating an `Animal` class and extending it with `Dog` and `Cat`.

## Section 6: Composition, Encapsulation & Polymorphism

1. **Bedroom Composition**  
   Create a `Bedroom` class composed of `Bed`, `Lamp`, and `Wall` classes. Demonstrate composition.

2. **Printer Class with Encapsulation**  
   Implement a `Printer` class with encapsulation: fields for toner level and duplex printing.

3. **Movie Class with Polymorphism**  
   Create a `Movie` superclass and subclasses like `Comedy`, `Horror`, and `Action`. Use polymorphism to print movie descriptions.

4. **Car Class Hierarchy**  
   Create a `Car` superclass and subclasses: `GasCar`, `ElectricCar`, `HybridCar`. Demonstrate method overriding.

## Section 7: Arrays

1. **Sort Array in Descending Order**  
   Write a program to sort an array of integers in descending order.

2. **Find Minimum Value in Array**  
   Implement a program that finds the minimum value in an array.

3. **Reverse Array In-Place**  
   Write a program to reverse an array in-place without using extra arrays.

4. **Traverse 2D Array**  
   Implement a program to traverse a 2D array and print it as a matrix.

## Section 8: Lists, Iterators, Autoboxing & Enums

1. **Grocery List Application**  
   Create a Grocery List application using `ArrayList<String>` where users can add, remove, and search for items.

2. **Playlist with LinkedList**  
   Create a `Playlist` using `LinkedList` that allows songs to be added and navigated forward/backward using an `Iterator`.

3. **Banking System**  
   Build a simple Banking System: `Bank`, `Branch`, `Customer`, and `Transaction` classes using `ArrayList`.

4. **Autoboxing and Unboxing**  
   Demonstrate autoboxing and unboxing by creating a program that stores `int` values in an `ArrayList<Integer>`.

5. **DayOfWeek Enum**  
   Create an enum `DayOfWeek` with values (`MONDAY` to `SUNDAY`). Write a program to print if the day is a weekday or weekend.

## Section 9: Exception Handling

1. **Try-Catch Block**  
   Write a program to handle an `ArithmeticException` for division by zero using a `try-catch` block.

2. **Custom Exception**  
   Create a custom exception class `InvalidAgeException` and use it in the `Person` class to validate age.

3. **Multiple Exceptions**  
   Write a program that handles multiple exceptions (`ArrayIndexOutOfBoundsException` and `NullPointerException`) in a single `try` block.

## Section 10: File I/O and Basic Input/Output

1. **Read from File**  
   Write a program to read a text file and print its contents to the console.

2. **Write to File**  
   Create a program that writes a list of strings to a text file.

3. **User Input with Scanner**  
   Implement a program that uses the `Scanner` class to read user input (e.g., name, age) and print a formatted output.

## Section 11: Generics and Collections

1. **Generic Class**  
   Create a generic class `Box<T>` that can store and retrieve an item of any type.

2. **HashMap Usage**  
   Write a program that uses a `HashMap` to store and retrieve student names and their grades.

3. **Set Operations**  
   Implement a program that demonstrates `HashSet` operations (add, remove, contains) with a collection of integers.

## Section 12: Basic Multithreading

1. **Thread Creation**  
   Write a program that creates two threads to print even and odd numbers from 1 to 20 concurrently.

2. **Synchronized Method**  
   Implement a program with a shared counter incremented by multiple threads, using synchronization to avoid race conditions.
