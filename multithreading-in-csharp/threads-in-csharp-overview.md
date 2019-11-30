---
title: "Threads In C# Overview"
description: "This article is a complete introduction to threading. It explains what is a thread and why it is used in programming. Threading enables your C# program to perform concurrent processing so that you can do more than one operation at a time."
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
author: shadman_kudchikar
---

## Contents

* [What Are Threads In C#?](#what-are-threads-in-csharp)
* [When To Use Thread In C#?](#when-to-use-thread-in-csharp)
* [Limitations Of Threads In C#](#limitations-of-threads-in-csharp)

## What Are Threads in C\#? {#what-are-threads-in-csharp}

In CLR or Windows environment each program you run creates a virtual address space and known as process. Contents of a process is not addressable directly to another process.

Each process has its own thread(s) and this thread has access to all data in that process, But if we are .Net programmers and so we are, we have managed code and we can say that this thread as managed thread which has access to all data in that process's app domain which is subdivision of process



Know more what is the difference in managed and unmanaged code, memory and size [here](https://stackoverflow.com/questions/3368802/what-is-the-difference-in-managed-and-unmanaged-code-memory-and-size).



So a process in its execution lifetime has this main thread that runs execution starting from the main method and in during this execution it may create one or more thread. This thread can execute code of the same executable or code defined in other dll in the same process

In Windows world, if a process does not have a thread it gets terminate.

## When To Use Thread In C\#? {#when-to-use-thread-in-csharp}

You can use threads in the following cases:

- Scalability (Be parallel) - If you have long running CPU bound operations, like to compute if 80 digit number is prime or not, you can scale this operation by paralleling this operation to multiple threads

- Responsive - You can keep client application responsive by keeping off lengthy operations from main thread (like CPU bound operation) and thus can also leverage the benefit of canceling the task

- Leverage asynchronous technique - If you have IO bound operation such reading a web content it may require some time in order of minutes, so you can leverage another thread to wait for this operation while you perform other task and thus even keep UI responsive

However,

C# provides async await syntax for this kind of asynchronous technique.

Also,

Being asynchronous is not parallel it just keeps the application responsive. Asynchronous means not waiting for an operation to finish, but registering a listener instead.

In general, use parallel threads (using [Thread class](/multithreading-in-csharp/getting-started-with-thread-class-in-csharp/) and [Task class](/tasks-in-csharp/csharp-task/) in C#) or asynchronous technique (using [C# async await](/tasks-in-csharp/async-await-c/) keyword) depending upon whether the problem is CPU bound or IO bound respectively.

**Thumb rule is to use threads for CPU bound operation and async for IO bound operation for a client application, and always use async for a server application.**

You can find some serious examples of CPU bound and non-CPU bound problems [here](https://www.quora.com/What-are-some-examples-of-CPU-bound-and-non-CPU-bound-problems-What-would-be-the-best-programming-language-to-tackle-each-situation).


## Limitations Of Threads In C\# {#limitations-of-threads-in-csharp}

- Multithreading leads to complex code thus reduce readability. It also increases the difficulty of debugging and testing. However, you can overcome this with good programming practice and commented code.

- Also in single core processor machine, Threads increase execution time (a little) than a sequential program, due to context switching. But still for scalability its good to use threads because when it will run on multi-core processor machine it will scale better.

Bottom line, Ignore all these limitations.