---
layout: post
title: Azure Command Queue
category: Vigil Journey
treeid: 
tags:
- azure
- cqrs
---

Command Query Responsibility Segregation ([CQRS](https://martinfowler.com/bliki/CQRS.html)) was not something that I easily understood. Martin Fowler did a thorough job of explaining it in often-linked post from 2011. However, I still did not quite grasp it at the time. I also wasn't working on any project large enough or complex enough that it made sense to use a pattern with that level of depth. When I came back to the concept many years later while working on a significantly more complex project, I could put together all of the puzzle pieces but I still couldn't quite see the whole picture. While researching the topic, I stumbled on a blog post by Cesar de la Torre from Microsoft - [CQRS BUS and Windows Azure technologies](https://blogs.msdn.microsoft.com/cesardelatorre/2012/02/22/cqrs-bus-and-windows-azure-technologies/). His picture was well worth the thousands of words that I had already read.

![CQRS - Basic patterns](/images/cqrs-diagram.jpg){: .centered }

```csharp
```