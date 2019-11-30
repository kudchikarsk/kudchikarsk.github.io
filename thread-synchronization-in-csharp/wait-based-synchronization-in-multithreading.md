--- 
title: Wait Based Synchronization In Multithreading 
description: This article explains how this wait based technique and lock-based synchronization works. A lock is a synchronization mechanism for enforcing limits on access to a resource in an environment where there are many threads of execution.
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
author: shadman_kudchikar
---

## Contents

* [Wait Based Synchronization In Multithreading](#wait-based-synchronization-in-multithreading)
* [Wait Based Primitives in CLR](#wait-based-primitives-in-clr)
	- [C# Monitor Class](#csharp-monitor-class)
	- [C# Mutex Class](#csharp-mutex-class)
	- [C# ReaderWriterLock Class](#csharp-readerwriterlock-class)

## Wait Based Synchronization In Multithreading {#wait-based-synchronization-in-multithreading}

In this chapter, I will explain how actually this wait based technique work as its a technique and not some programming syntax it can be adopted in any type of programming language or environment.

Let's dive in,

Suppose there are two threads namely X and Y and both want to access some resource R

Now to protect this resource we choose some lock primitive or synchronization primitive as LR (primitive here is some primitive type like int or array)

Now when thread X want to access resource R it will first acquire the lock ownership of LR, once this thread got ownership of LR it can access the resource R safely. As long as thread X has this ownership no other thread can access the LR ownership

While X has ownership if Y request to acquire the ownership of lock LR it requests will get block until thread X releases its ownership.

## Wait Based Primitives in CLR {#wait-based-primitives-in-clr}

They all share the same basic usage

*   Access the lock ownership
*   Manipulate the protected resource
*   Release the lock ownership

### C# Monitor Class {#csharp-monitor-class}

The Monitor class allows you to synchronize access to a region of code by taking and releasing a lock on a particular object by calling the Monitor.Enter, Monitor.TryEnter, and Monitor.Exit methods. Object locks provide the ability to restrict access to a block of code, commonly called a critical section. While a thread owns the lock for an object, no other thread can acquire that lock. You can also use the Monitor class to ensure that no other thread is allowed to access a section of application code being executed by the lock owner unless the other thread is executing the code using a different locked object.

### C# Mutex Class {#csharp-mutex-class}

You can use a Mutex object to provide exclusive access to a resource. The Mutex class uses more system resources than the Monitor class, but it can be marshaled across application domain boundaries, it can be used with multiple waits, and it can be used to synchronize threads in different processes. For a comparison of managed synchronization mechanisms, see Overview of Synchronization Primitives.

### C# ReaderWriterLock Class {#csharp-readerwriterlock-class}

The ReaderWriterLockSlim class addresses the case where a thread that changes data, the writer, must have exclusive access to a resource. When the writer is not active, any number of readers can access the resource. When a thread requests exclusive access, subsequent reader requests block until all existing readers have exited the lock, and the writer has entered and exited the lock.

For a comparison of managed synchronization mechanisms, see [Overview of Synchronization Primitives.](https://docs.microsoft.com/en-us/dotnet/standard/threading/overview-of-synchronization-primitives)

I'm going to discuss monitor and mutex in depth in the [next chapter](/thread-synchronization-in-csharp/csharp-monitor/)
