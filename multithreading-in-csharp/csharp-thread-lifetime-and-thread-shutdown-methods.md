---
title: "C# Thread Lifetime and Thread Shutdown Methods"
description: "We know that thread class models a thread. This thread, however, doesn't stay for infinity and has lifespan. This article explains how to stop a thread gracefully."
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
author: shadman_kudchikar
redirect_from: "/multithreading-in-csharp/getting-started-with-thread-class-in-csharp/"
---

## Contents

*   [Thread Life Cycle In C#](#thread-life-cycle-in-csharp)
*   [C# Stop Thread](#csharp-stop-thread)

## Thread Life Cycle In C\# {#thread-life-cycle-in-csharp}

So now we know how thread class models a thread. This thread, however, doesn't stay for infinity and has lifespan which is up to the return of the thread delegate method, Let's see an example

```csharp
using System;
using System.Diagnostics;
using System.Threading;

public class Example
{
    public static void Main()
    {
        //initialize a thread class object 
        //And pass your custom method name to the constructor parameter

        Thread t = new Thread(Speak);

        //start running your thread
        //dont forget to pass your parameter for the Speak method in 
        //Thread's Start method below
        t.Start("Hello World!");

        //wait until Thread "t" is done with its execution.
        t.Join();

        Console.WriteLine("Press Enter to terminate!");
        Console.ReadLine();
    }

    private static void Speak(object s)
    {
        //your code here that you want to run parallel
        //most of the time it will be a CPU bound operation

        string say = s as string;
        Console.WriteLine(say);

    } // <-- this line is where thread exit and shutdown
}
```

Here, line no 33 will be the last execution statement after which thread will be shutdown.

Some other reasons of thread shutdown are as follows:

### Synchronous exception

Thread also gets exit if it runs into an unhandled exception. This exception is considered as synchronous exception which occurs in normal sequential program like IndexOutOfRangeExecption.

### Asynchronous exception

This exception is an explicit exception raised by calling thread's Abort or Interrupt method in the running thread by some other thread which has reference to the running thread. This exception also exits thread execution. However, this is not a recommended method to shutdown a thread as it leaves the program to some improper state.

## C\# Stop Thread {#csharp-stop-thread}

Lets start with an example this time,

```csharp
using System;
using System.Diagnostics;
using System.Threading;

public class Example
{
    //set to volatile as its liable to change so we JIT to don't cache the value
    private static volatile bool _cancel = false;

    public static void Main()
    {
        //initialize a thread class object 
        //And pass your custom method name to the constructor parameter

        Thread t = new Thread(Speak);

        //start running your thread
        //dont forget to pass your parameter for the 
        //Speak method (ParameterizedThreadStart delegate) in Start method
        t.Start("Hello World!");

        //wait for 5 secs while Speak method print Hello World! for multiple times
        Thread.Sleep(5000);

        //signal thread to terminate
        _cancel = true;


        //wait until CLR confirms that thread is shutdown
        t.Join();
    }

    private static void Speak(object s)
    {

        while (!_cancel)
        {
            string say = s as string;
            Console.WriteLine(say);
        }

    }
}
```

Here we used a boolean field to signal another thread Speak method to stop running when `_cancel` is set to true.

Did you notice how we need to set the `_cancel` field as volatile. JIT usually cache this kind of fields as it doesn't seem to change within Speak method in the loop. By setting it to volatile we are signaling JIT not to cache this field because it is liable to change.

You can use your own communication mechanism to tell the ThreadStart method to finish, which is recommended method. Alternatively the Thread class has in-built support for instructing the thread to stop. The two principle methods are `Thread.Interrupt()` and `Thread.Abort()`, which is not recommended.
