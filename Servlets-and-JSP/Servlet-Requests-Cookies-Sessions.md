# Servlet: HTTP Requests, Parameters, Cookies, and Sessions

## What is a Servlet?

A **Servlet** is a server-side Java class that:
- Receives HTTP requests from clients (browsers, apps, other systems)
- Processes the requests
- Sends HTTP responses back to the client

Servlets handle dynamic web content and form the foundation of Java web applications.

---

## Basic Servlet Structure

A servlet extends `HttpServlet` and uses `@WebServlet` to map to a URL:

```java
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/demo")
public class DemoServlet extends HttpServlet {
    // Your code goes here
}
```

The `@WebServlet("/demo")` annotation maps this servlet to the URL path. When someone visits `http://yourserver/demo`, this servlet handles the request.

---

## 1. Handling HTTP Requests and Responses

The most common methods are:

* `doGet()` → handles browser requests (URL access, link clicks)
* `doPost()` → handles form submissions

### Simple GET Example

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp)
        throws IOException {

    resp.setContentType("text/html;charset=UTF-8");
    PrintWriter out = resp.getWriter();

    out.println("<html><body>");
    out.println("<h1>Hello from Servlet</h1>");
    out.println("<p>Request Method: " + req.getMethod() + "</p>");
    out.println("</body></html>");
}
```

Key components:

* `HttpServletRequest req` → contains data coming **from the client**
* `HttpServletResponse resp` → sends data **to the client**
* `setContentType()` tells the browser how to interpret the response (here: HTML)
* `PrintWriter` writes the HTML content to send back

When we override `doGet()`, we define what happens when someone makes a GET request to your servlet.

---

## 2. Reading Request Parameters (Form Data)

Request parameters come from:

* HTML forms
* Query strings in URLs (`?name=value&age=25`)

### HTML Form Example

```html
<form action="demo" method="post">
    Username: <input type="text" name="username"><br>
    Age: <input type="number" name="age"><br>
    <input type="submit" value="Submit">
</form>
```

The `action="demo"` points to our servlet URL, and `method="post"` specifies the HTTP method.

### POST Handling Example

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp)
        throws IOException {

    req.setCharacterEncoding("UTF-8");

    String username = req.getParameter("username");
    String age = req.getParameter("age");

    resp.setContentType("text/html;charset=UTF-8");
    PrintWriter out = resp.getWriter();

    out.println("<html><body>");
    out.println("<h1>Form Result</h1>");
    out.println("<p>Username: " + username + "</p>");
    out.println("<p>Age: " + age + "</p>");
    out.println("</body></html>");
}
```

The flow:
1. `setCharacterEncoding("UTF-8")` ensures special characters are handled correctly
2. `req.getParameter("username")` retrieves the value from the form field named "username"
3. The parameter name must match the `name` attribute in your HTML input

**Note:** `getParameter()` returns `null` if the parameter doesn't exist.

---

## 3. Servlet Initialization Parameters

Initialization parameters are configuration values set when the servlet starts. Use them for constants and configuration, not user data.

### Annotation Configuration

```java
@WebServlet(
    urlPatterns = "/demo",
    initParams = {
        @WebInitParam(name = "appName", value = "Demo Application"),
        @WebInitParam(name = "maxUsers", value = "100")
    }
)
```

### Reading the Parameters

```java
@Override
public void init() {
    String appName = getInitParameter("appName");
    String maxUsers = getInitParameter("maxUsers");
    System.out.println("Starting: " + appName);
    System.out.println("Max users: " + maxUsers);
}
```

The `init()` method runs **once** when the servlet first loads, not on every request. Use init parameters for:
- Application name
- Database connection settings  
- File paths
- Configuration constants

---

## 4. Using Cookies (Client-Side Storage)

Cookies store small pieces of data in the user's browser. The browser automatically sends cookies with every request to the same domain.

### Simple Visit Counter

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp)
        throws IOException {

    int count = 1;
    Cookie[] cookies = req.getCookies();

    if (cookies != null) {
        for (Cookie c : cookies) {
            if ("count".equals(c.getName())) {
                count = Integer.parseInt(c.getValue()) + 1;
            }
        }
    }

    Cookie cookie = new Cookie("count", String.valueOf(count));
    cookie.setMaxAge(60 * 60 * 24); // expires in 1 day
    cookie.setPath("/");  // Available to entire application
    resp.addCookie(cookie);

    resp.setContentType("text/html");
    PrintWriter out = resp.getWriter();
    out.println("<h1>Visit Count: " + count + "</h1>");
}
```

Cookie characteristics:
* Stored on client's computer (in browser)
* Limited size (~4KB typically)
* Can have expiration time (or be session-only)
* Automatically sent with requests to same domain
* Users can view and delete them

**Common uses:** User preferences (theme, language), tracking visits, storing non-sensitive data

---

## 5. Using HttpSession (Server-Side Storage)

Sessions store data **on the server** for each user. The browser only stores a session ID (in a cookie named `JSESSIONID`).

### Session Example

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp)
        throws IOException {

    HttpSession session = req.getSession();

    Integer visits = (Integer) session.getAttribute("visits");
    if (visits == null) visits = 0;
    visits++;
    session.setAttribute("visits", visits);

    resp.setContentType("text/html");
    PrintWriter out = resp.getWriter();
    out.println("<h1>Session Visits: " + visits + "</h1>");
}
```
Session characteristics:
* Data stored on server (more secure than cookies)
* Each user gets unique session ID
* Expires after inactivity (default ~30 minutes)
* Can store any Java object (not just strings)
* More secure for sensitive data

**Common uses:** Login status, shopping cart contents, user preferences, sensitive data

### Additional Session Methods

```java
// Get session only if exists (doesn't create new one)
HttpSession session = req.getSession(false);

// Remove specific attribute
session.removeAttribute("visits");

// End session completely (logout)
session.invalidate();

// Set timeout in seconds
session.setMaxInactiveInterval(1800); // 30 minutes

// Get session ID
String sessionId = session.getId();
```

---

## Cookies vs Sessions: When to Use What?

| Feature | Cookies | Sessions |
|---------|---------|----------|
| Storage Location | Client (browser) | Server |
| Security | Less secure (visible to user) | More secure |
| Size Limit | ~4KB | Limited by server memory |
| Data Types | Strings only | Any Java object |
| Expiration | Set by developer | Inactivity timeout |
| Best For | Preferences, themes | Login state, sensitive data |

**Guidelines:**
- Use **cookies** for simple preferences that aren't sensitive
- Use **sessions** for user-specific data, especially login information and sensitive content

---

## Complete Working Example

This servlet demonstrates all concepts together:

```java
import javax.servlet.annotation.WebServlet;
import javax.servlet.annotation.WebInitParam;
import javax.servlet.http.*;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet(
    urlPatterns = "/complete",
    initParams = {
        @WebInitParam(name = "appName", value = "Complete Demo")
    }
)
public class CompleteServlet extends HttpServlet {
    
    private String appName;
    
    @Override
    public void init() {
        appName = getInitParameter("appName");
        System.out.println("Servlet initialized: " + appName);
    }
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {
        
        // Handle cookies
        int cookieCount = 1;
        Cookie[] cookies = req.getCookies();
        if (cookies != null) {
            for (Cookie c : cookies) {
                if ("pageViews".equals(c.getName())) {
                    cookieCount = Integer.parseInt(c.getValue()) + 1;
                }
            }
        }
        Cookie viewCookie = new Cookie("pageViews", String.valueOf(cookieCount));
        viewCookie.setMaxAge(60 * 60 * 24);
        resp.addCookie(viewCookie);
        
        // Handle session
        HttpSession session = req.getSession();
        Integer sessionVisits = (Integer) session.getAttribute("sessionVisits");
        if (sessionVisits == null) sessionVisits = 0;
        sessionVisits++;
        session.setAttribute("sessionVisits", sessionVisits);
        
        // Send response
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter out = resp.getWriter();
        
        out.println("<html><body>");
        out.println("<h1>" + appName + "</h1>");
        out.println("<p>Cookie Page Views: " + cookieCount + "</p>");
        out.println("<p>Session Visits: " + sessionVisits + "</p>");
        out.println("<p>Session ID: " + session.getId() + "</p>");
        out.println("<hr>");
        out.println("<form method='post'>");
        out.println("Name: <input type='text' name='name'><br>");
        out.println("<input type='submit' value='Submit'>");
        out.println("</form>");
        out.println("</body></html>");
    }
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
            throws IOException {
        
        req.setCharacterEncoding("UTF-8");
        String name = req.getParameter("name");
        
        HttpSession session = req.getSession();
        session.setAttribute("userName", name);
        
        resp.setContentType("text/html;charset=UTF-8");
        PrintWriter out = resp.getWriter();
        
        out.println("<html><body>");
        out.println("<h1>Welcome, " + name + "!</h1>");
        out.println("<p>Your name has been saved in the session.</p>");
        out.println("<a href='complete'>Go back</a>");
        out.println("</body></html>");
    }
}
```

---

## Quick Reference

### Request Methods
```java
String value = req.getParameter("fieldName");    // Get form/URL parameter
Cookie[] cookies = req.getCookies();             // Get all cookies
HttpSession session = req.getSession();          // Get/create session
String method = req.getMethod();                 // GET, POST, etc.
```

### Response Methods
```java
resp.setContentType("text/html");                // Set response type
resp.addCookie(cookie);                          // Add cookie
PrintWriter out = resp.getWriter();              // Get output writer
resp.sendRedirect("page.jsp");                   // Redirect user
```

### Cookie Operations
```java
Cookie c = new Cookie("name", "value");          // Create cookie
c.setMaxAge(seconds);                            // Set expiration
c.setPath("/");                                  // Set cookie path
resp.addCookie(c);                               // Send to client
```

### Session Operations
```java
session.setAttribute("key", value);              // Store data
Object value = session.getAttribute("key");      // Retrieve data
session.removeAttribute("key");                  // Remove specific item
session.invalidate();                            // End session
session.setMaxInactiveInterval(seconds);         // Set timeout
String id = session.getId();                     // Get session ID
```
