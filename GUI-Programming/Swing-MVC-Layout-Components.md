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
- [Example: Component Showcase with Real-time Interactions](#component-showcase-with-real-time-interactions)
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

### **Example: Component Showcase with Real-time Interactions**

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ItemEvent;

public class SwingComponentsShowcase {

    // Constants - UI Configuration
    private static final int WINDOW_WIDTH = 750;
    private static final int WINDOW_HEIGHT = 650;
    private static final int PROGRESS_INITIAL_VALUE = 50;

    private static final Color HEADER_BACKGROUND = new Color(240, 248, 255);
    private static final Color PANEL_BACKGROUND = new Color(245, 245, 245);
    private static final Color TITLE_COLOR = new Color(0, 102, 204);
    private static final Color PROGRESS_COLOR = new Color(50, 205, 50);

    // Component References - Global access for event handlers
    private static JProgressBar progressBar;
    private static JPanel formPanel;
    private static JPanel headerPanel;
    private static JPanel selectionPanel;
    private static JLabel titleLabel;

    // Form input components
    private static JTextField nameField;
    private static JPasswordField passwordField;
    private static JTextArea commentArea;
    private static JComboBox<String> colorComboBox;
    private static JSlider volumeSlider;

    // Selection components
    private static JCheckBox boldCheckbox;
    private static JCheckBox italicCheckbox;
    private static JRadioButton smallRadio;
    private static JRadioButton mediumRadio;
    private static JRadioButton largeRadio;
    private static JList<String> fruitList;

    public static void main(String[] args) {
        SwingUtilities.invokeLater(SwingComponentsShowcase::createAndShowGUI);
    }

    /**
     * Creates and displays the main application window with all components.
     * Uses BorderLayout to organize header, form, selection panel, and buttons.
     */
    private static void createAndShowGUI() {
        JFrame frame = new JFrame("Swing Components Showcase");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(WINDOW_WIDTH, WINDOW_HEIGHT);
        frame.setLocationRelativeTo(null);

        JPanel mainPanel = new JPanel(new BorderLayout(10, 10));
        mainPanel.setBorder(BorderFactory.createCompoundBorder(
            BorderFactory.createEmptyBorder(10, 10, 10, 10),
            BorderFactory.createTitledBorder(
                BorderFactory.createEtchedBorder(),
                "All Core Swing Components")
        ));

        buildHeaderPanel();
        buildFormPanel();
        buildSelectionPanel();
        JPanel buttonPanel = buildButtonPanel();

        mainPanel.add(headerPanel, BorderLayout.NORTH);
        mainPanel.add(formPanel, BorderLayout.CENTER);
        mainPanel.add(selectionPanel, BorderLayout.WEST);
        mainPanel.add(buttonPanel, BorderLayout.SOUTH);

        frame.add(mainPanel);
        frame.setVisible(true);

        // Set initial Red color theme after all panels are built
        Color redBackground = new Color(255, 220, 220);
        formPanel.setBackground(redBackground);
        headerPanel.setBackground(redBackground.brighter());
        selectionPanel.setBackground(redBackground.brighter());

        setupRealTimeInteractions();
    }

    /**
     * Creates header panel with title label and progress bar.
     * Demonstrates JLabel styling and JProgressBar with string painting.
     */
    private static void buildHeaderPanel() {
        headerPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 20, 15));
        headerPanel.setBackground(HEADER_BACKGROUND);

        titleLabel = new JLabel("Welcome to Swing Demo!", SwingConstants.CENTER);
        titleLabel.setFont(new Font("SansSerif", Font.BOLD, 20));
        titleLabel.setForeground(TITLE_COLOR);
        headerPanel.add(titleLabel);

        progressBar = new JProgressBar(0, 100);
        progressBar.setValue(PROGRESS_INITIAL_VALUE);
        progressBar.setStringPainted(true);
        progressBar.setString(PROGRESS_INITIAL_VALUE + "%");
        progressBar.setPreferredSize(new Dimension(220, 35));
        progressBar.setForeground(PROGRESS_COLOR);
        progressBar.setFont(new Font("Arial", Font.BOLD, 12));
        headerPanel.add(progressBar);
    }

    /**
     * Creates form panel with text fields, password field, text area, combo box, and slider.
     */
    private static void buildFormPanel() {
        formPanel = new JPanel(new GridBagLayout());
        formPanel.setBackground(Color.WHITE);

        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(10, 10, 10, 10);
        gbc.anchor = GridBagConstraints.WEST;
        gbc.fill = GridBagConstraints.HORIZONTAL;

        // Name input field
        nameField = new JTextField(20);
        nameField.setToolTipText("Enter your full name");
        addFormRow(gbc, "Name:", nameField, 0);

        // Password input field
        passwordField = new JPasswordField(20);
        passwordField.setToolTipText("Enter your password");
        addFormRow(gbc, "Password:", passwordField, 1);

        // Multi-line comment area with scrolling
        gbc.gridx = 0;
        gbc.gridy = 2;
        formPanel.add(new JLabel("Comments:"), gbc);

        gbc.gridx = 1;
        commentArea = new JTextArea(4, 20);
        commentArea.setLineWrap(true);
        commentArea.setWrapStyleWord(true);
        commentArea.setToolTipText("Write your feedback or comments");
        JScrollPane scrollPane = new JScrollPane(commentArea);
        formPanel.add(scrollPane, gbc);

        // Color selection dropdown
        colorComboBox = new JComboBox<>(new String[]{"Red", "Green", "Blue", "Yellow"});
        colorComboBox.setToolTipText("Choose a background color theme");
        addFormRow(gbc, "Color:", colorComboBox, 3);

        // Volume slider with tick marks
        volumeSlider = new JSlider(0, 100, PROGRESS_INITIAL_VALUE);
        volumeSlider.setMajorTickSpacing(25);
        volumeSlider.setMinorTickSpacing(5);
        volumeSlider.setPaintTicks(true);
        volumeSlider.setPaintLabels(true);
        volumeSlider.setToolTipText("Adjust volume level (0-100)");
        addFormRow(gbc, "Volume:", volumeSlider, 4);
    }

    /**
     * Helper method to add a labeled form row.
     */
    private static void addFormRow(GridBagConstraints gbc, String labelText,
                                   JComponent component, int row) {
        gbc.gridx = 0;
        gbc.gridy = row;
        formPanel.add(new JLabel(labelText), gbc);

        gbc.gridx = 1;
        formPanel.add(component, gbc);
    }

    /**
     * Creates selection panel with checkboxes, radio buttons, and list.
     */
    private static void buildSelectionPanel() {
        selectionPanel = new JPanel();
        selectionPanel.setLayout(new BoxLayout(selectionPanel, BoxLayout.Y_AXIS));
        selectionPanel.setBorder(BorderFactory.createTitledBorder("Selection Controls"));
        selectionPanel.setBackground(PANEL_BACKGROUND);

        // Checkboxes for text styling
        boldCheckbox = new JCheckBox("Bold Text");
        boldCheckbox.setToolTipText("Apply bold style to title");
        italicCheckbox = new JCheckBox("Italic Text");
        italicCheckbox.setToolTipText("Apply italic style to title");
        selectionPanel.add(boldCheckbox);
        selectionPanel.add(italicCheckbox);
        selectionPanel.add(Box.createVerticalStrut(15));

        // Radio buttons for font size
        selectionPanel.add(new JLabel("Font Size:"));
        smallRadio = new JRadioButton("Small");
        mediumRadio = new JRadioButton("Medium", true);
        largeRadio = new JRadioButton("Large");

        ButtonGroup sizeGroup = new ButtonGroup();
        sizeGroup.add(smallRadio);
        sizeGroup.add(mediumRadio);
        sizeGroup.add(largeRadio);

        selectionPanel.add(smallRadio);
        selectionPanel.add(mediumRadio);
        selectionPanel.add(largeRadio);
        selectionPanel.add(Box.createVerticalStrut(15));

        // Scrollable list of fruits
        selectionPanel.add(new JLabel("Fruits:"));
        DefaultListModel<String> fruitModel = new DefaultListModel<>();
        String[] fruits = {"Apple", "Banana", "Cherry", "Date", "Elderberry", "Fig"};
        for (String fruit : fruits) {
            fruitModel.addElement(fruit);
        }

        fruitList = new JList<>(fruitModel);
        fruitList.setVisibleRowCount(5);
        fruitList.setSelectionMode(ListSelectionModel.SINGLE_SELECTION);
        JScrollPane listScroll = new JScrollPane(fruitList);
        listScroll.setPreferredSize(new Dimension(130, 110));
        selectionPanel.add(listScroll);
    }

    /**
     * Creates button panel with submit, clear, and exit buttons.
     */
    private static JPanel buildButtonPanel() {
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 20, 15));

        JButton submitBtn = new JButton("Submit");
        submitBtn.setToolTipText("Submit form data");
        submitBtn.addActionListener(e -> showSubmissionDialog());

        JButton clearBtn = new JButton("Clear");
        clearBtn.setToolTipText("Reset all fields");
        clearBtn.addActionListener(e -> resetAllFields());

        JButton exitBtn = new JButton("Exit");
        exitBtn.setToolTipText("Close application");
        exitBtn.addActionListener(e -> System.exit(0));

        buttonPanel.add(submitBtn);
        buttonPanel.add(clearBtn);
        buttonPanel.add(exitBtn);

        return buttonPanel;
    }

    /**
     * Displays a dialog with all submitted form values.
     */
    private static void showSubmissionDialog() {
        String name = nameField.getText().trim();
        String password = new String(passwordField.getPassword());
        String comment = commentArea.getText().trim();
        String color = (String) colorComboBox.getSelectedItem();
        int volume = volumeSlider.getValue();

        // Get selected values from left panel
        String boldText = boldCheckbox.isSelected() ? "Yes" : "No";
        String italicText = italicCheckbox.isSelected() ? "Yes" : "No";
        String fontSize = smallRadio.isSelected() ? "Small" :
                         mediumRadio.isSelected() ? "Medium" :
                         largeRadio.isSelected() ? "Large" : "None";
        String selectedFruit = fruitList.getSelectedValue();

        StringBuilder message = new StringBuilder("=== Form Submission ===\n\n");
        message.append("Name: ").append(name.isEmpty() ? "[Empty]" : name).append("\n");
        message.append("Password: ").append(password.isEmpty() ? "[None]" : "******").append("\n");
        message.append("Comment: ").append(comment.isEmpty() ? "[None]" : comment).append("\n");
        message.append("Color: ").append(color).append("\n");
        message.append("Volume: ").append(volume).append("%\n\n");
        message.append("=== Selection Panel ===\n\n");
        message.append("Bold: ").append(boldText).append("\n");
        message.append("Italic: ").append(italicText).append("\n");
        message.append("Font Size: ").append(fontSize).append("\n");
        message.append("Selected Fruit: ").append(selectedFruit == null ? "[None]" : selectedFruit);

        JOptionPane.showMessageDialog(null, message.toString(),
            "Submitted!", JOptionPane.INFORMATION_MESSAGE);
    }

    /**
     * Resets all form fields and selections to default values.
     */
    private static void resetAllFields() {
        // Reset form inputs
        nameField.setText("");
        passwordField.setText("");
        commentArea.setText("");
        colorComboBox.setSelectedIndex(0);
        volumeSlider.setValue(PROGRESS_INITIAL_VALUE);

        // Reset selections
        boldCheckbox.setSelected(false);
        italicCheckbox.setSelected(false);
        mediumRadio.setSelected(true);
        fruitList.clearSelection();

        // Reset visual components
        progressBar.setValue(PROGRESS_INITIAL_VALUE);
        progressBar.setString(PROGRESS_INITIAL_VALUE + "%");

        // Reset to default Red color theme
        Color redBackground = new Color(255, 220, 220);
        formPanel.setBackground(redBackground);
        headerPanel.setBackground(redBackground.brighter());
        selectionPanel.setBackground(redBackground.brighter());

        titleLabel.setFont(new Font("SansSerif", Font.BOLD, 20));
    }

    /**
     * Sets up real-time interactions between components:
     * - Volume slider updates progress bar
     * - Color combo box changes panel backgrounds
     * - Checkboxes modify title font style
     * - Radio buttons change title font size
     */
    private static void setupRealTimeInteractions() {
        // Slider -> Progress Bar synchronization
        volumeSlider.addChangeListener(e -> {
            int value = volumeSlider.getValue();
            progressBar.setValue(value);
            progressBar.setString(value + "%");
        });

        // ComboBox -> Background color theming
        colorComboBox.addItemListener(e -> {
            if (e.getStateChange() == ItemEvent.SELECTED) {
                String selectedColor = (String) e.getItem();
                Color backgroundColor = switch (selectedColor) {
                    case "Red" -> new Color(255, 220, 220);
                    case "Green" -> new Color(220, 255, 220);
                    case "Blue" -> new Color(220, 220, 255);
                    case "Yellow" -> new Color(255, 255, 220);
                    default -> Color.WHITE;
                };

                formPanel.setBackground(backgroundColor);
                headerPanel.setBackground(backgroundColor.brighter());
                selectionPanel.setBackground(backgroundColor.brighter());
            }
        });

        // Checkboxes -> Title font style update
        Runnable updateTitleStyle = () -> {
            int style = Font.PLAIN;
            if (boldCheckbox.isSelected()) style |= Font.BOLD;
            if (italicCheckbox.isSelected()) style |= Font.ITALIC;

            int size = smallRadio.isSelected() ? 16 :
                      mediumRadio.isSelected() ? 20 :
                      largeRadio.isSelected() ? 24 : 20;

            titleLabel.setFont(new Font("SansSerif", style, size));
        };

        boldCheckbox.addItemListener(e -> updateTitleStyle.run());
        italicCheckbox.addItemListener(e -> updateTitleStyle.run());

        // Radio buttons -> Title font size update
        smallRadio.addItemListener(e -> updateTitleStyle.run());
        mediumRadio.addItemListener(e -> updateTitleStyle.run());
        largeRadio.addItemListener(e -> updateTitleStyle.run());
    }
}

```

---

## **Summary**

- **MVC in Swing**: Separates data (Model), UI (View), and interaction logic (Controller) for clean, maintainable code.
- **Layout Management**: Use layout managers (`FlowLayout`, `BorderLayout`, etc.) instead of fixed positioning. Nest panels for complex UIs.
- **Basic Swing Components**: `JFrame`, `JPanel`, `JButton`, `JLabel`, `JTextField`, `JCheckBox`, `JComboBox`, `JSlider`, etc., form the foundation of Swing GUIs.
