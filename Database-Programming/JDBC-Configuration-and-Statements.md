## Table of Contents

- [JDBC Configuration and Advanced Features](#jdbc-configuration-and-advanced-features)
  - [1. JDBC Configuration](#1-jdbc-configuration)
    - [The JDBC Connection URL](#the-jdbc-connection-url)
    - [Establishing a Connection](#establishing-a-connection)
    - [Connection Pooling](#connection-pooling)
  - [2. Working with JDBC Statements](#2-working-with-jdbc-statements)
    - [Statement vs. PreparedStatement](#statement-vs-preparedstatement)
  - [3. Scrollable and Updatable Result Sets](#3-scrollable-and-updatable-result-sets)
    - [Scrollability Types](#scrollability-types)
    - [Concurrency Types](#concurrency-types)
    - [Scrollable & Updatable ResultSet Example](#scrollable--updatable-resultset-example)
  - [4. RowSets](#4-rowsets)
    - [Example: CachedRowSet (Disconnected RowSet)](#example-cachedrowset-disconnected-rowset)

---

# JDBC Configuration and Advanced Features

## 1. JDBC Configuration

JDBC (Java Database Connectivity) enables Java applications to connect to databases. Establishing a connection requires three core components:

* **JDBC Driver**
* **JDBC Connection URL**
* **DriverManager** or **DataSource**
---

### The JDBC Connection URL

A JDBC URL follows a standard structure:

```
jdbc:<subprotocol>:<subname>
```

| Component       | Description                | Example (MySQL)              |
| --------------- | -------------------------- | ---------------------------- |
| `jdbc`          | Communication protocol     | `jdbc`                       |
| `<subprotocol>` | Driver/DBMS identifier     | `mysql`                      |
| `<subname>`     | Database-specific location | `//localhost:3306/companydb` |

#### Example URL Formats

* **MySQL:**
  `jdbc:mysql://localhost:3306/mydatabase`

* **PostgreSQL:**
  `jdbc:postgresql://host:5432/dbname`
---

### Establishing a Connection

A `Connection` object represents the active session with a database.

#### A. Traditional Connection Handling (Manual Close)

```java
Connection conn = null;
String url = "jdbc:mysql://localhost:3306/companydb";
String user = "appuser";
String password = "secure123";

try {
    conn = DriverManager.getConnection(url, user, password);
    System.out.println("Connected successfully!");

    // Perform operations...

} catch (SQLException e) {
    e.printStackTrace();
} finally {
    if (conn != null) {
        try {
            conn.close();
        } catch (SQLException e) {
            System.err.println("Error closing connection: " + e.getMessage());
        }
    }
}
```

#### B. Try-with-Resources (AutoCloseable, Recommended)

```java
String url = "jdbc:mysql://localhost:3306/companydb";
String user = "appuser";
String password = "secure123";

try (Connection conn = DriverManager.getConnection(url, user, password)) { 
    System.out.println("Connected successfully!");

    // Perform operations...

} catch (SQLException e) {
    e.printStackTrace();
}
```

**Note:** Try-with-resources automatically closes the connection, eliminating the need for explicit `finally` blocks and reducing the risk of resource leaks.

---

### Connection Pooling

Opening a new database connection for every operation is resource-intensive and slow. **Connection pooling** maintains a pool of reusable connections to improve performance and reduce overhead. Popular pooling libraries include:

* **HikariCP** (fastest, most popular)
* **Apache DBCP** (Apache Commons)
* **C3P0** (legacy but stable)
* **Tomcat JDBC Pool**

Most enterprise applications use `DataSource`-based connection pools rather than direct `DriverManager` usage for better scalability and connection management. In frameworks like Spring Boot or Jakarta EE, built-in `DataSource` configurations often handle pooling automatically.

---

## 2. Working with JDBC Statements

JDBC provides three interfaces for executing SQL:

| Statement Type    | Interface                    | Description                    | Best Use Case           |
| ----------------- | ---------------------------- | ------------------------------ | ----------------------- |
| Statement         | `java.sql.Statement`         | Executes static SQL            | Simple, static queries  |
| PreparedStatement | `java.sql.PreparedStatement` | Parameterized SQL, precompiled | Secure DML/DQL (recommended) |
| CallableStatement | `java.sql.CallableStatement` | Calls stored procedures        | Complex database logic  |

For bulk operations, `PreparedStatement` supports batching via `addBatch()` and `executeBatch()` for improved performance.

---

### Statement vs. PreparedStatement

#### Insecure: Using Statement (Vulnerable to SQL Injection)

```java
String userId = "101";
String name = "John'; DROP TABLE employees; --";
String stmtSql = "UPDATE employees SET name = '" + name + "' WHERE id = " + userId;

try (Statement stmt = conn.createStatement()) {
    stmt.executeUpdate(stmtSql);  // SQL INJECTION VULNERABILITY!
}
```

**Problem:** The malicious input can execute arbitrary SQL commands, potentially dropping tables or exposing sensitive data.

#### Secure: Using PreparedStatement (Prevents SQL Injection)

```java
int safeUserId = 101;
String safeName = "John Doe";
String psSql = "UPDATE employees SET name = ? WHERE id = ?";

try (PreparedStatement ps = conn.prepareStatement(psSql)) {
    ps.setString(1, safeName);
    ps.setInt(2, safeUserId);
    int rows = ps.executeUpdate();
    System.out.println(rows + " row(s) updated securely.");
}
```

**Benefits of PreparedStatement:**
* Prevents SQL injection attacks
* Better performance (precompiled SQL)
* Automatic type handling and escaping

---

## 3. Scrollable and Updatable Result Sets

By default, a `ResultSet` is:

* **Forward-only** (can only move forward through results)
* **Read-only** (cannot modify data)

JDBC allows customizing **scrollability** and **concurrency** modes for more flexible data navigation and modification.

---

### Scrollability Types

| Type                      | Description                                                   |
| ------------------------- | ------------------------------------------------------------- |
| `TYPE_FORWARD_ONLY`       | Only moves forward (default, most efficient)                  |
| `TYPE_SCROLL_INSENSITIVE` | Scrolls both directions; does not reflect external DB changes |
| `TYPE_SCROLL_SENSITIVE`   | Scrolls; reflects DB changes in real-time (driver-dependent)  |

### Concurrency Types

| Type               | Description                             |
| ------------------ | --------------------------------------- |
| `CONCUR_READ_ONLY` | Cannot update data (default)            |
| `CONCUR_UPDATABLE` | Allows updating through the `ResultSet` |

---

### Scrollable & Updatable ResultSet Example

```java
String selectSql = "SELECT id, first_name, salary FROM employees WHERE department_id = 2";

try (Statement stmt = conn.createStatement(
        ResultSet.TYPE_SCROLL_INSENSITIVE,
        ResultSet.CONCUR_UPDATABLE)) {

    try (ResultSet rs = stmt.executeQuery(selectSql)) {

        // Navigate to the 2nd row
        rs.absolute(2);
        System.out.println("Row: " + rs.getRow() +
                ", Name: " + rs.getString("first_name"));

        // Update salary by 10%
        double currentSalary = rs.getDouble("salary");
        rs.updateDouble("salary", currentSalary * 1.10);
        rs.updateRow();  // Commit the update to the database

        // Move back to the first row
        rs.first();
    }
}
```

**Key Methods:**
* `absolute(int row)` - Move to specific row
* `first()`, `last()` - Navigate to first/last row
* `previous()`, `next()` - Move backward/forward
* `updateXxx()` - Update column values
* `updateRow()` - Commit changes to database

---

## 4. RowSets

A **RowSet** is a JavaBean wrapper around a `ResultSet`. It provides:

* Scrollability by default
* Event notifications (listeners)
* Optional disconnected operation

RowSets fall into two categories:

| RowSet Type    | Category     | Feature                            | Use Case       |
| -------------- | ------------ | ---------------------------------- | -------------- |
| `JdbcRowSet`   | Connected    | Always connected to database       | Desktop apps   |
| `CachedRowSet` | Disconnected | Works offline; syncs changes later | Web/multi-tier |
| `WebRowSet`    | Disconnected | Supports XML read/write            | Data exchange  |
| `FilteredRowSet` | Disconnected | Filters data without re-querying | Client-side filtering |
| `JoinRowSet`   | Disconnected | Joins multiple RowSets without SQL | Client-side joins |

---

### Example: CachedRowSet (Disconnected RowSet)

```java
import javax.sql.rowset.RowSetProvider;
import javax.sql.rowset.CachedRowSet;

String url = "jdbc:mysql://localhost:3306/companydb";
String user = "appuser";
String password = "secure123";

try (Connection conn = DriverManager.getConnection(url, user, password)) {

    CachedRowSet crs = RowSetProvider.newFactory().createCachedRowSet();
    crs.setCommand("SELECT id, first_name FROM employees");
    crs.setUrl(url);
    crs.setUsername(user);
    crs.setPassword(password);
    crs.execute();

    // Connection can now be closed - CachedRowSet works offline
    conn.close();

    // Navigate offline
    crs.absolute(1);
    System.out.println("Offline Row 1 Name: " + crs.getString("first_name"));

    // Modify data offline
    crs.updateString("first_name", "Alice (Modified Offline)");
    crs.updateRow();

    // Synchronize changes back to database
    crs.acceptChanges();
}
```
