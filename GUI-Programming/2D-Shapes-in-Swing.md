# Table of Contents
- [Working with 2D Shapes in Swing](#working-with-2d-shapes-in-swing)
  - [Introduction to 2D Graphics](#introduction-to-2d-graphics)
  - [Drawing Basic Shapes](#drawing-basic-shapes)
  - [Coding Example: Drawing Multiple Shapes](#coding-example-drawing-multiple-shapes)
- [Using Color in Swing](#using-color-in-swing)
  - [Understanding the Color Class](#understanding-the-color-class)
  - [Applying Colors to Shapes](#applying-colors-to-shapes)
  - [Coding Example: Colored Shapes](#coding-example-colored-shapes)
- [Using Special Fonts for Text in Swing](#using-special-fonts-for-text-in-swing)
  - [Font Class Overview](#font-class-overview)
  - [Applying Custom Fonts](#applying-custom-fonts)
  - [Coding Example: Custom Fonts](#coding-example-custom-fonts)
- [Summary](#summary)

## **Working with 2D Shapes in Swing**

### **Introduction to 2D Graphics**

In Swing, 2D graphics are drawn using the `java.awt.Graphics2D` class, an enhanced version of the `Graphics` class. It provides more precise control over geometry, colors, and transformations, enabling the creation of complex shapes like lines, rectangles, circles, and polygons. These shapes are drawn within a `JComponent` by overriding the `paintComponent` method.

### **Drawing Basic Shapes**

The `Graphics2D` class offers methods to draw various shapes:
- **Lines**: Use `drawLine(x1, y1, x2, y2)` to draw a line between two points.
- **Rectangles**: Use `drawRect(x, y, width, height)` for outlines or `fillRect(x, y, width, height)` for filled rectangles.
- **Ovals**: Use `drawOval(x, y, width, height)` for outlines or `fillOval(x, y, width, height)` for filled ovals.
- **Polygons**: Use `drawPolygon(int[] xPoints, int[] yPoints, int nPoints)` or `fillPolygon` for custom shapes.

### **Coding Example: Drawing Multiple Shapes**

This example demonstrates drawing a line, rectangle, and oval in a custom `JComponent`.

```java
import javax.swing.JComponent;
import java.awt.Graphics;
import java.awt.Graphics2D;

class ShapeComponent extends JComponent {
    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        Graphics2D g2d = (Graphics2D) g;

        // Draw a line
        g2d.drawLine(50, 50, 150, 50);

        // Draw a rectangle
        g2d.drawRect(50, 70, 100, 50);

        // Draw a filled oval
        g2d.fillOval(50, 130, 100, 50);
    }
}

import javax.swing.JFrame;

public class ShapeDemo {
    public static void main(String[] args) {
        JFrame frame = new JFrame("2D Shapes Demo");
        frame.setSize(300, 300);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.add(new ShapeComponent());
        frame.setVisible(true);
    }
}
```

This code creates a window displaying a line, a rectangular outline, and a filled oval.

---

## **Using Color in Swing**

### **Understanding the Color Class**

The `java.awt.Color` class manages colors in Swing. Colors can be specified using:
- Predefined constants (e.g., `Color.RED`, `Color.BLUE`).
- RGB values (e.g., `new Color(255, 0, 0)` for red).
- Transparency via RGBA (e.g., `new Color(255, 0, 0, 128)` for semi-transparent red).

The `Graphics` object uses `setColor(Color c)` to set the active color for drawing.

### **Applying Colors to Shapes**

Colors can be applied to both outlines (`draw` methods) and filled shapes (`fill` methods). Switching colors between drawing operations allows for visually distinct components.

### **Coding Example: Colored Shapes**

This example draws shapes with different colors.

```java
import javax.swing.JComponent;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.Color;

class ColorComponent extends JComponent {
    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        Graphics2D g2d = (Graphics2D) g;

        // Red filled rectangle
        g2d.setColor(Color.RED);
        g2d.fillRect(50, 50, 100, 50);

        // Blue outline oval
        g2d.setColor(Color.BLUE);
        g2d.drawOval(50, 110, 100, 50);

        // Green line
        g2d.setColor(Color.GREEN);
        g2d.drawLine(50, 170, 150, 170);
    }
}

import javax.swing.JFrame;

public class ColorDemo {
    public static void main(String[] args) {
        JFrame frame = new JFrame("Color Demo");
        frame.setSize(300, 300);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.add(new ColorComponent());
        frame.setVisible(true);
    }
}
```

This code displays a red filled rectangle, a blue oval outline, and a green line.

---

## **Using Special Fonts for Text in Swing**

### **Font Class Overview**

The `java.awt.Font` class controls text appearance in Swing. It defines three properties:
- **Font Name**: E.g., "Arial", "Times New Roman", or "Courier New".
- **Style**: `Font.PLAIN`, `Font.BOLD`, `Font.ITALIC`, or combined (e.g., `Font.BOLD | Font.ITALIC`).
- **Size**: Point size (e.g., 18 for 18pt).

Available fonts depend on the system, but common ones like Arial and Times New Roman are widely supported.

### **Applying Custom Fonts**

Use `setFont(new Font(name, style, size))` on the `Graphics` object to apply a font before drawing text with `drawString(text, x, y)`.

### **Coding Example: Custom Fonts**

This example draws text in different fonts and styles.

```java
import javax.swing.JComponent;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.Font;

class FontComponent extends JComponent {
    @Override
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);
        Graphics2D g2d = (Graphics2D) g;

        // Arial, bold, 18pt
        g2d.setFont(new Font("Arial", Font.BOLD, 18));
        g2d.drawString("Bold Arial Text", 50, 50);

        // Times New Roman, italic, 16pt
        g2d.setFont(new Font("Times New Roman", Font.ITALIC, 16));
        g2d.drawString("Italic Times Text", 50, 100);

        // Courier New, plain, 14pt
        g2d.setFont(new Font("Courier New", Font.PLAIN, 14));
        g2d.drawString("Plain Courier Text", 50, 150);
    }
}

import javax.swing.JFrame;

public class FontDemo {
    public static void main(String[] args) {
        JFrame frame = new JFrame("Font Demo");
        frame.setSize(300, 300);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.add(new FontComponent());
        frame.setVisible(true);
    }
}
```

This code displays text in Arial (bold), Times New Roman (italic), and Courier New (plain).

---

## **Summary**

- **2D Shapes**: Use `Graphics2D` to draw lines, rectangles, ovals, and polygons in a custom `JComponent` by overriding `paintComponent`.
- **Colors**: The `Color` class allows customization of shape appearance with predefined colors or RGB values.
- **Fonts**: The `Font` class enables text styling with different font names, styles, and sizes for enhanced visual appeal.
- These features make Swing a powerful toolkit for creating visually rich and interactive GUI applications.