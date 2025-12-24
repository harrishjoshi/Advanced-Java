# Java Servlet API

## Table of Contents

* [Overview of Servlet API Packages](#overview-of-servlet-api-packages)
* [The `javax.servlet` Package](#the-javaxservlet-package)
* [Core Classes and Interfaces (`javax.servlet`)](#core-classes-and-interfaces-javaxservlet)
* [The `javax.servlet.http` Package](#the-javaxservlethttp-package)
* [HTTP-Specific Classes and Interfaces](#http-specific-classes-and-interfaces)
* [Package Migration: `javax` → `jakarta`](#package-migration-javax--jakarta)

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
