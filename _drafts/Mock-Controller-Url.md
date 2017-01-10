---
layout: post
title: Mock Controller.Url in ASP.NET Core MVC
category: Vigil Journey
treeid: 96ccfc6f1e4326a7f2809976ee88c4388a624804
tags: 
- moq
- mock
- unittests
- controller
---

I have come to the point where I am building out the initial proof-of-concept for the WebAPI portion of this project. This gives me a place to continue testing out features, and see if how I envisioned things would work can actually work. One of the first parts of unit testing the controller was to mock any of the properties that I need to use that the web server would ordinarily wire up. In a `Controller` that primarily entails the `ControllerContext`, which would also wire the `Controller.User` and `Controller.HttpContext` properties, too. So how do I do that?

### Mock the User

I use Moq as my mocking framework.

{% highlight c# linenos=table %}
{% endhighlight %}