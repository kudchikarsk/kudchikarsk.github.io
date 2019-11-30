--- 
title: Introduction To Async And Parallel Programming Using C# Tasks
description: This article is introduction to task-based approach provided by C# .NET for async and parallel programming. Task objects are one of the central components of the task-based asynchronous pattern first introduced in the .NET Framework 4.
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
redirect_from: "/tasks-in-csharp/programming-for-responsiveness/"
author: shadman_kudchikar
---

## Content

* [Why We Need Async And Parallel Programming In C#?](#why-we-need-async-and-parallel-programming-in-csharp)
* [Why Task In C#?](#why-task-in-csharp)
	- [Why We Need Task-based Asynchronous Pattern In C\#?](#why-we-need-task-based-asynchronous-pattern-in-csharp)

## Why We Need Async And Parallel Programming In C#? {#why-we-need-async-and-parallel-programming-in-csharp}

- Responsiveness: We want to hide the latency of possibly long-running or blocking operation by starting the operation in the background. This we refer to asynchronous programming or async.

- Performance: We want to reduce the time of execution of CPU bound operation by dividing them into the chunk of operation and executing them in parallel. This we refer to parallel programming.

## Why Task In C#? {#why-task-in-csharp}

We have seen different methods to handle async and parallel operations using [Thread Class](/multithreading-in-csharp/getting-started-with-thread-class-in-csharp/) and [Thread Pool](/multithreading-in-csharp/thread-pool-in-csharp/).

Also, there are various other methods like [Async Programming Model](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) and [Event-Based Async Pattern](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/event-based-asynchronous-pattern-eap).

But,

### Why We Need Task-based Asynchronous Pattern In C\#? {#why-we-need-task-based-asynchronous-pattern-in-csharp}

The Task-based Asynchronous Pattern (TAP) is based on the `System.Threading.Tasks.Task` and `System.Threading.Tasks.Task<TResult>` types in the `System.Threading.Tasks` namespace, which are used to represent arbitrary asynchronous operations. TAP is the recommended asynchronous design pattern for new development.

Tasks provide a sophisticated way to handle async or parallel operation by providing various options like,

- Ability to cancel an ongoing operation
- Return resulting value from operation (like a method functions)
- Easy Exception Handling
- High-Level constructs like a parallel loop
- task continuation

and much more...

We will understand this concept better once we dive deep and look at some examples in the [next chapter](/tasks-in-csharp/getting-started-with-csharp-task/).