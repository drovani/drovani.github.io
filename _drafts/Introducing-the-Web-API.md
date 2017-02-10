---
layout: post
title: Introducing the Web API
category: Vigil Journey
treeid: d21abe492fae5bcfc2b713442bf6df88cc386cda
tags:
- webapi
- journey
- mvccore
---

Thus far in the series, the Vigil Journey project has been able to [create a patron]({% post_url 2016-09-16-User-Can-Create-New-Patron %}), and [update a patron]({% post_url 2016-10-19-User-Can-Update-Patron %}), and restate it as [a patron can be created and updated]({% post_url 2016-11-01-Patron-Can-Be-Created-and-Updated %}). However, as it has been acknowledged in the posts, this was all smoke and mirrors. Nothing was actually being persisted anywhere, and once the unit test was run, there was nothing to actual show for the code. All of the tests passed and there were lots of pretty green checkmarks, but nothing of real substance. This was my primary motivator for throwing together the [`SqlCommandQueue` and `SqlEventBus`]({% post_url 2017-01-26-Illusions-of-Queues-and-Buses %}) classes. I wanted to be able to demonstrate _something_.

While my initial instinct was to spin up a complete user interface for this, I held back and took on the next layer of the project - which is to instantiate the `CreatePatron` command and pass it to an `ICommandQueue`. Since that is now wired to an actual database, I would then have persisted `Events` and be able to have some semi-tangible evidence that I was actually progressing.

### ASP.NET Core MVC as an API

{% highlight c# linenos=table %}
{% endhighlight %}