## Table of Contents

* [Introduction to JSP](#introduction-to-jsp)
* [Key Features of JSP](#key-features-of-jsp)
* [JSP Tags](#jsp-tags)
* [JSP and Servlets](#jsp-and-servlets)
* [Java Web Frameworks](#java-web-frameworks)
* [Example: Welcome Page](#example-welcome-page)

---

## Introduction to JSP

**JavaServer Pages (JSP)** is a **server-side technology** used to create dynamic web content. It allows embedding Java code directly into HTML pages. JSP files are compiled by the **Servlet container** (e.g., Apache Tomcat) into Java Servlets on the first request or when the JSP is modified, and then executed to generate HTML.

**Purpose:**

* JSP is primarily used for the **presentation layer** in web applications.
* It separates the **view logic** from **business logic** handled by Servlets or other backend components.
* Supports rapid development for dynamic web pages.

**Advantages:**

* Easier to design dynamic pages using HTML and Java together.
* Reduces repetitive code with reusable components.
* Works seamlessly with Servlets and JavaBeans.

---

## Key Features of JSP

* **Mixing Java and HTML:** Combine server-side Java code with client-side HTML to generate dynamic content.
* **Implicit Objects:** Predefined objects available in JSP without explicit creation:

  | Object        | Description                                                |
  | ------------- | ---------------------------------------------------------- |
  | `request`     | Access HTTP request parameters and attributes              |
  | `response`    | Modify HTTP response (headers, cookies)                    |
  | `session`     | Manage user session data                                   |
  | `application` | Access ServletContext for global app data                  |
  | `out`         | Write output to response stream                            |
  | `config`      | Access ServletConfig for init parameters                   |
  | `pageContext` | Access page-scoped context (request, session, application) |
  | `page`        | Reference to current JSP page                              |
* **Tag Libraries (JSTL / Custom Tags):** Encapsulate repetitive logic into reusable tags.
* **Expression Language (EL):** Simplifies accessing JavaBeans and attributes:

  ```jsp
  ${user.name}  <!-- Retrieves 'name' property from 'user' object -->
  ```
* **JSP Standard Actions:** Predefined tags for common tasks:

  | Action Tag          | Purpose                                            |
  | ------------------- | -------------------------------------------------- |
  | `<jsp:include>`     | Include another JSP or static content dynamically  |
  | `<jsp:forward>`     | Forward request to another page                    |
  | `<jsp:param>`       | Pass parameters nested inside `<jsp:forward>` or `<jsp:include>` |
  | `<jsp:useBean>`     | Instantiate and manage JavaBeans                   |
  | `<jsp:setProperty>` | Set properties of a JavaBean                       |
  | `<jsp:getProperty>` | Retrieve properties of a JavaBean                  |
* **Separation of Concerns:** Encourages MVC architecture where Servlets handle logic and JSP handles presentation.

---

## JSP Tags

JSP tags are categorized into **scripting tags, directives, declarations, and actions**. Each serves a specific purpose in dynamic content creation.

Note: JSP scriptlets (<% %>) and declarations (<%! %>) are considered legacy and are discouraged in modern JSP development. Expression Language (EL), JSTL, and custom tags are preferred.

### 1. **Expression Tag**

Outputs a Java expression directly to the client.

```jsp
<%= "Hello World!" %>
<%= 5 + 10 %> 
```

### 2. **Scriptlet Tag**

Embeds Java code in the _jspService() method of the compiled servlet.

```jsp
<%
int counter = 5;
out.println("Counter value: " + counter);
%>
```

### 3. **Directive Tag**

Provides instructions to the JSP container. Executed at **translation time**.

* **Page Directive:** Configure page-level attributes.

  ```jsp
  <%@ page language="java" contentType="text/html;charset=UTF-8" import="java.util.*" %>
  ```

* **Include Directive:** Static inclusion of another file.

  ```jsp
  <%@ include file="header.jsp" %>
  ```

* **Taglib Directive:** Import custom or standard tag libraries.

  ```jsp
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
  ```

### 4. **Declaration Tag**

Declare instance variables or methods at the servlet class level.

```jsp
<%!
public String printWelcome() {
    return "Welcome to our site!";
}
%>

<%= printWelcome() %>
```

### 5. **Action Tag**

Predefined tags that control servlet behavior and work with JavaBeans.

* Forward requests:

```jsp
<jsp:forward page="next.jsp">
    <jsp:param name="id" value="123"/>
</jsp:forward>
```

* Include dynamic content:

```jsp
<jsp:include page="footer.jsp" />
```

* Use JavaBeans:

```jsp
<jsp:useBean id="user" class="com.example.User" scope="session" />
<jsp:setProperty name="user" property="name" value="John" />
<jsp:getProperty name="user" property="name" />
```

### 6. **Custom Tags**

Developers can create reusable JSP tags using **Tag Libraries** (`.tld`) for common tasks. Example:

```jsp
<%@ taglib uri="/WEB-INF/mytags.tld" prefix="my" %>
<my:helloWorld />
```

---

## JSP and Servlets

### Relationship:

* **Servlets:** Handle **business logic** (database interaction, request processing).
* **JSP:** Handles **presentation logic** (dynamic HTML content generation).

**Comparison Table:**

| Aspect         | Servlets                              | JSP                         |
| -------------- | ------------------------------------- | --------------------------- |
| Purpose        | Business logic and request processing | Presentation / UI rendering |
| Syntax         | Java methods (`doGet`, `doPost`)      | HTML + JSP tags             |
| Maintenance    | Harder for HTML changes               | Easier for designers        |
| Reusability    | High for logic                        | Primarily for views         |
| Learning Curve | Requires Java knowledge               | Easier for HTML developers  |

**MVC Flow Example:**

1. Client sends request → Servlet receives it.
2. Servlet processes logic and stores results in request/session.
3. Servlet forwards → JSP.
4. JSP renders HTML → response sent to client.

---

## Java Web Frameworks

Frameworks provide tools and conventions for building scalable web apps:

* **Spring / Spring Boot:** Inversion of Control, dependency injection, REST APIs.
* **JavaServer Faces (JSF):** Component-based UI, event-driven programming.
* **Apache Struts:** MVC-based, form handling, request mapping.
* **Play Framework:** Reactive, stateless, hot reload, REST support.

---

## Example: Welcome Page

### Servlet (`WelcomeServlet.java`)

```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/welcome")
public class WelcomeServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        String username = request.getParameter("user");
        if (username == null || username.trim().isEmpty()) {
            username = "Guest";
        }

        request.setAttribute("username", username);
        request.getRequestDispatcher("welcome.jsp").forward(request, response);
    }
}
```

### JSP Page (`welcome.jsp`)

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
    <title>Welcome Page</title>
</head>
<body>
    <h1>Welcome Page</h1>
    <p>Hello, ${username}!</p>

    <!-- Using JSP Scripting -->
    <%
        Integer visits = (Integer) session.getAttribute("visits");
        if (visits == null) {
            visits = 0;
        }
        visits++;
        session.setAttribute("visits", visits);
    %>

    <p>Visit count: <%= visits %></p>


    <!-- Using JSTL -->
    <c:choose>
        <c:when test="${username == 'Guest'}">
            <p>Welcome, Guest! Please log in.</p>
        </c:when>
        <c:otherwise>
            <p>Welcome back, ${username}!</p>
        </c:otherwise>
    </c:choose>
</body>
</html>
```
