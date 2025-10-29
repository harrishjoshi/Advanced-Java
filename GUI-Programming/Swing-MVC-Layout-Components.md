## Table of Contents

- [Swing and MVC Design Pattern](#swing-and-mvc-design-pattern)
  - [Introduction to MVC in Swing](#introduction-to-mvc-in-swing)
  - [Model in Swing](#model-in-swing)
  - [View in Swing](#view-in-swing)
  - [Controller in Swing](#controller-in-swing)
  - [Benefits of MVC in Swing](#benefits-of-mvc-in-swing)
  - [Example: MVC with JSlider and JLabel](#coding-example-mvc-with-jslider-and-jlabel)
- [Layout Management](#layout-management)
  - [Introduction to Layout Managers](#introduction-to-layout-managers)
  - [Common Layout Managers](#common-layout-managers)
    - [FlowLayout](#flowlayout)
    - [BorderLayout](#borderlayout)
    - [GridLayout](#gridlayout)
    - [BoxLayout](#boxlayout)
    - [GridBagLayout](#gridbaglayout)
  - [Choosing the Right Layout Manager](#choosing-the-right-layout-manager)
  - [Example: Combining Multiple Layouts](#coding-example-combining-multiple-layouts)
- [Basic Swing Components](#basic-swing-components)
  - [Overview of Swing Component Hierarchy](#overview-of-swing-component-hierarchy)
  - [Core Swing Components](#core-swing-components)
    - [JFrame](#jframe)
    - [JPanel](#jpanel)
    - [JButton](#jbutton)
    - [JLabel](#jlabel)
    - [JTextField and JTextArea](#jtextfield-and-jtextarea)
    - [JCheckBox and JRadioButton](#jcheckbox-and-jradiobutton)
    - [JComboBox](#jcombobox)
    - [JList](#jlist)
    - [JSlider](#jslider)
    - [JProgressBar](#jprogressbar)
  - [Example: Simple Login Form](#coding-example-simple-login-form)
- [Summary](#summary)

---

## **Swing and MVC Design Pattern**

### **Introduction to MVC in Swing**

Swing follows the **Model-View-Controller (MVC)** design pattern, which separates an application into three interconnected components:

- **Model**: Represents the data and business logic.
- **View**: Displays the data to the user (UI components).
- **Controller**: Handles user input and updates the Model and View accordingly.

Swing components are designed with MVC in mind, and many have built-in models (e.g., `DefaultListModel`, `ButtonModel`) that can be shared or replaced.

---

### **Model in Swing**

The **Model** holds the data and defines how it can be accessed or modified. Swing provides default model classes:

| Component    | Default Model Class  |
| ------------ | -------------------- |
| `JButton`    | `DefaultButtonModel` |
| `JList`      | `DefaultListModel`   |
| `JTable`     | `DefaultTableModel`  |
| `JTextField` | `PlainDocument`      |

We can create custom models by implementing interfaces like `ListModel`, `TableModel`, etc.

---

### **View in Swing**

The **View** is responsible for rendering the UI. In Swing, this includes:

- Visual components (`JButton`, `JLabel`, etc.)
- Layout managers
- Rendering logic (via `paintComponent`)

The View observes the Model and updates when data changes (often via property change listeners).

---

### **Controller in Swing**

The **Controller** mediates between View and Model. It:

- Listens to user events (via listeners)
- Updates the Model
- Triggers View refresh

In Swing, event listeners (e.g., `ActionListener`) typically act as controllers.

---

### **Benefits of MVC in Swing**

- **Separation of Concerns**: UI logic is decoupled from data.
- **Reusability**: Same model can be used with different views.
- **Testability**: Business logic can be tested independently.
- **Flexibility**: Change UI without affecting data logic.

---

### **Example: MVC with JSlider and JLabel**

```java
import javax.swing.*;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;
import java.awt.*;

public class MVCSliderDemo {

    // Model: Holds the slider value
    static class SliderModel {
        private int value = 50;
        private ChangeListener listener;

        public int getValue() { return value; }
        public void setValue(int value) {
            if (this.value != value) {
                this.value = value;
                if (listener != null) {
                    listener.stateChanged(new ChangeEvent(this));
                }
            }
        }

        public void addChangeListener(ChangeListener l) {
            this.listener = l;
        }
    }

    public static void main(String[] args) {
        JFrame frame = new JFrame("MVC Slider Demo");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(300, 150);
        frame.setLayout(new FlowLayout());

        // Model
        SliderModel model = new SliderModel();

        // View Components
        JSlider slider = new JSlider(0, 100, model.getValue());
        JLabel label = new JLabel("Value: " + model.getValue());

        // Controller: Sync slider (View) with model
        slider.addChangeListener(e -> model.setValue(slider.getValue()));

        // Observer: Update label when model changes
        model.addChangeListener(e -> label.setText("Value: " + model.getValue()));

        frame.add(slider);
        frame.add(label);
        frame.setVisible(true);
    }
}
```

---

## **Layout Management**

### **Introduction to Layout Managers**

Swing uses **layout managers** to automatically arrange components in a container. Unlike absolute positioning, layout managers adapt to resizing, screen size, and platform differences.

---

### **Common Layout Managers**

#### **FlowLayout**

Arranges components in a directional flow (left-to-right, top-to-bottom). Ideal for simple forms or toolbars.

```java
panel.setLayout(new FlowLayout());
```

#### **BorderLayout**

Divides container into five regions: `NORTH`, `SOUTH`, `EAST`, `WEST`, `CENTER`. Only one component per region.

```java
panel.add(button, BorderLayout.NORTH);
```

#### **GridLayout**

Arranges components in a rectangular grid of equal-sized cells.

```java
panel.setLayout(new GridLayout(2, 3)); // 2 rows, 3 columns
```

#### **BoxLayout**

Arranges components in a single row or column. Supports struts and glue for spacing.

```java
panel.setLayout(new BoxLayout(panel, BoxLayout.Y_AXIS));
```

#### **GridBagLayout**

Most powerful and flexible. Uses constraints to position components in a grid with variable cell sizes.

```java
GridBagConstraints gbc = new GridBagConstraints();
gbc.gridx = 0; gbc.gridy = 1;
panel.add(component, gbc);
```

---

### **Choosing the Right Layout Manager**

| Use Case                  | Recommended Layout               |
| ------------------------- | -------------------------------- |
| Simple left-to-right flow | `FlowLayout`                     |
| Header/Footer + Center    | `BorderLayout`                   |
| Form with labels & fields | `GridBagLayout` or `GroupLayout` |
| Equal button grid         | `GridLayout`                     |
| Vertical/horizontal stack | `BoxLayout`                      |

---

### **Example: Combining Multiple Layouts**

```java
import javax.swing.*;
import java.awt.*;

public class CombinedLayoutDemo {

    public static void main(String[] args) {
        JFrame frame = new JFrame("Combined Layouts");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(400, 300);

        // Main panel uses BorderLayout
        JPanel mainPanel = new JPanel(new BorderLayout());

        // North: Title (FlowLayout)
        JPanel titlePanel = new JPanel(new FlowLayout());
        titlePanel.add(new JLabel("<html><h1>User Registration</h1></html>"));
        mainPanel.add(titlePanel, BorderLayout.NORTH);

        // Center: Form (GridBagLayout)
        JPanel formPanel = new JPanel(new GridBagLayout());
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(5, 5, 5, 5);
        gbc.anchor = GridBagConstraints.WEST;

        gbc.gridx = 0; gbc.gridy = 0;
        formPanel.add(new JLabel("Name:"), gbc);
        gbc.gridx = 1;
        formPanel.add(new JTextField(15), gbc);

        gbc.gridx = 0; gbc.gridy = 1;
        formPanel.add(new JLabel("Email:"), gbc);
        gbc.gridx = 1;
        formPanel.add(new JTextField(15), gbc);

        mainPanel.add(formPanel, BorderLayout.CENTER);

        // South: Buttons (FlowLayout)
        JPanel buttonPanel = new JPanel(new FlowLayout());
        buttonPanel.add(new JButton("Submit"));
        buttonPanel.add(new JButton("Cancel"));
        mainPanel.add(buttonPanel, BorderLayout.SOUTH);

        frame.add(mainPanel);
        frame.setVisible(true);
    }
}
```

---

## **Basic Swing Components**

### **Overview of Swing Component Hierarchy**

```
JComponent
├── AbstractButton
│   ├── JButton
│   ├── JToggleButton
│   │   ├── JCheckBox
│   │   └── JRadioButton
│   └── JMenuItem
├── JLabel
├── JTextComponent
│   ├── JTextField
│   └── JTextArea
├── JList
├── JComboBox
├── JSlider
├── JProgressBar
└── JPanel
```

All visible Swing components extend `JComponent` (except `JFrame`, `JDialog`, etc., which extend `java.awt.Container`).

---

### **Core Swing Components**

#### **JFrame**

Top-level window with title bar and borders.

```java
JFrame frame = new JFrame("My App");
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
```

#### **JPanel**

Invisible container for grouping components. Uses layout manager.

```java
JPanel panel = new JPanel();
panel.setBorder(BorderFactory.createTitledBorder("Settings"));
```

#### **JButton**

Clickable button that triggers `ActionEvent`.

```java
JButton button = new JButton("Click Me");
button.addActionListener(e -> System.out.println("Clicked!"));
```

#### **JLabel**

Displays text or icons. Non-editable.

```java
JLabel label = new JLabel("Hello, Swing!", SwingConstants.CENTER);
```

#### **JTextField and JTextArea**

- `JTextField`: Single-line text input.
- `JTextArea`: Multi-line text input.

```java
JTextField field = new JTextField(20);
JTextArea area = new JTextArea(5, 20);
area.setLineWrap(true);
```

#### **JCheckBox and JRadioButton**

Selection controls.

```java
JCheckBox check = new JCheckBox("Enable Feature");
JRadioButton radio1 = new JRadioButton("Option 1");
JRadioButton radio2 = new JRadioButton("Option 2");

// Group radio buttons
ButtonGroup group = new ButtonGroup();
group.add(radio1); group.add(radio2);
```

#### **JComboBox**

Drop-down list for selection.

```java
String[] items = {"Red", "Green", "Blue"};
JComboBox<String> combo = new JComboBox<>(items);
combo.addActionListener(e -> System.out.println(combo.getSelectedItem()));
```

#### **JList**

Scrollable list of items.

```java
DefaultListModel<String> model = new DefaultListModel<>();
model.addElement("Apple");
model.addElement("Banana");
JList<String> list = new JList<>(model);
```

#### **JSlider**

Allows selecting a value from a range.

```java
JSlider slider = new JSlider(0, 100, 50);
slider.setMajorTickSpacing(20);
slider.setPaintTicks(true);
slider.setPaintLabels(true);
```

#### **JProgressBar**

Shows progress of a task.

```java
JProgressBar progress = new JProgressBar(0, 100);
progress.setValue(65);
progress.setStringPainted(true);
```

---

### **Example: Simple Login Form**

```java
import javax.swing.*;
import java.awt.*;

public class LoginFormDemo {

    public static void main(String[] args) {
        JFrame frame = new JFrame("Login");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(300, 200);

        JPanel panel = new JPanel(new GridBagLayout());
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(5, 5, 5, 5);
        gbc.fill = GridBagConstraints.HORIZONTAL;

        // Username
        gbc.gridx = 0; gbc.gridy = 0;
        panel.add(new JLabel("Username:"), gbc);
        gbc.gridx = 1;
        JTextField userField = new JTextField(15);
        panel.add(userField, gbc);

        // Password
        gbc.gridx = 0; gbc.gridy = 1;
        panel.add(new JLabel("Password:"), gbc);
        gbc.gridx = 1;
        JPasswordField passField = new JPasswordField(15);
        panel.add(passField, gbc);

        // Buttons
        gbc.gridx = 0; gbc.gridy = 2; gbc.gridwidth = 2;
        gbc.anchor = GridBagConstraints.CENTER;
        JPanel buttonPanel = new JPanel(new FlowLayout());
        JButton loginBtn = new JButton("Login");
        JButton cancelBtn = new JButton("Cancel");
        buttonPanel.add(loginBtn);
        buttonPanel.add(cancelBtn);
        panel.add(buttonPanel, gbc);

        // Action
        loginBtn.addActionListener(e -> {
            String user = userField.getText();
            String pass = new String(passField.getPassword());
            JOptionPane.showMessageDialog(frame, "Login: " + user + ", Pass: " + pass);
        });

        frame.add(panel);
        frame.setVisible(true);
    }
}
```

---

## **Summary**

- **MVC in Swing**: Separates data (Model), UI (View), and interaction logic (Controller) for clean, maintainable code.
- **Layout Management**: Use layout managers (`FlowLayout`, `BorderLayout`, etc.) instead of fixed positioning. Nest panels for complex UIs.
- **Basic Swing Components**: `JFrame`, `JPanel`, `JButton`, `JLabel`, `JTextField`, `JCheckBox`, `JComboBox`, `JSlider`, etc., form the foundation of Swing GUIs.
