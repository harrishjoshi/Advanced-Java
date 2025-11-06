# Table of Contents

- [Database Programming with JDBC](#database-programming-with-jdbc)
  - [What is Database Programming?](#what-is-database-programming)
  - [The Role of JDBC in Java Applications](#the-role-of-jdbc-in-java-applications)
  - [Understanding the JDBC Architecture](#understanding-the-jdbc-architecture)
  - [JDBC Driver Types](#jdbc-driver-types)
    - [Type 1: JDBC-ODBC Bridge Driver](#type-1-jdbc-odbc-bridge-driver)
    - [Type 2: Native-API Driver (Partly Java)](#type-2-native-api-driver-partly-java)
    - [Type 3: Network Protocol Driver (Pure Java)](#type-3-network-protocol-driver-pure-java)
    - [Type 4: Thin Driver (Pure Java)](#type-4-thin-driver-pure-java)
  - [Typical Uses of JDBC](#typical-uses-of-jdbc)
    - [Executing SQL Queries and Updates](#executing-sql-queries-and-updates)
    - [Working with Result Sets](#working-with-result-sets)
    - [Connection Management and Transactions](#connection-management-and-transactions)
    - [Coding Example: A Simple JDBC Application](#coding-example-a-simple-jdbc-application)
  - [Summary](#summary)

## **Database Programming with JDBC**

## **What is Database Programming?**

**Database programming** refers to the process of designing, implementing, and managing systems that store, retrieve, update, and manipulate data using a **Database Management System (DBMS)**. It involves writing code to interact with databases through **SQL (Structured Query Language)** or higher-level abstractions.

Unlike file-based storage, database programming ensures:
- **Data integrity** (via constraints and transactions)
- **Concurrent access** (multiple users at once)
- **Efficient querying** (using indexes and optimized engines)
- **Security and recovery** (backups, access control)

In Java, **JDBC (Java Database Connectivity)** is the standard API for database programming, enabling seamless integration between Java applications and relational databases.

---

## **The Role of JDBC in Java Applications**

JDBC is the cornerstone of database programming in Java. It allows developers to:
- Connect to databases (local or remote)
- Execute SQL statements
- Retrieve and process results
- Manage transactions and connections

Used in:
- **Web applications** (Spring Boot, Jakarta EE)
- **Enterprise systems** (banking, inventory, CRM)
- **Desktop tools** (data editors, reporting apps)
- **Microservices** (with connection pooling)

JDBC ensures **database independence** — the same Java code works with MySQL, PostgreSQL, Oracle, or SQL Server by switching only the driver.

---

## **Understanding the JDBC Architecture**

JDBC follows a **layered, driver-based architecture**:

1. **Application** → Calls JDBC API
2. **JDBC API** (`java.sql`, `javax.sql`) → Standard interfaces
3. **JDBC Driver** → Database-specific implementation
4. **Database** → Executes native SQL over network or locally

```
[Java App] → [JDBC API] → [JDBC Driver] → [Database]
```

The `DriverManager` or `DataSource` handles connection lifecycle.

---

## **JDBC Driver Types**

JDBC supports **four driver types**, each balancing portability, performance, and complexity.

| Type | Name                          | Pure Java | Platform Independent | Performance | Use Case                              |
|------|-------------------------------|-----------|----------------------|-------------|---------------------------------------|
| 1    | JDBC-ODBC Bridge              | No        | No                   | Low         | Legacy, prototyping                   |
| 2    | Native-API (Partly Java)      | Partial   | No                   | High        | High-performance desktop apps         |
| 3    | Network Protocol (Middleware) | Yes       | Yes                  | Medium      | Applets, distributed systems          |
| 4    | Thin Driver (Pure Java)       | Yes       | Yes                  | High        | **Modern apps (recommended)**         |

### **Type 1: JDBC-ODBC Bridge Driver**
- Uses Microsoft’s ODBC as a bridge.
- Requires ODBC setup on client.
- **Deprecated** (removed in Java 8+).
- Rarely used today.

### **Type 2: Native-API Driver (Partly Java)**
- Calls native database libraries (e.g., OCI for Oracle).
- Uses JNI and native `.dll`/`.so` files.
- Fast but **not portable**.
- Common in legacy client-server apps.

### **Type 3: Network Protocol Driver (Pure Java)**
- Client → Middleware → Database.
- Middleware translates generic protocol to DB-specific.
- Enables thin clients and firewall traversal.
- Adds latency; less common now.

### **Type 4: Thin Driver (Pure Java)**
- Direct socket communication with database.
- **No native code**, no middleware.
- **Most popular** — used in 99% of modern Java apps.
- Examples: `com.mysql.cj.jdbc.Driver`, `org.postgresql.Driver`

---

## **Typical Uses of JDBC**

### **Executing SQL Queries and Updates**

```java
String sql = "UPDATE employees SET salary = ? WHERE id = ?";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setDouble(1, 75000.0);
    stmt.setInt(2, 101);
    int rows = stmt.executeUpdate();
    System.out.println(rows + " row(s) updated.");
}
```

### **Working with Result Sets**

```java
try (Statement stmt = conn.createStatement();
     ResultSet rs = stmt.executeQuery("SELECT name, email FROM users")) {
     
    while (rs.next()) {
        System.out.println(rs.getString("name") + " <" + rs.getString("email") + ">");
    }
}
```

### **Connection Management and Transactions**

```java
try (Connection conn = DriverManager.getConnection(url, user, pass)) {
    conn.setAutoCommit(false);
    
    // Multiple operations
    // ...
    
    conn.commit();
} catch (SQLException e) {
    if (conn != null) conn.rollback();
    throw e;
}
```

### **Coding Example: A Simple JDBC Application**

```java
import java.sql.*;

public class DatabaseDemo {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/companydb";
        String user = "appuser";
        String password = "secure123";

        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            // Insert employee
            String insert = "INSERT INTO employees (name, department) VALUES (?, ?)";
            try (PreparedStatement ps = conn.prepareStatement(insert)) {
                ps.setString(1, "Bob the Builder");
                ps.setString(2, "Engineering");
                ps.executeUpdate();
            }

            // Query employees
            String select = "SELECT * FROM employees WHERE department = ?";
            try (PreparedStatement ps = conn.prepareStatement(select)) {
                ps.setString(1, "Engineering");
                try (ResultSet rs = ps.executeQuery()) {
                    while (rs.next()) {
                        System.out.printf("ID: %d, Name: %s, Dept: %s%n",
                            rs.getInt("id"),
                            rs.getString("name"),
                            rs.getString("department"));
                    }
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

---

## **Summary**

**Database programming** is the backbone of data-driven applications. **JDBC** empowers Java developers to interact with databases efficiently and securely.

- **Four JDBC driver types** cater to different needs; **Type 4 (Thin Driver)** dominates modern development.
- Use **`PreparedStatement`** to prevent **SQL injection**.
- Always close resources with **`try-with-resources`**.
- **JDBC is the foundation** for Java ORM frameworks like **Hibernate**, **JPA**, and **MyBatis**.
