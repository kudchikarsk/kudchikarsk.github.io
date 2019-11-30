---
title: "Multithreading In C#"
description: "This tutorial explains how to take advantage of Thread class provided by C# and .NET Framework to create robust applications that are responsive and parallel."
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
redirect_from: "/thread-synchronization-in-csharp/"
author: shadman_kudchikar
---

## Introduction

Applications are getting complex as user expectation rise. To truly utilize multicore processor system and stay responsive at the same time, one must use multiple threads in an application, often called as parallelism.

In this tutorial you will learn how to take advantage of Thread Class provided by C# and .NET Framework to create robust applications that are responsive, parallel and meet the user expectations.

Futher, we will discuss the issue in terms of program correctness in a multithreaded program. And then we'll take a look at some of the thread synchronization techniques that are available to you as a CLR or .NET programmer.

Finally, we're going to look at task-based approach provided by C# .NET for async and parallel programming.


## Modules

* [Threads In C# Overview.](/multithreading-in-csharp/threads-in-csharp-overview)
* [Getting Started With Thread Class In C#.](/multithreading-in-csharp/getting-started-with-thread-class-in-csharp)
* [C# Thread Lifetime And Thread Shutdown Method.](/multithreading-in-csharp/csharp-thread-lifetime-and-thread-shutdown-methods)
* [Thread Pool In C#.](./thread-pool-in-csharp)
* [Thread Synchronization And Race Condition](/thread-synchronization-in-csharp/thread-synchronization-and-race-condition)
* [Thread Synchronization Techniques](/thread-synchronization-in-csharp/thread-synchronization-techniques)
* [Wait Based Synchronization In Multithreading](/thread-synchronization-in-csharp/wait-based-synchronization-in-multithreading)
* [C# Monitor](/thread-synchronization-in-csharp/csharp-monitor)
* [Introduction To Parallel Programming Using C# Tasks](/tasks-in-csharp/introduction-to-parallel-programming-using-csharp-tasks/)
* [Getting Started With C# Task](/tasks-in-csharp/getting-started-with-csharp-task/)
* [Programming For Responsiveness](/tasks-in-csharp/programming-for-responsiveness/)
* [C# Task](/tasks-in-csharp/csharp-task/)

## What You Will Learn

* How to model threads using C# thread class.
* Control thread lifetime and coordinate thread shutdown.
* Using thread pool to queue your work item.
* What is thread synchronization and a race condition.
* Thread synchronization techniques.
* What is wait based synchronization in multithreading.
* How to use Monitor class in C#.
* What is task?
* Task exectuion model.
* Harvesting result from task.
* Task handling techniques.
* Exception handling in task.

## Requirements

* You will need Visual Studio IDE (for programming in C# .NET) to get started with this tutorial.
* All demos in this tutorial are built using Visual Studio and complete solution demo is available here:
	- [Solution 1](https://github.com/kudchikarsk/multithreading-in-csharp-demo)
	- [Solution 2](https://github.com/kudchikarsk/thread-synchronization-in-csharp)
	- [Solution 3](https://github.com/kudchikarsk/tasks-in-csharp)

## Issues

* You can use the Comments section at the bottom of each module to ask a question or report a problem.
* Also feel free to ask me questions by sending me a message on my [Instagram][Instagram] account, or add me on [LinkedIn][LinkedIn]!



[Instagram]: https://www.instagram.com/kudchikarsk
[LinkedIn]: https://linkedin.com/in/kudchikarsk