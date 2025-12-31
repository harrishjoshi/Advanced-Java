# Java Servlet API

## Table of Contents

* [Overview of Servlet API Packages](#overview-of-servlet-api-packages)
* [The `javax.servlet` Package](#the-javaxservlet-package)
* [Core Classes and Interfaces (`javax.servlet`)](#core-classes-and-interfaces-javaxservlet)
* [The `javax.servlet.http` Package](#the-javaxservlethttp-package)
* [HTTP-Specific Classes and Interfaces](#http-specific-classes-and-interfaces)
* [Package Migration: `javax` → `jakarta`](#package-migration-javax--jakarta)
* [Creating Servlets: Three Approaches](#creating-servlets-three-approaches)

---

## Overview of Servlet API Packages

The **Java Servlet API** defines how Java-based web components (servlets) handle client requests and generate responses on a server.

It consists of two primary packages:

* **`javax.servlet`**
  Provides *core, protocol-independent* APIs such as lifecycle management, configuration, filtering, and request/response abstraction.

* **`javax.servlet.http`**
  Extends the core API with *HTTP-specific* functionality such as handling GET/POST requests, sessions, cookies, and headers.

> In practice, almost all web applications use **both packages**, typically by extending `HttpServlet` while relying on core features like `ServletContext` and `Filter`.

---

## The `javax.servlet` Package

This is the **foundation of the Servlet API**. It defines:

* The **servlet lifecycle** (`init`, `service`, `destroy`)
* Request and response abstractions
* Application configuration and shared context
* Filters and listeners

Although protocol-independent by design, this package is almost always used together with HTTP.

---

## Core Classes and Interfaces (`javax.servlet`)

| Component           | Type           | Purpose                                                                                                             |
| ------------------- | -------------- | ------------------------------------------------------------------------------------------------------------------- |
| `Servlet`           | Interface      | The root interface of all servlets. Defines the lifecycle methods `init()`, `service()`, and `destroy()`.           |
| `GenericServlet`    | Abstract class | A protocol-independent abstract base class that implements `Servlet` and `ServletConfig`. Subclasses must implement `service()`. Rarely used directly in HTTP-based applications; `HttpServlet` is typically used instead. | 
| `ServletConfig`     | Interface      | Provides servlet-specific initialization parameters and access to `ServletContext`.                                 |
| `ServletContext`    | Interface      | Represents the entire web application. Used for shared data, global configuration, and resource access.             |
| `ServletRequest`    | Interface      | Represents a generic client request, independent of protocol. Provides access to parameters, attributes, locale, and input stream. | 
| `ServletResponse`   | Interface      | Represents a generic server response, independent of protocol. Provides output stream/writer and content type control. |
| `RequestDispatcher` | Interface      | Forwards a request to another resource or includes its output.                                                      |
| `Filter`            | Interface      | Intercepts requests and responses for cross-cutting concerns (logging, security, compression).                      |
| `FilterChain`       | Interface      | Allows a filter to pass control to the next filter or target servlet.                                               |

### Key Notes

* **`ServletContext` is application-wide**
  Shared across all users and servlets.

  ```java
  context.setAttribute("visitorCount", count);
  ```

* **`ServletConfig` is servlet-specific**
  Each servlet has its own configuration.

  ```java
  String dbUrl = getServletConfig().getInitParameter("databaseUrl");
  ```

* **Filters run before and/or after servlets**
  Common use cases: authentication, logging, request validation.

---

## The `javax.servlet.http` Package

This package provides **HTTP-aware extensions** to the core servlet API. Since browsers communicate via HTTP, this package is used in nearly all Java web applications.

---

## HTTP-Specific Classes and Interfaces

| Component                    | Type           | Purpose                                                                                |
| ---------------------------- | -------------- | -------------------------------------------------------------------------------------- |
| `HttpServlet`                | Abstract class | Base class for HTTP servlets. Overrides `service()` to route requests to `doGet()`, `doPost()`, etc. | 
| `HttpServletRequest`         | Interface      | Adds HTTP features: headers, cookies, URL paths, query parameters, and session access. |
| `HttpServletResponse`        | Interface      | Adds HTTP response control: status codes, redirects, cookies.                          |
| `HttpSession`                | Interface      | Maintains user-specific state across multiple requests.                                |
| `Cookie`                     | Class          | Stores small pieces of client-side data.                                               |
| `HttpServletRequestWrapper`  | Class          | Wraps and modifies requests (commonly used in filters).                                |
| `HttpServletResponseWrapper` | Class          | Wraps and modifies responses (e.g., buffering output).                                 |

> **Important:**  
> The servlet container always calls the `service()` method first.  
> `HttpServlet` internally routes the request to `doGet()`, `doPost()`, etc., based on the HTTP method.

### Commonly Overridden `HttpServlet` Methods

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) { }
protected void doPost(HttpServletRequest req, HttpServletResponse resp) { }
protected void doPut(HttpServletRequest req, HttpServletResponse resp) { }
protected void doDelete(HttpServletRequest req, HttpServletResponse resp) { }
```

### Key Notes 

* **GET** → Retrieve data
* **POST** → Submit data
* **PUT / DELETE** → Common in REST APIs for updating or deleting server-side resources 
* **`HttpSession` is user-specific** (unlike `ServletContext`)

  ```java
  session.setAttribute("user", userObject);
  ```

---

## Package Migration: `javax` → `jakarta`

Java EE was transferred from Oracle to the Eclipse Foundation and renamed **Jakarta EE**. Due to trademark restrictions, all APIs were renamed from `javax.*` to `jakarta.*` starting with **Jakarta EE 9**.

### Version Mapping

| Servlet API Version         | Package Name        |
| ----------------------- | ------------------- |
| Servlet 4.0 and earlier | `javax.servlet.*`   |
| Servlet 5.0 and later   | `jakarta.servlet.*` |

### Code Comparison

```java
// Before (Java EE / Servlet 4.x)
import javax.servlet.http.HttpServlet;

// After (Jakarta EE / Servlet 5+)
import jakarta.servlet.http.HttpServlet;
```

### Annotation Example

```java
// Old
@javax.servlet.annotation.WebServlet("/hello")

// New
@jakarta.servlet.annotation.WebServlet("/hello")
```

### Server Compatibility

| Server                | Package     |
| --------------------- | ----------- |
| Tomcat 9 and earlier  | `javax.*`   |
| Tomcat 10+ (not compatible with `javax.*`), Jetty 11+ | `jakarta.*` |

---

## Creating Servlets: Three Approaches

There are three primary ways to create servlets in Java, each providing different levels of abstraction and functionality. Understanding these approaches helps us choose the right one for our needs.

### 1. Implementing the `Servlet` Interface Directly

This is the most basic and low-level approach. We implement all five methods defined in the `Servlet` interface, giving us complete control but requiring more boilerplate code.

**When to use:** Rarely used in modern applications. Only consider this approach if we need complete control over the servlet lifecycle or are building a custom, non-HTTP protocol handler.

**Key characteristics:**
- Must implement all five methods: `init(ServletConfig)`, `service()`, `getServletConfig()`, `getServletInfo()`, and `destroy()`
- Protocol-independent (works with any protocol, not just HTTP)
- Most verbose and labor-intensive approach
- We manually manage `ServletConfig` storage and retrieval

**Example:**

```java
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import java.io.*;

@WebServlet("/basic")
public class BasicServlet implements Servlet {
    
    private ServletConfig config;

    @Override
    public void init(ServletConfig config) throws ServletException {
        this.config = config;
        System.out.println("BasicServlet initialized");
    }

    @Override
    public ServletConfig getServletConfig() {
        return this.config;
    }

    @Override
    public void service(ServletRequest req, ServletResponse res)
            throws ServletException, IOException {
        res.setContentType("text/html");
        PrintWriter out = res.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello from Basic Servlet</h1>");
        out.println("<p>This servlet implements Servlet interface directly</p>");
        out.println("<p>Current time: " + new java.util.Date() + "</p>");
        out.println("</body></html>");
    }

    @Override
    public String getServletInfo() {
        return "BasicServlet - implements Servlet interface";
    }

    @Override
    public void destroy() {
        System.out.println("BasicServlet destroyed");
    }
}
```

### 2. Extending `GenericServlet` Abstract Class

This approach reduces boilerplate by providing default implementations for most `Servlet` interface methods. We only need to implement the `service()` method.

**When to use:** Useful when building protocol-independent servlets or when we need servlet functionality without HTTP-specific features. However, this is still rarely used in typical web applications.

**Key characteristics:**
- Implements `Servlet` and `ServletConfig` interfaces
- Provides default implementations for `init(ServletConfig)`, `getServletConfig()`, `getServletInfo()`, and `destroy()`
- We only need to implement the `service(ServletRequest, ServletResponse)` method
- Still protocol-independent (no HTTP-specific features)
- Convenience methods like `getInitParameter()` and `getServletContext()` are available directly

**Example:**

```java
import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import java.io.*;

@WebServlet("/generic")
public class GenericServletExample extends GenericServlet {

    @Override
    public void init() throws ServletException {
        System.out.println("GenericServlet initialized");
        // Can access init parameters directly
        String configValue = getInitParameter("configKey");
    }

    @Override
    public void service(ServletRequest req, ServletResponse res)
            throws ServletException, IOException {
        res.setContentType("text/html");
        PrintWriter out = res.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello from Generic Servlet</h1>");
        out.println("<p>This servlet extends GenericServlet</p>");
        out.println("<p>Protocol-independent implementation</p>");
        out.println("<p>Current time: " + new java.util.Date() + "</p>");
        out.println("</body></html>");
    }

    @Override
    public void destroy() {
        System.out.println("GenericServlet destroyed");
    }
}
```

### 3. Extending `HttpServlet` Abstract Class (Recommended)

This is the **most commonly used approach** for web applications. `HttpServlet` extends `GenericServlet` and adds HTTP-specific functionality, automatically routing requests to appropriate methods based on the HTTP method (GET, POST, PUT, DELETE, etc.).

**When to use:** Use this for virtually all HTTP-based web applications. This is the standard and recommended approach for modern servlet development.

**Key characteristics:**
- Extends `GenericServlet`, inheriting all its convenience methods
- HTTP-specific: works with `HttpServletRequest` and `HttpServletResponse`
- Automatically routes requests to `doGet()`, `doPost()`, `doPut()`, `doDelete()`, etc., based on HTTP method
- Override only the HTTP methods we need (typically `doGet()` and/or `doPost()`)
- Provides access to HTTP-specific features: sessions, cookies, headers, status codes
- Built-in support for HTTP methods: OPTIONS, TRACE, HEAD

**Example:**

```java
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;
import java.io.*;

@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    @Override
    public void init() {
        System.out.println("Servlet initialized");
        // Access init parameters
        String dbUrl = getInitParameter("databaseUrl");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {
        // Access HTTP-specific features
        String userAgent = req.getHeader("User-Agent");
        HttpSession session = req.getSession();
        
        resp.setContentType("text/html");
        PrintWriter out = resp.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello from the Servlet</h1>");
        out.println("<p>Current time: " + new java.util.Date() + "</p>");
        out.println("<p>Session ID: " + session.getId() + "</p>");
        out.println("</body></html>");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {
        // Handle form submissions
        String name = req.getParameter("name");
        String email = req.getParameter("email");
        
        resp.setContentType("text/html");
        PrintWriter out = resp.getWriter();
        out.println("<html><body>");
        out.println("<h1>Form Submitted!</h1>");
        out.println("<p>Name: " + name + "</p>");
        out.println("<p>Email: " + email + "</p>");
        out.println("</body></html>");
    }

    @Override
    public void destroy() {
        System.out.println("Servlet destroyed");
    }
}
```

Alternatively, we can map servlets using the traditional `web.xml` deployment descriptor:
```xml
<web-app xmlns="...">
   <!-- Servlet Declaration -->
   <servlet>
       <servlet-name>MyServlet</servlet-name>
       <servlet-class>com.example.ServletClass</servlet-class>
       <!-- Optional: Initialization Parameters -->
       <init-param>
           <param-name>configKey</param-name>
           <param-value>configValue</param-value>
       </init-param>
       <!-- Optional: Load on Startup -->
       <load-on-startup>1</load-on-startup>
   </servlet>
   
   <!-- Servlet Mapping -->
   <servlet-mapping>
       <servlet-name>MyServlet</servlet-name>
       <url-pattern>/hello</url-pattern>
   </servlet-mapping>
   
   <!-- Multiple URL Patterns -->
   <servlet-mapping>
       <servlet-name>MyServlet</servlet-name>
       <url-pattern>/greeting</url-pattern>
   </servlet-mapping>
</web-app>
```
