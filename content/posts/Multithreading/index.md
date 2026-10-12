---
date: '2026-08-04T09:32:27+05:00'
draft: false
title: 'Multi Threading'
featureimage: "multithreading-feature.png"

heroStyle: "background"

showTableOfContents: true
---
## Introduction to Multithreading

Multithreading is a programming technique that allows a single process (application) to be divided into multiple concurrent execution paths called threads. By sharing the same memory space and resources, these threads can execute tasks independently, drastically improving system performance, responsiveness, and hardware utilization. 
Before moving forward first we need to know what a process and thread is in terms of computing.
---
## Process
In computing, a process is an *active instance* of a computer program being executed.It represents the lifecycle of a program running in memory, containing the program code, current state, memory layout (stack and heap), and allocated system resources managed by the operating system.
## Thread
A thread is the smallest unit of execution inside a process.A process can have multiple threads and threads share the same code and global data but have their own registers and local variables (stack).
---
## Multithreading in Python
On single-core CPUs, Python achieves concurrency using context switching (frequent switching between threads). This makes threads appear to run in parallel (multitasking).

Consider the diagram below to understand how multiple threads exist in memory:

![output](/illustration2.png)

### Threading in Python
Python provides the threading module to work with threads. To begin working with threads, first have a look at a non-threaded procedural program.
```python
# Example of Procedual Programming

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

say_hello()
square()
cude()
say_bye()
```
---
Continued ...
