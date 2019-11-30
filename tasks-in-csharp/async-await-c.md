--- 
title: C#&colon; Using Async and Await to Run Code Asynchronously
description: In this article, you’ll learn about the Task-based asynchronous programming model along with async and await keyword in C#. You will also learn about the application of this asynchronous principle across .Net Applications.
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
author: shadman_kudchikar
---

In this article, you’ll learn about the Task-based asynchronous programming model along with async and await keyword in C#. You will also learn about the application of this asynchronous principle across .Net Applications.

When you write code as a sequence of statements using async and await keyword, you can read that code as each statement completes before the next begins. 

The async keyword only enables the await keyword (and manages the method results). The beginning of an async method is executed just like any other method. That is, it runs synchronously until it hits an "await" (or throws an exception). The "await" keyword is where things can get asynchronous.

Here, compiler performs a number of transformations because some of those statements may start work and return a Task that represents the ongoing work. Task object typically executes asynchronously on a thread pool thread rather than synchronously on the main application thread. For more information on Tasks, see my blog post [C# Task](/tasks-in-csharp/csharp-task/).

**C# Task along with async await keyword enables developers to define asynchronous functions within a single method definition, instead of having "begin" and "end" function pairs or separate callbacks. This makes coding the asynchronous function very intuitive and clear.**

That's the goal of this syntax: enable code that reads like a sequence of statements, but executes in a much more complicated order based on external resource allocation and when tasks complete.

This Task-based asynchronous programming model provides an abstraction over asynchronous code. Let's dive deeper to better understand this concept.

## Content
- [Callback Hell](#callback-hell)
- [Async Await Keyword](#async-await-keyword)
- [Application Of Asynchronous Principles Accross .Net](#application-of-asynchronous-principles-accross-net)
- [Furthur Reading](#furthur-reading)

## Callback Hell
In previous chapter we discussed how to use the [Continuation](/tasks-in-csharp/csharp-task/#adding-a-continuation) feature of a [C# Task](/tasks-in-csharp/csharp-task/) to make a responsive application. This means that you can execute another task as soon as the first task finishes. Thus, you can avoid the thread block.

Below is another example of creating such a continuation,

Here I made a small WPF application that fetches data from the URL given in the textbox. You can find the complete project [here](https://github.com/kudchikarsk/async-await-demo).

```cs
using System;
using System.Net.Http;
using System.Threading.Tasks;
using System.Windows;

namespace WpfApp
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void Button_Click(object sender, RoutedEventArgs e)
        {
            var url = textBox.Text;
            var isValidUrl =  Uri.TryCreate(url, UriKind.Absolute, out _);
            if (!isValidUrl)
            {
                textBlock.Text = "Given url is not valid.";
                return;
            }

            var currentContext = TaskScheduler.FromCurrentSynchronizationContext();
            var httpClient = new HttpClient();

            var responseTask = httpClient.GetAsync(url);
            //First continuation start
            responseTask.ContinueWith(r => {
                try
                {
                    var response = r.Result;
                    response.EnsureSuccessStatusCode();

                    var dataTask = response.Content.ReadAsStringAsync();
                    //Second continuation start
                    dataTask.ContinueWith(d => {
                        textBlock.Text = d.Result;
                    }, currentContext);
                    //Second continuation end
                }
                catch (Exception ex)
                {
                    textBlock.Text = ex.Message;
                }
            });
            //First continuation ends
        }
    }
}
```

Here `.ContinueWith` what simply does is that it adds the action delegate, defined as its first parameter, in the task callback event. And this delegate gets callback when the task operation is finished.

You can learn more about delegates and events [here](/delegates-and-events-in-csharp/).

Callbacks are not all bad; they worked — they still do. But, what happens if we have a callback inside a callback, inside a callback — you get the point. It gets messy and unmaintainable really quick.

The problem described above is named *"callback hell"*. Which you can see a bit in the above example.

To overcome the above problem which is more related to code readability, C# introduced the async await keyword. Let's try it.

Also, you may have noted the following line in the above code: 

```cs
var currentContext = TaskScheduler.FromCurrentSynchronizationContext();
```

The `currentContext` is used to pass as parameter in the second continuation which in turn helps to overcome the problem of thread affinity that we already discussed in the [previous chapter](/tasks-in-csharp/csharp-task/#adding-tasks-for-responsiveness).



## Async Await Keyword

A very common thing to first try out when you encounter asynchronous principles in .NET is to simply mark your method with the async keyword. Let's go ahead and try that and also remove all the continuation from the code and directly use the .Result property to access the result from our asynchronous task operation.

Let's see how that affects our application. 

```cs
using System;
using System.Net.Http;
using System.Windows;

namespace WpfApp
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private async void Button_Click(object sender, RoutedEventArgs e)
        {
            var url = textBox.Text;
            var isValidUrl =  Uri.TryCreate(url, UriKind.Absolute, out _);
            if (!isValidUrl)
            {
                textBlock.Text = "Given url is not valid.";
                return;
            }

            var httpClient = new HttpClient();
            var response = httpClient.GetAsync(url).Result;
            try
            {

                response.EnsureSuccessStatusCode();
                var data = response.Content.ReadAsStringAsync().Result;
                textBlock.Text = data;
            }
            catch (Exception ex)
            {
                textBlock.Text = ex.Message;
            }
        }
    }
}
```

You'll notice here that I can mark my `Button_Click` handler as async. And if you run the application and see if this affected the performance of our application, you'll quickly notice that the application UI gets locks up. Let's jump into the code and discuss about why this is still not an asynchronous operation. 

![](/images/async-await/demo1.JPG)

As you see here, Visual Studio will tell us that this method is marked as async, but it lacks the await keyword. So the code inside this method will still run synchronously, and that's a big problem because we want to leverage the asynchronous principles. 

Why is this a problem?

Now, in the above code you may have seen the following line:

```cs
var response = httpClient.GetAsync(url).Result;
```

Here GetAsync returns a task of an HttpResponseMessage, a task which discussed in the previous chapter is a representation of our asynchronous operation. This asynchronous operation happens on a different thread. 

So if we call `.Result`, which is one of the first things that people try to get the Result out of their asynchronous operation, this is actually a really bad idea.

It will actually block the thread until this Result is available, so this is really problematic because this means that code will run synchronously. 

Instead of calling `.Result`, how do we make sure that we get the HttpResponseMessage out of our asynchronous operation and only proceed when this done that too in a callback way without using the ContinueWith method in order to attain the readability?

Actually,

What we need to do is to make sure that whenever we encounter the async keyword, we also have the await keyword inside that same method. Like this,

```cs
var response = await httpClient.GetAsync(url);
```

**The await keyword is a way for us to indicate that we want to get the Result out of this asynchronous operation only once the data is available without blocking the current thread.** So the above code gives us the HttpResponseMessage. 

Also,

While reading the content from the response you'll find that ReadAsString is also an asynchronous operation, and it also hints us here that we need to await this as well.

```cs
var data = await response.Content.ReadAsStringAsync();
```

We could, of course, say ReadAsStringAsync and then call the Result property, but this would block again and make this code run synchronously, and in a lot of cases, calling `.Result` or `.Wait` will, in fact, deadlock the application. So avoid calling `.Result` or `.Wait`.

Let's see the final result with all the changes we did,

```cs
using System;
using System.Net.Http;
using System.Windows;

namespace WpfApp
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private async void Button_Click(object sender, RoutedEventArgs e)
        {
            var url = textBox.Text;
            var isValidUrl =  Uri.TryCreate(url, UriKind.Absolute, out _);
            if (!isValidUrl)
            {
                textBlock.Text = "Given url is not valid.";
                return;
            }

            var httpClient = new HttpClient();
            var response = await httpClient.GetAsync(url);
            try
            {

                response.EnsureSuccessStatusCode();
                var data = await response.Content.ReadAsStringAsync();
                textBlock.Text = data;
            }
            catch (Exception ex)
            {
                textBlock.Text = ex.Message;
            }
        }
    }
}

```

So,

The await keyword, allows us to retrieve the result out of our asynchronous operation when that's available. It also makes sure that there is no exceptions or problems with the task that it's currently awaiting. **So not only is the await keyword a great way for us to get the Result out of the asynchronous operation. It also validates the current operation.** 

And,

**What it's also doing is introducing continuation, as we've mentioned earlier, the await keyword does the same behind the scene and puts all the code beneath it inside the continuation.**

> **Note:** We also have a second await keyword, which in its turn introduces a continuation for the code beneath that as well. You can have as many await keywords as you want inside your asynchronous method. They will all introduce a continuation, which allows you to make sure that the code beneath the await keyword is only executed once the asynchronous operation that you're awaiting is completed. 

## Application Of Asynchronous Principles Accross .Net

Asynchronous principles are suited for any type of I/O operations. As we do in this case, we interact with an API over the web, but it could also be reading and writing from disk or memory or do things like database operations. In our case here, we're fetching some data from our API using the GetAsync method on our HttpClient. 

The asynchronous principles that we talk about in our applications are not only meant for Windows application or mobile applications. We can also apply the same principle to the server-side code in ASP.NET. 

Let's see an example:

```cs
using System;
using System.Net.Http;
using System.Threading.Tasks;
using System.Web.Http;

namespace WebApplication.Controllers
{
    public class TestController : ApiController
    {
        // GET: api/Test
        public async Task<IHttpActionResult> Get(string url)
        {
            var isValidUrl = Uri.TryCreate(url, UriKind.Absolute, out _);
            if (!isValidUrl)
            {
                return BadRequest("Given url is not valid.");
            }

            var httpClient = new HttpClient();
            var response = await httpClient.GetAsync(url);
            try
            {

                response.EnsureSuccessStatusCode();
                var data = await response.Content.ReadAsStringAsync();
                return Ok(data);
            }
            catch (Exception ex)
            {
                return BadRequest(ex.Message);
            }
        }
    }
}

```

Here is a test controller inside a web project that's allowing us to pretty much do the same thing that we do in our Windows application. You can see here that we have the async keyword. 

Our action inside of our controller returns a Task of ActionResult. This means that ASP.NET will have a reference to the ongoing asynchronous operations and will then when all the results are available, get the ActionResult out of this action. 

Now what's interesting here is that this is not, in fact, making the client asynchronous. 

So, what's the benefit?

The benefit of using async and await inside ASP.NET is to relieve IIS or the web server that you were using so that it can go ahead and work with other requests as your data is being loaded from disk, the database, or from another API. The primary benefit of asynchronous code on the server-side is scalability.

So as you notice here, the asynchronous principles are really powerful no matter if we are working in ASP.NET, in Windows, or any type of .NET applications.

## Furthur Reading
- [Async/Await - Best Practices in Asynchronous Programming](https://msdn.microsoft.com/en-us/magazine/jj991977.aspx) by [Stephen Cleary](https://blog.stephencleary.com/) - This article is intended as a “second step” in learning asynchronous programming. This article highlights best practices that can get lost in the avalanche of available documentation.

- [Concurrency in C# Cookbook: Asynchronous, Parallel, and Multithreaded Programming 2nd Edition](https://amzn.to/2HoBUeU) by [Stephen Cleary](https://blog.stephencleary.com/) - If you’re one of many developers still uncertain about concurrent and multithreaded development, this practical cookbook will change your mind. With more than 85 code-rich recipes in this updated second edition, author Stephen Cleary demonstrates parallel processing and asynchronous programming techniques using libraries and language features in .NET and C# 8.0. 

- [Concurrency in .NET: Modern patterns of concurrent and parallel programming](https://amzn.to/2ZpAZRI) by [Riccardo Terrell](http://www.rickyterrell.com/) - Concurrency in .NET teaches you how to build concurrent and scalable programs in .NET using the functional paradigm. This intermediate-level guide is aimed at developers, architects, and passionate computer programmers who are interested in writing code with improved speed and effectiveness by adopting a declarative and pain-free programming style.

In the [next chapter](/tasks-in-csharp/task-parallelism-c/) we will look at different methods to achieve parallelism using Tasks in C#.