---
layout: post
title: Adding Create Functionality
series: Technical Assessment
category: "Rovani in C&sharp;"
treeid: techassessment-basic/tree/590d44659fd286391e44d12fbce753963d71328a
tags:
- career
- angular
---

I am starting to make some real progress with this. I have a better idea of how components, service injection, and input binding works in Angular. These are all concepts that I am very familiar with in my extensive use of [Knockout](https://knockoutjs.com/), but anytime one is learning a new framework, there will be a learning curve in figuring out how _this particular framework_ implements various features. Next on the requirements list is the ability to create a new blog post.

{% include series.html %}

## Creating a Form

Back to the Angular tutorial, this piece took several steps to get to where I needed to be to start writing code. Unfortunately, the opening tutorial ([Add a new hero](https://angular.io/tutorial/toh-pt6#add-a-new-hero)) only shows how to declare an input's id and then how to wire a button to pass the value of that element back to the component. This works fine for a single value, but I need the ability for a user to edit multiple fields and send them all to component. I now needed to jump into the "Fundamentals" section of the docs; specifically, the "[Forms](https://angular.io/guide/forms-overview)" section.

![Create New Blog Post](/images/techass-create-new-blog-post.png)

Working through everything, now was the time for the big test - would it actually submit my data to the server?

![POST Request Body](/images/techass-post-request-body.png)