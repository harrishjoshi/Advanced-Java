
## Table of Contents
- [Using BeanInfo Interface](#using-beaninfo-interface)
- [Bound and Constrained Properties](#bound-and-constrained-properties)
- [Persistence](#persistence)
- [Customizers](#customizers)
- [Common Pitfalls & Gotchas](#common-pitfalls--gotchas)
- [Summary](#summary)

---

## Using BeanInfo Interface

The **BeanInfo interface** allows us to **explicitly control** what information is exposed about our JavaBean, instead of relying entirely on automatic introspection.

By default, the `Introspector` class automatically discovers properties, methods, and events through reflection. In practice we rarely implement `BeanInfo` directly; we usually extend `SimpleBeanInfo` and override only the methods we need (for example, `getPropertyDescriptors()`, `getMethodDescriptors()` or `getBeanDescriptor()`). Extending `SimpleBeanInfo` preserves default behavior for descriptors we do not override.

However, we can create a custom `BeanInfo` class to:

- Hide certain properties or methods
- Provide custom descriptions
- Specify property editors
- Set display names and icons
- Control the order of properties

### Naming Convention

If our bean is named `Student`, the BeanInfo class must be named `StudentBeanInfo` (or registered explicitly) and placed in the same package.

### Example: Basic Bean

```java
import java.io.Serializable;

public class Student implements Serializable {
    private int id;
    private String name;

    public Student() {}

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

### Example: Hiding Properties with BeanInfo

```java
import java.beans.*;
import java.io.Serializable;

public class Student implements Serializable {
    private int id;
    private String name;
    private String internalCode; // We'll hide this

    public Student() {}

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public String getInternalCode() { return internalCode; }
    public void setInternalCode(String code) { this.internalCode = code; }
}

public class StudentBeanInfo extends SimpleBeanInfo {
    
    @Override
    public PropertyDescriptor[] getPropertyDescriptors() {
        try {
            // Only expose id and name
            PropertyDescriptor id = new PropertyDescriptor("id", Student.class);
            PropertyDescriptor name = new PropertyDescriptor("name", Student.class);

            // internalCode is NOT included, so it's hidden
            return new PropertyDescriptor[] { id, name };
            
        } catch (IntrospectionException e) {
            System.err.println("Failed to create property descriptors: " + e);
            return null;
        }
    }
}
```

**Note:** returning `null` from `getPropertyDescriptors()` tells the `Introspector` to fall back to automatic introspection; returning an **empty array** signals explicitly that the bean exposes no properties. 

### Key Points

- We extend `SimpleBeanInfo` (provides default implementations)
- We override methods like `getPropertyDescriptors()`, `getMethodDescriptors()`, `getEventSetDescriptors()`
- We use `PropertyDescriptor` to define each property with custom metadata
- Properties not included in the array are **hidden** from introspection tools

---

## Bound and Constrained Properties

JavaBeans support two special types of properties that notify listeners when values change.

### 1. Bound Properties

A **bound property** fires a `PropertyChangeEvent` whenever its value changes, allowing other objects to listen and react to the change.

**Steps to implement:**

1. Add a `PropertyChangeSupport` helper object
2. Provide methods to add/remove `PropertyChangeListener`
3. Fire events in setters when value changes

### Example: Basic Bound Property

```java
import java.beans.*;
import java.io.Serializable;

public class Student implements Serializable {
    private String name;
    private PropertyChangeSupport changes = new PropertyChangeSupport(this);

    public Student() {}

    public void addPropertyChangeListener(PropertyChangeListener listener) {
        changes.addPropertyChangeListener(listener);
    }

    public void removePropertyChangeListener(PropertyChangeListener listener) {
        changes.removePropertyChangeListener(listener);
    }

    public String getName() {
        return name;
    }

    public void setName(String newName) {
        String oldName = this.name;
        this.name = newName;
        changes.firePropertyChange("name", oldName, newName);
    }
}
```

**Implementation:**

```java
public class Demo {
    public static void main(String[] args) {
        Student student = new Student();
        
        // Listen for changes
        student.addPropertyChangeListener(evt -> {
            System.out.println("Name changed to: " + evt.getNewValue());
        });
        
        student.setName("Alice");  // Prints: Name changed to: Alice
    }
}
```

### Example: Bound Property with Best Practices

```java
import java.beans.*;
import java.io.Serializable;

public class Student implements Serializable {
    private String name;
    private int age;
    private PropertyChangeSupport changes = new PropertyChangeSupport(this);

    public Student() {}

    public void addPropertyChangeListener(PropertyChangeListener listener) {
        changes.addPropertyChangeListener(listener);
    }

    public void removePropertyChangeListener(PropertyChangeListener listener) {
        changes.removePropertyChangeListener(listener);
    }

    public String getName() {
        return name;
    }

    // Best practice: only fire when value actually changes
    public void setName(String newName) {
        String oldName = this.name;
        if (!java.util.Objects.equals(oldName, newName)) {
            this.name = newName;
            changes.firePropertyChange("name", oldName, newName);
        }
    }

    public int getAge() {
        return age;
    }

    public void setAge(int newAge) {
        int oldAge = this.age;
        if (oldAge != newAge) {
            this.age = newAge;
            changes.firePropertyChange("age", oldAge, newAge);
        }
    }
}
```

**Note:** Checking for equality before firing events avoids noisy or duplicate event firing.

**Thread Safety:** `PropertyChangeSupport` is thread-safe for listener add/remove and firing, but if our bean has other shared mutable state we should consider additional synchronization when calling setters from multiple threads.

### 2. Constrained Properties

A **constrained property** allows listeners to **veto** (reject) a proposed change before it happens by throwing a `PropertyVetoException`.

**Steps to implement:**

1. Add a `VetoableChangeSupport` helper object
2. Provide methods to add/remove `VetoableChangeListener`
3. Fire vetoable events **before** changing the value
4. Declare `throws PropertyVetoException` in the setter

### Example: Basic Constrained Property

```java
import java.beans.*;
import java.io.Serializable;

public class Student implements Serializable {
    private int age;
    private VetoableChangeSupport vetos = new VetoableChangeSupport(this);

    public Student() {}

    public void addVetoableChangeListener(VetoableChangeListener listener) {
        vetos.addVetoableChangeListener(listener);
    }

    public void removeVetoableChangeListener(VetoableChangeListener listener) {
        vetos.removeVetoableChangeListener(listener);
    }

    public int getAge() {
        return age;
    }

    public void setAge(int newAge) throws PropertyVetoException {
        int oldAge = this.age;
        vetos.fireVetoableChange("age", oldAge, newAge);  // Can be rejected
        this.age = newAge;  // Only happens if not vetoed
    }
}
```

**Implementation:**

```java
public class Demo {
    public static void main(String[] args) {
        Student student = new Student();
        
        // Add validator
        student.addVetoableChangeListener(evt -> {
            int newAge = (Integer) evt.getNewValue();
            if (newAge < 18) {
                throw new PropertyVetoException("Age must be 18+", evt);
            }
        });
        
        try {
            student.setAge(25);  // OK
            student.setAge(15);  // Rejected!
        } catch (PropertyVetoException e) {
            System.out.println("Rejected: " + e.getMessage());
        }
    }
}
```

### Example: Full Constrained Property Implementation

Note: callers must handle `PropertyVetoException` because the setter declares `throws PropertyVetoException`. When writing APIs that expose constrained setters, document the checked exception in the API Javadoc so callers know to handle `PropertyVetoException`.

```java
import java.beans.*;
import java.io.Serializable;

public class Student implements Serializable {
    private int age;
    private VetoableChangeSupport vetos = new VetoableChangeSupport(this);

    public Student() {}

    public void addVetoableChangeListener(VetoableChangeListener listener) {
        vetos.addVetoableChangeListener(listener);
    }

    public void removeVetoableChangeListener(VetoableChangeListener listener) {
        vetos.removeVetoableChangeListener(listener);
    }

    public int getAge() {
        return age;
    }

    // Constrained property: can be vetoed
    public void setAge(int newAge) throws PropertyVetoException {
        int oldAge = this.age;
        
        // Fire BEFORE changing - listeners can veto
        vetos.fireVetoableChange("age", oldAge, newAge);
        
        // If no exception thrown, apply the change
        this.age = newAge;
    }
}
```

**Implementation:**

```java
public class Demo {
    public static void main(String[] args) {
        Student student = new Student();
        
        // Add a validator that rejects ages below 18
        student.addVetoableChangeListener(evt -> {
            if ("age".equals(evt.getPropertyName())) {
                int newAge = (Integer) evt.getNewValue();
                if (newAge < 18) {
                    throw new PropertyVetoException(
                        "Age must be at least 18", evt);
                }
            }
        });
        
        try {
            student.setAge(25);  // OK - accepted
            System.out.println("Age set to 25");
            
            student.setAge(15);  // REJECTED - throws exception
        } catch (PropertyVetoException e) {
            System.out.println("Change rejected: " + e.getMessage());
        }
    }
}
```

### Combining Bound and Constrained Properties

A property may be both *constrained* and *bound*. The typical sequence is: (1) fire the vetoable event before changing the state, (2) apply the new value if no veto occurs, and (3) fire the property-change event after the state has been updated.

### Example: Combined Property

```java
import java.beans.*;
import java.io.Serializable;

public class Student implements Serializable {
    private int age;
    private VetoableChangeSupport vetos = new VetoableChangeSupport(this);
    private PropertyChangeSupport changes = new PropertyChangeSupport(this);

    public Student() {}

    // Add listener methods...
    public void addVetoableChangeListener(VetoableChangeListener l) {
        vetos.addVetoableChangeListener(l);
    }

    public void addPropertyChangeListener(PropertyChangeListener l) {
        changes.addPropertyChangeListener(l);
    }

    public int getAge() {
        return age;
    }

    public void setAge(int newAge) throws PropertyVetoException {
        int oldAge = this.age;
        
        vetos.fireVetoableChange("age", oldAge, newAge);  // Step 1: Validate
        
        if (oldAge != newAge) {
            this.age = newAge;  // Step 2: Apply
            changes.firePropertyChange("age", oldAge, newAge);  // Step 3: Notify
        }
    }
}
```

**Implementation:**

```java
public class Demo {
    public static void main(String[] args) {
        Student student = new Student();
        
        // Add validator (constrained)
        student.addVetoableChangeListener(evt -> {
            if ((Integer) evt.getNewValue() < 0) {
                throw new PropertyVetoException("Age cannot be negative", evt);
            }
        });
        
        // Add observer (bound)
        student.addPropertyChangeListener(evt -> {
            System.out.println("Age updated to: " + evt.getNewValue());
        });
        
        try {
            student.setAge(20);  // Validated, then observers notified
        } catch (PropertyVetoException e) {
            System.out.println("Change rejected: " + e.getMessage());
        }
    }
}
```

### Example: Full Implementation

```java
import java.beans.*;
import java.io.Serializable;

public class Student implements Serializable {
    private int age;
    
    private VetoableChangeSupport vetos = new VetoableChangeSupport(this);
    private PropertyChangeSupport changes = new PropertyChangeSupport(this);

    public Student() {}

    public void addVetoableChangeListener(VetoableChangeListener listener) {
        vetos.addVetoableChangeListener(listener);
    }

    public void removeVetoableChangeListener(VetoableChangeListener listener) {
        vetos.removeVetoableChangeListener(listener);
    }

    public void addPropertyChangeListener(PropertyChangeListener listener) {
        changes.addPropertyChangeListener(listener);
    }

    public void removePropertyChangeListener(PropertyChangeListener listener) {
        changes.removePropertyChangeListener(listener);
    }

    public int getAge() {
        return age;
    }

    public void setAge(int newAge) throws PropertyVetoException {
        int oldAge = this.age;
        
        // Step 1: Fire vetoable event (can be rejected)
        vetos.fireVetoableChange("age", oldAge, newAge);
        
        // Step 2: Apply the change only if different
        if (oldAge != newAge) {
            this.age = newAge;
            
            // Step 3: Fire property change event (notification only)
            changes.firePropertyChange("age", oldAge, newAge);
        }
    }
}
```

### Comparison

| Feature              | Bound Property              | Constrained Property           |
|----------------------|-----------------------------|--------------------------------|
| Purpose              | Notify after change         | Validate before change         |
| Listener Type        | `PropertyChangeListener`    | `VetoableChangeListener`       |
| Support Class        | `PropertyChangeSupport`     | `VetoableChangeSupport`        |
| Exception            | None                        | `PropertyVetoException`        |
| When Event Fires     | After value changes         | Before value changes           |
| Can Prevent Change   | No                          | Yes (by throwing exception)    |

---

## Persistence

**Persistence** means saving the state of a JavaBean to storage (file, database, etc.) and restoring it later. Java provides two main mechanisms:

### 1. Serialization (Binary Format)

The standard Java serialization mechanism saves objects to binary format.

**Requirements:**
- Implement `java.io.Serializable` interface
- All fields must be serializable (or marked `transient`)

### Example: Basic Serialization

```java
import java.io.*;

public class Student implements Serializable {
    private int id;
    private String name;

    public Student() {}
    
    public Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() { return id; }
    public String getName() { return name; }
}
```

**Saving a bean:**

```java
public class SaveDemo {
    public static void main(String[] args) throws IOException {
        Student student = new Student(101, "Alice");
        
        ObjectOutputStream out = new ObjectOutputStream(
            new FileOutputStream("student.ser"));
        out.writeObject(student);
        out.close();
        
        System.out.println("Saved!");
    }
}
```

**Loading a bean:**

```java
public class LoadDemo {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        ObjectInputStream in = new ObjectInputStream(
            new FileInputStream("student.ser"));
        Student student = (Student) in.readObject();
        in.close();
        
        System.out.println("Loaded: " + student.getName());
    }
}
```

### Example: Serialization with transient Fields

```java
import java.io.*;

public class Student implements Serializable {
    private static final long serialVersionUID = 1L; // Version control
    
    private int id;
    private String name;
    private transient String password; // Won't be saved

    public Student() {}

    public Student(int id, String name, String password) {
        this.id = id;
        this.name = name;
        this.password = password;
    }

    // Getters and setters...
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }
}
```

**Important:** Keep `serialVersionUID` stable across compatible versions; changing it (or failing to manage it) can make previously serialized data unreadable by newer class versions.

**Note:** during Java deserialization the no-arg constructor is *not* invoked; instead the object is reconstructed from the serialized stream. If custom initialization is required on deserialization, implement `readObject(ObjectInputStream)` or use `readResolve()` as appropriate.

**Saving (Serializing) a Bean:**

```java
import java.io.*;

public class PersistenceDemo {
    public static void saveBean() {
        try (ObjectOutputStream out = new ObjectOutputStream(
                new FileOutputStream("student.ser"))) {
            
            Student student = new Student(101, "Alice", "secret123");
            out.writeObject(student);
            System.out.println("Bean saved successfully");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Loading (Deserializing) a Bean:**

```java
import java.io.*;

public class PersistenceDemo {
    public static void loadBean() {
        try (ObjectInputStream in = new ObjectInputStream(
                new FileInputStream("student.ser"))) {
            
            Student student = (Student) in.readObject();
            System.out.println("Loaded: " + student.getName());
            System.out.println("Password: " + student.getPassword()); // null (transient)
            
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

### 2. XMLEncoder/XMLDecoder (Human-Readable Format)

Java provides `XMLEncoder` and `XMLDecoder` for saving beans in XML format, which is human-readable and editable. Note: beans serialized with `XMLEncoder` must expose a **public no-arg constructor** and **public setters** for properties to be persisted.

### Example: Basic XML Persistence

```java
import java.beans.*;
import java.io.*;

public class Student implements Serializable {
    private int id;
    private String name;

    public Student() {}  // Required!

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

**Saving to XML:**

```java
public class XMLSaveDemo {
    public static void main(String[] args) throws IOException {
        Student student = new Student();
        student.setId(101);
        student.setName("Alice");
        
        XMLEncoder encoder = new XMLEncoder(
            new FileOutputStream("student.xml"));
        encoder.writeObject(student);
        encoder.close();
        
        System.out.println("Saved to XML!");
    }
}
```

**Loading from XML:**

```java
public class XMLLoadDemo {
    public static void main(String[] args) throws IOException {
        XMLDecoder decoder = new XMLDecoder(
            new FileInputStream("student.xml"));
        Student student = (Student) decoder.readObject();
        decoder.close();
        
        System.out.println("Loaded: " + student.getName());
    }
}
```

### Example: Full XML Persistence

```java
import java.beans.*;
import java.io.*;

public class XMLPersistenceDemo {
    public static void saveToXML() {
        try (XMLEncoder encoder = new XMLEncoder(
                new FileOutputStream("student.xml"))) {
            
            Student student = new Student();
            student.setId(101);
            student.setName("Alice");
            
            encoder.writeObject(student);
            System.out.println("Bean saved to XML");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Generated XML:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<java version="1.8.0" class="java.beans.XMLDecoder">
 <object class="Student">
  <void property="id">
   <int>101</int>
  </void>
  <void property="name">
   <string>Alice</string>
  </void>
 </object>
</java>
```

**Loading from XML:**

```java
import java.beans.*;
import java.io.*;

public class XMLPersistenceDemo {
    public static void loadFromXML() {
        try (XMLDecoder decoder = new XMLDecoder(
                new FileInputStream("student.xml"))) {
            
            Student student = (Student) decoder.readObject();
            System.out.println("Loaded from XML: " + student.getName());
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**Practical caveats:**

- `XMLEncoder` requires a public no-arg constructor and public setters for properties it must serialize.
- It struggles with complex object graphs (cyclic references) and may not preserve object identity the way binary serialization does.

### Comparison

| Feature              | Serialization               | XMLEncoder                     |
|----------------------|-----------------------------|--------------------------------|
| Format               | Binary                      | XML (text)                     |
| Human-Readable       | No                          | Yes                            |
| File Size            | Smaller                     | Larger                         |
| Requires Interface   | `Serializable`              | Proper JavaBean conventions    |
| Speed                | Faster                      | Slower                         |
| Use Case             | Internal storage, caching   | Configuration files, debugging |

---

## Customizers

A **Customizer** is a custom GUI component that provides a specialized interface for configuring a JavaBean, typically used in visual development tools and IDEs.

Instead of editing properties one-by-one in a property sheet, a customizer provides a complete, user-friendly dialog for configuring all aspects of a bean.

### When to Use Customizers

- Bean has many related properties that should be edited together
- Properties have complex interdependencies
- Need custom validation or specialized UI controls
- Want to provide a better user experience in visual editors

### Implementation Steps

1. Create a class that extends `java.awt.Component` or `javax.swing.JPanel`
2. Implement the `java.beans.Customizer` interface
3. Provide a GUI for editing the bean
4. Register the customizer in our `BeanInfo` class

### Example: Basic Customizer

```java
import java.awt.*;
import java.beans.*;
import java.io.Serializable;
import javax.swing.*;

public class Student implements Serializable {
    private String name;

    public Student() {}

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}

// Simple customizer with one field
public class StudentCustomizer extends JPanel implements Customizer {
    private Student student;
    private JTextField nameField;
    private PropertyChangeSupport changes = new PropertyChangeSupport(this);

    public StudentCustomizer() {
        setLayout(new FlowLayout());
        add(new JLabel("Name:"));
        nameField = new JTextField(15);
        add(nameField);
        
        nameField.addActionListener(e -> {
            if (student != null) {
                student.setName(nameField.getText());
                changes.firePropertyChange("studentData", null, null);
            }
        });
    }

    @Override
    public void setObject(Object bean) {
        this.student = (Student) bean;
        nameField.setText(student.getName());
    }

    @Override
    public void addPropertyChangeListener(PropertyChangeListener l) {
        changes.addPropertyChangeListener(l);
    }

    @Override
    public void removePropertyChangeListener(PropertyChangeListener l) {
        changes.removePropertyChangeListener(l);
    }
}

// Register the customizer
public class StudentBeanInfo extends SimpleBeanInfo {
    @Override
    public BeanDescriptor getBeanDescriptor() {
        return new BeanDescriptor(Student.class, StudentCustomizer.class);
    }
}
```

**Testing the customizer:**

```java
import javax.swing.*;

public class TestCustomizer {
    public static void main(String[] args) {
        Student student = new Student();
        student.setName("Alice");
        
        StudentCustomizer customizer = new StudentCustomizer();
        customizer.setObject(student);
        
        JFrame frame = new JFrame("Edit Student");
        frame.add(customizer);
        frame.setSize(250, 100);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);
    }
}
```

### Example: Full Customizer with Multiple Fields

```java
import java.awt.*;
import java.beans.*;
import java.io.Serializable;
import javax.swing.*;

public class Student implements Serializable {
    private int id;
    private String name;
    private int age;

    public Student() {}

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }
}

// The Customizer
public class StudentCustomizer extends JPanel implements Customizer {
    private Student student;
    private PropertyChangeSupport changes = new PropertyChangeSupport(this);
    
    private JTextField idField;
    private JTextField nameField;
    private JSpinner ageSpinner;

    public StudentCustomizer() {
        setLayout(new GridLayout(3, 2, 5, 5));
        
        // Create UI components
        add(new JLabel("Student ID:"));
        idField = new JTextField();
        add(idField);
        
        add(new JLabel("Name:"));
        nameField = new JTextField();
        add(nameField);
        
        add(new JLabel("Age:"));
        ageSpinner = new JSpinner(new SpinnerNumberModel(18, 1, 100, 1));
        add(ageSpinner);
        
        // Add listeners to update bean when fields change
        idField.addActionListener(e -> updateBean());
        nameField.addActionListener(e -> updateBean());
        ageSpinner.addChangeListener(e -> updateBean());
    }

    @Override
    public void setObject(Object bean) {
        this.student = (Student) bean;
        
        // Load current values into UI
        idField.setText(String.valueOf(student.getId()));
        nameField.setText(student.getName());
        ageSpinner.setValue(student.getAge());
    }

    private void updateBean() {
        if (student != null) {
            try {
                student.setId(Integer.parseInt(idField.getText()));
                student.setName(nameField.getText());
                student.setAge((Integer) ageSpinner.getValue());
                
                // Prefer a meaningful property name when possible
                changes.firePropertyChange("studentData", null, null);
                // or use null to indicate a general change:
                // changes.firePropertyChange(null, null, null);
            } catch (NumberFormatException e) {
                // Handle invalid input
            }
        }
    }

    @Override
    public void addPropertyChangeListener(PropertyChangeListener listener) {
        changes.addPropertyChangeListener(listener);
    }

    @Override
    public void removePropertyChangeListener(PropertyChangeListener listener) {
        changes.removePropertyChangeListener(listener);
    }
}

// The BeanInfo that registers the customizer
public class StudentBeanInfo extends SimpleBeanInfo {
    
    @Override
    public BeanDescriptor getBeanDescriptor() {
        BeanDescriptor bd = new BeanDescriptor(Student.class, StudentCustomizer.class);
        bd.setDisplayName("Student Bean");
        bd.setShortDescription("A student with ID, name, and age");
        return bd;
    }
}
```

### Using the Customizer

Visual tools like NetBeans or Eclipse will automatically detect and use the customizer. For manual testing:

```java
import javax.swing.*;

public class CustomizerDemo {
    public static void main(String[] args) {
        Student student = new Student();
        student.setId(101);
        student.setName("Alice");
        student.setAge(20);
        
        StudentCustomizer customizer = new StudentCustomizer();
        customizer.setObject(student);
        
        JFrame frame = new JFrame("Student Customizer");
        frame.add(customizer);
        frame.setSize(300, 150);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setVisible(true);
    }
}
```

### Key Points

- Customizer must implement `java.beans.Customizer` interface
- We register it via `BeanDescriptor` in our `BeanInfo` class
- We provide `setObject()` to receive the bean to edit
- We fire `PropertyChangeEvent` when bean is modified (prefer meaningful property names)
- Used primarily in visual development tools, not in runtime code

---

## Summary

- **BeanInfo Interface**: Explicit control of introspection metadata — hide properties/methods, provide display names, editors, icons, and supply a `Customizer` via `BeanDescriptor`.
- **Bound Properties**: We notify listeners after property changes using `PropertyChangeSupport`
- **Constrained Properties**: We allow listeners to veto changes before they happen using `VetoableChangeSupport`
- **Persistence**: We can save/restore bean state via serialization (binary) or XMLEncoder (human-readable)
- **Customizers**: We can provide custom GUI editors for complex bean configuration in visual tools
