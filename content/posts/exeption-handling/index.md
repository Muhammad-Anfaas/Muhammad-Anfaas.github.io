---
title: "Introduction to Exception Handling in C++"
date: 2026-11-07
draft: false
featureimage: "handling-feature.png"
heroStyle: "background"
showTableOfContents: true

---

# Introduction to Exception Handling in C++

Exception handling is a mechanism that lets a program detect and respond to runtime errors without crashing. Instead of letting a problem such as division by zero, a failed file read, or running out of memory bring everything down, the program can *throw* an exception, jump to code that knows how to deal with it, and carry on in a controlled way.

Before moving forward, we first need to understand what an exception is and why it is preferred over older error handling approaches.

## What is an Exception?
An exception is an event that occurs during program execution and disrupts its normal flow. In C++, an exception is an **object** that is thrown when something goes wrong, and it travels up the call stack until some code catches it.

## Why Not Just Use Error Codes?
Before exceptions, functions usually reported failure by returning a special value (like `-1` or `nullptr`). This has real drawbacks:

- The caller can simply forget to check the return value, and the error goes unnoticed.
- Error handling code gets mixed into the normal logic, making it hard to read.
- Some functions (like constructors) have no return value to carry an error.
- A valid result and an error value can be hard to tell apart.

Exceptions solve these problems by separating the normal path from the error path, and by making errors impossible to ignore silently.

Consider the diagram below to understand how an exception travels through the call stack:

![output](/call_stack.png)

## try, throw, catch
C++ exception handling is built upon three keywords:

- `try`: marks a block of code that might throw an exception
- `throw`: signals that an error has occurred and sends an exception object
- `catch`: defines a handler that responds to a specific type of exception

Let's look at a basic example:

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

double divide(int a, int b) {
    if (b == 0) {
        throw runtime_error("Division by zero!");
    }
    return static_cast<double>(a) / b;
}

int main() {
    try {
        cout << divide(10, 2) << endl;
        cout << divide(5, 0) << endl;
        cout << "This line never runs" << endl;
    }
    catch (const runtime_error& e) {
        cout << "Error: " << e.what() << endl;
    }

    cout << "Program continues normally" << endl;
    return 0;
}
```

**Output:**

![output](/output_1.png)


The first call succeeds. The second call throws, so execution leaves the `try` block immediately (the "never runs" line is skipped) and jumps to the matching `catch` block. After the handler finishes, the program continues normally.

> **Note:** Always catch exceptions by **const reference** (`const runtime_error& e`). Catching by value makes a copy and can slice derived exception types.

---

## What Happens When we Don't use a Handler?
If an exception is thrown and nothing catches it, the program calls `std::terminate()` and ends abruptly:

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

int main() {
    cout << "Before Exception" << endl;
    throw runtime_error("Nobody will ever catches me");
    cout << "After Exception" << endl;
    return 0;
}
```

**Output**
![output](/output_2.png)


This is exactly the kind of crash exception handling is meant to prevent as crashing of our code is way more better than simply executing wrongfully.

---

## Multiple Catch Blocks
A single `try` block can be followed by several `catch` blocks, each handling a different exception type. The first matching handler runs.

```cpp
#include <iostream>
#include <string>
#include <stdexcept>
using namespace std;

void parse(const string& text) {
    try {
        int number = stoi(text);
        cout << "Parsed: " << number << endl;
    }
    catch (const invalid_argument& e) {
        cout << "Invalid number: " << text << endl;
    }
    catch (const out_of_range& e) {
        cout << "Number too large: " << text << endl;

    catch (const exception& e)
    {
        cout<<"general error:"<<endl;
    }
    }
}

int main() {
    parse("42");
    parse("abc");
    parse("99999999999");
    return 0;
}
```

**Output:**
![output](/output_3.png)

`stoi` throws `std::invalid_argument` when the text isn't a number, and `std::out_of_range` when the value doesn't fit in an `int` and the general exception doesnt run.

### Catch-All Handler
To catch any exception regardless of its type, C++ gives us the leverage to use : `...`:

```cpp
try {
    // some risky code
}
catch (const exception& e) {
    cout << "Standard exception: " << e.what() << endl;
}
catch (...) {
    cout << "Unknown exception caught" << endl;
}
```

One should always put `catch (...)` last, since it matches with everything. Moreover, it doesnot it gives you no access to the exception object.

### Order in Writing Catches
Order Matches because the first matching handler wins, always list **derived** types before their **base** types. This is wrong:

```cpp
try {
    throw out_of_range("index problem");
}
catch (const exception& e) {          // base class catches everything first
    cout << "General handler" << endl;
}
catch (const out_of_range& e) {       // never reached
    cout << "Specific handler" << endl;
}
```

The specific handler is unreachable here. we sould put `out_of_range` first and `exception` after it in the code.

---

## Standard Exceptions 
The C++ standard library provides a hierarchy of exception classes in `<stdexcept>` and `<exception>`, all derived from `std::exception`. Every one of them has a `what()` method that returns a description of the error.
![output](/output_4.png)

---

## Creating Custom Exceptions
For errors specific to our application,we can  define our own exception class. The usual approach is to inherit from `std::runtime_error` (or `std::exception`), which gives us `what()` function and similar other useful functions for free and lets our exception t0 be caught by standard handlers too.

```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

class InsufficientFundsException : public runtime_error {
    double shortfall_;

public:
    InsufficientFundsException(double balance, double amount)
        : runtime_error("Insufficient funds: balance " + to_string(balance) +
                        ", requested " + to_string(amount)),
          shortfall_(amount - balance) {}

    double shortfall() const { return shortfall_; }
};

class BankAccount {
    double balance_;

public:
    explicit BankAccount(double balance) : balance_(balance) {}

    void withdraw(double amount) {
        if (amount > balance_) {
            throw InsufficientFundsException(balance_, amount);
        }
        balance_ -= amount;
        cout << "Withdrew " << amount << ", remaining: " << balance_ << endl;
    }
};

int main() {
    BankAccount account(500);

    try {
        account.withdraw(200);
        account.withdraw(800);
    }
    catch (const InsufficientFundsException& e) {
        cout << e.what() << endl;
        cout << "You are short by " << e.shortfall() << endl;
    }

    return 0;
}
```

**Output:**
![output](/output_5.png)

---

## Rethrowing Exceptions
Sometimes a handler can do part of the job (like logging) but isn't the right place to fully resolve the problem. Use a bare `throw;` to pass the same exception up to the caller:

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

void process() {
    try {
        throw runtime_error("Disk read failed");
    }
    catch (const exception& e) {
        cout << "Logging: " << e.what() << endl;
        throw;   // rethrow the same exception
    }
}

int main() {
    try {
        process();
    }
    catch (const exception& e) {
        cout << "Handled in main: " << e.what() << endl;
    }
    return 0;
}
```

**Output:**
![output](/output_6.png)

We use a use a `throw;` rather than `throw e;`. The second form throws a *copy* of the caught object and can slice a derived exception down to its base type.

---

## Stack Unwinding and RAII
RAII (Resource Acquisition Is Initialization) is a C++ programming design pattern that binds the life cycle of a resource (such as heap memory, file handles, network sockets, or mutex locks) to the lifetime of an object.
It has 3 Core Principles
- **Acquisition in Constructor**: A resource is allocated or acquired inside a class constructor when the object is created.
- **Release in Destructor**: The resource is automatically released inside the class destructor when the object goes out of scope.
- **Deterministic Cleanup**: Because C++ guarantees that destructors run when an object's scope ends—even if an exception is thrown—RAII eliminates resource leaks without needing a garbage collector.
When an exception is thrown, C++ performs **stack unwinding**: it exits each function on the way to the handler and calls the destructors of all local objects. Hence due to **RAII**if a resource is owned by an object, its destructor releases it automatically when an exception occurs.

![output](/stackunwinding.png)*Stack Unwinding example in Cpp*

Example Code:


```cpp
#include <iostream>
#include <stdexcept>
#include <string>
using namespace std;

class Resource {
    string name_;

public:
    explicit Resource(const string& name) : name_(name) {
        cout << name_ << " acquired" << endl;
    }
    ~Resource() {
        cout << name_ << " released" << endl;
    }
};

void risky() {
    Resource file("File");
    cout << "Doing work..." << endl;
    throw runtime_error("Something went wrong");
    cout << "Unreachable" << endl;
}

int main() {
    try {
        risky();
    }
    catch (const exception& e) {
        cout << "Caught: " << e.what() << endl;
    }
    return 0;
}
```

**Output:**

![output](/output.png)

Notice that "File released" prints **before** the handler runs. The destructor was called during unwinding, so no cleanup code was needed in the `catch` block. This is why modern C++ prefers `std::unique_ptr`, `std::vector`, `std::fstream` and similar RAII types over manual `new`/`delete` and `fopen`/`fclose`.

---

## The noexcept Specifier
A function marked `noexcept` promises that it will not throw an exception:

```cpp
int add(int a, int b) noexcept {
    return a + b;
}
```

If a `noexcept` function does throw, the program calls `std::terminate()` immediately. This promise helps the compiler optimize, and some standard containers (like `std::vector`) use it to decide whether they can move objects safely when growing. Destructors are `noexcept` by default, so you should never let an exception escape from one.

---

## Best Practices
In conclusion to all this, these are some of the good practices to follow :
- **Throw by Value, Catch by Const Reference**: Use throw MyError(...) and catch (const MyError& e) to avoid unnecessary copying.
- **Use Exceptions for Exceptional Situations**: Don't use exceptions for normal control flow, such as terminating loops.
- **Prefer Standard Exception Types**: Use standard exceptions or derive custom exceptions from std::exception.
- **Use RAII**: Manage resources automatically through object lifetimes instead of manual cleanup.
- **Never Throw from Destructors**: Throwing exceptions from destructors can cause program termination.
- **Catch Only What You Can Handle**: Let unhandled exceptions propagate to functions capable of resolving them.
- **Never Swallow Exceptions**: Avoid empty catch blocks; handle, log, or rethrow exceptions.
