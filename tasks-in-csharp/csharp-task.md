--- 
title: C# Task
description: This tutorial serves to be a quick guide for using Tasks in C#. It discusses different methods to create tasks and handle their execution.
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
redirect_from: "/tasks-in-csharp/getting-started-with-csharp-task/"
author: shadman_kudchikar
---

Task is one of the central elements of the task-based asynchronous pattern first introduced in the .NET Framework 4.

Task object typically executes asynchronously on a thread pool thread rather than synchronously on the main application thread.

As we learned in the [previous chapter](/multithreading-in-csharp/thread-pool-in-csharp/#csharp-thread-pool-queue) we can offload the work to thread pool using the queue user work item method. However, this method has its weaknesses as we can't tell whether the operation has finished or what a return value is.

This is where a Task can be helpful. The Task can tell you if the work is completed and if the operation returns a result. A Task is an object that represents some work that should be done.

Tasks provide a sophisticated way to handle async or parallel operation by providing various options like,

- Ability to cancel an ongoing operation
- Return resulting value from operation (like a method functions)
- Easy Exception Handling
- High-Level constructs like a parallel loop
- task continuation

We will understand this concept better once we dive deep and look at some examples. Let's get started.

## Content
- [Why Use A Task In C# ?](#why-use-a-task-in-c)
- [C\# Task ](#csharp-task)
    - [C\# Task Example](#csharp-task-example)
    - [Execution Model Of A Task](#execution-model-of-a-task)
- [C# Task That Returns A Value](#c-task-that-returns-a-value)
- [How To Avoid Blocking Of Current Thread? How To Be Responsive?](#how-to-avoid-blocking-of-current-thread-how-to-be-responsive)
- [Adding A Continuation](#adding-a-continuation)
- [Scheduling Different Continuation Tasks](#scheduling-different-continuation-tasks)
- [Demo Of A Non-Responsive Application](#demo-of-a-non-responsive-application)
- [Adding Tasks For Responsiveness](#adding-tasks-for-responsiveness)

## Why Use A Task In C# ? {#why-use-a-task-in-c}
- Tasks can be used to make your application more responsive. If the thread that manages the user interface offloads work to another thread from the thread pool, it can keep processing user events and ensure that the application can still be used.

- You can also parallelize your CPU bound operation on to multiple processors using task.

## C\# Task {#csharp-task}

To get started with Tasks in .NET C# you have to create an object of Task Class in C#, which is available in namespace System.Threading.Tasks and provide the code, to be executed within the task, as the task action parameter.

### C\# Task Example {#csharp-task-example}
Let's see an example,

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace _01_Getting_Started_With_Task
{
    class Program
    {
        static void Main(string[] args)
        {
            //create task object and pass anonymous method 
            //to task constructor parameter as work to do 
            //within the task
            Task t = new Task(() =>
              {
                  for (int i = 0; i < 100; i++)
                  {
                      //print task t thread id
                      var threadId = Thread.CurrentThread.ManagedThreadId;
                      Console.WriteLine("Task Loop Current Thread Id:" + threadId);
                  }
              });

            //start task t execution
            t.Start();

            for (int i = 0; i < 100; i++)
            {
                //print main thread id
                var threadId = Thread.CurrentThread.ManagedThreadId;
                Console.WriteLine("Main Loop Current Thread Id " + threadId);
            }

            //wait for task t to complete its execution
            t.Wait();

            Console.WriteLine("Press enter terminate the process!");
            Console.ReadLine();
        }
    }
}
```

Let's see what's happening in the above program line by line.

On line 17 we are creating the Task t and provide the code to execute. But, Task t start its execution after calling `t.Start()` method at the line 27.

So,

From this point, our program is split into two programs that are executing in the parallel codestream also known as a fork or split in the program.

So basically code in task and code below the task's start method, both are executing together. You can't see this but this is happening and you have to imagine this, that now your program is executing two parallel codestreams.

Also, you must have noticed that we passed an anonymous method to Task constructor parameter, as a code to be executed within the task. This we can do because Task Class constructor takes Action as a parameter which is nothing but a delegate with void return type.

### Execution Model Of A Task {#execution-model-of-a-task}

Let's discuss the basic execution model of a task,

In the example above, we created a task and provide a basic operation to be performed by the task.

So this task operation is executed in a separate thread. Each task we create is executed within its own thread.

In a single-core machine, this won't be efficient as there will be a lot of context switching between these threads and they will end up using the same single-core processor.

However,

In a multi-core processor, which is the case nowadays, this will be very efficient as this threads will get distributed within the multi-core machine (each thread executing in the separate processor) and thus will perform better than a sequential program.

Let's dive deeper into the execution model of task in the [next chapter](/tasks-in-csharp/programming-for-responsiveness/).

Also, In the above example, you may have seen the below code,

```cs
//wait for task t to complete its execution
t.Wait();
```

Calling `Wait` method of the task is equivalent to calling the `Join` method on a [thread](/multithreading-in-csharp/getting-started-with-thread-class-in-csharp/#csharp-start-new-thread). When the `Wait` method is called within the `Main` method the main thread pauses its execution until the task `t` completes its execution.

## C# Task That Returns A Value {#c-task-that-returns-a-value}

The .NET Framework also has the generic version of task Task&lt;T&gt; class that you can use if a Task should return a value. Here T is the data type you want to return as a result. Below code shows how this works.

```cs
using System;
using System.Threading.Tasks;

namespace Example2
{
    class Program
    {
        static void Main(string[] args)
        {
            Task<int> t = Task.Run(() =>
            {
                return 32;
            });
            Console.WriteLine(t.Result); // Displays 32

            Console.WriteLine();
            Console.WriteLine("Press Enter to terminate!");
            Console.ReadLine();
        }
    }
}
```

Attempting to read the `Result` property on a Task will force the thread that's trying to read the result to wait until the task is finished, which is equivalent to calling `Join` method on a thread and calling `Wait` method in the task as mentioned before.

As long as the Task has not finished, it is impossible to give the result. If the task is not finished, this call will block the current thread.

## How To Avoid Blocking Of Current Thread? How To Be Responsive?

As I mentioned at the start of this chapter,

> Tasks can be used to make your application more responsive.

But, until now the only thing I told you is this,

> Calling `Wait` method of the task is equivalent to calling the `Join` method on a thread. When the `Wait` method is called within the `Main` method the main thread pauses its execution until the task `t` completes its execution.

and this,

> Attempting to read the `Result` property on a Task will force the thread that's trying to read the result to wait until the task is finished.

Then how in the world task can help be responsive? Your question is right and below is the answer that you are looking for.

## Adding A Continuation {#adding-a-continuation}

Another great feature that task supports is the continuation. This means that you can execute another task as soon as the first task finishes. Thus, you can avoid the block that we discussed before. This method is similar to calling the callback method when a certain operation is finished.

Below is an example of creating such a continuation.

```cs
using System;
using System.Threading.Tasks;

namespace Example3
{
    class Program
    {
        static void Main(string[] args)
        {
            Task<int> t = Task.Run(() =>
            {
                return 32;
            }).ContinueWith((i) =>
            {
                return i.Result * 2;
            });

            t.ContinueWith((i) =>
            {
                 Console.WriteLine(i.Result);
            });

            Console.WriteLine("Press Enter to terminate!");
            Console.ReadLine();
        }
    }
}
```

## Scheduling Different Continuation Tasks {#scheduling-different-continuation-tasks}

The ContinueWith method has a couple of overloads that you can use to configure when the continuation will run. This way you can add different continuation methods that will run when an exception happens, the Task is canceled, or the Task completes successfully. Below code shows how to do this.

```cs
using System;
using System.Threading.Tasks;

namespace Example_4
{
    class Program
    {
        static void Main(string[] args)
        {
            Task<int> t = Task.Run(() =>
            {
                return 32;
            });
            t.ContinueWith((i) =>
            {
                Console.WriteLine("Canceled");
            }, TaskContinuationOptions.OnlyOnCanceled);
            t.ContinueWith((i) =>
            {
                Console.WriteLine("Faulted");
            }, TaskContinuationOptions.OnlyOnFaulted);
            var completedTask = t.ContinueWith((i) =>
            {
                Console.WriteLine(i.Result);
                Console.WriteLine("Completed");
            }, TaskContinuationOptions.OnlyOnRanToCompletion);
            
            Console.WriteLine("Press Enter to terminate!");
            Console.ReadLine();
        }
    }
}
```

Using task with continuation is a great way to create a responsive application that doesn't block the main thread. In desktop applications like Windows Forms and WPF, we can use this feature of the task to create a very responsive application that doesn't block the UI thread.

## Demo Of A Non-Responsive Application {#demo-of-a-non-responsive-application}

To give demo of a non  responsive application I created a simple Windows Forms application which you can download from [here][project-files]. In this application we simply calculate the nth term of fibonacci series. In the application when we put some higher value (ex: "900000000") for calculating nth term we see that application UI get lock up until the calculation is going on and after a while when execution is complete we see the result along with time required by program to get result.

Here is the code of the application,

```csharp
using System;
using System.Diagnostics;
using System.Windows.Forms;

namespace _02_Program_For_Responsiveness
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Calculate_Click(object sender, EventArgs e)
        {           
            var stopWatch = new Stopwatch();
            stopWatch.Start();
            textBox2.Text = Fibo(textBox1.Text).ToString();
            stopWatch.Stop();
            label2.Text = (stopWatch.ElapsedMilliseconds / 1000).ToString();
        }

        private ulong Fibo(string nthValue)
        {        
            try
            {
                ulong x = 0, y = 1, z = 0, nth, i;
                nth = Convert.ToUInt64(nthValue);
                for (i = 1; i <= nth; i++)
                {
                    z = x + y;
                    x = y;
                    y = z;
                }

                return z;
            }
            catch { }

            return 0;
        }
    }
}
```

## Adding Tasks For Responsiveness {#adding-tasks-for-responsiveness}

Lets add task in our ```Calculate_Click``` method to make it responsive,

```csharp
 private void Calculate_Click(object sender, EventArgs e)
        {
            var task=new Task(() =>
            {
                var stopWatch = new Stopwatch();
                stopWatch.Start();
                textBox2.Text = Fibo(textBox1.Text).ToString();
                stopWatch.Stop();
                label2.Text = (stopWatch.ElapsedMilliseconds / 1000).ToString();
            });

            task.Start();
        }
```

When you run this version of application you will find that its not updating values; Also if you look into debug window of visual studio you will notice that there is InvalidOperationExecption raised each time you hit the calculate button.

**Why?**

WPF and most Windows UI frameworks have something called "[Thread affinity][thread-affinity]". This means you can't change UI stuff on any thread that isn't the main UI thread.

**First solution attempt**

To solve this problem one thing we can do is we can specify the .NET to run this particular task in UI thread which is our current thread instead of running into a separate thread by using the task scheduler parameter in ```task.Start()``` method like this,

```csharp
task.Start(TaskScheduler.FromCurrentSynchronizationContext());
```

Here is the complete ```Calculate_Click``` method example,

```csharp
 private void Calculate_Click(object sender, EventArgs e)
        {
            var task=new Task(() =>
            {
                var stopWatch = new Stopwatch();
                stopWatch.Start();
                textBox2.Text = Fibo(textBox1.Text).ToString();
                stopWatch.Stop();
                label2.Text = (stopWatch.ElapsedMilliseconds / 1000).ToString();
            });

            task.Start(TaskScheduler.FromCurrentSynchronizationContext());
        }
```

However when you run this version of application you will find that user interface is once again lock up. 

**Again Why?**

Because, we're running the task on the same UI thread, consuming the same UI thread's execution cycle until the task execution is done, which is essentially the entire time which locks up the user interface and thus we're back to where we started.

**Correct solution**

The key solution to above problem is that we want to do the CPU intense work in one task which will run on worker thread by default and run the UI related work on another task, and the key is here that we want to run the second task only after when the first task is done with its execution. To achieve this functionality  task provide ```ContinueWith``` method which does the exact same thing here is the solution.

**Also,**

You have to use a `TaskScheduler` associated with the current UI SynchronizationContex as second parameter in `task.ContinueWith` to run new continuation task on the UI thread.

Let's see how the final code will look like,

```csharp
private void Calculate_Click(object sender, EventArgs e)
        {
            Stopwatch stopWatch = new Stopwatch();
            string result="";

            var task=new Task(() =>
            {
                stopWatch.Start();
                result = Fibo(textBox1.Text).ToString();                
            });

            task.ContinueWith((previousTask) =>
            {
                textBox2.Text = result;
                stopWatch.Stop();
                label2.Text = (stopWatch.ElapsedMilliseconds / 1000).ToString();
                stopWatch.Reset();
            },
            TaskScheduler.FromCurrentSynchronizationContext()
            );

            task.Start();
        }
```
And that's how it's done!

However there are still some other improvements that can be done in the above code which we will discuss in the [next chapter](/tasks-in-csharp/async-await-c/). In the next chapter you'll learn what are async and await keywords in C#, and how to use async-await feature along with Task for asynchronous programming.

[thread-affinity]: https://stackoverflow.com/questions/8733303/why-do-ui-controls-in-wpf-have-thread-affinity
[project-files]: https://github.com/kudchikarsk/tasks-in-csharp