---
title: "C# Multithreading"
description: "Multithreading in C# is a process in which multiple threads work simultaneously. It is a process to achieve multitasking. It saves time because multiple tasks are being executed at a time."
course: multithreadingcsharp
comments: true
aside: multithreading_tutorial_aside.html
redirect_from: "/c-threading-tutorial/"
author: shadman_kudchikar
---

Multithreading in C# is a process in which multiple threads work simultaneously. It is a process to achieve multitasking. It saves time because multiple tasks are being executed at a time. To create multithreaded application in C#, we need to use `System.Threading` namespace.

The `System.Threading` namespace contains classes and interfaces to provide the facility of multithreaded programming. It also provides classes to synchronize the thread resource.

In this tutorial you will learn how to take advantage of Thread Class provided by C# in `System.Threading` namespace to create robust applications that are responsive and parallel.

Futher, we will discuss the issue in terms of program correctness in a multithreaded program. And then we'll take a look at some of the Thread synchronization techniques that are available to you as a .NET programmer.

Finally, we're going to look at Task-based approach provided by C# for async and parallel programming.


## Modules

{% assign courses = "multithreadingcsharp" | split: ',' %}
<ul>
{% for course in courses %}		
{% for item in site.courses[course] %} 
{% assign currIndex = forloop.index0 %}	
{% if currIndex != 0 %} 					  					
<li>
<p>
<a href="{{ item.url }}">{{ item.title }}</a> - {{ item.description }}
</p>
</li>  	
{% endif %}
{% endfor %}		
{% endfor %}
</ul>	 

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

## Requirements

* You will need Visual Studio IDE (for programming in C# .NET) to get started with this tutorial.
* All demos in this tutorial are built using Visual Studio and complete solution demo is available here:
	- [Solution 1](https://github.com/kudchikarsk/multithreading-in-csharp-demo)
	- [Solution 2](https://github.com/kudchikarsk/thread-synchronization-in-csharp)
	- [Solution 3](https://github.com/kudchikarsk/tasks-in-csharp)
	- [Solution 4](https://github.com/kudchikarsk/csharp-task)

## Issues

* You can use the Comments section at the bottom of each module to ask a question or report a problem.



[Instagram]: https://www.instagram.com/kudchikarsk
[LinkedIn]: https://linkedin.com/in/kudchikarsk