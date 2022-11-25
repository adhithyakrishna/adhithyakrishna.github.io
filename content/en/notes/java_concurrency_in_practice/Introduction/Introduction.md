---
title: "Ch01 - Introduction"
date: 2020-02-28T10:08:56+09:00
description: Contains Notes for the book Java concurrency in practice - https://jcip.net/) 
draft: false
collapsible: false
meta_image: notes/java_concurrency_in_practice/jcip.png
image: notes/java_concurrency_in_practice/jcip.png
weight: 5
---

{{< featuredImage >}}

Contains Notes for the book Java concurrency in practice - https://jcip.net/)

Running a single program at a time was inefficient use of expensive and scarce computer resources

#### Processes
Processes are isolated, independently executing programs to which operating system allocates resources such as memory, file handles and security credentials.

Processes communicate with one another through a variety of coarse-grained communication mechanism: sockets, signal handlers, shared memory, semaphores and fils.


#### Multi program execution
Several motivating factors led to the development of OS that allowed multiple programs to execute simultaneously.

1) ***Resource Utilization***: Programs have to sometimes wait for external operations such as input or output. While waiting for external operations, it is more efficient to use the wait time to let another program run.

2) ***Fairness***: Multiple users and programs may have equal claims on machine's resources. It is preferable to let the users share the resources via finer-grained time slicing rather than keeping them wait until the current program completes exectuion.

3) ***Convenience***: It is easier to write several program that each perform a single task and have them coordinate with each other as necessary than to write a single program that performs all the tasks.


The same factors that motivated the development of processes also motivated the development of ***threads***.

#### Threads 
Threads are light weight processes, most operating systems treat threads, not processes as the basic unit of scheduling.

1) Allow multiple streams of program control flow to co-exist within a process.
2) They share a process wide resource such as ***memory and file handles***.
3) Each thread has its own program counter, stack and local variables. 
4) Multiple threads within the same program can be scheduled simultaneously on multiple CPUs.

{{< img src=/notes/java_concurrency_in_practice/Clipboard_2022-11-11-14-00-02.png title="Single vs Multithreaded" caption="Java Concurrency in practice" alt="Single vs Multithreaded" width="700px" position="center" >}}

In ***absence of explicit coordination*** threads execute ***simultaneously and asynchronously*** with respect to one another. Since threads share the ***memory addresss space*** of their owning process, all threads within a process have access to same variables and allocate objects from the same heap allowing finer grained data sharing than inter-process mechanisms.
But without ***explicit synchronization*** to coordinate access to shared data, a thread may modify variables that another thread is in the middle of using, with unpredicatable results.