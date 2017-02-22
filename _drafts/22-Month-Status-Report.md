---
layout: post
title: 22 Month Status Report
category: Vigil Journey
treeid: dc688a9322bb3467b39c8d66a8c8883482e0a6f7
excerpt_separator: <!--more-->
tags:
- vigiljourney
- statusreport
---

As I was writing the [previous post]({% post_url 2017/2017-02-20-Introducing-Vigil-Web-API %}), it came as a shock how far behind my posts on the progress of the Vigil Donor Relationship Management System had gotten. I was going too deep into the code and making too many changes that I didn't pause to write a post about my progress. My posts are 22 commits behind, and there are quite a few changes I've made to the workflow and to the application.

In terms of workflow, I have embraced the [GitHub Flow workflow](https://guides.github.com/introduction/flow/). For my little project, since there is no discussion and no deployments (as of yet), the workflow is as simple as Branch, Commit(s), Pull, Merge, Delete. I like the way that a merge can squash all of the commits made on a branch, so that I have one summarized collection of everything that I did. This way, I can easily differentiate the content of a post by doing one per Pull Request / Merge. Hopefully, this process will now keep the posts coming regularly, help me be more concise about what should be in a post, and better contain runaway developing.

<!--more-->

<aside style="float: right;">
    <img src="/images/vigil-project-22-months.png" alt="Vigil Projects after 22 Months" />
</aside>

### Quantity of code

As I look at the total row count of all of the code, I am actually surprised by how many lines of code I have produced. It is a pittance compared to what I could output if I had been working on this project full time for the last two years. However, for something that I have only been poking at every once in a while, I still find myself astonished that all of the simple "Create this" and "Update that" have turned into this much actionable code.

### Vigil.Domain - Used Everywhere!

The Domain project contains a lot of interfaces and a few abstract classes. The key functionality that has been created here are the structure for what it takes to build a messaging and event sourcing system. More will probably be added here as a way to keep shared functionality across different implementations - be in for `Patron` entities or an Ordering and Inventory system or possibly a job taks/queue system.


### Future Tangents

There are a number of quality-of-life improvements that could easily cause me to stray down a long tangent of work. It would be nice if my project templates included all of the settings that I apply to each new project. These include:

- Set the "Author" to "drovani"
- Set "RepositoryType" to "git"
- Set "RepositoryUrl" to "https://github.com/drovani/Vigil"
- Clear the "Description"
- Set "SignAssembly" to "true", and set the "AssemblyOriginatorKeyFile" to the `vigil.snk` in the root solution folder
- For Test projects:
  - Set the "RootNamespace" to be the same as the project being tested.
  - Add `xunit.runner.json` and set the "methodDisplay" to "method"
  - Include the `Moq`, `xunit`, and `xunit.runner.visualstudio` NuGet packages

Other side projects would include putting together all of the shiny automated build routines that all of the big projects have. Being able to have Pull Requests kick off builds to make sure nothing breaks - that would be cool. Turning all of the projects into NuGet packages and making them a little more portable would be an interesting experiment.

My problem is that each of these tangents only delay my progress on creating productive code that actually leads the project to a useful state.