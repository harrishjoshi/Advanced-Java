# Table of Contents
- [GUI Programming with Swing](#gui-programming-with-swing)
  - [What is GUI Programming?](#what-is-gui-programming)
  - [Introduction to Java for GUI Development](#introduction-to-java-for-gui-development)
  - [Understanding AWT (Abstract Window Toolkit)](#understanding-awt-abstract-window-toolkit)
  - [Understanding Swing Framework](#understanding-swing-framework)
  - [Understanding JFrame - The Foundation Window](#understanding-jframe---the-foundation-window)
    - [Essential JFrame Setup](#essential-jframe-setup)
  - [Custom Information Display with JComponent](#custom-information-display-with-jcomponent)
    - [The paintComponent Method](#the-paintcomponent-method)
    - [Coding Example: A Custom Drawing Component](#coding-example-a-custom-drawing-component)
  - [Summary](#summary)

## **GUI Programming with Swing**

## **What is GUI Programming?**

A **GUI**, or **Graphical User Interface**, is the "face" of a program. Instead of a user typing commands into a text-only terminal, they interact with a program through visual elements like **buttons**, **menus**, **text boxes**, and **images**. GUIs are used every day in applications like web browsers, desktop apps, and mobile apps.

---

## **Introduction to Java for GUI Development**

Java is a popular choice for building GUIs due to its versatility and key advantages. It can be used to create everything from desktop applications to mobile apps. Its "write once, run anywhere" philosophy means a Java program written on one computer will run on any other computer with Java installed, without needing any changes.

There are two main toolkits for creating Java GUIs:

* **AWT (Abstract Window Toolkit)** - The older, original approach.  
* **Swing** - The modern, preferred approach.

---

## **Understanding AWT (Abstract Window Toolkit)**

**AWT** was Java's first attempt at GUI programming, created in 1995. It's a "platform-dependent" toolkit, meaning it uses computer's native operating system (OS) to create components. A button created with AWT on a Windows machine will use the Windows button style, while the same code on a Mac will use the Mac's button style.

This platform dependency leads to what are known as **heavy-weight components**. Each AWT component is a "real" OS component, which uses more system resources and can lead to inconsistent appearance and limited customization options.

---

## **Understanding Swing Framework**

**Swing** is Java's modern GUI toolkit, designed to overcome AWT's limitations. It is a part of the **Java Foundation Classes (JFC)** and is written entirely in Java. This makes Swing's components **light-weight** and **platform-independent**.

Instead of relying on the OS, Swing draws its own components directly. This results in a consistent look and feel across different operating systems. Swing also offers a much richer set of components (like tables, trees, and tabbed panels), greater customization, and better performance.

| Feature | AWT (Abstract Window Toolkit) | Swing (Java Foundation Classes) |
| :---- | :---- | :---- |
| **Component Type** | **Heavy-weight** (created by the OS) | **Light-weight** (drawn by Java) |
| **Platform-Dependent** | **Yes** (looks different on Windows, Mac, Linux) | **No** (looks the same everywhere) |
| **Customization** | Limited (stuck with OS defaults) | High (can change colors, fonts, look and feel) |
| **Performance** | Slower (communicates with the OS) | Faster (all within Java) |
| **Component Set** | Basic components only | Rich and advanced components |

---

## **Understanding JFrame - The Foundation Window**

A **JFrame** is the main window of a Swing application. Think of it as the foundation of a house that holds all the other components like buttons and text fields. It's a **top-level container** that provides the basic window structure, including the title bar, minimize/maximize buttons, and a close button.

### **Essential JFrame Setup**

A typical Swing application starts by creating a JFrame object and setting its properties. Here's a simple example:

```java
import javax.swing.JFrame;  
import javax.swing.JLabel;  

public class FirstWindow {  
    public static void main(String[] args) {  
        // 1. Create a JFrame object with a title  
        JFrame frame = new JFrame("My First GUI Window");

        // 2. Set the window's size (width, height in pixels)  
        frame.setSize(400, 300);

        // 3. Set the default close operation  
        // This makes the program exit when the close button is clicked. 
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        // 4. Create and add a component (a simple text label in this case)  
        JLabel label = new JLabel("Hello, Swing!");  
        frame.add(label);

        // 5. Make the frame visible
        frame.setVisible(true);  
    }  
}
```

This code creates a window 400 pixels wide and 300 pixels tall with the title 'My First GUI Window'. A simple text label is added, and the window is made visible.

---

## **Custom Information Display with JComponent**

**JComponent** is the base class for most Swing components. While standard components like JButton and JLabel can be used to build common interfaces, sometimes we need more flexibility. By extending JComponent, custom components can be created to draw graphics, such as charts, diagrams, or even a game board. This gives us full control over how the component looks and behaves.

To create a custom component, you extend JComponent and override the special paintComponent method. This method is called automatically whenever the component needs to be drawn.

### **The paintComponent Method**

The paintComponent(Graphics g) method is the drawing canvas. The Graphics object (g) is the ‘paintbrush’ that draws text, shapes, and images. The coordinate system for drawing starts at (0,0) in the top-left corner of the component.

### **Coding Example: A Custom Drawing Component**

This example creates a custom component that draws a red rectangle and some text.

```java
import javax.swing.JComponent;  
import java.awt.Graphics;  
import java.awt.Color;  
import java.awt.Font;

// Custom class that extends JComponent to handle custom drawing  
class MyDrawingComponent extends JComponent {

    // Override the paintComponent method to define what gets drawn  
    @Override  
    protected void paintComponent(Graphics g) {  
        // Always call the superclass's paintComponent first  
        super.paintComponent(g);

        // Set the drawing color to red  
        g.setColor(Color.RED);

        // Draw a filled rectangle at (50, 50) with size 100x100  
        g.fillRect(50, 50, 100, 100);

        // Set the drawing color to blue  
        g.setColor(Color.BLUE);

        // Set the font for text  
        g.setFont(new Font("Arial", Font.BOLD, 18));

        // Draw text at coordinates (50, 200)  
        g.drawString("A custom component!", 50, 200);  
    }  
}

import javax.swing.JFrame;  

// Main class to set up the JFrame and add the custom component  
public class CustomComponentDemo {  
    public static void main(String[] args) {  
        JFrame frame = new JFrame("Custom Component Demo");  
        frame.setSize(400, 300);  
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        // Create an instance of the custom component  
        MyDrawingComponent myComponent = new MyDrawingComponent();

        // Add the custom component to the frame  
        frame.add(myComponent);

        frame.setVisible(true);  
    }  
}
```

This code demonstrates how to create a JFrame and then add a custom JComponent to it. The MyDrawingComponent class takes care of the drawing logic, making the code clean and organized.

---

## **Summary**

**Swing is the modern and preferred choice** for Java GUI programming because of it's **platform-independent** nature, has a rich feature set, and offers better performance than AWT.

* Every Swing application starts with a **JFrame** to create the main window.  
* **Components** (like buttons and labels) are the building blocks of GUI.  
* To create custom visuals, extend **JComponent** and override the **paintComponent** method.  
* **Event handling** is what makes GUI interactive, allowing it to respond to user actions like mouse clicks and key presses.
