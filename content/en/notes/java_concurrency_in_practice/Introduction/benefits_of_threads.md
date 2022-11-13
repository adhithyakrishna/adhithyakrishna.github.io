---
title: "Ch02 - Benefits of Threads"
date: 2022-11-13T14:50:54-08:00
description: Contains Notes for the book Java concurrency in practice - https://jcip.net/) 
draft: false
collapsible: false
meta_image: notes/java_concurrency_in_practice/jcip.png
image: notes/java_concurrency_in_practice/jcip.png
weight: 10
---

{{< featuredImage >}}

Contains Notes for the book Java concurrency in practice - https://jcip.net/)

#### Benefits of threads
Proper utilization of thread can
1) Reduce development and maintenance cost.
2) Improve the performance of complex applications.

Threads are useful in GUI applications for improving the responsiveness of the user interface. In server applications, threads help in improving the resource utilzation and throughput.

Since the ***basic unit of scheduling*** is the thread. A single threaded program can run on at most one processor at a time. When a single threaded program is run on a 100 processor system, it is giving up access to 99%.

When designed properly, a multithreaded program can improve throughput by utilizing available processor resources more effectively.

Using multiple threads can help achieve a better throughput on a single processor system as well. When a thread is waiting for the I/O to complete, another thread can still run allowing the application to make progress during the blocking I/O.

Assigning a thread to ***each type of task*** will make it look like its sequential and insulates domain logic from the details of
1) Scheduling
2) Interleaved operations
3) Asynchronous I/O
4) Resource waits

A complicated async workflow can be decomposed into a number of simpler ***synchronous workflows***, each running in a seperate thread, interacting with each other at a specific ***synchronization points***.

Example: RMI (Remote Method Invocation):
The framework handles the details of
1) Request management
2) Thread creation
3) Load balancing
4) Dispatching portions of the request handling to appropriate app component at the appropriate point in the work-flow.

Servlet writers need not worry about how many requests are being processed at the same time or whether the socket input and output streams block. When a servlet's service method is called in response to a web client, it can process the request ***synchronously*** as if it were a single threaded program, greatly simplifying the component development and reduce the learning curve of such frameworks.

A server appliation that is single threaded would require non-blocking I/O which is more complicated and error-prone than synchronous I/O.
