---
title: "Ch03 - Risks of threads"
date: 2022-11-16T11:51:25-08:00
description: Contains Notes for the book Java concurrency in practice - https://jcip.net/
draft: false
collapsible: false
meta_image: tech/java_concurrency_in_practice/jcip.png
image: tech/java_concurrency_in_practice/jcip.png
weight: 15
---

{{< featuredImage >}}

Contains Notes for the book Java concurrency in practice - https://jcip.net/
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

Two threads can call getNext and receive the same value since operations in multiple threads may be arbitrarily interleaved by the runtime. The result is that same sequence number is returned from multiple calls in different threads.

{{< img src=/tech/java_concurrency_in_practice/clipboard_20240815170817.png title="Safety hazards" caption="Unsafe sequence example" alt="Unsafe sequence example" width="700px" position="center" >}}

The above unsafe sequence example illustrates the common concurrency hazard called a **race condition**.

Since threads share the same memory address space and run concurrently, they can access or modify variables that other threads might be using. The convenience is that it makes data sharing much easier compared to other inter-thread communication mechanisms. The risk is that threads can be confused by having data change unexpectedly.

Allowing multiple threads to access and modify the same variables introduces non-sequentiality into an otherwise sequential programming model.

For a multi-threaded program to be predictable, access to shared variables must be properly coordinated so that the threads do not interfere with one another.

```java
public class Sequence {
private int nextValue;
  public synchronized int getNext() {
    return nextValue++;
  }
}
```

UnsafeSequence can be fixed by making `getNext()` a synchronized method.

In absence of synchronization, **the compiler, hardware and runtime** are allowed to take substantial liberties with the timing and ordering of actions such as **caching variables in registers or processor-local caches**. These tricks are in aid of better performance and are generally desirable but developer has to clearly identify where the data is being shared across threads so these optimizations don't undermine safety.
#### Liveness Hazards
Use of threads introduces additional forms of liveness failures.

Safety → nothing bad ever happens
Liveness → something good eventually happens.

A liveness failure occurs when an activity gets into a state such that it is permanently unable to make forward progress. Examples include deadlock, starvation, and livelock. 

#### Performance Hazards

Multi-threaded programs are subject to all performance hazards of single threaded programs and to others as well that are introduced by the use of threads.

**Context-switches**, when the scheduler suspends the active thread temporarily so another thread can run, have significant costs, such as: 
1) Saving and restoring execution context
2) Loss of locality
3) CPU time spent scheduling threads instead of running them.

When threads share data, they must use synchronization mechanisms that can inhibit:
1) Compiler optimizations
2) Cache flushes
3) Memory cache invalidation
4) Synchronization traffic on the memory bus

All of the above factors introduce additional performance costs.
#### Threads are Everywhere

Every Java application uses threads. When the JVM starts, it creates threads for JVM housekeeping tasks and a main thread for running the main method.

When concurrency is introduced into an application by a framework, it is usually impossible to restrict concurrency awareness to the framework code. Frameworks by nature make callbacks to application components that in turn access the application state.
Similarly, the need for thread safety does not end with the components called by the framework—it extends to all code paths that access the program state accessed by the components. Thus, the need for thread safety is contagious.

Objects accessed by the tasks themselves should be made thread-safe, encapsulating the thread safety within the shared objects.