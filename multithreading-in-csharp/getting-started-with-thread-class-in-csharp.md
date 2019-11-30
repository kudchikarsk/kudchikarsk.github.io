---
title: "Getting Started With Thread Class In C#"
description: "This article explains how to use Thread Class in C#. In C#, the Thread class is used for working with threads. It allows creating and accessing individual threads in a multithreaded application."
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
redirect_from: "/multithreading-in-csharp/threads-in-csharp-overview/"
author: shadman_kudchikar
---

## Contents

* [C# Start New Thread](#csharp-start-new-thread)
* [Difference Between Foreground And Background Thread In C# ](#difference-between-foreground-and-background-thread-in-csharp)
* [C# Start Thread With Parameters](#csharp-start-thread-with-parameters)

## C# Start New Thread {#csharp-start-new-thread}

Threads in C# are modelled by Thread Class. When a process starts (you run a program) you get a single thread (also known as the main thread) to run your application code. To explicitly start another thread (other than your application main thread) you have to create an instance of thread class and call its Start method to run the thread using C#, Let's see an example

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
         Thread t = new Thread(SomeMethod);

         //start running your thread
         t.Start();

         //while thread is running in parallel 
         //you can carry out other operations here        

         Console.WriteLine("Press Enter to terminate!");
         Console.ReadLine();
     }

     private static void SomeMethod()
     {
         //your code here that you want to run parallel
         //most of the time it will be a CPU bound operation

         Console.WriteLine("Hello World!");
     }
 }      
```

When you run this program you may see _Press Enter to terminate!_ message first and then _Hello World!_ as they both run in parallel, so it is not guaranteed which execute first.

So,

We can use Thread's `Join()` method to halt our main thread until reference thread (that is "t" variable in our case) is truly shutdown.

Another method to do this would be by using boolean `IsAlive` property of thread which gives instantaneous snapshot of thread's state whether it is running or not. Like this,

```csharp
while ( t.IsAlive ) { } 
```

However, `t.Join()` is the recommended method.

Here is an example

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
        Thread t = new Thread(SomeMethod);

        //start running your thread
        t.Start();

        //while thread is running in parallel 
        //you can carry out other operations here

        //wait until Thread "t" is done with its execution.
        t.Join();

        Console.WriteLine("Press Enter to terminate!");
        Console.ReadLine();
    }

    private static void SomeMethod()
    {
        //your code here that you want to run parallel
        //most of the time it will be a CPU bound operation

        Console.WriteLine("Hello World!");
    }
}
```

Now,

Thread doesn't start running until you call `thread.Start()` method, So before calling this Start method you can set some properties of a thread like its name and priority. Setting name of the thread will only help you in debugging, by setting name you can easily point out your thread in Visual Studio Thread window, Let's see an example

```csharp
Thread t = new Thread(SomeMethod);

t.Name="My Parallel Thread";

t.Priority=ThreadPriority.BelowNormal;

//start running your thread
t.Start();
```


## Difference Between Foreground And Background Thread In C\# {#difference-between-foreground-and-background-thread-in-csharp}

There is also this another thread property IsBackground. If set to true your thread will be a background thread otherwise it will be a foreground thread, by default its false so it will always be a foreground thread, Let's see an example

```csharp
Thread t = new Thread(SomeMethod);

//set thread object as a background thread
t.IsBackground = true;

//start running your thread
t.Start();
```

Suppose if a foreground thread is the only thread (your main thread is done with execution and terminated) in your process, so your process is about to exit. However, it won't, your process will wait for foreground thread to complete its execution. Thus, It will prevent application to exit until the foreground thread is done with the execution.

However,

Background thread will exit as soon as your process exits even though background thread is not completely done with the execution.

Learn more about how foreground & background threads work in C# .NET [here](https://docs.microsoft.com/en-us/dotnet/standard/threading/foreground-and-background-threads).


## C# Start Thread With Parameters {#csharp-start-thread-with-parameters}

As you saw in example before that we pass method name to thread constructor parameter like this,

```csharp
Thread t = new Thread(SomeMethod);
```

We able to do this because this thread contructor takes delegate as parameter. Its supports two type of delegates, Here is the definition of first delegate

```csharp
public delegate void ThreadStart()
```

this we already saw in the above example, other is

```csharp
public delegate void ParameterizedThreadStart(object obj)
```

If your custom method takes argument you can pass a ParameterizedThreadStart delegate to constructor, Let's see an example

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
        //dont forget to pass your parameter for the Speak method 
        //in Thread's Start method below
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

    }
}
```

Did you notice now we need to pass the Speak method argument to Start method.

So far we have used only static method. However, you can also use instance methods as a thread constructor parameter, Let's see an example

```csharp
using System;
using System.Diagnostics;
using System.Threading;

public class Example
{
    public static void Main()
    {
        Person person = new Person();

        //initialize a thread class object 
        //And pass your custom method name to the constructor parameter
        Thread t = new Thread(person.Speak);

        //start running your thread
        //dont forget to pass your parameter for 
        //the Speak method in Thread's Start method below
        t.Start("Hello World!");

        //wait until Thread "t" is done with its execution.
        t.Join();

        Console.WriteLine("Press Enter to terminate!");
        Console.ReadLine();
    }
}

public class Person
{
    public void Speak(object s)
    {
        //your code here that you want to run parallel
        //most of the time it will be a CPU bound operation

        string say = s as string;
        Console.WriteLine(say);

    }
}
```
In the above example, we used ParameterizedThreadStart delegate however same applies to ThreadStart delegate, both of them can be used with an instance method.