---
layout: post
title: Discovering the ASP.NET Core MVC Stack
category: Vigil Journey
treeid: 
series: discovering-mvc
tags:
- coremvc
---

Now that the Vigil Project has breached into the API layer, I started to learn all kinds of new information about what takes place before the Controller's Action method is called and what happens after that method returns. The MVC framework, especially while using Visual Studio, make it incredibly easy to forget about all of the heavy lifting that the framework does &ndash; long before my code is ever executed. It is only when I wanted to start changing how pieces worked that I really start to see the true depth and breadth of what Microsoft has created. As I begin to discover new pieces of the framework, I felt that the best way to retain this information is to repeat it; and since no one around me wants to hear about all of this, I'll just type it into a blog post.

{% include series.html %}

### Upon First Introduction

My first introduction to MVC (through various tutorials) gave me the impression that the full stack looks basically like this:

<pre>
Server                           Controller's                       Server
Receives      ------------->       Action       ------------->      Sends
Request                            Method                           Response
</pre>

There's a lot of black box magic going on in those arrows. But you know what? It doesn't matter. When all I wanted to do was respond to a simple request, then all I had to do was create a `Controller` and an `Action` and then _magic happened_, and I got a functioning application. Do I have any clue about how the data got to the method? Or how the `Controller` was even instantiated in order for _something_ to have an object to call my method? __Nope__! And that's the point.

### The First Challenge - Explicit Constructor

The Create action for the `BaseController` is simple. It just take an instance of the `CreatePatron` command and publishes is to the `CommandQueue`. This is essentially the same as the unit test that proved that a "[patron can be created]({% post_url 2016/2016-11-01-Patron-Can-Be-Created-and-Updated %})".

```csharp
[HttpPost]
public IActionResult Create([FromBody]CreatePatron command)
{
    if (command == null || !ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }
    else
    {
        CommandQueue.Publish(command);
        return Accepted(Url.Action(nameof(Get),
            new { id = command.PatronId }));
    }
}
```

However, this quickly goes awry the first time I execute the code, because the `CreatePatron` command (as with all `PatronCommand` classes) has no default constructor. MVC _really_ wants a default constructor, but I _really_ want to guarantee that the `GeneratedBy` and `GeneratedOn` values are properly populated. "Surely," though I, "this should be an easy update and I'll be able to move onto something else."

I was wrong.

### How Does an `IInputFormatter` Get Chosen