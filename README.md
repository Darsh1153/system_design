# System Design

## Vertical Scaling (Scale Up)

### Definition

**Vertical Scaling** (also known as **Scale Up**) is the process of increasing the hardware resources of a **single server** to improve its performance and handle more traffic.

Instead of adding more servers, you upgrade the existing server by increasing resources such as:

- CPU
- RAM
- Storage

This allows the same machine to process more requests and support higher workloads.

---

## Example

Suppose an Amazon application is initially running on a server with:

- **2 CPU**
- **4 GB RAM**

As the number of users increases, instead of adding another server, the existing server is upgraded to:

- **6 CPU**
- **16 GB RAM**

Only the hardware capacity of the same server changes.

```text
Clients
      👤      👤      👤      👤      👤
        \      |      |      |      /
         \     |      |     /
          \    |      |    /
           +--------------------+
           |   Amazon Server    |
           |    2 CPU           |
           |    4 GB RAM        |
           +--------------------+
                    |
                    | Upgrade Hardware
                    v
           +--------------------+
           |    Same Server     |
           |    6 CPU           |
           |    16 GB RAM       |
           +--------------------+
```

---

## Advantages

- ✅ Easy to implement
- ✅ No application code changes required
- ✅ Simple architecture
- ✅ No need to configure load balancing

---

## Disadvantages

- ❌ Hardware has a maximum limit
- ❌ Single Point of Failure (SPOF)
- ❌ Scaling can become expensive
- ❌ Downtime may be required during hardware upgrades

---

## Real-World Example

An e-commerce website begins receiving more traffic.

Instead of deploying additional servers:

**Before**

- 2 CPU
- 4 GB RAM

↓

**After Upgrade**

- 6 CPU
- 16 GB RAM

The same server is now capable of handling more users.

---

# Horizontal Scaling (Scale Out)

## Definition

**Horizontal Scaling** (also known as **Scale Out**) is the process of adding **multiple servers (instances)** instead of upgrading a single server.

Incoming traffic is distributed among these servers using a **Load Balancer**.

This approach allows applications to serve a much larger number of users while improving reliability and fault tolerance.

---

## Architecture

```text
                     DNS
                      |
                      v
              +----------------+
              | Load Balancer  |
              +----------------+
                /   |   |    \
               /    |   |     \
              v     v   v      v

        +--------+ +--------+ +--------+ +--------+
        |Server1 | |Server2 | |Server3 | |Server4 |
        |2 CPU   | |2 CPU   | |2 CPU   | |2 CPU   |
        |4 GB RAM| |4 GB RAM| |4 GB RAM| |4 GB RAM|
        +--------+ +--------+ +--------+ +--------+

                ^      ^      ^      ^
                |      |      |      |
        ---------------------------------------
        |    |     |     |     |      |       |
       C1   C2    C3    C4    C5    ...     Cn
                 (Clients)
```

---

## How Horizontal Scaling Works

1. A client sends a request to your application.
2. DNS resolves the domain name to the Load Balancer.
3. The Load Balancer receives all incoming requests.
4. It distributes requests among multiple servers.
5. Each server processes a portion of the traffic.

Since multiple servers share the workload, the application can support significantly more users.

---

# Load Balancer

## Definition

A **Load Balancer** is a component that distributes incoming requests across multiple servers.

Its primary responsibilities are:

- Prevent any single server from becoming overloaded.
- Improve application availability.
- Increase scalability.
- Improve fault tolerance by routing traffic away from unhealthy servers.

---

## Round Robin Algorithm

One of the simplest load balancing algorithms is **Round Robin**.

It distributes requests sequentially across all available servers.

```text
Request 1  → Server 1
Request 2  → Server 2
Request 3  → Server 3
Request 4  → Server 4
Request 5  → Server 1
Request 6  → Server 2
...
```

Each server receives requests in turn, ensuring an even distribution when all servers have similar capacity.

---

## Real-World Example

Suppose an e-commerce application experiences a massive increase in traffic.

Instead of upgrading a single server, multiple application servers are deployed.

```text
Users
   |
   v
Load Balancer
   |
-----------------------------------
|        |         |         |
S1       S2        S3        S4
```

The Load Balancer distributes incoming requests across all available servers, allowing the application to efficiently handle millions of users.

---

# Vertical Scaling vs Horizontal Scaling

| Feature | Vertical Scaling (Scale Up) | Horizontal Scaling (Scale Out) |
|----------|-----------------------------|--------------------------------|
| Method | Upgrade existing server | Add more servers |
| Hardware | Increase CPU, RAM, Storage | Add additional machines |
| Cost | Can become expensive | More cost-effective at scale |
| Maximum Limit | Limited by hardware | Practically unlimited |
| Single Point of Failure | Yes | No (with proper redundancy) |
| Performance | Better for moderate growth | Better for massive growth |
| Downtime | May require downtime | Usually little to no downtime |
| Complexity | Simple | More complex (requires Load Balancer) |

---

# Key Takeaways

- **Vertical Scaling** increases the capacity of a single server by upgrading its hardware.
- **Horizontal Scaling** increases capacity by adding more servers.
- A **Load Balancer** distributes requests across multiple servers.
- **Round Robin** is one of the simplest load balancing algorithms.
- Modern large-scale applications such as **Amazon, Netflix, Google, and Facebook** primarily rely on **Horizontal Scaling** because it provides better scalability, reliability, and fault tolerance.

# Serverless:

- Serverless is a cloud computing model where developers focus on writing code while the cloud provider manages the underlying infrastructure, scaling, and maintenance. You pay based on actual execution rather than keeping servers running continuously.

# AWS Lambda:

- AWS Lambda is a serverless compute service that executes your function in response to events such as HTTP requests, file uploads, or scheduled tasks. It automatically scales and charges only for the compute time used.


# Singleton Design Pattern

## What is a Singleton?

A **Singleton** is a design pattern that ensures **only one object of a class is created** throughout the application and provides a **global way to access that object**.

### Think of it like this

Imagine an office has only **one printer**.

Every employee uses the same printer.

```text
Employee A ----\
Employee B -----\
Employee C -------> Printer
Employee D -----/
```

No employee buys a new printer every time they need to print.

Similarly, in Java, sometimes we need only **one object** for the entire application.

Examples:

- Database Connection Manager
- Logger
- Configuration Manager
- Cache Manager
- Thread Pool

---

# Why can't we use a normal class?

Suppose we have:

```java
class Database {

}
```

Now anyone can create objects.

```java
Database d1 = new Database();
Database d2 = new Database();
Database d3 = new Database();
```

Memory

```text
Heap

Database Object 1

Database Object 2

Database Object 3
```

Sometimes this is wasteful.

For example, if this class manages the database connection, we don't want multiple database managers.

We want **only one**.

---

# How does Singleton solve this?

It follows three simple rules.

## Rule 1: Make the constructor private

```java
private Database() {
    System.out.println("Database created.");
}
```

### Why?

Normally we create objects like this:

```java
new Database();
```

But if the constructor is private, nobody outside the class can create an object.

For example:

```java
Database d = new Database();
```

Compilation Error:

```text
Database() has private access
```

Now the obvious question is:

> If nobody can use `new`, then who creates the object?

The answer is:

> **The class itself creates the object.**

A class can access its own private members.

---

## Rule 2: Create one static object

```java
private static Database obj = new Database();
```

This line creates **the only Database object**.

Notice something interesting.

Even though the constructor is private, this line works because it is inside the same class.

Memory now looks like this:

```text
Heap

+----------------+
| Database Object|
+----------------+
```

Only one object exists.

### Why is it static?

Because static members belong to the **class**, not to individual objects.

There is only one copy of a static variable.

If it wasn't static, every Database object would have its own copy, which defeats the purpose.

---

## Rule 3: Provide a public method to access the object

```java
public static Database getInstance() {
    return obj;
}
```

Since nobody can use

```java
new Database();
```

we provide another way to get the object.

Whenever someone needs the Database object, they call:

```java
Database.getInstance();
```

This method simply returns the already existing object.

No new object is created.

---

# Complete Code

```java
class Database {

    private Database() {
        System.out.println("Database created.");
    }

    private static Database obj = new Database();

    public static Database getInstance() {
        return obj;
    }
}
```

---

# Using the Singleton

```java
class Main {

    public static void main(String[] args) {

        Database obj1 = Database.getInstance();
        Database obj2 = Database.getInstance();

        System.out.println(obj1);
        System.out.println(obj2);
    }
}
```

---

# What happens step by step?

### Step 1

When the `Database` class is loaded, Java executes:

```java
private static Database obj = new Database();
```

Output:

```text
Database created.
```

At this point, the object already exists.

---

### Step 2

```java
Database obj1 = Database.getInstance();
```

Internally,

```java
getInstance()
```

returns

```java
return obj;
```

So `obj1` points to the Database object.

```text
obj1
  |
  ▼

Database Object
```

---

### Step 3

```java
Database obj2 = Database.getInstance();
```

Again,

```java
return obj;
```

Now:

```text
obj1 --------\
              \
obj2 ---------> Database Object
```

Notice:

No second object is created.

Both variables point to the **same object**.

---

# Output

```text
Database created.
Database@5acf9800
Database@5acf9800
```

Both addresses are identical.

This proves that:

```java
obj1 == obj2
```

returns

```text
true
```

because they both reference the same object.

---

# Memory Diagram

```text
Stack

obj1 -----------\
                 \
obj2 -------------> Database Object
                 /
static obj ------/
```

Three references, but **only one object** exists in memory.

---

# Why is `getInstance()` static?

Imagine it wasn't static.

```java
public Database getInstance() {
    return obj;
}
```

To call it, we would need an object.

```java
Database d = new Database();
d.getInstance();
```

But we cannot create an object because the constructor is private.

Therefore, `getInstance()` must also be static so that we can call:

```java
Database.getInstance();
```

without creating an object first.

---

# Easy Way to Remember

A Singleton follows three simple rules:

1. **Private Constructor**
   - Prevents others from creating objects.

2. **Private Static Object**
   - Stores the one and only instance.

3. **Public Static getInstance()**
   - Gives everyone access to that single object.

---

# One-Line Summary

**Singleton = One object, shared by everyone.**
