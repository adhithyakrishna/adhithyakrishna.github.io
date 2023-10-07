---
title: "Ch03 - Risks of threads"
date: 2022-11-16T11:51:25-08:00
description: Contains Notes for the book Java concurrency in practice - https://jcip.net/) 
draft: false
collapsible: false
meta_image: tech/java_concurrency_in_practice/jcip.png
image: tech/java_concurrency_in_practice/jcip.png
weight: 15
---

{{< featuredImage >}}

Contains Notes for the book Java concurrency in practice - https://jcip.net/)
In the absence of synchronization, the ordering of operations in multiple threads is unpredictable.
#### Safety Hazards

```java
public class UnsafeSequence {
private int value;
  /** Returns a unique value. */
  public int getNext() {
    return value++;
  }
}
```

In the above example,```value++``` is three separate operations.
1) Read the value
2) Add one to it
3) Write the new value.

Two threads can call getNext and receive the same value since operations in multiple threads may be arbitarily interleaved by the runtime. The result is that same sequence number is returned from multiple calls in different threads.

![](@attachment/Clipboard_2022-11-11-15-19-58.png)

The above unsafe sequence example illustrate the common concurrency hazard called **race condition**.

Since threads share the same memory address space and run concurrently, they can access or modify variables that other threads might be using. Convenience here is, it makes data sharing much easier compared to other inter-thread communication mechanisms. The risk here is, threads can be confused by having data change unexpectedly.

Allowing multiple threads to access and modify the same variables will introduce the element of non sequentially into an otherwise sequential programming model.

For a multi-threaded program to be predictable, access to shared variables must be properly coordinated so that the threads do not interfere with one another.

```java
public class Sequence {
private int nextValue;
  public synchronized int getNext() {
    return nextValue++;
  }
}
```

Unsafe-sequence can be fixed by making ``getNext()`` a synchronized method.

In absence of synchronization, **the compiler, hardware and runtime** are allowed to take substantial liberties with the timing and ordering of actions such as **caching variables in registers or processor-local caches**. These tricks are in aid of better performance and are generally desirable but developer has to clearly identify where the data is being shared across threads so these optimizations don't undermine safety.
#### Liveness Hazards
Use of threads introduces additional form of liveliness failure.

Safety -> nothing bad ever happens
liveliness -> something good eventually happens.

A liveliness failure occurs when an activity gets into a state such that it is permanently unable to make forward progress. Example deadlock, starvation and livelock. 

#### Performance Hazards

Multi-threaded programs are subject to all performance hazards of single threaded programs and to others as well that are introduced by the use of threads.

**Context-switches** when the scheduler suspends the active thread temporarily so another thread can run - have significant costs. Such as 
1) Saving and restoring execution context
2) Loss of locality
3) CPU time spent scheduling threads instead of running them.

When threads share data, they must use synchronization mechanisms that can inhibit 
1) Compiler optimizations
2) Flush
3) Invalidate memory caches
4) Create synchronization traffic on memory bus.

All of the above factors introduce additional performance costs.
# Threads are Everywhere

Every java app uses threads. When JVM starts, it creates threads for JVM house keeping tasks and main thread for running main method.

When concurrency is introduced into an application by a framework, it is usually impossible to restrict the concurrency awareness to the framework code. Framework by nature make callbacks to the application components that in turn access the application state.
Similarly the need for thread safety does not end with the components called by the framework - it extends to all code paths that access the program state accessed by the components. Thus the need for thread safety is contagious.

Objects accessed by the tasks themselves should be made thread-safe, encapsulating the thread safety within the shared objects.