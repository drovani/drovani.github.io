---
layout: post
title: 22 Month Status Report
category: Vigil Journey
treeid: 17fad5dea13436de0d640a661537ccfbce9e9c6c
excerpt_separator: <!--more-->
tags:
- vigiljourney
- statusreport
---

As I was writing the [previous post]({% post_url 2017/2017-02-20-Introducing-Vigil-Web-API %}), it came as a shock how far behind my posts on the progress of the Vigil Donor Relationship Management System had gotten. I was going too deep into the code and making too many changes that I didn't pause to write a post about my progress. My posts are 22 commits behind, and there are quite a few changes I've made to the workflow and to the application.

In terms of workflow, I have embraced the [GitHub Flow workflow](https://guides.github.com/introduction/flow/). For my little project, since there is no discussion and no deployments (as of yet), the workflow is as simple as Branch, Commit(s), Pull, Merge, Delete. I like the way that a merge can squash all of the commits made on a branch, so that I have one summarized collection of everything that I did. This way, I can easily differentiate the content of a post by doing one per Pull Request / Merge. Hopefully, this process will now keep the posts coming regularly, help me be more concise about what should be in a post, and better contain runaway developing.

<!--more-->

<aside style="float: left;">
    <img src="/images/vigil-project-22-months.png" alt="Vigil Projects after 22 Months" />
</aside>

### Quantity of code

As I look at the total row count of all of the code, I am actually surprised by how many lines of code I have produced. It is a pittance compared to what I could output if I had been working on this project full time for the last two years. However, for something that I have only been poking at every once in a while, I still find myself astonished that all of the simple "Create this" and "Update that" have turned into this much actionable code.