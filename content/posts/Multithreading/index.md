---
date: '2026-08-04T09:32:27+05:00'
draft: false
title: 'Multi Threading'
featureimage: "multithreading-feature.png"

heroStyle: "background"

showTableOfContents: true
---

# Introduction to Multithreading

Multithreading is a programming technique that allows a single process (application) to be divided into multiple concurrent execution paths called threads. By sharing the same memory space and resources, these threads can execute tasks independently, drastically improving system performance, responsiveness, and hardware utilization.

Before moving forward, we first need to know what a process and a thread are in terms of computing.

## Process
In computing, a process is an *active instance* of a computer program being executed. It represents the lifecycle of a program running in memory, containing the program code, current state, memory layout (stack and heap), and allocated system resources managed by the operating system.

## Thread
A thread is the smallest unit of execution inside a process. A process can have multiple threads, and threads share the same code and global data but have their own registers and local variables (stack).

## Multithreading in Python
On single-core CPUs, Python achieves concurrency using context switching (frequent switching between threads). This makes threads appear to run in parallel (multitasking).

Consider the diagram below to understand how multiple threads exist in memory:

![output](/illustration2.png)

### Threading in Python
Python provides the `threading` module to work with threads. To begin working with threads, first have a look at a non-threaded procedural program.

```python
# Example of Procedural Programming
import time

def say_hello():
    print("Hello!")

def square(num):
    print(f"Square: {num*num}")
    time.sleep(1)

def cube(num):
    print(f"Cube: {num*num*num}")
    time.sleep(1)

def say_bye():
    print("Bye!")

start = time.perf_counter()

say_hello()
square(4)
cube(4)
say_bye()

print(f"Time taken: {time.perf_counter() - start:.2f} seconds")
```

**Output:**
```
Hello!
Square: 16
Cube: 64
Bye!
Time taken: 2.00 seconds
```

Every function runs one after another, so the program waits for `square()` to finish its 1 second sleep before `cube()` can even start. Total time: about 2 seconds.

---

## Creating Threads
A thread is created using the `Thread` class from the `threading` module. It takes two main arguments:

- `target`: the function the thread will run
- `args`: a tuple of arguments to pass to that function

Two methods control a thread's lifecycle:

- `start()`: begins running the thread
- `join()`: makes the main program wait until that thread has finished

Now let's rewrite the same program using threads:

```python
# Example of Multithreading
import threading
import time

def say_hello():
    print("Hello!")

def square(num):
    print(f"Square: {num*num}")
    time.sleep(1)

def cube(num):
    print(f"Cube: {num*num*num}")
    time.sleep(1)

def say_bye():
    print("Bye!")

start = time.perf_counter()

say_hello()

t1 = threading.Thread(target=square, args=(4,))
t2 = threading.Thread(target=cube, args=(4,))

t1.start()
t2.start()

t1.join()
t2.join()

say_bye()

print(f"Time taken: {time.perf_counter() - start:.2f} seconds")
```

**Output:**
```
Hello!
Square: 16
Cube: 64
Bye!
Time taken: 1.00 seconds
```

Both threads sleep at the same time, so the total time drops from 2 seconds to about 1. Without the `join()` calls, `say_bye()` could print before the threads finish, because the main thread doesn't wait on its own.

> **Note:** `args` must be a tuple. For a single argument, don't forget the trailing comma: `args=(4,)`.

---

## Running Many Threads
When you have more than a couple of tasks, create the threads in a loop:

```python
import threading
import time

def worker(task_id):
    print(f"Task {task_id} started")
    time.sleep(1)
    print(f"Task {task_id} finished")

threads = []

for i in range(5):
    t = threading.Thread(target=worker, args=(i,))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print("All tasks completed")
```

All five tasks finish in about 1 second instead of 5. The order of the printed lines may change from run to run, because the operating system decides which thread runs when.

---

## Race Conditions and Locks
Since threads share the same memory, problems appear when two threads modify the same variable at the same time. This is called a **race condition**.

```python
import threading

counter = 0

def increment():
    global counter
    for _ in range(100000):
        counter += 1

threads = [threading.Thread(target=increment) for _ in range(5)]

for t in threads:
    t.start()
for t in threads:
    t.join()

print(f"Final counter: {counter}")
```

You would expect `500000`, but the result can come out lower and change between runs. That's because `counter += 1` is really three steps (read, add, write), and two threads can read the same old value before either writes the new one.

The fix is a **Lock**, which lets only one thread at a time run a protected section:

```python
import threading

counter = 0
lock = threading.Lock()

def increment():
    global counter
    for _ in range(100000):
        with lock:
            counter += 1

threads = [threading.Thread(target=increment) for _ in range(5)]

for t in threads:
    t.start()
for t in threads:
    t.join()

print(f"Final counter: {counter}")
```

**Output:**
```
Final counter: 500000
```

Using `with lock:` acquires the lock automatically and releases it when the block ends, even if an error occurs.

---

## The Global Interpreter Lock (GIL)
CPython, the standard Python implementation, has a mechanism called the **Global Interpreter Lock (GIL)**. It allows only one thread to execute Python bytecode at a time, even on a multi-core CPU.

This means threads help in some situations and not in others:

| Task type | Examples | Does threading help? |
|---|---|---|
| **I/O-bound** | Network requests, file reads, database queries, `sleep()` | Yes. While one thread waits, another can run. |
| **CPU-bound** | Heavy math, image processing, large loops | Generally no. Use `multiprocessing` instead. |

This is why our `sleep()` example got faster: a sleeping thread releases the GIL, so the other thread can work in the meantime. Recent Python versions also offer an optional free-threaded build without the GIL, but the standard build still has it.

---

## ThreadPoolExecutor
Creating and joining threads by hand gets repetitive. The `concurrent.futures` module provides `ThreadPoolExecutor`, which manages a pool of threads for you:

```python
from concurrent.futures import ThreadPoolExecutor
import time

def square(num):
    time.sleep(1)
    return num * num

numbers = [1, 2, 3, 4, 5]

with ThreadPoolExecutor(max_workers=5) as executor:
    results = list(executor.map(square, numbers))

print(results)
```

**Output:**
```
[1, 4, 9, 16, 25]
```

`executor.map()` runs the function on each item in parallel threads and returns the results in the original order. The `with` block waits for all threads to finish, so no manual `join()` is needed.

---