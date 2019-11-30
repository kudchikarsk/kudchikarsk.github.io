--- 
title: Programming For Responsiveness
description: This article explains basic Task class usage provided by C# .NET. Here I'm going to fist show a demo application which does not have a responsive user interface and gets lock up when user interacts, then we will fix this application code for responsiveness.
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
author: shadman_kudchikar
---

## Content

* [Demo Of A Non-Responsive Application](#demo-of-a-non-responsive-application)
* [Adding Tasks For Responsiveness](#adding-tasks-for-responsiveness)

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

However there are still some other improvements that can be done in the above code which we will discuss in the [next chapter](/tasks-in-csharp/async-await-c/).

[thread-affinity]: https://stackoverflow.com/questions/8733303/why-do-ui-controls-in-wpf-have-thread-affinity
[project-files]: https://github.com/kudchikarsk/tasks-in-csharp