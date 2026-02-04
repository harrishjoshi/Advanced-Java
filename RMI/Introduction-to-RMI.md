
## Table of Contents

* [Distributed Object Model](#distributed-object-model)
* [What is RMI?](#what-is-rmi)
* [The Roles of Client and Server](#the-roles-of-client-and-server)
* [Remote Method Calls](#remote-method-calls)
* [Stubs and Skeletons](#stubs-and-skeletons)
* [The RMI Programming Model](#the-rmi-programming-model)
* [Parameters and Return Values in Remote Methods](#parameters-and-return-values-in-remote-methods)
* [Remote Object Activation](#remote-object-activation)
* [Simple RMI Example](#simple-rmi-example)
* [What is CORBA?](#what-is-corba)
* [Comparison with CORBA](#comparison-with-corba)

---

## Distributed Object Model

Java RMI follows a **distributed object model** where objects can be accessed and invoked across different Java Virtual Machines.

* **Remote Object**
  An object whose methods can be invoked from another JVM, possibly on a different machine.

* **Remote Interface**
  A Java interface that defines the methods of a remote object. It must extend `java.rmi.Remote`.

* **Remote Method Invocation (RMI)**
  The mechanism that allows invoking methods of a remote object.

* **Marshalling**
  The process of converting parameters and return values into a transmittable form.

* **Unmarshalling**
  The process of reconstructing transmitted data back into objects.

* **Stub**
  A client-side proxy object that represents the remote object.

* **Skeleton**
  A server-side dispatching component used in early RMI versions. It is obsolete in modern Java.

---

## What is RMI?

**Java RMI (Remote Method Invocation)** is a Java API that enables the development of distributed applications.

* Allows an object running in one JVM to invoke methods on an object running in another JVM.
* Uses stubs on the client side and server-side dispatch mechanisms.
* Provides location transparency while preserving object-oriented principles.
* Implemented using built-in packages such as `java.rmi`, `java.rmi.server`, and `java.rmi.registry`.

### Key Benefits

* Simplifies distributed programming by eliminating manual socket handling.
* Supports object-oriented features such as inheritance and polymorphism.
* Platform independent within the Java ecosystem.

---

## The Roles of Client and Server

Traditional distributed systems require manual design of data formats and communication protocols.

### Without RMI

* Client formats data and sends it to the server.
* Server parses data, performs computation, and sends the response back.
* Client parses and processes the response.

### With RMI

* The client invokes methods on a local stub object.
* The stub marshals parameters and sends the request over the network.
* The server unmarshals the request and invokes the actual remote object.
* The result is marshalled, transmitted back, and unmarshalled by the client stub.

### Communication Flow

1. Client calls a method on the stub.
2. Stub marshals parameters and transmits the request.
3. Server receives the request and invokes the remote object.
4. Result is returned to the client after unmarshalling.

---

## Remote Method Calls

Remote method calls allow a client to invoke methods on a remote object as if it were local.

* The client interacts only with the stub.
* The stub manages network communication, serialization, and deserialization.
* Network failures and communication issues are reported using `RemoteException`.
* Every remote method must declare `throws RemoteException`.

---

## Stubs and Skeletons

### Stub

* Acts as a proxy for the remote object on the client side.
* Implements the remote interface.
* Handles marshalling of parameters and unmarshalling of return values.
* Generated dynamically at runtime in modern Java versions.

### Skeleton

* Used in early RMI implementations to dispatch calls to server objects.
* Removed starting from Java 1.2 and replaced with dynamic method dispatch.
* No longer required or generated.

---

## The RMI Programming Model

Steps involved in building an RMI application:

1. Define a remote interface that extends `Remote` and declares `RemoteException`.
2. Implement the remote interface.
3. Export the remote object using `UnicastRemoteObject` or `UnicastRemoteObject.exportObject`.
4. Create the server program and bind the object to the RMI registry using `Naming.rebind`.
5. Create the client program and obtain the remote object reference using `Naming.lookup`.
6. Invoke remote methods through the obtained reference.

---

## Parameters and Return Values in Remote Methods

* Primitive data types are passed by value.
* Objects that implement `Serializable` are passed by value through object serialization.
* Objects that implement `Remote` are passed by reference in the form of a stub.
* Large serialized objects can negatively impact performance.
* Remote methods must declare `RemoteException`.
* Application-specific checked exceptions must also be declared.

---

## Remote Object Activation

Remote Object Activation was a mechanism for creating remote objects on demand.

* Implemented using `java.rmi.activation.Activatable` and the `rmid` daemon.
* Allowed remote objects to be activated only when invoked.
* Deprecated for removal in Java 15 and completely removed in Java 17. 

### Modern Alternatives

* Dependency injection frameworks
* Application servers and containers
* Microservices with lazy initialization

---

## Simple RMI Example

### Remote Interface

```java
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface Hello extends Remote {
    String sayHello() throws RemoteException;
}
```

### Implementation

```java
import java.rmi.RemoteException;
import java.rmi.server.UnicastRemoteObject;

public class HelloImpl extends UnicastRemoteObject implements Hello {

    protected HelloImpl() throws RemoteException {
        super();
    }

    @Override
    public String sayHello() throws RemoteException {
        return "Hello from RMI Server!";
    }
}
```

### Server

```java
import java.rmi.Naming;
import java.rmi.registry.LocateRegistry;

public class HelloServer {

    public static void main(String[] args) {
        try {
            Hello obj = new HelloImpl();
            LocateRegistry.createRegistry(1099);
            Naming.rebind("HelloService", obj);
            System.out.println("Server ready");
        } catch (Exception e) {
            System.err.println("Server exception: " + e);
        }
    }
}
```

### Client

```java
import java.rmi.Naming;

public class HelloClient {

    public static void main(String[] args) {
        try {
            Hello service = (Hello) Naming.lookup("rmi://localhost/HelloService");
            System.out.println(service.sayHello());
        } catch (Exception e) {
            System.err.println("Client exception: " + e);
        }
    }
}
```

---

## What is CORBA?

**Common Object Request Broker Architecture (CORBA)** is a distributed object middleware standard defined by the Object Management Group.

* Enables communication between distributed objects written in different programming languages.
* Uses an Object Request Broker to locate objects and route method calls.
* Interfaces are defined using Interface Definition Language.
* Language-specific stubs and skeletons are generated from IDL.
* Uses Internet Inter-ORB Protocol for network communication.
* Supports naming, transactions, events, and security services.

---

## Comparison with CORBA

| Aspect               | Java RMI                            | CORBA                                |
| -------------------- | ----------------------------------- | ------------------------------------ |
| Language Support     | Java only                           | Multi-language                       |
| Protocol             | JRMP, RMI-IIOP                      | IIOP                                 |
| Interface Definition | Java interfaces                     | IDL                                  |
| Middleware           | Built into JDK                      | External ORB                         |
| Ease of Use          | Simple for Java developers          | Steeper learning curve               |
| Interoperability     | Limited to Java or IIOP             | Strong cross-platform support        |
| Parameter Passing    | Pass by value and reference         | Pass by value with object references |
| Modern Status        | Still supported, activation removed | Largely legacy                       |
