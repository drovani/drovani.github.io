---
layout: post
title: Materialize A Patron
category: Vigil Journey
treeid: 
tags:
- versionedevent
- eventsourcing
---

There are two ways to retrieve the current values associated with an entity. The canonical source is the collection of events that describe how an entity came into existance and then was affected over time. I am calling this "materializing" the entity. Once the current state of the entity is found, it can then be persisted to a different medium for easier access. In the example of a `Patron`, there are few circumstances where the entire history of the entity is needed. Instead, most requests for the information will only be looking for the most current iteration.

{% highlight c# linenos=table %}

{% endhighlight %}