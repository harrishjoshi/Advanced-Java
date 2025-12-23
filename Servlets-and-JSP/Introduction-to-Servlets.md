
# Web Programming with Servlets

## Table of Contents

* [Server-Side Web Programming](#server-side-web-programming)
* [Introduction to CGI](#introduction-to-cgi)
* [Background of Java Servlets](#background-of-java-servlets)
* [Java Servlets Overview](#java-servlets-overview)
* [Comparison with CGI](#comparison-with-cgi)
* [The Servlet Container](#the-servlet-container)
* [Servlet Lifecycle](#servlet-lifecycle)
* [Simple Servlet Example](#simple-servlet-example)

---

## Server-Side Web Programming

Server-side web programming refers to executing application logic on the web server rather than in the user’s browser. The browser sends an HTTP request, the server processes it, and returns a dynamically generated response such as HTML.

Server-side code typically handles authentication, database operations, business logic, and session management.

**Key Benefits**

* Dynamic content generation
* Improved security since logic and data remain on the server
* Ability to maintain user sessions across multiple requests

**Server-Side vs. Client-Side**

| Aspect             | Server-Side            | Client-Side           |
| ------------------ | ---------------------- | --------------------- |
| Execution Location | Web server             | User’s browser        |
| Security           | Higher                 | Lower                 |
| Processing Power   | Server resources       | Client device         |
| Examples           | Servlets, PHP, ASP .NET | JavaScript, HTML, CSS |

---

## Introduction to CGI

CGI (Common Gateway Interface) is an early standard for generating dynamic web content. For each request, the server creates a new process, executes a script, and returns the output to the client.

CGI is simple and flexible but inefficient for applications with many users.

**Advantages**

* Language-independent
* Simple programming model

**Limitations**

* High overhead due to process creation
* Poor scalability
* High memory usage
* No built-in session support

---

## Background of Java Servlets

Java Servlets were introduced by Sun Microsystems in 1997 to address the performance and scalability limitations of CGI.

Instead of creating a new process for each request, servlets use a single instance and handle multiple requests using threads. This design significantly improves efficiency and scalability.

**Design Goals**

* Reduce request-handling overhead
* Support high concurrency
* Provide built-in lifecycle, session, and security management

---

## Java Servlets Overview

A Java Servlet is a server-side Java class that extends `HttpServlet` and runs within a servlet container such as Apache Tomcat. Servlets receive HTTP requests, process them, and generate HTTP responses.

Servlets are commonly used for tasks such as login processing, form handling, and database interaction.

**Key Characteristics**

* Container-managed lifecycle
* Multi-threaded request handling
* URL-based request mapping
* Platform independence

**API Hierarchy**

```
java.lang.Object
  └─ GenericServlet
       └─ HttpServlet
            └─ Custom Servlet
```

---

## Comparison with CGI

| Aspect          | CGI                     | Java Servlets            |
| --------------- | ----------------------- | ------------------------ |
| Execution Model | One process per request | Single instance, threads |
| Performance     | Slow                    | Fast                     |
| Scalability     | Limited                 | High                     |
| Session Support | Manual                  | Built-in                 |
| Resource Usage  | High                    | Low                      |

---

## The Servlet Container

A servlet container provides the runtime environment required to execute servlets. It manages infrastructure concerns so developers can focus on application logic.

**Responsibilities**

* Loading and initializing servlets
* Routing HTTP requests to servlets
* Managing threads and concurrency
* Handling sessions and security

**Common Containers**

* Apache Tomcat
* Jetty
* Enterprise application servers

---

## Servlet Lifecycle

The servlet lifecycle defines the sequence of events from servlet creation to removal. The lifecycle is fully managed by the servlet container. Only one servlet instance is created, and it serves multiple client requests concurrently using threads.

### Lifecycle Process Flow

1. **Client Request Arrival**
   A client sends an HTTP request to the web server. The servlet container identifies the appropriate servlet based on URL mapping.

2. **Loading and Instantiation**
   If the servlet is not already loaded, the container loads the servlet class and creates a single instance using the no-argument constructor. This may occur at application startup or on the first request.

3. **Initialization (`init()`)**
   After instantiation, the container calls the `init()` method exactly once.
   This method is used to perform one-time initialization tasks such as:

   * Establishing database connections
   * Reading configuration parameters
   * Initializing shared resources

   If `init()` fails, the servlet is not placed into service.

4. **Request Handling (`service()` method)**
   For every client request, the container calls the `service()` method.
   In the case of `HttpServlet`, `service()` automatically dispatches the request to methods such as `doGet()`, `doPost()`, `doPut()`, or `doDelete()` based on the HTTP request type.

   Multiple requests are handled simultaneously using separate threads. Therefore:

   * Instance variables must be used carefully
   * Code must be thread-safe

5. **Response Generation**
   The servlet processes the request and generates a response, typically HTML, JSON, or XML, which is sent back to the client.

6. **Destruction (`destroy()`)**
   When the application is stopped or the servlet is removed, the container calls the `destroy()` method exactly once.
   This method is used to:

   * Close database connections
   * Release resources
   * Perform cleanup tasks

After `destroy()` completes, the servlet instance becomes eligible for garbage collection.

**Summary of Lifecycle Methods**

* `init()` → Called once
* `service()` / `doGet()` / `doPost()` → Called for each request
* `destroy()` → Called once

---

## Simple Servlet Example

```java
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;
import java.io.*;

@WebServlet("/hello")
public class ServletClass extends HttpServlet {

    @Override
    public void init() {
        System.out.println("Servlet initialized");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {
        resp.setContentType("text/html");
        PrintWriter out = resp.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello from the Servlet</h1>");
        out.println("<p>Current time: " + new java.util.Date() + "</p>");
        out.println("</body></html>");
    }

    @Override
    public void destroy() {
        System.out.println("Servlet destroyed");
    }
}
```
